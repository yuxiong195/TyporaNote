# 校园论坛网站项目

## 登录模块功能

### 表结构设计

（salt字段（UUID随机字符串0-5）+password字段）结合md5加密

激活码字段：用于邮箱激活账号（UUID）

状态字段：用于验证用户是否激活



### 注册功能

1. 激活过程先存入用户数据，生成salt字段和激活码字段

2. 然后发送激活邮件（该邮件需要携带生成的激活码，激活路径（包含userID，激活码，用于访问激活控制器），邮件地址）



### 验证码功能

1、使用kaptchaProducer包生成验证码图片image和字母text。

2、再使用UUID生成kaptchaOwner装入cookie唯一标识该验证码，设置生存时间，响应给客户端

3、使用kaptchaOwner作为键，text作为值存入redis中，并将image以image/png格式输出给客户端



### 请求的权限校验

#### 构建登录凭证LoginTicket类

代码

```java
public class LoginTicket {

    private int id;
    private int userId;
    private String ticket;
    private int status;
    private Date expired;
}
```

可以把他理解为一张权限令牌，就像工牌，上面记录着userId、状态、Ticket（校验码）、获取时间。

用户登录时会生成该登录凭证实例，并使用Ticket为键登录凭证实例为值存入redis。

![image-20211015033249337](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20211015033249337.png)



#### 实现拦截器检验

后续请求使用拦截器实现权限校验，特点：拦截的是URL，从cookie中获取凭证。一般情况下，所有的请求（URL）都需要做登陆校验。

```java
public class LoginTicketInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // 从cookie中获取凭证
        String ticket = CookieUtil.getValue(request, "ticket");

        if (ticket != null) {
            // 查询凭证
            LoginTicket loginTicket = userService.findLoginTicket(ticket);
            // 检查凭证是否有效
            if (loginTicket != null && loginTicket.getStatus() == 0 && loginTicket.getExpired().after(new Date())) {
                // 根据凭证查询用户
                User user = userService.findUserById(loginTicket.getUserId());
                // 在本次请求中持有用户
                hostHolder.setUser(user);
            }
        }

        return true;
    }
}
```



这里使用的是拦截器实现了HandlerInterceptor接口

参考（Spring 过滤器 拦截器 AOP区别通俗易懂）：https://blog.csdn.net/dreamwbt/article/details/82658842



### 登录对象信息持有

通过redis旁路缓存（根据userID）的user对象，结合Thread Local实现登录对象信息持有。一个请求对应一个服务器线程（Thread-per-Request）来进行操作。





#### ThreadLoal学习

ThreadLocal(线程本地变量)通常理解为“采用了空间换时间的设计思想，主要用来实现在多线程环境下的线程安全和保存线程上下文中的变量”。

使用ThreadLoal存储用户信息，用于代替session对象存储用户信息，好处有：

- 比如线程中处理一个非常复杂的业务，可能方法有很多，线程中的很多方法都需要用到session内的对象。那么，使用 ThreadLocal 可以代替一些参数的显式传递，也便于开发编码；
- Session 的特性很适合 ThreadLocal ，因为 Session 之前当前会话周期内有效，会话结束便销毁。



使用：创建HostHolder类封装ThreadLoal对象

- 因为每个 Thread 内有自己的实例副本，且该副本只能由当前 Thread 使用。这是也是 ThreadLocal 命名的由来。
- 既然每个 Thread 有自己的实例副本，且其它 Thread 不可访问，那就不存在多线程间共享的问题。

于是实际的流程就是：每个用户的请求都会被LoginTicketInterceptor的preHandle拦截，然后从cookie中获取凭证（LoginTicket类），判断该请求是否带有凭证并检查凭证是否有效（这里也是使用键去redis中找），然后根据凭证中的userId查询得到用户所有信息user（这一步也是去redis找）。找到后``hostHolder.setUser(user);``，将user存入该实例的ThreadLocal<User>的。	





##### **ThreadLocal实现原理**

**Thread-》ThreadLocalMap**

**ThreadLocal类内部维护了一个静态内部类ThreadLocalMap。ThreadLocalMap底层是一个Entry数组。ThreadLocal维护的映射关系是：以当前threadlocal对象为键，以线程私有变量为值存入Entry。所以一个线程可以有多个ThreadLocal，它们的关系是一个threadlocal对于一个私有变量。**



项目应用简单展示源码构造一个ThreadLocalMap过程：

```java
public class HostHolder {

    private ThreadLocal<User> users = new ThreadLocal<>();

    public void setUser(User user) {
        users.set(user);
    }

    public User getUser() {
        return users.get();
    }

    public void clear() {
        users.remove();
    }

}

public class Thread implements Runnable {
	/* ThreadLocal values pertaining to this thread. This map is maintained
     * by the ThreadLocal class. */
    ThreadLocal.ThreadLocalMap threadLocals = null;
 
    
}

public class ThreadLocal<T> {
    //空参构造
    public ThreadLocal() {
    }
    
    /**
     * Sets the current thread's copy of this thread-local variable
     * to the specified value.  Most subclasses will have no need to
     * override this method, relying solely on the {@link #initialValue}
     * method to set the values of thread-locals.
     *
     * @param value the value to be stored in the current thread's copy of
     *        this thread-local.
     */
    public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);//如果没有就在这创建ThreadLocalMap
    }
    
    /**
     * Create the map associated with a ThreadLocal. Overridden in
     * InheritableThreadLocal.
     *
     * @param t the current thread
     * @param firstValue value for the initial entry of the map
     */
    void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);//这里将Thread的引用指向实例化
    }
    
    static class ThreadLocalMap{
                /**
         * Construct a new map initially containing (firstKey, firstValue).
         * ThreadLocalMaps are constructed lazily, so we only create
         * one when we have at least one entry to put in it.
         */
        ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
            table = new Entry[INITIAL_CAPACITY];
            int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
            table[i] = new Entry(firstKey, firstValue);
            size = 1;
            setThreshold(INITIAL_CAPACITY);
        }
    }
}

    

```



以下啰嗦的废话：

一个线程内可以存在多个 ThreadLocal 对象，所以其实是 ThreadLocal 内部维护了一个 Map ，这个 Map 不是直接使用的 HashMap ，而是 ThreadLocal 实现的一个叫做 ThreadLocalMap 的静态内部类，Thread类中又维护了一个ThreadLocal.ThreadLocalMap，用于指定唯一实例。

ThreadLocalMap底层是一个Entry数组，但是一个Thread可以有多个ThreadLocal，一个ThreadLocal对应一个变量数据，封装成Entry存到ThreadLocalMap中，所以就有多个Entry。比如有两个变量，我就要建两个ThreadLocal对象。

![在这里插入图片描述](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/20210706125817989.png)



##### 内存泄漏问题

> 内存泄漏:指的是堆内存由于某种原因程序未释放或无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统崩溃等严重后果。

每个thread中都存在一个map, map的类型是ThreadLocal.ThreadLocalMap。下面简称为Map。

Map中的key为一个threadlocal实例，每个key都弱引用指向threadlocal。弱引用的特点是，如果这个对象只存在弱引用，那么在下一次垃圾回收的时候必然会被清理掉。

所以如果 ThreadLocal 没有被外部强引用的情况下，在垃圾回收的时候会被清理掉的，这样一来 `ThreadLocalMap` 中使用这个 ThreadLocal 的 key 也会被清理掉。但是，value 是强引用，不会被清理，这样一来就会出现 key 为 null 的 value。

**如何避免内存泄露**

使用完线程后调用 ThreadLocal 的 remove 方法。

参考：https://zhuanlan.zhihu.com/p/354153342



总的来说，ThreadLocal 适用于每个线程需要自己独立的实例且该实例需要在多个方法中被使用，也即变量在线程间隔离而在方法或类间共享的场景。而且在线程中使用它可以给我们提供一个线程内的本地局部变量，这样就可以减少在一个线程中因为多函数之间的操作导致共享变量传值的复杂性，说白了，我们使用ThreadLocal可以做到在一个线程内随时随地的取用，而且与其他的线程互不干扰。

参考：[ThreadLocal原理分析与使用场景](https://www.cnblogs.com/luxiaoxun/p/8744826.html)

[thread、threadlocal、threadLocalMap的关系](https://blog.csdn.net/qq_31363843/article/details/109742402?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-1.no_search_link&spm=1001.2101.3001.4242)





## redis应用场景

​	Redis 是一个独立的系统软件，和业务应用程序是两个软件，当我们部署了 Redis 实例后，它只会被动地等待客户端发送请求，然后再进行处理。所以，如果应用程序想要使用 Redis 缓存，我们就要在程序中增加相应的缓存操作代码。所以，我们也把 Redis 称为**旁路缓存**，也就是说，读取缓存、读取数据库和更新缓存的操作都需要在应用程序中来完成。

减少数据库的交互

### 优化登录模块

**使用Redis缓存用户信息（如findUserById方法）**

- 处理每次请求时，都要根据id凭证查询用户信息，访问的频率非常高，并且很多业务也都需要用到user信息。
- 实现：在UserService中实现User信息的**只读缓存（更新即删除）**

**使用Redis存储登录凭证（如LoginTicketInterceptor拦截器中preHandle方法拦截请求验证登录凭证）** 

- 处理每次请求时，都要查询用户的登录凭证，访问的频率非常高，如findLoginTicket频繁对LoginTicket表进行查询。
- 实现：在UserService中将原先的对MySQL中LoginTicket表的访问转变成对redis的访问

**使用Redis存储验证码** 

- 验证码需要频繁的访问与刷新，对性能要求较高。 
- 验证码不需永久保存，通常在很短的时间后就会失效。
- **分布式部署时，存在Session共享的问题。** 





### 会话管理架构演进

首先敏感数据使用session存放。

粘性session，固定ip发送固定服务器。问题：负载均衡策略难以平衡各服务器负载，不完美

数据库集群存储会话数据——》NoSQL数据库存储

![image-20211015031523178](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20211015031523178.png)



### 点赞，关注，取消关注功能

- 需求

  开发关注、取消关注功能

  统计用户的关注数、粉丝数

- 关键实现

  关注的目标可以是用户、帖子、题目等，在实现时将这些目标抽象为实体，以达到复用代码

- 性能

  需要频繁访问和修改数据，即热数据。另外redis是一个单线程的事务调度，即不用考虑多线程并发会带来的相关线程安全问题。
  
- 实现

  **使用前缀+(userId/entityId)等形式生成唯一键**



session是存储在应用服务器的内存里，redis会部署在单独的服务器上，会做集群的。性能上都是访问内存，差异不大。



问题

Q：mybatis本身是可以开启命名空间缓存的，这个redis对用户信息的缓存还有更多意义吗？

A：MyBatis占用应用服务器的**本地缓存**，**而Redis是分布式缓存，层次不同，可以同时应用**。





###  统计网站数据（ip访问量，用户活跃量）

redis hyperlog

redis bitmap：https://www.cnblogs.com/chjxbt/p/10615304.html

## kafka构建TB级异步消息系统

样例：社交网站通知系统

### 技术使用问题

**你这个功能为什么要用kafka？**

 我觉得我这个系统使用kafka主要能在事务并发量很大的情况下减轻传统模式的服务器与数据库端的压力，并对整个系统起到一个解耦的效果。

**如传统模式就是业务系统调用数据库系统接口，首先要保证事务的基本要素（原子性一致性隔离性持久性）就需要处理并发情况，每次的点赞，评论，关注的更新消息就会直接提交至数据库，数据库虽然有事务处理机制，像MVCC的并发控制等。但即便这样当数据量过大也会影响系统处理性能。如你提交一条评论或者点一个赞需要等待一个事务返回结果再同步返回结果给前端界面显示，这样就不一定能保证性能的要求，造成用户体验不足。**

**另一方面用消息队列你就可以转储消息嘛，我服务端接收请求就往MQ里面扔，扔完就正常返回没我线程事了，起到一个解耦的作用。另一方面你消费者处理是你另外的事情，你自己创线程慢慢消费数据。**



**其实数据量不大情况没啥意义。。。用个阻塞队列也能实现。但kafka的机制比较完善，如失败处理与可恢复性避免系统故障导致数据丢失，多个下游服务器的话就转储消息分担系统负载嘛，保证消息的顺序性等情况**



为什么要使⽤ kafka，为什么要使⽤消息队列? 

**缓冲和削峰** ：上游数据时有突发流量，下游可能扛不住，或者下游没有⾜够多的机器来保证冗余，kafka在中间可以起到⼀个缓冲的作⽤，把消息暂存在kafka中，下游服务就可以按照⾃⼰的节奏进⾏慢处理。 	

**异步通信** ：很多时候，⽤户不想也不需要⽴即处理消息。消息队列提供了异步处理机制，允许⽤户把⼀个消息放⼊队列，但并不⽴即处理它。想向队列中放⼊多少消息就放多少，然后在需要的时候再去处理它们。

**解耦和扩展性** ：项⽬开始的时候，并不能确定具体需求。消息队列可以作为⼀个接⼝层，解耦重要的业务流程。只需要遵守约定，针对数据编程即可获取扩展能⼒。

**冗余** ：可以采⽤⼀对多的⽅式，⼀个⽣产者发布消息，可以被多个订阅topic的服务消费到，供多个毫⽆关联的业务使⽤。

**健壮性** ：消息队列可以堆积请求，所以消费端业务即使短时间死掉，也不会影响主要业务的正常进⾏。







**为什么需要消息系统，mysql不能满足吗？**

**1.解耦：**

允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。

**2.冗余：**

消息队列把数据进行持久化直到它们已经被完全处理，通过这一方式规避了数据丢失风险。许多消息队列所采用的”插入-获取-删除”范式中，在把一个消息从队列中删除之前，需要你的处理系统明确的指出该消息已经被处理完毕，从而确保你的数据被安全的保存直到你使用完毕。

**3.扩展性：**

因为消息队列解耦了你的处理过程，所以增大消息入队和处理的频率是很容易的，只要另外增加处理过程即可。

**4.灵活性 & 峰值处理能力：**

在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见。如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。

**5.可恢复性：**

系统的一部分组件失效时，不会影响到整个系统。消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。

**6.顺序保证：**

在大多使用场景下，数据处理的顺序都很重要。大部分消息队列本来就是排序的，并且能保证数据会按照特定的顺序来处理。（Kafka 保证一个 Partition 内的消息的有序性）

**7.缓冲：**

有助于控制和优化数据流经过系统的速度，解决生产消息和消费消息的处理速度不一致的情况。

**8.异步通信：**

很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。





### 消息队列系统介绍

一个消息系统负责将数据从一个应用传递到另外一个应用，应用只需关注于数据，无需关注数据在两个或多个应用间是如何传递的。分布式消息传递基于可靠的消息队列，在客户端应用和消息系统之间**异步传递消息**。有两种主要的消息传递模式：**点对点传递模式、发布-订阅模式**。大部分的消息系统选用发布-订阅模式。**Kafka就是一种发布-订阅模式**。

- **系统解耦**
- 异步通信
- 由于队列能转储消息，对于超出系统承载能力的场景，可以用 MQ 作为 “漏斗” 进行限流保护，即所谓的流量削峰。
- 我们还可以利用队列本身的顺序性，来满足消息必须按顺序投递的场景；利用队列 + 定时任务来实现消息的延时消费



任何 MQ 无外乎：**一发一存一消费**，这是 MQ 最核心的功能需求。另外，从技术维度来看 MQ 的通信模型，可以理解成：两次 RPC + 消息转储。

外链：消息队列（mq）是什么？ - Lowry的回答 - 知乎 https://www.zhihu.com/question/54152397/answer/1802083263



### 阻塞队列

**什么是阻塞？**

1. 这个队列是线程安全的（内部进行了加锁控制）。
2. 当队列满的时候，往队列里插元素，就会阻塞，，直到队列不满才会进行插入操作。
   当队列为空的时候，从队列里取出元素，此时也会阻塞。。一直阻塞到队列不为空的时候才完成取元素操作。



阻塞队列可以帮我们完成“生产者消费者模型”。**在生产者与消费者之间构建一个缓存队列，起到线程通信间的缓存作用。**

**实现方式有如下几种：**

1. BlockingQueue
   - 解决线程通信的问题
   - 阻塞方法：put，take
2. 生产者消费者模式
3. 实现类（Java Api）
   - ArrayBlockingQueue
   - LinkedBlockingQueue 
   - PriorityBlockingQueue、SynchronousQueue、DelayQueue等。

![image-20210916223345713](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20210916223345713.png)



### **Kafka学习**

1. 什么是kafka

   **Kafka是一种分布式的发布-订阅模式的消息系统**。Kafka是一个分布式，可划分的，冗余备份的持久性的日志服务，它主要用于处理流式数据。
   
   灵活性&峰值处理能力

![image-20210917104702395](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20210917104702395.png)

术语解释

Broker(服务器，消息的代理)，内置Zookeeper

Topic(主题，消息类型)；Partiton(分区)；Offset(索引)

```
Kafka集群中的每台服务器叫Broker，整个集群由Zookeeper进行管理
Kafka采用发布订阅模式，每条消息都要发送到指定的Topic上
每个Topic可分为多个Partition，这样可以提高Kafka的并发执行能力
```

![image-20220306112452642](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/image-20220306112452642.png)





#### 关系示例

![img](https://images2018.cnblogs.com/blog/1228818/201805/1228818-20180507190731172-1317551019.png)

上图中一个topic配置了3个partition。Partition1有两个offset：0和1。Partition2有4个offset。Partition3有1个offset。副本的id和副本所在的机器的id恰好相同。

如果一个topic的副本数为3，那么Kafka将在集群中为每个partition创建3个相同的副本。集群中的每个broker存储一个或多个partition。多个producer和consumer可同时生产和消费数据。



#### broker

Kafka 集群包含一个或多个服务器，服务器节点称为broker。

broker存储topic的数据。如果某topic有N个partition，集群有N个broker，那么每个broker存储该topic的一个partition。

如果某topic有N个partition，集群有(N+M)个broker，那么其中有N个broker存储该topic的一个partition，剩下的M个broker不存储该topic的partition数据。

如果某topic有N个partition，集群中broker数目少于N个，那么一个broker存储该topic的一个或多个partition。在实际生产环境中，尽量避免这种情况的发生，这种情况容易导致Kafka集群数据不均衡。 

#### Topic

每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）

类似于数据库的表名

#### **Partition**

topic中的数据分割为一个或多个partition。

每个topic至少有一个partition。每个partition中的数据使用多个segment文件存储。partition中的数据是有序的，不同partition间的数据丢失了数据的顺序。如果topic有多个partition，消费数据时就不能保证数据的顺序。在需要严格保证消息的消费顺序的场景下，需要将partition数目设为1。



**参考链接**：https://www.cnblogs.com/qingyunzong/p/9004509.html

参考学习：阻塞队列-消息队列https://blog.csdn.net/weixin_43771381/article/details/119565871

简单看看消息队列应用场景：https://www.cnblogs.com/pony1223/p/9521613.html

kafka官网：http://kafka.apache.org



#### Zookeeper 在 Kafka 中的作用

##### 1、Broker注册

**Broker是分布式部署并且相互之间相互独立，但是需要有一个注册系统能够将整个集群中的Broker管理起来**，此时就使用到了Zookeeper。在Zookeeper上会有一个专门**用来进行Broker服务器列表记录**的节点：

/brokers/ids

每个Broker在启动时，都会到Zookeeper上进行注册，即到/brokers/ids下创建属于自己的节点，如/brokers/ids/[0...N]。

Kafka使用了全局唯一的数字来指代每个Broker服务器，不同的Broker必须使用不同的Broker ID进行注册，创建完节点后，**每个Broker就会将自己的IP地址和端口信息记录**到该节点中去。其中，Broker创建的节点类型是临时节点，一旦Broker宕机，则对应的临时节点也会被自动删除。


链接：https://www.jianshu.com/p/a036405f989c




### 测试实现简单样例

#### windows启动命令

```shell
# 启动服务器  （先启动zookeeper服务器，再启动kafka）  ！！！千万不要手动暴力关闭，用下面的命令关闭
bin\windows\zookeeper-server-start.bat config\zookeeper.properties
bin\windows\kafka-server-start.bat config\server.properties

# 创建主题
kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1  --topic test

# 查看当前服务器的主题
kafka-topics.bat --list --bootstrap-server localhost:9092
# 创建生产者，往指定主题上发消息
kafka-console-producer.bat --broker-list localhost:9092 --topic test
# 消费者
kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning

# 关闭kafka服务器
bin\windows\kafka-server-stop.bat
# 关闭zookeeper服务器 
bin\windows\zookeeper-server-stop.bat
```



Demo测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class KafkaTests {

    @Autowired
    private KafkaProducer kafkaProducer;

    @Test
    public void testKafka() {
        kafkaProducer.sendMessage("test", "你好");
        kafkaProducer.sendMessage("test", "在吗");

        try {
            Thread.sleep(1000 * 10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}

@Component
class KafkaProducer {

    @Autowired
    private KafkaTemplate kafkaTemplate;

    public void sendMessage(String topic, String content) {
        kafkaTemplate.send(topic, content);
    }

}

@Component
class KafkaConsumer {

    @KafkaListener(topics = {"test"})
    public void handleMessage(ConsumerRecord record) {
        System.out.println(record.value());
    }


}
```

生产者通过KafkaTemplate的api发送消息，发送消息是主动的

消费者接收消息是被动的，阻塞的（监听主题后）



### 项目应用

#### 发送系统通知

事件驱动

1. **触发事件**

   评论，点赞，关注后发送消息，将消息封装后持久化进数据库message表中

2. **处理事件**

   - 封装事件对象 

   - 开发事件的生产者 
   - 开发事件的消费者



数据库message表结构

| from_id  | to_id    | conversation_id                                           | content      | status |
| -------- | -------- | --------------------------------------------------------- | ------------ | ------ |
| 发送方id | 接收方id | 私信会话id：如111_151/站内消息类型：comment或like、follow | 私信会话内容/JSON格式内容 | 是否已读 |



具体实现：

```java
//封装事件对象
public class Event {

    //事件类型(如被评论，被点赞，被关注三种事件类型)
    private String topic;
    //引起事件行为的用户
    private int userId;
    //事件发生在哪个实体上(如帖子对象，评论对象)
    private int entityType;
    //实体Id
    private int entityId;
    //实体作者，即被通知的对象
    private int entityUserId;
    //记录其他可能存在的额外数据，使该事件对象具有扩展性
    private Map<String, Object> data = new HashMap<>();
}
```

```java
/*
* 事件消费者
* 作用：监听消息队列，将获取的消息存入数据库中
* */
@Component
public class EventConsumer implements CommunityConstant {

    private static final Logger logger = LoggerFactory.getLogger(EventConsumer.class);

    @Autowired
    private MessageService messageService;

    @KafkaListener(topics = {TOPIC_COMMENT, TOPIC_LIKE, TOPIC_FOLLOW})
    public void handleCommentMessage(ConsumerRecord record) {
        if (record == null || record.value() == null) {
            logger.error("消息的内容为空!");
            return;
        }

        Event event = JSONObject.parseObject(record.value().toString(), Event.class);
        if (event == null) {
            logger.error("消息格式错误!");
            return;
        }

        // 使用message实体接收通知消息，复用私信表结构message
        Message message = new Message();
        message.setFromId(SYSTEM_USER_ID);
        message.setToId(event.getEntityUserId());
        //这里将主题存入message表中的conversation_id字段，用于区分私信与通知类型
        message.setConversationId(event.getTopic());
        message.setCreateTime(new Date());

        //构造内容map对象，存放
        Map<String, Object> content = new HashMap<>();
        content.put("userId", event.getUserId());
        content.put("entityType", event.getEntityType());
        content.put("entityId", event.getEntityId());

        //将envent中的key-value对象取出存入content中
        if (!event.getData().isEmpty()) {
            for (Map.Entry<String, Object> entry : event.getData().entrySet()) {
                content.put(entry.getKey(), entry.getValue());
            }
        }

        message.setContent(JSONObject.toJSONString(content));
        messageService.addMessage(message);
    }
}
```



### 消息系统使用常见问题

https://www.cnblogs.com/yulove/p/13121636.html

#### [Kafka如果消费者组成员失败，消息将如何处理？](https://www.thinbug.com/q/59479333)

集群与分布式系统情况下：

每个使用者都将分配有分区。 假设我们有6个分区：

消费者1：Partiton 1和2
消费者2：分区3和4
使用者3：分区5和6

消费者1发生故障时，消费者组将重新平衡并将空闲分区分配给其他消费者，从而为我们提供以下设置：

消费者2：分区 **1** 和3和4
使用者3：分区 **2** 和5＆6

其他使用者将从该分区上最后提交的偏移量开始。





## Elasticsearch，分布式搜索引擎

### 什么是Elasticsearch？

着重功能就是用来做**数据的检索和分析**

- **应用程序搜索**
- 网站搜索
- 企业搜索
- **日志处理和分析**
- 基础设施**指标和容器监测**
- 应用程序性能监测
- 地理空间**数据分析和可视化**
- 安全分析
- 业务分析



### 相对于MySQL它的优点

#### 1.响应时间

mysql使用like之类的模糊查询，遍历全表，效率较低。

#### 2.分词

mysql不支持分词。例如，当用户在搜索框输入“四川火锅”时，数据库通常只能把这四个字去进行全部匹配。可是在文本中，可能会出现“推荐四川好吃的火锅”，这时候就没有结果了。

#### 3.相关性

例如，当用户搜索“咖啡厅”的时候，他很可能更想知道附近哪里可以喝咖啡，而不是怎么开咖啡厅。

#### 4.可视化界面

参考：https://blog.csdn.net/weixin_39819880/article/details/82083034



es为什么快？

### **倒排索引**

#### 基本结构

单词索引(Term Index )：为每个基础单词建立的索引，用于在庞大的单词词典中快速检索，结合压缩技术减小内存占用等

单词词典(Term Dictionary)：搜索引擎的通常索引单位是单词，单词词典是由文档集合中出现过的所有单词构成的字符串集合，单词词典内每条索引项记载单词本身的一些信息以及指向“倒排列表”的指针。

**倒排列表(Posting List)**：倒排列表记载了出现过某个单词的所有文档的文档列表及单词在该文档中出现的位置信息，每条记录称为一个倒排项(Posting)。根据倒排列表，即可获知哪些文档包含某个单词。

倒排文件(Inverted File)：所有单词的倒排列表往往顺序地存储在磁盘的某个文件里，这个文件即被称之为倒排文件，倒排文件是存储倒排索引的物理文件。



**比如我想查找帖子中包含“Java”的内容。**

使用类似MySQL中的B+树索引的话，通过关键字模糊匹配查找，会进行全表检索，只能依次将字段所有数据遍历后判断数据中是否包含 ”Java“ ;这样效率十分低下。

但如果我们重新构建一个索引结构：

| Term(词典) | Posting List |
| :--------- | ------------ |
| 李         | [1,3]        |
| 张         | [2]          |
| Java       | [4]          |
| JavaScript | [4,5]        |
| hello      | [2]          |

即首先约定好最细粒度的词汇，每次获取内容时即对其拆分也就是所谓**”分词“**。

当要查询的内容中包含“Java”的数据时，只需要通过这个索引结构查询到 Posting List 中所包含的数据，再通过映射的方式查询到最终的数据。



​	如果是Java和JavaScript以及分词后的词典数量巨大呢？如何找到我们想要的词？不可能遍历吧？而且全一一存下来内存大小也不允许，这便要用到所谓的字典树来存实现了。

事实上，我们可以再对这些词进行索引构建：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMxLnpoaW1nLmNvbS84MC92Mi1lNDYzMmFjMTM5MmIwMWY3YTM5ZDk2M2ZkZGIxYTFlMF83MjB3LmpwZw?x-oss-process=image/format,png)

再结合ES的一些压缩技术，Term的索引结构可以简化成这样：

![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9waWMzLnpoaW1nLmNvbS84MC92Mi1lNDU5OWI2MThlMjcwZGY5YjY0YTc1ZWI3N2JmYjMyNl83MjB3LmpwZw?x-oss-process=image/format,png)

![img](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/v2-fad54ba1315d23660bb3b72f07585bca_720w.jpg)

​	如果我们是以 j 开头的 Term 进行搜索，首先第一步就是通过在内存中的 Term Index 查询出以 j 打头的 Term 在 Term Dictionary 字典文件中的哪个位置(这个位置可以是一个文件指针，可能是一个区间范围)。

紧接着在将这个位置区间中的所有 Term 取出，由于已经排好序，便可通过二分查找快速定位到具体位置;这样便可查询出 Posting List。



#### 更多优化

当我们搜索的内容分词后得到多个Posting List后如何快速地找出它们的交集？

如Java和Spring和SpringBoot同时出现的内容，进行相关性的排序？

| Term(词典) | Posting List |
| ---------- | ------------ |
| Java       | [1,2,3,5]    |
| Spring     | [2,3]        |
| SpringBoot | [1,2,4]      |

显然ID为2的数据项相关性最高。

这时我们便可使用 **Bitmap** 的方式进行存储(还节省存储空间)，同时利用先天的位与计算便可得出结果。

Bit-map的基本思想就是用一个bit位来标记某个元素对应的Value，而Key即是该元素。由于采用了Bit为单位来存储数据，因此在存储空间方面，可以大大节省。（PS：划重点 **节省存储空间**）如：

```text
[1, 2，3, 5] ⇒ 11101 

[2, 3] ⇒ 01100 
 
[1, 2, 4] ⇒ 11010 
```

原理很简单，二进制从0开始，即存在置1，不存在置0。然后求出它们的与运算。

01000 ⇒ [2] 

即可得出最高相关性。

同样的查询需求在 MySQL 中并没有特殊优化，只是先将数据量小的数据筛选出来之后再筛选第二个字段，效率自然也就没有 ES 高。



#### **总结一下**

![preview](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/v2-d8ad7c4715a0f809fdb0d80f071e4268_r.jpg)

参考Elasticsearch查询速度为什么这么快？：https://zhuanlan.zhihu.com/p/266116262



### **开发社区搜索功能** 

1.  搜索服务 

- \- 将帖子保存至Elasticsearch服务器。 
- \- 从Elasticsearch服务器删除帖子。 
- \- 从Elasticsearch服务器搜索帖子。 

2. 发布事件 

- \- 发布帖子时，将帖子异步的提交到Elasticsearch服务器。 
- \- 增加评论时，将帖子异步的提交到Elasticsearch服务器。 
- \- 在消费组件中增加一个方法，消费帖子发布事件。



### 两个端口作用

**9200 端口**

`9200`是`HTTP`协议的`RESTful`接口。使用RestFul风格接收请求 

**9300 端口**

`9300`是`TCP`通讯端口，集群间和`TCPClient`都走的它。





https://segmentfault.com/a/1190000038895410

https://www.cnblogs.com/sunsky303/p/9438737.html

MQ，Es，驱动设计（DDD），具备大数据能力。







 

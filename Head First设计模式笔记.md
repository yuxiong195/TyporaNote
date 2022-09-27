# Head First设计模式笔记



## 策略模式



## 装饰者模式

模式：动态地将责任附加到对象上。若要扩展功能，装饰着提供了有别于继承的另一种选择。

原则：开闭原则



## 代理模式



![image-20211201235905470](Head First设计模式笔记.assets/image-20211201235905470-1638374346757.png)

简单说，就是Proxy类组合实际被代理的类，并实现共用接口。Proxy在实现的接口方法里调用被代理类RealSubject的方法。





## 工厂模式



Spring使用工厂模式可以通过 `BeanFactory` 或 `ApplicationContext` 创建 bean 对象。

**Spring IOC容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。**IOC 容器负责创建对象，将对象连接在一起，配置这些对象，并从创建中处理这些对象的整个生命周期，直到它们被完全销毁。



## 单例设计模式

在我们的系统中，有一些对象其实我们只需要一个，比如说：**线程池**、缓存、对话框、注册表、**日志对象**、充当打印机、显卡等设备驱动程序的对象。事实上，这一类对象只能有一个实例，如果制造出多个实例就可能会导致一些问题的产生，比如：程序的行为异常、资源使用过量、或者不一致性的结果。



**单例模式是指确保一个类在任何情况下都绝对只有一个实例，并提供一个全局的访问点。**

**实现特点是：隐藏其所有的构造方法；提供一个全局的访问点。**



**使用单例模式的好处：**

- 对于频繁使用的对象，可以**省略创建对象所花费的时间**，这对于那些重量级对象而言，是非常可观的一笔系统开销；
- 由于new操作的次数减少，因而**对系统内存的使用频率也会降低**，这将减轻GC压力，缩短GC停顿时间。



**Spring中bean的默认作用域就是singleton(单例)的**



### 单例模式与线程问题

懒汉式：默认不会实例化，什么时候用什么时候new。

饿汉式：在类加载的时候就实例化，并且创建单例对象。

```java
//懒汉式(存在线程安全的写法)
class Lazy{
    private static Lazy a = null;
    
    //当多个线程同时进入getInstance，a对象会存在线程安全问题,故要加锁
    public static Lazy getInstance() {
        if(a==null) {
            //可能会导致重复创建对象然后分别返回多个不同的对象。。
            a = new Lazy();
        }
        return a;
    }
    
    //不暴露空参构造器
    private Lazy() {
    }
}

//饿汉式
class Hungry{
    private volatile static Hungry b = new Hungry();
    public static Hungry getInstance() {
        return b;
    }

}

//懒汉式(改进写法)
class Lazy{
    private volatile static Lazy a = null;
    
    //当多个线程同时进入getInstance，a对象会存在线程安全问题,故要加锁
    public static Lazy getInstance() {
        //这里加null判断是为了提高执行效率，防止多个线程无效等待(进入锁后并没有实际作用)
        if(a==null) {
            synchronized(Lazy.class) {
				if(a==null) {//这里是因为可能多个线程都进入到了第一个null中，所以仍需判断一次
    				a = new Lazy();
                }
            }
        }
        return a;
    }
    
    //不暴露空参构造器
    private Lazy() {
    }
}


```

执行效率上：饿汉式没有加任何的锁，因此执行效率比较高。懒汉式一般使用都会加同步锁，效率比饿汉式差。
性能上：饿汉式在类加载的时候就初始化，不管你是否使用，它都实例化了，所以会占据空间，浪费内存。懒汉式什么时候需要什么时候实例化，相对来说不浪费内存。



# 参考链接

## 谈谈Spring中都用到了哪些设计模式？

https://www.cnblogs.com/kyoner/p/10949246.html	

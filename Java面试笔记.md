# Java基础笔记

知识的深度，和个人的理解

其实面试就是分成三块问题：**项目相关的问题，计算机基础知识（主要是计算机网络和操作系统），数据结构和算法。**

越是大厂可能越注重知识的深度，所以对所学的知识不要只停留在八股文的层面。面试其实说再多都不如自己去实际经历一把，你会在面试的过程中越来越强。

不管学哪块的知识，不要只停留在书本上，多去实践敲一些代码，比如当时面试官和我说，你学习jvm的垃圾回收器，你可以试着去打印日志看一看垃圾回收的过程。



人工智能，产品落地实现



## JavaSE

### 类和对象

问题：

#### **接口和抽象类的区别**？

**抽象类**：使用abstract关键字修饰的类，叫做抽象类

1. **抽象类不能被实例化，抽象类是被设计用来继承的**
2. 抽象类其实就和普通类一样，可以有属性，普通方法，构造方法，抽象方法；
3. 抽象方法只有方法的声明，没有方法体



**接口**：使用interface定义的结构，叫做接口，**接口是用来给别人实现的**

1. 接口中的所有方法都是抽象方法，jdk1.8以后，可以在接口中增加默认的方法（实现通用方法情况，无需重写）只需要加上关键字static或者default修饰即可。
   - 使用static来修饰的称之为静态方法，静态方法通过接口名来调用；
   - 使用default来修饰的称之为默认方法，默认方法通过对象实例来调用；
2. 接口中可以用属性，但属性都是静态常量public static final
3. 一个类可以实现多个接口，接口可以继承多个接口
4. 接口没有构造方法，但注意接口支持静态方法
5. **接口类的修饰只能是public和abstract**；接口中成员函数的修饰也是只能用public和abstract，也不能用static 或final来修饰；成员变量只能被public, static 和 final 修饰，成员变量默认有 public static final



**说了这么多区别，能不能举一个实际场景体现他们两者的区别？**

- **抽象类是用来捕捉子类的通用特性的，包括属性及行为**。is a，偏用于描述类是一个什么，山东富士是苹果。
- **我们通常使用接口对行为进行抽象**。has a，偏向于描述类有这个行为。
- 接口能够解决抽象类不能被多继承的缺点。如当类已经继承抽象类但由于扩展它需要继承新的抽象行为类这是不行的。
- 要实现的方法(功能)不是当前类族的必要(属性)。就可以选择使用接口定义，让类自己选择是否要实现！
- 对系统设计来说，越抽象，越顶层，越脱离某一具体实现的设计，越能提高代码的灵活性，扩展性，以及可维护性。

https://zhuanlan.zhihu.com/p/51977526?utm_source=wechat_session&utm_medium=social&utm_oi=1012364251268308992&utm_campaign=shareopn



#### super关键字和this关键字

- super（参数）：调用基类中的某一个构造函数（应该为构造函数中的第一条语句） 
- this（参数）：调用本类中另一种形成的构造函数（应该为构造函数中的第一条语句）
- super:　它引用当前对象的直接父类中的成员（用来访问直接父类中被隐藏的父类中成员数据或函数，基类与派生类中有相同成员定义时如：super.变量名  super.成员函数名(实参)

- super()和this()类似,区别是，super()从子类中调用父类的构造方法，this()在同一类内调用其它方法

- 调用super()必须写在子类构造方法的第一行，否则编译不通过。**每个子类构造方法的第一条语句，都是隐含地调用super()，如果父类没有这种形式的构造函数，那么在编译的时候就会报错。**因为类加载器会先加载父类。





#### **Object类有哪些方法?**

答：clone，hashcode和equals，toString，getClass，wait和notify和notifyAll，还有finalize用于垃圾回收时调用。它们都是native方法。

equals未重写比较规则的话就是java中的==号，如果是对象的话比较的就是栈中的引用地址，基本类型的话比较的就是值。Stirng类重写的equals方法规则是两个String对象只要内容相同即返回true

hashcode：哈希码也称散列码。该方法返回一个int整数，用于确定对象的在哈希表（堆）中的索引位置

参考：https://blog.csdn.net/u014590757/article/details/80262163



#### hashcode()和equals()方法的关系

![img](https://yu-xiong-yu.gitee.io/typora-img/Img/v2-1ff488ef57f96de4066599f45be04d80_720w.jpg)

思考在上述图中的规则原因。

参考：https://zhuanlan.zhihu.com/p/90464718

为什么要有hashcode：https://blog.csdn.net/hayre/article/details/110588186



#### **"static”关键字是什么意思？Java中是否可以覆盖(override)一个private或者是static的方法？**

"static"关键字表明一个成员变量或者是成员方法可以在没有所属的类的实例变量的情况下被访问。

Java中static方法不能被覆盖，因为**方法覆盖是基于运行时动态绑定的**，而**static方法是编译时静态绑定的**。static方法跟类的任何实例都不相关，所以概念上不适用。

java中也不可以覆盖private的方法，因为private修饰的变量和方法只能在当前类中使用，如果是其他的类继承当前类是不能访问到private变量或方法的，当然也不能覆盖。

参考：https://www.runoob.com/java/java-override-overload.html



#### Java 重写(Override)与重载(Overload)的区别

方法的重写(Overriding)和重载(Overloading)是java多态性的不同表现，重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式。

- (1)方法重载是一个类中定义了多个方法名相同,而他们的参数的数量不同或数量相同而类型和次序不同,则称为方法的重载(Overloading)。
- (2)方法重写是在子类存在方法与父类的方法的名字相同,而且参数的个数与类型一样,返回值也一样的方法,就称为重写(Overriding)。
- (3)方法重载是一个类的多态性表现,而方法重写是子类与父类的一种多态性表现。





#### **Java支持的数据类型有哪些？什么是自动拆装箱？**

Java语言支持的8种基本数据类型是：

- byte
- short
- int
- long
- float
- double
- boolean
- char

![img](https://yu-xiong-yu.gitee.io/typora-img/Img/20180905141955842)

Java 基本数据类型存储位置：https://blog.csdn.net/ncuzengxiebo/article/details/83745065

![img](https://images2017.cnblogs.com/blog/845772/201712/845772-20171224220628396-847270327.png)

8大基本数据类型都在栈中分配，引用数据类型则在堆中分配。

​	这些数据类型的变量都在栈中被创建。如`int i = 10;`，`String s = new String("hello")`里的i和s都可称作变量，只是i直接指向JVM栈中的内存栈上的，数据本身的值就是存储在栈空间里面；而String类型变量s存放的是指向堆中对象的**引用值**，这里的引用值可以理解成计算机中的内存块地址，有这个唯一的引用值，才能找到堆中指定的对象。它们同样也是存放在栈空间中。

数组同样也是引用数据类型，数组类型的存储位置：

![image-20210802150556202](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20210802150556202.png)



##### 包装类

**为什么要用包装类？**

1. java语言是面向对象的，对象封装有很多好处，可以把数据和这些数据的方法结合在一起
2. 

自动装箱是Java编译器在基本数据类型和对应的对象包装类型之间做的一个转化。比如：把int转化成Integer，double转化成Double，等等。反之就是自动拆箱。

如：java在编译的时候Integer a=100，被编译成Integer a=Integer.valueOf(100)。

这个**Integer.valueOf**很重要。

```java

//源码

public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
    return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}

//测试
public static void main(String[] args) {
    Integer i01 = -128;
    //java在编译的时候Integer a=100，被编译成Integer a=Integer.valueOf(100)。
    int i02 = -128;
    Integer i03 = Integer.valueOf(-128);
    Integer i04 = new Integer(-128);

    System.out.println(i01 == i02);
    System.out.println(i01 == i03);
    System.out.println(i02 == i04);
    System.out.println(i03 == i04);//false
}
```



##### Integer缓存问题分析

```java
public class IntTest {
	public static void main(String[] args) {
	    Integer a = 100, b = 100, c = 150, d = 150;
	    System.out.println(a == b);
	    System.out.println(c == d);
	}
}
```



**因为缓存了 -128 到 127 之间的数值。为什么？**

我们知道，`Integer var = ?` 形式声明变量，会通过 `java.lang.Integer#valueOf(int)` 来构造 `Integer` 对象。而Integer 类中定义的缓存数组范围就是 -128 到 127 ，数组大小即为256，在此范围内即直接提取整数对象，否则创建新整数对象。

**如果不要求必须新建一个整型对象，缓存最常用的值（提前构造缓存范围内的整型对象），会更省空间，速度也更快。**

这给我们一个非常重要的启发：

> 如果想减少内存占用，提高程序运行的效率，可以将常用的对象提前缓存起来，需要时直接从缓存中提取。
>
> 缓冲常用对象提升性能让我想到了spring容器，其实核心原理是相通的，在程序启动过程中把需要使用的对象提前构造好，用的时候直接从spring拿就可以。CDN、Redis也是这样。

另外：部分包装类实现了池化技术，-128～127 以内的对象可以重用。

*结合字节码学习*

https://blog.csdn.net/weixin_47067712/article/details/114384574?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link



#### Double精度丢失问题

比如
1.1+0.1=1.2000000000000002

根本原因在于，数学意义上的小数不是每个都能用二进制在有限位数内精确的表示。

像 0.1，1.1 这样的小数没有精确的二进制表示，多出的部分便会进行舍去（待处理部分有没有达到前一位的一半，达到就进位，没达到就舍去。0 舍 1 入），然后求和就不是1.2了。

参考：如何理解double精度丢失问题？ - 邱昊宇的回答 - 知乎 https://www.zhihu.com/question/42024389/answer/93528601



#### 内部类

![nbl0](https://yu-xiong-yu.gitee.io/typora-img/Img/nbl0.png)

 



#### Java异常处理

![img](https://yu-xiong-yu.gitee.io/typora-img/Img/exception-hierarchy.png)

##### Error

Error 用来指示运行时环境发生的错误。

例如，JVM 栈溢出，OOM。一般地，程序不会从错误中恢复。

error 表示恢复不是不可能但很困难的情况下的一种严重问题。比如说内存溢出。不可能指望程序能处理这样的情况。 



##### Exception 

异常类有两个主要的子类：IOException 类和 RuntimeException 类。

常见的异常有：空指针异常，数组越界，IO异常等等

异常代表一种设计或实现问题。也就是说，它表示如果程序运行正常，便从不会发生的情况。



##### throws/throw 关键字：

- throws声明在方法后面。如果一个方法没有捕获到一个检查性异常，那么该方法必须使用 throws 关键字来声明。
- throw在代码块内使用，手动声明new。可以携带自定义错误信息

```java
public class testException {
    public static void main(String[] args) throws ArithmeticException{
        int a = 10;
        int b = new Scanner(System.in).nextInt();
        if (b==0) {
            throw new ArithmeticException("分母不能为0");
        } else {
            System.out.println(a/b);
        }
        System.out.println("end");
    }
}
```



### 基本语法

#### switch

首先看个简单例子：

```java
int x = 9;
switch (x){
    default:
        System.out.println("abc");
    case 0:
        System.out.println("null");
    case 1:
        System.out.println("123");
}
```

答：上述案例全部情况都会输出。

语法注意事项：

- 根据给定条件x选择情况，匹配就执行相应语句块
- case里面break可以带可以不带，带了的话命中该case并执行完后就会退出switch语句。不带的话自该条case全部都执行直到遇到break
- 上面把default写在第一条的作用是：直接执行相当于命中case，后续情况同上述case，遇到break结束。



### String类

#### 类重点概述

1. String常量池中字符串对象具有**”唯一性“**和**“不可变性”**，即对字符串对象的任何操作在常量池中都不会影响到原分配的字符串存储对象
2. String实现了Serializable接口：表示字符串是支持序列化的。
           实现了Comparable接口：表示String可以比较大小
3. String内部定义了final char[] value用于存储字符串数据，所以String本质是个char数组。

```java
//JDK源码
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {
    /** String本质是个char数组. 而且用final关键字修饰.*/
    private final char value[];
	...
	...
}
```

> String可以被看作常量的关键在哪？如下
>
> 摘录：String 是不可变，关键是因为 SUN 公司的工程师，在后面所有 String 的方法里很小心的没有去动value数组里的元素，也没有暴露内部成员字段。private final char value[] 这一句里，private的私有访问权限的作用都比 final 大。而且设计师还很小心地把整个 String 设成 final 禁止继承，避免被其他人继承后破坏。所以 String 是不可变的关键都在底层的实现，而不是一个 final。考验的是工程师构造数据类型，封装数据的功力。

*jdk1.8中字符串常量池在方法区（元空间）*



#### 创建String的形式

- 通过字面量的方式，即`String = "abc";`的形式（区别于new给一个字符串赋值，String变量引用指向堆中String对象，堆对象再指向字符串常量池中对象；)定义的字符串值声明在字符串常量池中，引用直接指向。
- intern方法。当调用 intern方法时，**如果池已经包含一个等于此String对象的字符串（用equals(oject)方法确定），则返回池中的字符串。否则，将此String对象添加到池中，并返回此String对象的引用**

![image-20210802113904499](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20210802113904499.png)

面试题一：String s = new String("abc");方式创建对象，在内存中创建了几个对象？
答：两个。一个是堆空间中new结构，另一个是char[]对应的常量池中的数据："abc"。

面试题二：（考察String字面量引用赋值与参数传递）

```Java
/**
 * 一道面试题
 * @author shkstart
 * @create 2019 上午 11:32
 */
public class StringTest {

    String str = new String("good");
    char[] ch = { 't', 'e', 's', 't' };

    //这里的形参str是创建在一个新的栈帧空间，传来的引用让它指向原str指向的堆空间
    public void change(String str, char ch[]) {
        str = "test ok";//这里只会在字符串常量池中新建"test ok"，让形参中的str指向它而已
        ch[0] = 'b';
    }
    public static void main(String[] args) {
        StringTest ex = new StringTest();
        ex.change(ex.str, ex.ch);
        System.out.println(ex.str);//good
        System.out.println(ex.ch);//best
    }
}
```



#### 字符串拼接方式赋值的对比

1.常量（String变量使用final修饰）与常量的拼接结果在常量池中生成。且常量池中不会存在相同内容的常量（如拼接后存在相同的字符串就不创建）。

2.只要拼接对象中其中一个是变量，结果就在堆中创建，再指向常量池。（这种拼接相当于new String()的操作）

3.如果拼接的结果调用intern()方法，返回值就在常量池中（intern方法返回常量池字符串的地址）

*例：String str = new String("Hello world") 创建了 2 个对象，一个驻留在字符串池，一个分配在 Java 堆，str 指向堆上的实例。*
*String.intern() 能在运行时向字符串池添加常量*

4.相对第1点来说，如果不是使用常量拼接，则还是会调用StringBuilder方法创建对象并拼接。如下图

![image-20211031154257493](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20211031154257493.png)

#### 常用方法

##### 获取字符串信息

- int length():获取长度。
- char charAt(int index):
- int indexOf(int ch):返回的是ch在字符串中第一次出现的位置。

##### 判断

- boolean contains(str):字符串中是否包含某一个子串。特殊之处：indexOf(str):可以索引str第一次出现位置，如果返回-1.表示该str不在字符串中存在。
- boolean startsWith(str);字符串是否是以指定内容开头。
- boolean endsWith(str);字符串是否是以指定内容结尾。



##### 转换

- char[] toCharArray(): 将字符串转成字符数组。
- byte[]  getBytes(): 将字符串转成字节数组。



##### 子串。获取字符串中的一部分

- String substring(begin);
- String substring(begin,end);
-  String[] split(regex);



##### 转换，去除空格

- String toUpperCase();将字符串转成大写
- String toLowerCase();小写
- String trim();将字符串两端的多个空格去除。

参考：https://blog.csdn.net/qq_39657597/article/details/80007955



##### intern()方法

intern方法是String类中的native方法（本地方法）。**它的作用是保证该字符串返回的形式是其在字符串常量池中的直接引用！不管调用的String形式是怎样。**如果常量池中没有该字符串则会在其中创建。

![image-20211031160043425](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20211031160043425.png)

```java
public class testString {
    public static void main(String[] args) {
        String s1 = "a";
        String s2 = "b";
        String s3 = "ab";
        
        String s4 = s1+s2;
        System.out.println(s3.hashCode()==s4.hashCode());//true
        System.out.println(s3==s4);//false
        System.out.println(s3==s4.intern());//true
        
        StringBuilder stringBuilder = new StringBuilder("abc");
        String abcOj = new String(s3+"c");
        System.out.println(stringBuilder.toString() == abcOj);//false
        System.out.println(stringBuilder.toString().intern()==abcOj.intern());//true
    }
}
```



#### String、StringBuffer  和 StringBuilder区别



**场景**

1. 在字符串内容不经常发生变化的业务场景优先使用String类。例如：[常量声明](https://www.zhihu.com/search?q=常量声明&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType"%3A"answer"%2C"sourceId"%3A1588779683})、少量的字符串拼接操作等。如果有大量的字符串内容拼接，避免使用String与String之间的“+”操作，因为这样会产生大量无用的中间对象，耗费空间且执行效率低下（新建对象、回收对象花费大量时间）。
2. 在频繁进行字符串的运算（如拼接、替换、删除等），并且运行在多线程环境下，建议使用StringBuffer，例如XML解析、HTTP参数解析与封装。
3. 在频繁进行字符串的运算（如拼接、替换、删除等），并且运行在单线程环境下，建议使用StringBuilder，例如SQL语句拼装、JSON封装等

作者：仰望云端
链接：https://www.zhihu.com/question/55477005/answer/1588779683
来源：知乎



StringBuffer 是线程安全，效率较低。**StringBuilder非线程安全，效率较高。**



### 集合

Java集合类库将接口与实现分离

![image-20210713144930037](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20210713144930037.png)

集合类的基本接口是 Collection 接口,这个接口有两个基本方法:

```java
public interface Collection<E> extends Iterable<E> {
	boolean add(E element);
	Iterator<E> iteratorQ；
}
```



#### 迭代器

Collection顶层接口：**Iterable.java**

```java
package java.lang;

import java.util.Iterator;
import java.util.Objects;
import java.util.Spliterator;
import java.util.Spliterators;
import java.util.function.Consumer;

/**
 * Implementing this interface allows an object to be the target of
 * the "for-each loop" statement. See
 * <strong>
 * <a href="{@docRoot}/../technotes/guides/language/foreach.html">For-each Loop</a>
 * </strong>
 *
 * @param <T> the type of elements returned by the iterator
 *
 * @since 1.5
 * @jls 14.14.2 The enhanced for statement
 */
public interface Iterable<T> {
    /**
     * Returns an iterator over elements of type {@code T}.
     *
     * @return an Iterator.
     */
    Iterator<T> iterator();

    /**
     * Performs the given action for each element of the {@code Iterable}
     * until all elements have been processed or the action throws an
     * exception.  Unless otherwise specified by the implementing class,
     * actions are performed in the order of iteration (if an iteration order
     * is specified).  Exceptions thrown by the action are relayed to the
     * caller.
     *
     * @implSpec
     * <p>The default implementation behaves as if:
     * <pre>{@code
     *     for (T t : this)
     *         action.accept(t);
     * }</pre>
     *
     * @param action The action to be performed for each element
     * @throws NullPointerException if the specified action is null
     * @since 1.8
     */
    default void forEach(Consumer<? super T> action) {
        Objects.requireNonNull(action);
        for (T t : this) {
            action.accept(t);
        }
    }

}

```





迭代器遍历集合的方式

```java
Collection<String> c = . . .;
Iterator<String> iter = c.iterator();
while (iter.hasNext()) {
    String element = iter.next();
    do something with element..
}
```

用“ foreach” 循环可以更加简练地表示同样的循环操作，编译器简单地将“ foreach” 循环翻译为带有迭代器的循环。



#### ArrayList

无参构造初始数组大小默认为0

有参构造返回指定大小长度数组

jdk1.8ArrayList主要方法和扩容机制：https://blog.csdn.net/u010890358/article/details/80515284

**扩容机制**

扩容开始于集合添加元素方法，添加元素有两种方法：

```Java1
public boolean add(E e)
public void add(int index, E element)
```

<u>若数组大小为0，添加第一个元素开始扩容，创建数组大小为10。</u>

**当数组空间不够时，容器便会自动扩容。新数组的大小为原数组大小的1.5倍加1。之后再通过System.arrayCopy方法进行数组拷贝。**

ArrayList扩容机制(基于jdk1.8)：https://blog.csdn.net/qq_26542493/article/details/88873168



#### 散列表（hashtable） 

散列表用链表数组实现。

常见应用：HashSet，HashMap。。

HashSet底层其实是用HashMap实现存储的, HashSet封装了一系列HashMap的方法。 依靠HashMap来存储元素值,(利用hashMap的key键进行存储), 而value值默认为Object对象。所以HashSet也不允许出现重复值, 判断标准和HashMap判断标准相同, 两个元素的hashCode相等并且通过equals()方法返回true.

Hashtable是线程安全的，它的每个方法中都加入了Synchronize方法。



#### 映射（Map）

![img](https://img-blog.csdn.net/20170317181610752?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvanVzdGxvdmV5b3Vf/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**Java** 类库为映射提供了两个通用的实现：**HashMap** 和 **TreeMap**。这两个类都实现了 **Map** 接口。

##### HashMap

哈希表基础知识：https://zhuanlan.zhihu.com/p/95156642

用漫画告诉你—什么是HashMap？：https://zhuanlan.zhihu.com/p/78079598

###### **hashmap底层**：

1. 底层数组+链表实现，**可以存储null键和null值**，线程不安全。
2. HashMap的主干是一个Entry数组。Entry是HashMap的基本组成单元，每一个Entry包含一个key-value键值对。数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的。

put（）方法过程：添加单个元素的方法，每当传入的Key对象在HashMap对象中不存在，就会新增一个key-value元素到HashMap中，如果Key对象已经存在于HashMap中，则会覆盖Key对象对应的Value值，每当新增一个key-value元素时，put（）方法的返回值为null，**若是覆盖了原有的key-value中的Value值，则put（）方法返回的是旧的Value对象**（注意这里可以利用这个特点，做业务判断）



###### **HashMap如何扩容？**

数组长度初始为16，负载因子0.75，当数组容量占比大于这个数值是就会进行扩容；**扩容的方式是新创建一个数组是原来的2倍，然后把原数组的所有Entry都重新Hash一遍放到新的数组**。这里重新Hash是因为数组扩大了，所以一般哈希函数也会有变化，这里的Hash也就是把之前的数据通过新的哈希函数计算出新的位置来存放。



###### **如何处理hash冲突的？**

介绍两种主要的方法，一个是开放寻址法，一个是链式寻址法。

- 开放寻址法即冲突就往数组后一位查找，直到找到空位，如Java中的ThreadLocal就是利用了开放寻址法。
- 链式寻址法就是以当前Entry为头节点创建链表，**当链表大于等于8时就会转换成红黑树结构（将链表转换成红黑树前会判断，如果当前数组的长度小于 64，那么会选择先进行数组扩容，而不是转换为红黑树）**，小于等于6时就会还原成链表。这样设计是因为中间有个7作为一个缓冲值，来避免频繁的进行树和链表的转换，因为转换频繁也是影响性能的啊。

链地址法的hash算法简介：

```java
public V put(K key, V value) {
        return putVal(hash(key), key, value, false, true);
}
static final int hash(Object key) {
 	int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

将键的hashcode异或自身的高十六位，防止出现低位都是0的大数hashcode出现。结合取余下标算法可能得到的是0下标。如KeyA hashCode：10101011110011010000000000000000





###### **HashMap的长度为什么要是2的n次方?**

主要在于：计算索引的方式是 `i = (length - 1) & hash`；

**首先我们从分配数组下标的算法中看问题：从hash%length优化为hash&(length-1)**

​	HashMap为了存取高效，要尽量较少碰撞，就是要尽量把数据分配均匀，每个链表长度大致相同，这个实现就在把数据存到哪个数组下标(链表的索引)中的算法；

​	这个算法实际就是取模，hash%length，计算机中直接求余效率不如位移运算，源码中做了优化hash&(length-1)，**其实就是按位“与”的时候，每一位都能 &1 ，也就是和1111……1111111进行与运算**。因为length-1的值为2的n次方-1，其二进制都是n个1。可以说，Hash算法最终得到的index结果，完全取决于Key的Hashcode值的最后几位。



**那么这种算法可能会遇到什么问题呢？**

由于对象的hash码是随机的，于是就要考虑length-1的值是否会影响运算的结果了。

假设hashmap默认长度为10，则length-1二进制表示为1001。进行上述算法运算有如下情况：

hashcode值后四位为1111、1011、1101、1001的情况，与运算结果均相同！均为1001，即9。而有的index结果也不可能出现，如0111，即7！故此种情况显然不是我们想看到的。

而length-1为15时，1111的与运算结果即hashcode自身了。这样的结果显然冲突更少也更合理。

https://blog.csdn.net/sidihuo/article/details/78489820

https://blog.csdn.net/qq_43664324/article/details/106259046



###### hashMap 线程不安全体现在哪里

在多线程环境下，如果有多个线程执行插入动作并且命中了同一个entry，那么就有可能出现数据覆盖的情况，导致线程不安全。或者链表尾插错误生成，形参多叉树之类的。

或者resize扩容重哈希的过程，多线程并发可能各个线程都会新建一个数组进行重哈希，而最终只会保留一个，

https://www.cnblogs.com/lin346112883/p/9828637.html



##### ConcurrentHashMap 

不过遗憾的是，HashMap不是线程安全的。也就是说，在多线程环境下，操作HashMap会导致各种各样的线程安全问题，比如在HashMap**扩容重哈希**时出现的死循环问题，脏读问题等。

在1.7的ConcurrentHashMap中，无论是读操作还是写操作都能保证很高的性能：在进行读操作时(几乎)不需要加锁，而在写操作时通过锁分段技术只对所操作的段加锁而不影响客户端对其它段的访问。

![img](http://static.zybuluo.com/Rico123/zaqxg0nmu1qq79lm6wpwss2f/ConcurrentHashMap.jpg)

参考：https://blog.csdn.net/justloveyou_/article/details/72783008/



jdk1.8改进：

- 链表转为红黑树
- 不再使用段机制，**转而直接对每个桶加锁**



相关问题：

**1、为什么concurrentHashMap、Hashtable等不允许key/value为null呢？而HashMap可以呢？**

**2、*\*什么是快速失败机制（fail-fast）呢？\**
答：在用迭代器遍历一个集合对象时，如果遍历过程中对集合对象的内容进行了修改（增加、删除、修改），则会抛出 Concurrent Modification Exception。**



#### 面试常见问题



##### **请你简单介绍一下ArrayList和LinkedList的区别**

1. **是否保证线程安全：** `ArrayList` 和 `LinkedList` 都是不同步的，也就是不保证线程安全；
2. **底层数据结构：** `Arraylist` 底层使用的是 **`Object` 数组**；`LinkedList` 底层使用的是 **双向链表** 数据结构（JDK1.6 之前为循环链表，JDK1.7 取消了循环。注意双向链表和双向循环链表的区别，下面有介绍到！）
3. **插入和删除是否受元素位置的影响**：
   - `ArrayList` 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。 比如：执行`add(E e)`方法的时候， `ArrayList` 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是 O(1)。但是如果要在指定位置 i 插入和删除元素的话（`add(int index, E element)`）时间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。
   - `LinkedList` 采用链表存储，所以，如果是在头尾插入或者删除元素不受元素位置的影响（`add(E e)`、`addFirst(E e)`、`addLast(E e)`、`removeFirst()` 、 `removeLast()`），时间复杂度为 O(1)，如果是要在指定位置 `i` 插入和删除元素的话（`add(int index, E element)`，`remove(Object o)`）， 时间复杂度为 O(n) ，因为需要先移动到指定位置再插入。
4. **是否支持快速随机访问：** `LinkedList` 不支持高效的随机元素访问，而 `ArrayList` 支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于`get(int index)`方法)。
5. **内存空间占用：** `ArrayList` 的空间浪费主要体现在在 list 列表的结尾会预留一定的容量空间，而 LinkedList 的空间花费则体现在它的每一个元素都需要消耗比 ArrayList 更多的空间（因为要存放直接后继和直接前驱以及数据）。

我们在项目中一般是不会使用到 `LinkedList` 的，需要用到 `LinkedList` 的场景几乎都可以使用 `ArrayList` 来代替，并且，性能通常会更好！就连 `LinkedList` 的作者约书亚 · 布洛克（Josh Bloch）自己都说从来不会使用 `LinkedList` 。



##### **阐述ArrayList、Vector、LinkedList的存储性能和特性**

Vector中的方法由于添加了synchronized修饰，因此Vector是线程安全的容器，但性能上较ArrayList差，因此已经是Java中的遗留容器

但是由于ArrayList和LinkedListed都是非线程安全的，如果遇到多个线程操作同一个容器的场景，则可以通过工具类Collections中的synchronizedList方法将其转换成线程安全的容器后再使用（这是对装潢模式的应用，将已有对象传入另一个类的构造器中创建新的对象来增强实现）

为什么要优先使用链表呢？ 使用链表的唯一理由是尽可能地减少在列表中间插人或删除元素所付出的代价。如果列表只有少数几个元素， 就完全可以使用 ArrayList。



##### **HashMap 和 Hashtable 的区别**

一.历史原因:Hashtable 是基于陈旧的 Dictionary 类的，HashMap 是 Java 1.2 引进的 Map 接口的一个实现 

二.同步性:Hashtable 是线程安全的，也就是说是同步的，而 HashMap 是线程不安全的，不是同步的 

三.值：只有 HashMap 可以让你将空值作为一个表的条目的 key 或 value



##### **hashmap 扩容因子为什么是 0.75** 

负载因子是0.75的时候，空间利用率比较高，而且避免了相当多的Hash冲突，使得底层的链表或者是红黑树的高度比较低，提升了空间效率。









##### **请说明Collection 和 Collections的区别。**

Collection 是集合类的上级接口，继承与他的接口主要有 Set 和 List. 

Collections 是针对集合类的一个帮助类，他提供一系列静态方法实现对各种集合的**搜索、排序、线程安全化**等操作。



##### **请简单说明一下什么是迭代器**？

高层抽象来说它是一种设计模式，迭代器（Iterator）模式，又叫做游标模式。它的含义是，提供一种方法访问一个容器对象中各个元素，而又不需暴露该对象的内部细节。

简单的说，迭代器就是一个接口Iterator，实现了该接口的类就叫做可迭代类，这些类多数时候指的就是java.util包下的集合类。用于提供一种访问一个集合对象各个元素的途径，同时又不需要暴露该对象的内部细节

参考链接：https://blog.csdn.net/weixin_42221030/article/details/80522815



##### **HashSet,TreeSet和LinkedHashSet的区别**

**Set接口**	

Set不允许包含相同的元素，如果试图把两个相同元素加入同一个集合中，add方法返回false。

Set判断两个对象相同不是使用==运算符，而是根据**equals**方法。也就是说，只要两个对象用equals方法比较返回true，Set就不会接受这两个对象。

**HashSet**

HashSet 是基于 HashMap 来实现的，是一个不允许有重复元素的集合。

HashSet 不是线程安全的， 如果多个线程尝试同时修改 HashSet，则最终结果是不确定的。 

要像hashSet中添加自定义类对象，必须要重写**hashCode和equals()**方法

**TreeSet**

特点：有序，元素唯一，查询速度比list快

底层是基于`TreeMap`来实现的，所以底层结构也是红黑树，因为他和`HashSet`不同的是不需要重写`hashCode()`和`equals()`方法，因为它去重是依靠比较器来去重，因为结构是红黑树，所以每次插入都会遍历比较来寻找节点插入位置，如果发现某个节点的值是一样的那就会直接覆盖。

要使用树集， 必须能够比较对象元素。这些元素必须实现 **Comparable** 接口（即需在对象类中重写compareTo方法），或者构造集时必须提供一个 **Comparator**。

*注：在调用 x.compareTo(y) 的时候，这个compareTo 方法必须确实比较两个对象的内容， 并返回比较的结果。 当 x 小于 y 时，返回一个负数；当 x 等于 y 时，返回 0; 否则返回一个正数。*

**LinkedHashSet**
LinkedHashSet集合同样是根据元素的hashCode值来决定元素的存储位置，但是它同时使用链表维护元素的次序。这样使得元素看起 来像是以插入顺序保存的，也就是说，当遍历该集合时候，LinkedHashSet将会以元素的添加顺序访问集合的元素。LinkedHashSet在迭代访问Set中的全部元素时，性能比HashSet好，但是插入时性能稍微逊色于HashSet。



### Java反射简单笔记(类Class的理解)

<u>**反射的特征：体现程序运行时的动态性**，是动态语言的关键</u>

如java文件的执行过程：先编译，后运行：运行时才确定具体要创建的对象

1. 反射机制允许程序在执行期借助于Reflection API取得任何类的内部信息（比如成员变量、构造器、成员方法等），并能操作对象的属性及方法。反射在设计模式和一些框架底层都会用到。
2. 在加载完类之后，在堆中就产生了一个对应的Class类型的对象。这个对象包含了类的完整结构信息。这个Class对象就像一面镜子，通过这面镜子可以看到类的结构，所以形象的称之为：反射



 

#### 关于java.lang.Class类的理解

在Object类中定义了以下的方法，此方法

将被所有子类继承：

```Java
● public final Class getClass()
```

以上的方法返回值的类型是一个Class类，此类是Java反射的源头。

实际上所谓反射从程序的运行结果来看也很好理解，即：可以通过对象反射求出类的名称。

![image-20220609203255327](https://gitee.com/yu-xiong-yu/typora-img/raw/8aa1e36bf0c5c6b0f63083ca1e5c0e08e2bbe910/Img/202206092032417.png)

#### 1.类的加载过程

程序经过javac.exe命令后(是编译命令，执行编译过程)，会生成一个或多个字节码文件(.class结尾)。

接着我们使用java.exe命令对某个字节码文件进行解释运行。相当于将该字节码文件加载到内存中。

以上过程就叫做类的加载(编译过程不在类的加载过程中)。

加载到内存中的类，就称为运行时类，**此运行时类就作为Class的一个实例**

**即：一个Class对象对应的是一个加载到JVM中的一个.class文件**

 

如Person.java文件，Person.class是类本身。所以说类也是对象，是Class的一个对象
换句话说：Class类的实例对应着一个运行时类，所以想提供Class的一个实例，不能new Class，Class的一个实例就是用一个运行时类来进行赋值的。如代码：

```Java
Class clazz = Person.class;
```

加载到内存中的运行时类，会缓存一定的时间,在此时间之内是唯一存在的，我们可以通过不同的方式来获取此运行时类
在运行时类的声明周期内是唯一的，不会去重复的加载第二个，或者第三个

 

#### 2.三种获取Class类型的实例的方法：

方法1：通过`Object`类中的`getClass()`方法返回一个`Class`类型的实例

```java
public static Class getPersonClass1() {
        /********** Begin *********/
    	Person person = new Person();
        return person.getClass();
        /********** End *********/
    }
```



方法2：通过静态方法`Class.forName("全类名")`获取类名对应的`Class`对象

```Java
public static Class getPersonClass2() {
        /********** Begin *********/
        Class clazz = null;
        try {
            clazz = Class.forName("step1.Person");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return clazz;
        /********** End *********/
    }
```

方法3：通过类字面常量`Class.class`获取

示例如下：

```java
public static Class getPersonClass3() {
    /********** Begin *********/　　　　　　//调用运行时类的属性.class
    Class clazz = Person.class;//Class带泛型，因为是Person给它赋的值，所以具体操作的是Person
    System.out.println(clazz);//class test.Person,不加泛型不会影响输出的效果,加上泛型可以避免后面的操作进行强转
    return Person.class;
    /********** End *********/
}
```

　　　　　

 方法4：（较少使用）

```java
1     //方式四：使用类的加载器:ClassLoader
2         //因为这个测试方法是写在test这个类中的
3         ClassLoader classLoader = test.class.getClassLoader();
4 
5         //得到之后，显式地load class，即显式的去加载某一个类
6         Class clazz4 = classLoader.loadClass("com.test.Person");
7         System.out.println(clazz4);//输出class com.test.Person
8         System.out.println(clazz==clazz4);//true
9     
```

 

#### 3.Class类的常用方法

![image-20210626174945097](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20210626174945097.png)

new Instance();

**创建类的对象：**调用Class对象的newInstance()方法

要 求： 

1）类必须有一个无参数的构造器。

2）类的构造器的访问权限需要足够，通常设置为public。





### 注解

从JDK5开始,Java增加对元数据的支持，也就是注解，注解与注释是有一定区别的，**可以把注解理解为代码里的特殊标记，这些标记可以在编译，类加载，运行时被读取，并执行相应的处理**。通过注解开发人员可以在不改变原有代码和逻辑的情况下在源代码中嵌入补充信息。于强制类型转换错误的情况，[编译器](https://baike.baidu.com/item/编译器/8853067)可能不提示错误，在运行的时候才出现异常，这是一个安全隐患。因此，为了解决这一问题，J2SE 1.5引入泛型也是自然而然的了



声明周期/作用时期

```java
package java.lang.annotation;
public enum RetentionPolicy {
    SOURCE,            /* Annotation信息仅存在于编译器处理期间，编译器处理完之后就没有该Annotation信息了  */

    CLASS,             /* 编译器将Annotation存储于类对应的.class文件中。默认行为  */

    RUNTIME            /* 编译器将Annotation存储于class文件中，并且可由JVM读入 */
}
```

参考：https://www.runoob.com/w3cnote/java-annotation.html



### 泛型

这个问题我想从集合这个角度切入吧，因为平常接触泛型的最多方式就是使用集合了

Java集合（Collection）中元素的类型是多种多样的。例如，有些集合中的元素是Byte类型的，而有些则可能是String类型的，等等。Java允许程序员构建一个元素类型为Object的Collection，其中的元素可以是任何类型。在[Java SE](https://baike.baidu.com/item/Java SE/4662159) 1.5之前，没有泛型（Generics）的情况下，通过对类型Object的引用来实现参数的“任意化”，“任意化”带来的缺点是要作显式的[强制类型转换](https://baike.baidu.com/item/强制类型转换/1580197)，而这种转换是要求开发者对实际参数类型可以在预知的情况下进行的。

#### 作用

**第一是泛化。**可以用T代表任意类型Java语言中引入泛型是一个较大的功能增强不仅语言、类型系统和编译器有了较大的变化，以支持泛型，而且类库也进行了大翻修，所以许多重要的类，比如集合框架，都已经成为泛型化的了，这带来了很多好处。

**第二是类型安全。泛型的一个主要目标就是提高Java程序的类型安全，使用泛型可以使编译器知道变量的类型限制，进而可以在更高程度上验证类型假设**。如果不用泛型，则必须使用强制类型转换，而强制类型转换不安全，在运行期可能发生ClassCast Exception异常，如果使用泛型，则会在编译期就能发现该错误。

**第三是消除强制类型转换。**泛型可以消除源代码中的许多强制类型转换，这样可以使代码更加可读，并减少出错的机会。如一个dog放入arrayList<object>，要先转为object；取出时又要从object转化为dog，这样一来效率也低。

**第四是向后兼容。**支持泛型的Java编译器（例如JDK1.5中的[Javac](https://baike.baidu.com/item/Javac/5156702)）可以用来编译经过泛型扩充的Java程序（Generics Java程序），但是现有的没有使用泛型扩充的Java程序仍然可以用这些编译器来编译。



#### 泛型方法

你可以写一个泛型方法，该方法在调用时可以接收不同类型的参数。根据传递给泛型方法的参数类型，编译器适当地处理每一个方法调用。



#### 类 型 擦 除

意思是原始类型T用第一个限定的类型变量来替换， 如果没有给定限定就用 Object 替换。如：

```java
public class Interval <T extends Comparable & Serializable〉implements Serializable
{
	private T lower;
	private T upper;
	public Interval (T first, T second) {
        if (first.compareTo(second) <= 0) { 
            lower = first; upper = second; 
        }
        else { 
            lower = second; upper = first; 
        } 
    } 
}
//原始类型 Interval 如下所示：
public class Interval implements Serializable
{
	private Comparable lower;
	private Coiparable upper;
	public Interval (Coiparable first, Coiparable second) { . . . } 
}
```



### 序列化

#### 为什么要序列化和反序列化

​    我们知道，当两个进程进行远程通信时，可以相互发送各种类型的数据，包括文本、图片、音频、视频等， 而这些数据都会以**二进制序列**的形式在网络上传送。那么当两个Java进程进行通信时，能否实现进程间的对象传送呢？答案是可以的。如何做到呢？这就需要Java序列化与反序列化了。换句话说，一方面，发送方需要把这个Java对象转换为字节序列，然后在网络上传送；另一方面，接收方需要从字节序列中恢复出Java对象。当我们明晰了为什么需要Java序列化和反序列化后，我们很自然地会想Java序列化的好处。**其好处一是实现了数据的持久化，通过序列化可以把数据永久地保存到硬盘上（通常存放在文件里），二是，利用序列化实现远程通信，即在网络上传送对象的字节序列。**



#### **序列化ID的作用：** 

​    其实，这个序列化ID起着关键的作用，它决定着是否能够成功反序列化！简单来说，java的序列化机制是通过在运行时判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地实体类中的serialVersionUID进行比较，如果相同则认为是一致的，便可以进行反序列化，否则就会报序列化版本不一致的异常。等会我们可以通过代码验证一下。



只有实现了Serializable或Externalizable接口的类的对象才能被序列化，否则抛出异常。

https://blog.csdn.net/baidu_37107022/article/details/76860371



### 日期类

#### SimpleDateFormat基本使用

1. Date转String

   先设置要转换的日期格式，再做格式化，代码如下：SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMddHHmmss");  注：格式中间可以再插入/、-、:等日期时间分隔符Date date = new Date();String str = sdf.format(date);System.out.println("date="+date+", str="+str);

2. String转Date

   SimpleDateFormat sdf = new SimpleDateFormat("yyyyMMddHHmmss");String str = "20160927082630";Date date = sdf.parse(str);System.out.println("date="+date+", str="+str);

```java
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy/MM/dd/HH:mm:ss");
Date parse = dateFormat.parse("2021/11/16/17:30:01");
System.out.println(parse);
//Tue Nov 16 17:30:01 CST 2021
```



#### SimpleDateFormat的安全问题

解决方案：

1. 使用基于JDK1.8的DateTimeFormatter
2. ThreadLocal

https://542869246.github.io/2019/01/02/%E8%BF%98%E5%9C%A8%E4%BD%BF%E7%94%A8SimpleDateFormat%EF%BC%9F%E4%BD%A0%E7%9A%84%E9%A1%B9%E7%9B%AE%E5%B4%A9%E6%B2%A1%EF%BC%9F/



### Java8

#### Lambda表达式

https://www.runoob.com/java/java8-lambda-expressions.html



### JDK

#### JDK与JRE的区别

JDK：java 开发工具包(Java Develpment Kit)的简称
JRE：Java运行时环境(Java Runtime Environment)的简称

主要区别
1、面向人群不同：
JDK是面向开发人员使用的SDK。
JRE是Java Runtime Enviroment是指Java的运行环境，是面向Java程序的使用者。

2、重要程度不同：
JRE的地位就象一台PC机一样，编写的Java程序必须要JRE才能运行。只要你的电脑安装了JRE，就可以正确运行Java应用程序。

jdk是java开发工具包，是Sun公司针对java开发人员的产品，jdk中包含jre，在jdk的安装目录下有一个jre的目录，里面有两个文件夹bin和lib，在这里可以认为bin里面就是jvm，lib中是java工作需要的类库，而java和lib合起来就称为jre
JDK(Java Development Kit) 是整个JAVA的核心，包括了Java运行环境（Java Runtime Envirnment），一堆Java工具（javac/java/jdb等）和Java基础的类库（即Java API 包括rt.jar）。





待更、、

## Java并发编程

*JUC*就是java.util .concurrent工具包的简称



### 基础知识

#### 线程的状态

```java
// 
public enum State {
       NEW,//初始状态，尚未启动。线程创建之后它将处于 NEW（新建） 状态
       RUNNABLE,//运行中状态。调用 start() 方法后开始运行——》可细化成ready和running状态
       BLOCKED,// 阻塞状态，竞争监视器锁失败，等待进入同步代码块
       WAITING,//等待状态，无限期等待其他线程的通知的线程在此状态。如线程的执行了wait方法，需要notify才能够返回到运行状态。
       TIMED_WAITING,//超时等待状态，相当于在等待状态的基础上增加了超时限制，比如通过 sleep（long millis）方法或 wait（long millis）方法可以将 Java 线程置于 TIMED_WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。
       TERMINATED;//终止状态
 }
————————————————

```





#### 线程生命周期

![image-20220610181148272](https://yx-typora-img-1306943318.cos.ap-guangzhou.myqcloud.com/img/202206101811409.png)

![image-20210922213010631](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20210922213010631.png)

详细

主线程-》子线程



#### 优先级

优先级代表线程执行的机会的大小，优先级高的可能先执行，低的可能后执行。

在 Java  源码中，**优先级从低到高分别是 1 到 10，线程默认 new 出来的优先级都是 5**，源码如下：

```java
 /**
  * The minimum priority that a thread can have.
  */
 public final static int MIN_PRIORITY = 1;

/**
  * The default priority that is assigned to a thread.
  */
 public final static int NORM_PRIORITY = 5;

 /**
  * The maximum priority that a thread can have.
  */
 public final static int MAX_PRIORITY = 10;
————————————————
```



### **创建线程有几种不同的方式？你喜欢哪一种？为什么？**

有三种使⽤线程的⽅法：

- **继承Thread类创建线程：**

  \- 定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。因此把run()方法成为执行体
  \- 创建Thread子类的实例也就是创建了线程对象
  \- 调用线程对象的start()方法来启动线程

- **通过Runnable接口创建线程类：**

  \- 定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体
  \- 创建Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象
  \- 调用线程对象的start()方法来启动该线程

- **通过Callable和Future创建对象：**

  \- 创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程的执行体，并且有返回值
  \- 创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该FutrueTask对象封装了该Callable对象的call()方法的返回值
  \- 使用FutureTask对象作为Thread对象的target创建并启动新线程
  \- 调用FutureTask对象的get()方法来获得子线程执行结束后的返回值

实现Runnable接口这种方式更受欢迎，因为这不需要继承Thread类。在应用设计中已经继承了别的对象的情况下，只能实现接口。同时，线程池也是非常高效的，很容易实现和使用。

**实现接⼝** **VS** **继承** **Thread**

实现接⼝会更好⼀些，因为：

Java 不⽀持多重继承，因此继承了 Thread 类就⽆法继承其它类，但是可以实现多个接⼝；

类可能只要求可执⾏就⾏，继承整个 Thread 类开销过⼤。



### FutureTask类和Callable接口

​	首先我们要知道，多线程机制中run方法的执行是没有返回值的，我们也不可以知道线程中方法的执行进度如何。就难以实现异步执行的需求。



而Future接口便提供了这种思想，它的核心思想是：一个方法f，计算过程可能非常耗时，等待f返回，显然不明智。可以在调用f的时候，立马返回一个Future，可以通过Future这个数据结构去***控制***方法f的计算过程

Future接口声明了如下方法：

![image-20211026131242694](http://yu-xiong-yu.gitee.io/typora-img//Img/image-20211026131242694.png)

这些方法的作用分别是：

- get方法：获取计算结果（如果还没计算完，也是必须等待的）
- get(long, TimeUnit)：给定最多等待的计算时间，前者是时间长度，后者是时间单位、
- cancel方法：还没计算完，可以取消计算过程
- isDone方法：判断是否计算完
- isCancelled方法：判断计算是否被取消



**而FutureTask类是很好的Future思想的实现类**

下图是FutureTask类的继承关系：

![image-20211026130157248](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20211026130157248-1635224520194.png)

使用Callable接口得到返回值，结合FutureTask查看或控制call方法执行进度。

 Callable接口可以看作是Runnable接口的补充，call方法带有返回值，并且可以抛出异常。



### 线程是如何启动的？调用start()方法和run()方法有什么区别？

想说结论：直接调用run()方法，会使得代码在主线程中执行，并没有开辟一个线程；只有调用start()方法，才能成功开辟一个新的线程。

想要弄明白一个问题，最好的办法就是从源码入手，我们也从这两个方法的源码开始：

```java
public synchronized void start() {
    /**
     * This method is not invoked for the main method thread or "system"
     * group threads created/set up by the VM. Any new functionality added
     * to this method in the future may have to also be added to the VM.
     *
     * A zero status value corresponds to state "NEW".
     */
     // 没有初始化，抛出异常
    if (threadStatus != 0)
        throw new IllegalThreadStateException();

    /* Notify the group that this thread is about to be started
     * so that it can be added to the group's list of threads
     * and the group's unstarted count can be decremented. */
    group.add(this);
 // 是否启动的标识符
    boolean started = false;
    try {
     // start0() 是启动多线程的关键
     // 这里会创建一个新的线程，是一个 native 方法
     // 执行完成之后，新的线程已经在运行了
        start0();
        // 主线程执行
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
            /* do nothing. If start0 threw a Throwable then
              it will be passed up the call stack */
        }
    }
}

————————————————
@Override
public void run() {
    // 简单的运行，不会新起线程，target 是 Runnable
    if (target != null) {
        target.run();
    }
}


```

最主要的是 start0() 方法，start0 被标记成 native ，也就是本地方法，并不需要我们去实现或者了解。

start() 方法调用 start0() 方法后，该线程并不一定会立马执行，只是将线程变成了可运行状态（NEW —> RUNNABLE）。具体什么时候执行，取决于 CPU ，由 CPU 统一调度。

我们又知道 Java 是跨平台的，可以在不同系统上运行，每个系统的 CPU 调度算法不一样，所以就需要做不同的处理，这件事情就只能交给 JVM 来实现了，start0() 方法自然就表标记成了 native。



原文链接：https://blog.csdn.net/weixin_47082274/article/details/106071357





### 线程间的通信

wait/notify()方法

notify：随机唤醒一个等待队列里的线程

notifyAll：唤醒等待队列里的所有线程

https://blog.csdn.net/qq_20009015/article/details/88630778





### 线程池

**为什么要用线程池:**

1. 减少线程创建和销毁的次数，使同一个线程可以多次复用
2. 可以根据系统情况，调整线程的数量。防止创建过多的线程，消耗过多的内存（每个线程1M左右）

阿里巴巴手册：使用线程池的好处是减少在创建和销毁线程上所消耗的时间以及系统资源的开销，解决资源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题。



Executor顶级接口与其继承和实现类

![图片](https://yu-xiong-yu.gitee.io/typora-img/Img/640)



#### ExecutorService

Java里面线程池的顶级接口是Executor，但是严格意义上讲Executor并不是一个线程池，而只是一个执行线程的工具。**真正的线程池接口是ExecutorService**。



**Executors工厂类，提供了一系列工厂方法用于创先线程池，返回的线程池都实现了ExecutorService接口。**

常用的线程池有：**newSingleThreadExecutor**、**newFixedThreadPool**、**newCachedThreadPool**

在JDK帮助文档中，有如此一段话：

使用较为方便的 `Executors` 工厂方法可以创建：

`Executors.newCachedThreadPool()`（无界线程池，可以进行自动线程回收）

`Executors.newFixedThreadPool(int)`（固定大小线程池）

`Executors.newSingleThreadExecutor()`（单个后台线程）

参考：https://www.cnblogs.com/xuxinstyle/p/10513743.html





#### ThreadPoolExecutor类

​	点进Executors的源码里可以看到，上述的几个线程池都是通过ThreadPoolExecutor类来完成创建的。ThreadPoolExecutor继承了抽象类AbstractExecutorService，AbstractExecutorService又实现了ExecutorService接口。故可验证上述结论**“返回的线程池都实现了ExecutorService接口”**。

![image-20211029114322512](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20211029114322512.png)

java.uitl.concurrent.ThreadPoolExecutor类是线程池中最核心的一个类。在ThreadPoolExecutor类中提供了四个构造方法：

```java
public class ThreadPoolExecutor extends AbstractExecutorService {
    .....
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
            BlockingQueue<Runnable> workQueue);
 
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
            BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory);
 
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
            BlockingQueue<Runnable> workQueue,RejectedExecutionHandler handler);
 
    public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
        BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory,RejectedExecutionHandler handler);
    ...
}

```

##### 核心参数

- 1、corePoolSize线程池的常驻核心线程数
- 2、maximumPoolSize能容纳的最大线程数，线程池中能够容纳同时执行的最大线程数，此值必须大于等于1
- 3、keepAliveTime空闲线程存活时间，多余的空闲线程存活时间，当前池中线程数量超过corePoolSize时，当空闲时间达到keepAliveTime时，多余线程会被销毁直到剩下corePoolSize个线程为止
- 4、unit 存活的时间单位，keepAliveTime单位
- 5、workQueue 存放提交但未执行任务的队列，被提交但尚未被执行的任务
- 6、threadFactory 创建线程的工厂类，用于创建线程，一般默认即可
- 7、handler 等待队列满后的拒绝策略，表示当队列满了，并且工作线程大于等于线程池的最大线程数(maximumPoolSize)时如何来拒绝请求执行的runnable的策略*/

**拒绝策略**JDK默认提供了4种：

　　AbortPolicy(默认)：直接抛出RejectedExecutionException异常组织系统正常运行

　　CallerRunPolicy: "调用者运行"一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量。就是回退，谁启用的这个线程，就回退到哪，让调用者执行这个线程。

　　DiscardOldestPolicy: 抛弃队列中等待最久的任务，然后把当前任务加入队列中尝试再次提交当前任务。

　　DiscardPolicy: 该策略默默的丢弃无法处理的任务，不予任何处理也不抛出异常，如果允许任务丢失，这是最好的一种策略。



##### 执行流程

![img](https://yu-xiong-yu.gitee.io/typora-img/Img/v2-19ba0715a5cf7c0ef4f621f7ba95415a_720w.jpg)

参考：https://www.cnblogs.com/qingquanzi/p/8146638.html





**ThreadPoolExecutor是Executors类的底层实现，故阿里巴巴Java开发手册明确表示不允许使用Executors去创建线程池，而是通过ThreadPoolExecutor方式创建，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。**

　  说明： Executors返回的线程池对象的弊端如下：

　　1） FixedThreadPool和SingleThreadPool:

　　　　允许的请求队列长度为 Integer.MAX_VALUE,可能会堆积大量的请求，从而导致OOM。(out of memory)

　　2）CachedThreadPool和ScheduledThreadPool:

　　　　允许的创建线程数量为Integer.MAX_VALUE,可能会创建大量的线程，从而导致OOM.

 



### 定时任务与框架

#### ScheduledExecutorService

```java
	ScheduledExecutorService executor = new ScheduledThreadPoolExecutor(3);
    
	//public ScheduledThreadPoolExecutor(int corePoolSize) {
    //    super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
    //          new DelayedWorkQueue());
    //}
	
	public ScheduledFuture<?> scheduleAtFixedRate(Runnable command,
                                                  long initialDelay,
                                                  long period,
                                                  TimeUnit unit)
/任务，初始延迟时间，任务间隔执行时间，时间单位
```

https://baijiahao.baidu.com/s?id=1676819549425433715&wfr=spider&for=pc



### 多线程如何保证线程安全

参考：https://blog.csdn.net/qq_26545305/article/details/79516610

#### 线程安全等级

一般我们常说某某类是线程安全的，某某是非线程安全的。其实线程安全并不是一个“非黑即白”单项选择题。按照“线程安全”的安全程度**由强到弱**来排序，我们可以将java语言中各种操作共享的数据分为以下5类：**不可变、绝对线程安全、相对线程安全、线程兼容和线程对立**。



#### 线程安全的实现方法

保证线程安全**以是否需要同步手段**分类，分为同步方案和无需同步方案。

##### **同步方案**

包括：**阻塞同步（互斥）**和**非阻塞同步**两种方案

- **阻塞同步最主要的问题就是进行线程阻塞和唤醒**所带来的性能问题，属于一种**悲观的并发策略**。实现：synchronized、ReentrantLock。
- 非阻塞同步。随着硬件指令集的发展，出现了**基于冲突检测的乐观并发策略**。实现：CAS，缺点：ABA问题



##### **无需同步方案**

**要保证线程安全，并不是一定就要进行同步**。当线程的一些方法本来就不涉及共享数据，那它自然就**无需任何同步操作去保证正确性**。

实现：线程本地存储（Thread Local）

​	如果一段代码中所需的数据必须与其他代码共享，那就看看这些共享数据的代码是否能保证在同一个线程中执行？如果能保证，我们就可以把共享数据的**可见范围**限制在同一个线程之内。这样无需同步也能保证线程之间不出现数据的争用问题。



### 乐观锁与悲观锁

我更倾向于把它们两者理解为应对并发的策略。

简单举两个实现例子：

Synchronized属于**悲观锁**，悲观地认为程序中的并发情况严重，所以严防死守。CAS属于**乐观锁**，乐观地认为程序中的并发情况不那么严重，所以让线程不断去尝试更新。

- 悲观锁适合写操作多的场景，先加锁可以保证写操作时数据正确。
- 乐观锁适合读操作多的场景，不加锁的特点能够使其读操作的性能大幅提升。



#### 乐观锁的实现方式

1. 使用**版本标识**version来确定读到的数据与提交时的数据是否一致。数据提交后修改版本标识，不一致时可以采取丢弃和再次尝试的策略。
2. java 中的 Compare and Swap 即 **CAS**



#### CAS机制

##### **什么是CAS？**

CAS是一种处理并发的机制。是英文单词**Compare And Swap**的缩写，翻译过来就是比较并替换。CAS属于乐观锁的实现。

CAS机制当中使用了3个基本操作数：**内存地址V**，**旧的预期值A**，**要修改的新值B**。

提交更新一个变量的时候，只有当变量的预期值A和内存地址V当中的实际值相同时，才会将内存地址V对应的值修改为B。否则什么都不做。

参考：https://blog.csdn.net/qq_35571554/article/details/82892806

![img](https://yu-xiong-yu.gitee.io/typora-img/Img/c2fdfc039245d688e61add19d69dad18d31b24dc-1638288959698.png)



##### **CAS的缺点**

**1.CPU开销较大**

在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力。

**2.不能保证代码块的原子性**

CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用Synchronized了。

   因为它本身就只是一个锁住总线的原子交换操作啊。两个CAS操作之间并不能保证没有重入现象。



**3.ABA问题**

问：三个线程同时在CAS期间对变量x进行操作。变量x被线程1从A改成B，然后线程2又将x改回为A，然后线程3发现变量还是原来的值A就可以正常提交修改吗？

解决：就是使用版本号标志。每当修改操作一次版本号加1，这样比较时候，不仅需要比较值还比较了版本号。但是在java5中，已经提供了AtomicStampedReference来解决问题了。	



##### 总结

Java的CAS操作可以实现现代CPU上硬件级别的原子指令（不是依靠JVM或者操作系统的锁机制），而同时`volatile`关键字又保证了线程间共享变量的可见性和指令的顺序性，因此凭借这两种手段，就可以实现不依靠操作系统实现的锁机制来保证并发时共享变量的一致性。

CAS虽然很高效解决了原子操作问题，但是CAS仍然存在问题：

- 循环时间长，开销很大：就是如果CAS失效，就会一直进行尝试，当时间过长仍然失败，那么就会给CPU带来很大的开销。
- 只能保证一个共享变量的原子操作：当对一个变量执行操作时，可以使用循环 CAS 的方式来保证原子操作，但对多个变量操作时，CAS 目前无法直接保证操作的原子性。可以这样解决：使用互斥锁来保证原子性、将多个变量封装成对象，通过 AtomicReference 来保证原子性。









### synchronized关键字理解

​	进入 synchronized 时，使得本地缓存失效，synchronized 块中对共享变量的读取必须从主内存读取；退出 synchronized 时，会将进入 synchronized 块之前和 synchronized 块中的写操作刷入到主存中。**synchronized 在退出的时候，能保证 synchronized 块中对于共享变量的写入一定会刷入到主内存中**。



#### synchronized的锁优化

在 Java 早期版本中，`synchronized` 属于 **重量级锁**，效率低下。因为监视器锁（monitor）是依赖于底层的操作系统的 `Mutex Lock` 来实现的，Java 的线程是映射到操作系统的原生线程之上的。如果要挂起或者唤醒一个线程，都需要操作系统帮忙完成，而操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高。

**自旋锁与适应性自旋锁**

​	阻塞或唤醒一个Java线程需要操作系统切换CPU状态来完成，这种状态转换需要耗费处理器时间。如果同步代码块中的内容过于简单，状态转换消耗的时间有可能比用户代码执行的时间还要长。**在许多场景中，同步资源的锁定时间很短，为了这一小段时间去切换线程，线程挂起和恢复现场的花费可能会让系统得不偿失。如果物理机器有多个处理器，能够让两个或以上的线程同时并行执行，我们就可以让后面那个请求锁的线程不放弃CPU的执行时间，看看持有锁的线程是否很快就会释放锁。**

​	而为了让当前线程“稍等一下”，我们需让当前线程进行自旋，如果在自旋完成后前面锁定同步资源的线程已经释放了锁，那么当前线程就可以不必阻塞而是直接获取同步资源，从而避免切换线程的开销。自旋锁的实现原理同样也是CAS，同时配合线程执行一个忙循环（自旋），这项技术就是所谓的自旋锁。

​	**但自旋锁本身是有缺点的，它不能代替阻塞，自旋等待虽然一定程度上避免了线程切换的开销，但它要占用处理器时间。**如果锁被占用的时间很短，自旋等待的效果就会非常好。反之，如果锁被占用的时间很长，那么自旋的线程只会白浪费处理器资源。所以，自旋等待的时间必须要有一定的限度，如果自旋超过了限定次数（默认是10次，可以使用-XX:PreBlockSpin来更改）没有成功获得锁，就应当挂起线程。



在JDK 6中对自旋锁的优化，引入了自适应的自旋。自适应意味着自旋的时间不再是固定的了，而是由前一次在同一个锁上的自旋时间及锁的拥有者的状态来决定的。如果在同一个锁对象上，自旋等待刚刚成功获得过锁，并且持有锁的线程正在运行中，那么虚拟机就会认为这次自旋也很有可能再次成功，进而允许自旋等待持续相对更长的时间，比如持续100次忙循环。

另一方面，如果对于某个锁，自旋很少成功获得过锁，那在以后要获取这个锁时将有可能直接省略掉自旋过程，以避免浪费处理器资源。有了自适应自旋，随着程序运行时间的增长及性能监控信息的不断完善，虚拟机对程序锁的状况预测就会越来越精准，虚拟机就会变得越来越“聪明”了



#### Synchronized是如何实现线程同步的？

​	synchronized通过Monitor来实现线程同步，Monitor是依赖于底层的操作系统的Mutex Lock（互斥锁）来实现的线程同步。 Monitor可以理解为一个同步工具或一种同步机制，通常被描述为一个对象。每一个Java对象就有一把看不见的锁，称为内部锁或者Monitor锁。

​	如同我们在自旋锁中提到的“阻塞或唤醒一个Java线程需要操作系统切换CPU状态来完成，这种状态转换需要耗费处理器时间。如果同步代码块中的内容过于简单，状态转换消耗的时间有可能比用户代码执行的时间还要长”。这种方式就是synchronized最初实现同步的方式，这就是JDK 6之前synchronized效率低的原因。这种依赖于操作系统Mutex Lock所实现的锁我们称之为“重量级锁”。

​	JDK 6中为了减少获得锁和释放锁带来的性能消耗，引入了“偏向锁”和“轻量级锁”。所以目前锁一共有4种状态，级别从低到高依次是：无锁、偏向锁、轻量级锁和重量级锁。锁状态只能升级不能降级。

所以目前锁一共有4种状态，级别从低到高依次是：无锁、偏向锁、轻量级锁和重量级锁。锁状态只能升级不能降级。



对于这四种锁状态如何实现？答：**“Java对象头”**

HotSpot虚拟机对象的对象头部分包括两类信息。第一类是用于存储对象自身的运行时数据，如哈希码（HashCode）、GC分代年龄、锁状态标志、线程持有的锁、偏向线程ID、偏向时间戳等，这部分数据的长度在32位和64位的虚拟机（未开启压缩指针）中分别为32个比特和64个比特，官方称它为“Mark Word”。例如在32位的HotSpot虚拟机中，**如对象未被同步锁锁定的状态下，Mark Word的32个比特存储空间中的25个比特用于存储对象哈希码**，**4个比特用于存储对象分代年龄（存储内容）**，**2个比特用于存储锁标志位**，**1个比特固定为0**。在其他状态（轻量级锁定、重量级锁定、GC标记、可偏向）[1]下对象的存储内容如表2-1所示。

![image-20220330222811027](https://yu-xiong-yu.gitee.io/typora-img/Img/202203302228194.png)



**偏向锁**

偏向锁是指一段同步代码一直被一个线程所访问，那么该线程会自动获取锁，降低获取锁的代价。

在大多数情况下，锁总是由同一线程多次获得，不存在多线程竞争，所以出现了偏向锁。其目标就是在只有一个线程执行同步代码块时能够提高性能。

当一个线程访问同步代码块并获取锁时，会在Mark Word里存储锁偏向的线程ID。在线程进入和退出同步块时不再通过CAS操作来加锁和解锁，而是检测Mark Word里是否存储着指向当前线程的偏向锁。

**轻量级锁**

是指当锁是偏向锁的时候，被另外的线程所访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，从而提高性能。

在代码进入同步块的时候，如果同步对象锁状态为无锁状态（锁标志位为“01”状态，是否为偏向锁为“0”），虚拟机首先将在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝，然后拷贝对象头中的Mark Word复制到锁记录中。

拷贝成功后，虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock Record里的owner指针指向对象的Mark Word。

如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，表示此对象处于轻量级锁定状态。

如果轻量级锁的更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行，否则说明多个线程竞争锁。

**若当前只有一个等待线程，则该线程通过自旋进行等待。但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。**



**重量级锁**

升级为重量级锁时，锁标志的状态值变为“10”，此时Mark Word中存储的是指向重量级锁的指针，此时等待锁的线程都会进入阻塞状态。

整体的锁状态升级流程如下：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/hEx03cFgUsUWxF3IJSFicIicbpueYm7MoKm1LZdLiccu4M4FTLJibfGDCAxcP6C8MlD2g6reTaNKdndbGqfu4tqiafw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

综上，总结：

- **偏向锁通过对比Mark Word解决加锁问题，避免执行CAS操作**。
- **而轻量级锁是通过用CAS操作和自旋来解决加锁问题，避免线程阻塞和唤醒而影响性能（作用于自旋时间短，并发线程少的情况）**。
- 重量级锁是将除了拥有锁的线程以外的线程都阻塞。





#### 相关问题

一、synchronized和lock的用法区别

synchronized：在需要同步的对象中加入此控制，synchronized可以加在方法上，也可以加在特定代码块中，括号中表示需要锁的对象。

lock：需要显示指定起始位置和终止位置。一般使用ReentrantLock类做为锁，多个线程中必须要使用一个ReentrantLock类做为对象才能保证锁的生效。且在加锁和解锁处需要通过lock()和unlock()显示指出。所以一般会在finally块中写unlock()以防死锁。



### 一些锁的概念

参考链接：https://mp.weixin.qq.com/s?__biz=MjM5NjQ5MTI5OA==&mid=2651749434&idx=3&sn=5ffa63ad47fe166f2f1a9f604ed10091&chksm=bd12a5778a652c61509d9e718ab086ff27ad8768586ea9b38c3dcf9e017a8e49bcae3df9bcc8&scene=38#wechat_redirect





#### **公平锁 VS 非公平锁**

> **实现类：java.util.concurrent.locks.ReentrantLock**

ReentrantLock里面有一个内部类Sync，Sync继承AQS（AbstractQueuedSynchronizer），添加锁和释放锁的大部分操作实际上都是在Sync中实现的。它有公平锁FairSync和非公平锁NonfairSync两个子类。ReentrantLock默认使用非公平锁，也可以通过构造器来显示的指定使用公平锁。



#### **独享锁（写锁） VS 共享锁（读锁）**

> **实现类：java.util.concurrent.locks.ReentrantReadWriteLock**

读锁的共享锁可保证并发读非常高效，而读写、写读、写写的过程互斥，因为读锁和写锁是分离的。





**存在一个write线程，所有读操作都阻塞？**

只有等待其他读线程都释放了读锁，写锁才能被当前线程获取，而写锁一旦被获取，则其他读写线程的后续访问均被阻塞。





### AbstractQueuedSynchronizer(AQS)

#### 类概述

​	AQS的完整类名是——AbstractQueuedSynchronizer（直译过来就是抽象队列同步器）。从字面上就能看出来，是专门用于实现同步功能的一个抽象框架。事实上，AQS是著名的JDK并发包设计者Doug Lea定义的一套用于**控制多线程并发访问共享资源的基础框架**，许多并发包工具实现都依赖于它，如常用的**ReentrantLock、ReadWriteLock、Semaphore、CountDownLatch和ThreadPoolExecutor 的 Worker**。


state字段（int类型，32位），该字段用来描述有多少线程获持有锁。

Sync是AQS的一个子类，这种结构在CountDownLatch、ReentrantLock、Semaphore里面也都存在。



举个例子：实现一个类似ReentrantLock功能的可重入锁 ，需要一个变量维护锁被重入的次数，同时必须保证多线程同时操作该变量时是线程安全的；同时竞争锁失败的线程，可能需要让它们陷入阻塞并进行排队，同时要在合适的时机将其唤醒。其实不只是对于锁而言，其他的进行多线程并发控制的同步组件基本都需要上述这些操作。这一系列的操作流程都是繁杂而重复的，此时AQS的意义就凸显出来了。

​	AQS定义好了这一套骨架流程的操作，不同的同步组件只需要继承AQS并重写指定的方法，就可以实现同步组件自身的功能逻辑。这其实就是**模板方法的思想**。

参考：https://blog.csdn.net/beslet/article/details/120098886



#### 同步状态的获取和释放

![在这里插入图片描述](https://img-blog.csdnimg.cn/5baf55c905fb4a82a83608c80d6943bc.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAQ29kZSBNYXZz,size_20,color_FFFFFF,t_70,g_se,x_16)

​	





### **CountDownLatch**

https://baijiahao.baidu.com/s?id=1663690872148615289&wfr=spider&for=pc	

### CyclicBarrier 是什么？

从字面上的意思可以知道，这个类的中文意思是“循环栅栏”。大概的意思就是一个可循环利用的屏障。

它的作用就是会让所有线程都等待完成后才会继续下一步行动。

#### 构造方法

```java
public CyclicBarrier(int parties)
public CyclicBarrier(int parties, Runnable barrierAction)
```

**解析：**

- parties 是参与线程的个数
- 第二个构造方法有一个 Runnable 参数，这个参数的意思是最后一个到达线程要做的任务

#### 重要方法

```java
public int await() throws InterruptedException, BrokenBarrierException
public int await(long timeout, TimeUnit unit) throws InterruptedException, BrokenBarrierException, TimeoutException
```

**解析：**

- 线程调用 await() 表示自己已经到达栅栏
- BrokenBarrierException 表示栅栏已经被破坏，破坏的原因可能是其中一个线程 await() 时被中断或者超时



例：

```java
package Test2;

import java.util.concurrent.CyclicBarrier;

/**
 * @Description: 循环顺序输出ABC
 * @ProjectName: java1125
 * @ClassName: CyclicBarrierTest
 * @Author: YX
 * @Date: 2021/12/2 16:09
 */
public class CyclicBarrierTest {
    private static CyclicBarrier cyclicBarrier_AB = new CyclicBarrier(2);
    private static CyclicBarrier cyclicBarrier_BC = new CyclicBarrier(2);
    private static CyclicBarrier cyclicBarrier_ABC = new CyclicBarrier(3);
    
    public static void main(String[] args) {
        
        Thread threadA = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    while(true) {
                        System.out.println("A");
                        //控制A执行完在执行
                        cyclicBarrier_AB.await();
                        //等待ABC都运行完
                        cyclicBarrier_ABC.await();
                    }
                }catch (Exception ex){
                    ex.printStackTrace();
                }
            }
        });

        Thread threadB = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    while(true) {
                        cyclicBarrier_AB.await();
                        System.out.println("B");
                        //控制B执行完在执行C
                        cyclicBarrier_BC.await();
                        //等待ABC都运行完
                        cyclicBarrier_ABC.await();
                    }
                }catch (Exception ex){
                    ex.printStackTrace();
                }
            }
        });

        Thread threadC = new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    while(true) {
                        cyclicBarrier_BC.await();
                        System.out.println("C");
                        //等待ABC都运行完
                        cyclicBarrier_ABC.await();
                    }
                }catch (Exception ex){
                    ex.printStackTrace();
                }

            }
        });

        threadC.start();
        threadB.start();
        threadA.start();
        
    }
}

```

参考：https://www.jianshu.com/p/333fd8faa56e









### **java 多线程中 Thread.sleep(0)表示什么意思？**

Thread.Sleep(0)的作用，就是“**触发操作系统立刻重新进行一次CPU竞争**”。竞争的结果也许是当前线程仍然获得CPU控制权，也许会换成别的线程获得CPU控制权。这也是我们在大循环里面经常会写一句Thread.Sleep(0) ，因为这样就给了其他线程比如Paint线程获得CPU控制权的权力，这样界面就不会假死在那里。

参考链接：https://blog.csdn.net/xiaoxiaole0313/article/details/106740691







# 拓展知识

**Lombok注解介绍**

https://blog.51cto.com/u_15191078/2755758



**gradle和maven有什么用？分别有什么区别？**

Gradle和Maven都是项目自动构建工具，将项目中的依赖jar与编译好的源代码打包和构建

参考： https://www.zhihu.com/question/29338218/answer/51293828

# 其他基础常见面试题

## 操作系统

![程序与系统调用关系图](https://yu-xiong-yu.gitee.io/typora-img/Img/%E7%A8%8B%E5%BA%8F%E4%B8%8E%E7%B3%BB%E7%BB%9F%E8%B0%83%E7%94%A8%E5%85%B3%E7%B3%BB%E5%9B%BE-1637548616732.jpeg)

### 并发和并行的区别

**并发的关键是你的处理器有处理多个任务的能力（不是顺序执行任务），完成这些任务有多个步骤（对应相应的代码），不同任务步骤是可以穿插进行的，不强调要同时。**



**并行的关键是你需要有同时处理多个任务的能力。而实现并行的条件就是多核处理器，它可以实现在同一时间点上同时运行多个任务。**

所以我认为它们最关键的点就是：是否是『同时』



参考并发与并行的区别是什么？ - 邱昊宇的回答 - 知乎 https://www.zhihu.com/question/33515481/answer/452128444

并发与并行的区别是什么？ - 刘志军的回答 - 知乎 https://www.zhihu.com/question/33515481/answer/199929767





### **操作系统中进程和线程的区别、共享区域**

**对操作系统来说，线程是程序的最小执行单位，进程是最小的资源管理单元。**线程可以比作是轻量级的进程，是程序执行的最小单位,线程间的切换和调度的成本远远小于进程。

答：

**进程**是指一个具有一定独立功能的程序关于某个数据集合的一次**运行活动**，注！进程是资源分配的最小单位

- 进程表示一个逻辑控制流，就是一种计算过程，它造成一个假象，好像这个进程一直在独占CPU资源
- 进程拥有一个**独立的虚拟内存地址空间**，它造成一个假象，好像这个进程一致在独占存储器资源

操作系统利用fork系统调用来创建一个子进程。fork所创建的子进程会复制父进程的虚拟地址空间，现代操作系统使用了写时复制Copy on write的方式来优化fork的性能，fork刚创建的子进程采用了共享的方式，只用指针指向了父进程的物理资源。

进程上下文切换保存的内容有：

1.页表 -- 对应虚拟内存资源
2.文件描述符表/打开文件表 -- 对应打开的文件资源
3.寄存器 -- 对应运行时数据
4.信号控制信息/进程运行信息

​	当内存进行进程调度时进行的进程上下文切换就容易理解了，**一个进程运行要依赖这么些资源，那么进程上下文切换就要把这些资源都保存起来写回到内存中，等下次这个进程被调度时再把这些资源再加载到寄存器和高速缓存硬件。**





**线程**是比进程更小的活动单位，它是进程中的一个执行路径。线程是调度的基本单位

**线程解决的最大问题就是它可以很简单地表示共享资源的问题，这里说的资源指的是存储器资源，资源最后都会加载到物理内存，一个进程的所有线程都是共享这个进程的同一个虚拟地址空间的。**

线程的上下文切换：

​	对操作系统来说，它看到要被调度进来的线程和刚运行的线程是同一个进程的，那么**线程的上下文切换只需要保存线程的一些运行时的数据，比如线程的id、寄存器中的值、栈数据**。而不需要像进程上下文切换那样要保存页表、文件描述符表、信号控制数据和进程信息等数据。页表是一个很重的资源，我们之前说过，如果采用一级页表的结构，那么32位机器的页表要达到4MB的物理空间。所以线程上下文切换是很轻量级的。

在Linux系统中，线程是使用clone系统调用，clone是一个轻量级的fork。





**线程与进程的区别归纳：**
**a.地址空间和其它资源**：进程间相互独立，同一进程的各线程间共享。某进程内的线程在其它进程不可见。

**b.通信**：进程间通信IPC，线程间可以直接读写进程数据段（如全局变量）来进行通信——需要线程同步和互斥手段的辅助，以保证数据的一致性。

**c.调度和切换**：线程上下文切换比进程上下文切换要快得多。所谓操作系统的任务调度，实际上的调度对象是线程，⽽进程只是给线程提供了虚拟内存、全局变量等资源。

**总结：父与子，上下文切换重与轻，通信间难与易**。

参考：https://blog.csdn.net/pange1991/article/details/84770181





**了解：操作系统父进程为什么要创建子进程呢?**

Linux是一个多用户操作系统,在同一时间会有许多的用户在争夺系统的资源.有时进程为了早一点完成任务就创建子进程来争夺资源. 一旦子进程被创建,父子进程一起从fork处继续执行,相互竞争系统的资源

**fork返回值问题**

1.派生子进程的进程，即父进程，其pid不变； 

2.对子进程来说，fork返回给它0,但它的pid绝对不会是0；之所以fork返回0给它，是因为它随时可以调用getpid()来获取自己的pid； 

3.fork之后父子进程除非采用了同步手段，否则不能确定谁先运行，也不能确定谁先结束。认为子进程结束后父进程才从fork返回的，这是不对的，fork不是这样的，vfork才这样。

参考：https://www.cnblogs.com/yuanshuang/p/5571127.html







### 进程

#### 进程生命周期

创建状态、就绪状态、运行状态、阻塞状态、结束状态。

还可以加一个**描述进程没有占⽤实际的物理内存空间的情况，这个状态就是挂起状态**。

![image-20211030123618643](https://yu-xiong-yu.gitee.io/typora-img/Img/image-20211030123618643.png)

#### 进程的控制结构（PCB）

在操作系统中，是⽤**进程控制块**（*process control block*，*PCB*）数据结构来描述进程的。

**PCB** **是进程存在的唯⼀标识**，这意味着⼀个进程的存在，必然会有⼀个 PCB，如果进程消失了，那么 PCB 也会随之消失。

PCB 具体包含什么信息呢？

进程描述信息、**进程控制和管理信息**



#### 进程的同步和互斥的概念

互斥：只要共享临界资源的各进程对临界区的执行在时间上互斥就可以了，至于各进程之间哪个先后进入临界区的顺序是没有关系的。

同步：就是并发进程在一些关键点上可能需要互相等待和互通消息，这种相互制约的等待与互通消息称为进程同步。（如计算机与打印机配合打印文件的关系）

#### 进程调度

调度算法

1. 最简单的⼀个调度算法，就是⾮抢占式的**先来先服务（First Come First Seved, FCFS）算法**了
2. 最短作业优先调度算法
3. 高响应⽐优先调度算法
4. 时间⽚轮转调度算法
   - **每个进程被分配⼀个时间段，称为时间⽚（****Quantum****），即允许该进程在该时间段中运⾏**
   - 如果时间⽚⽤完，进程还在运⾏，那么将会把此进程从 CPU 释放出来，并把 CPU 分配给另外⼀个进程；
   - 如果该进程在时间⽚结束前阻塞或结束，则 CPU ⽴即进⾏切换；



### 进程通信的方式有哪些？



#### **管道**

- 优点：简单
- 缺点：通信效率低，不适合进程间频繁交换数据

典型应用：Linux中的管道命令。分为匿名管道和命令管道：

**匿名管道**顾名思义，它没有名字标识，匿名管道是特殊⽂件只存在于内存，没有存在于⽂件系统中，shell 命令中的「 | 」竖线就是匿名管道，通信的数据是**⽆格式的流并且⼤⼩受限**，通信的⽅式是**单向**的，数据只能在⼀个⽅向上流动，如果要双向通信，需要创建两个管道，再来**匿名管道是只能⽤于存在⽗⼦关系的进程间通信**，匿名管道的⽣命周期随着进程创建⽽建⽴，随着进程终⽌⽽消失。

**命名管道**突破了匿名管道只能在亲缘关系进程间的通信限制，因为使⽤命名管道的前提，需要在⽂件系统创建⼀个类型为 p 的设备⽂件，那么毫⽆关系的进程就可以通过这个设备⽂件进⾏通信。另外，不管是匿名管道还是命名管道，进程写⼊的数据都是**缓存在内核**中，另⼀个进程读取数据时候⾃然也是从内核中获取，同时通信数据都遵循**先进先出**原则，不⽀持lseek 之类的⽂件定位操作。



#### **消息队列**

是保存在内核中的消息链表；消息队列通信过程中，存在用户态和内核态之间的数据拷贝开销，很好理解，因为内核和用户的进程之间数据读取时需要拷贝数据消息

- 优点：适合进程中较为频繁的数据通信
- 缺点：不适合比较大数据的传输；通信可能不及时



#### **共享内存**

用途：解决用户态和内核态之间的数据拷贝开销问题

原理：拿出一块虚拟地址空间来，映射到相同的物理内存中。



#### **信号量（PV操作）**

用途：**为了防止共享内存通信方式带来的多进程竞争共享资源**，而造成的数据错乱，实现的保护机制

原理：信号量其实是一个整型的计数器，主要用于实现进程间的互斥与同步，而不是用于缓存进程间通信的数据



信号量（Semaphore）是一个整型变量，可以对其执行 down 和 up 操作，也就是常见的 P 和 V 操作。

- **down** : 如果信号量大于 0 ，执行 -1 操作；如果信号量等于 0，进程睡眠，等待信号量大于 0；
- **up** ：对信号量执行 +1 操作，唤醒睡眠的进程让其完成 down 操作。

down 和 up 操作需要被设计成原语，不可分割，通常的做法是在执行这些操作的时候屏蔽中断。P 操作是⽤在进⼊临界区之前，V 操作是⽤在离开临界区之后，这两个操作是必须成对出现的。

如果信号量的取值只能为 0 或者 1，那么就成为了 **互斥量（Mutex）** ，0 表示临界区已经加锁，1 表示临界区解锁。



#### **信号**

用途：上面说的都是常规状态下工作通信模式，而信号适用于**异常情况**下的工作模式通知进程。

举例：后台运行的进程，通过进程PID号，使用`kill -9 PID`命令立即结束进程。

信号是进程间通信机制中唯一的异步通信机制，可以在任何时候发送给某一进程

进程对信号的处理方式有：1、执行默认操作；2、捕捉信号；3、忽略信号



#### **Socket**

用途：跨网络与不同主机上的进程之间通信

根据创建 socket 类型的不同，通信的⽅式也就不同：

- 实现 TCP 字节流通信：
- 实现 UDP 数据报通信
- 实现本地进程间通信：用于**同⼀台主机上进程间通信**



#### **总结**

由于每个进程的⽤户空间都是独⽴的，不能相互访问，这时就需要借助内核空间来实现进程间通信，原因很简单，每个进程都是共享⼀个内核空间。

主要有以下六种通信方式，并各自适用不同场景问题：

**管道，消息队列，共享内存，信号量，信号，socket**

- 共享内存结合信号量使用：本地多进程通信，用来实现进程间共享的、非常庞大的、读写操作频率很高的数据
- **socket**：目前最常用的选择，非常适用于分布式系统中的进程通信

参考：目前linux进程间通信的常用方法是什么(pipe？信号量？消息队列？)? - 二律背反的回答 - 知乎 https://www.zhihu.com/question/23995948/answer/136236554



以上，就是进程间通信的主要机制了。你可能会问了，那线程通信间的⽅式呢？

同个进程下的线程之间都是共享进程的资源，只要是共享变量都可以做到线程间通信，⽐如全局变量，所以对于线程间关注的不是通信⽅式，⽽是关注多线程竞争共享资源的问题，信号量也同样可以在线程间实现互斥与同步：

- 互斥的⽅式，可保证任意时刻只有⼀个线程访问共享资源；
- 同步的⽅式，可保证线程 A 应在线程 B 之前执⾏；



### 死锁

**什么是死锁？**

多个进程可以竞争有限数量的资源。当一个进程申请资源时，如果这时没有可用资源，那么这个进程进入等待状态。有时，如果所申请的资源被其他等待进程占有，那么该等待进程有可能再也无法改变状态。这种情况称为 **死锁**。 

链接: https://javaguide.cn/cs-basics/operating-system/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98&%E7%9F%A5%E8%AF%86%E7%82%B9%E6%80%BB%E7%BB%93/#_2-5-%E8%BF%9B%E7%A8%8B%E7%9A%84%E8%B0%83%E5%BA%A6%E7%AE%97%E6%B3%95



####  必要条件

- 互斥：每个资源要么已经分配给了一个进程，要么就是可用的。
- 占有且等待：已经得到了某个资源的进程可以再请求新的资源。
- 不可抢占：已经分配给一个进程的资源不能强制性地被抢占，它只能被占有它的进程显式地释放。
- 环路等待：有两个或者两个以上的进程组成一条环路，该环路中的每个进程都在等待下一个进程所占有的资源



#### 如何避免死锁

死锁只有同时满⾜互斥、持有并等待、不可剥夺、环路等待这四个条件的时候才会发⽣。

所以要避免死锁问题，就是要破坏其中⼀个条件即可，最常⽤的⽅法就是使⽤资源有序分配法来破坏环路等待条件。就是采用某种调度次序能够使得每一个进程运行完毕，则称该状态是安全的，避免了死锁。



##### 银行家算法

算法要做的是判断对请求的满足是否会进入不安全状态，如果是，就拒绝请求；否则予以分配。

该算法会拒绝会进入不安全状态的请求。



### 内存管理

#### 什么是虚拟内存

虚拟内存是计算机系统内存管理的一种技术。它使得应用程序认为它拥有连续可用的内存（一个连续完整的地址空间），而实际上，它通常是被分隔成多个物理内存碎片，还有部分暂时存储在外部磁盘存储器上，在需要时进行数据交换。

虚拟内存为每个进程提供了一个一致的、私有的地址空间。这样会更加有效地在多进程环境中管理内存并减少出错。

##### 虚拟内存主要有下面两个特点：

第一，由于每个进程都有自己的页表，所以每个进程的虚拟内存空间就是相互独立的。进程也没有办法访问其他进程的页表，所以这些页表是私有的。这就解决了多进程之间地址冲突的问题。

第二，PTE 中除了物理地址之外，还有一些标记属性的比特，比如控制一个页的读写权限，标记该页是否存在等。在内存访问方面，操作系统提供了更好的安全性。

另外，虚拟内存可以充分使用 CPU 提供的机制来完成很多重要的任务。例如，fork 借用写保护来实现写时复制，JVM 中借用改变某一个页的读权限来实现 safepoint 查询等等。这些内容我们都会在以后的课程加以介绍。由于 CPU 对内存提供了更多保护的能力，所以 X86 架构的 CPU 把这种工作模式称为保护模式，与可以直接访问物理内存的实模式形成了对比。

*参考：编程高手必学的内存知识-01（极客时间）*



#### 管理虚拟地址与物理地址之间的关系

​	那既然有了虚拟地址空间，那必然要把虚拟地址「映射」到物理地址，这个事情通常由操作系统来维护。

那么对于虚拟地址与物理地址的映射关系，可以有**分段**和**分⻚**的⽅式，同时两者结合都是可以的。

##### 页表

​	页表是一种特殊的[数据结构](https://baike.baidu.com/item/数据结构/1450)，放在系统空间的页表区，存放逻辑页与物理页帧的对应关系。 每一个[进程](https://baike.baidu.com/item/进程/382503)都拥有一个自己的页表，[PCB](https://baike.baidu.com/item/PCB/16067368)表中有指针指向页表。





#### 页面的换入换出

​	由于程序运行符合局部性原理，CPU 访问内存会有很明显的重复访问的倾向性。那对于那些没有被经常使用到的内存，我们可以把它换出到主存之外，比如硬盘上的 swap 区域。新的虚拟内存页可以被映射到刚腾出来的这个物理页。这就涉及到了页面换入换出的调度问题。

如何能最大化地在空间和时间上都取得平衡，这就要精心地设计页面的调度算法



#### 内存页面置换算法 

每个进程都有⾃⼰的虚拟空间，⽽物理内存只有⼀个，所以当启⽤了⼤量的进程，物理内存必然会很紧张，于是操作系统会通过**内存交换**技术，把不常使⽤的内存暂时存放到硬盘（换出），在需要的时候再装载回物理内存（换⼊）。

- FIFO页面置换算法：先进先出，淘汰最先进入内存的页面。
- LRU（Least Currently Used）页面置换算法：最近最久未使用，淘汰最近最久未使用的页面。
- LFU（Least Frequently Used）页面置换算法：最少使用页面置换算法，淘汰之前使用时间中使用最少的页面。

————————————————
版权声明：本文为CSDN博主「Java鱼仔」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_41973594/article/details/114107497




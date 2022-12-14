# 什么是泛型

Java泛型（ generics） 是JDK 5中引⼊的⼀个新特性， 允许在定义类和接⼜的时候使⽤类型参数（ type parameter） 。

声明的类型参数在使⽤时⽤具体的类型来替换。 泛型最主要的应⽤是在JDK 5中的新集合类框架中。

泛型最⼤的好处是可以提⾼代码的复⽤性。 以List接⼜为例，我们可以将String、 Integer等类型放⼊List中， 如不⽤泛型， 存放String类型要写⼀个List接口， 存放Integer要写另外⼀个List接口， 泛型可以很好的解决这个问题。

# 类型擦除

### 一、各种语言中的编译器是如何处理泛型的

通常情况下，一个编译器处理泛型有两种方式：

1.Code specialization。在实例化一个泛型类或泛型方法时都产生一份新的目标代码（字节码or二进制代码）。例如，针对一个泛型List，可能需要 针对String，Integer，Float产生三份目标代码。

2.Code sharing。对每个泛型类只生成唯一的一份目标代码；该泛型类的所有实例都映射到这份目标代码上，在需要的时候执行类型检查和类型转换。

**C++** 中的模板（template）是典型的Code specialization实现。**C++** 编译器会为每一个泛型类实例生成一份执行代码。执行代码中Integer List和String List是两种不同的类型。这样会导致**代码膨胀（code bloat）**。 **C#** 里面泛型无论在程序源码中、编译后的IL中（Intermediate Language，中间语言，这时候泛型是一个占位符）或是运行期的CLR中都是切实存在的，List<Integer>与List<String>就是两个不同的类型，它们在系统运行期生成，有自己的虚方法表和类型数据，这种实现称为类型膨胀，基于这种方法实现的泛型被称为真实泛型。 **Java**语言中的泛型则不一样，它只在程序源码中存在，在编译后的字节码文件中，就已经被替换为原来的原生类型（Raw Type，也称为裸类型）了，并且在相应的地方插入了强制转型代码，因此对于运行期的Java语言来说，ArrayList<Integer>与ArrayList<String>就是同一个类。所以说泛型技术实际上是Java语言的一颗语法糖，Java语言中的泛型实现方法称为**类型擦除**，基于这种方法实现的泛型被称为伪泛型。

C++和C#是使用Code specialization的处理机制，前面提到，他有一个缺点，那就是**会导致代码膨胀**。另外一个弊端是在引用类型系统中，浪费空间，因为引用类型集合中元素本质上都是一个指针。没必要为每个类型都产生一份执行代码。而这也是Java编译器中采用Code sharing方式处理泛型的主要原因。

Java编译器通过Code sharing方式为每个泛型类型创建唯一的字节码表示，并且将该泛型类型的实例都映射到这个唯一的字节码表示上。将多种泛型类形实例映射到唯一的字节码表示是通过**类型擦除**（type erasue）实现的。

### 二、什么是类型擦除

前面我们多次提到这个词：**类型擦除**（type erasue），那么到底什么是类型擦除呢？

> **类型擦除指的是通过类型参数合并，将泛型类型实例关联到同一份字节码上。编译器只为泛型类型生成一份字节码，并将其实例关联到这份字节码上。类型擦除的关键在于从泛型类型中清除类型参数的相关信息，并且再必要的时候添加类型检查和类型转换的方法。 类型擦除可以简单的理解为将泛型java代码转换为普通java代码，只不过编译器更直接点，将泛型java代码直接转换成普通java字节码。 类型擦除的主要过程如下： 1.将所有的泛型参数用其最左边界（最顶级的父类型）类型替换。（这部分内容可以看：**[Java泛型中extends和super的理解](http://hollischuang.gitee.io/tobetopjavaer/#/archives/255)**） 2.移除所有的类型参数。**

### 三、Java编译器处理泛型的过程

**code 1:**

```java
public static void main(String[] args) {  
    Map<String, String> map = new HashMap<String, String>();  
    map.put("name", "hollis");  
    map.put("age", "22");  
    System.out.println(map.get("name"));  
    System.out.println(map.get("age"));  
}  
```

rorO

**反编译后的code 1:**

```java
public static void main(String[] args) {  
    Map map = new HashMap();  
    map.put("name", "hollis");  
    map.put("age", "22"); 
    System.out.println((String) map.get("name"));  
    System.out.println((String) map.get("age"));  
}  
```

我们发现泛型都不见了，程序又变回了Java泛型出现之前的写法，泛型类型都变回了原生类型，



**code 2:**

```java
interface Comparable<A> {
    public int compareTo(A that);
}

public final class NumericValue implements Comparable<NumericValue> {
    private byte value;

    public NumericValue(byte value) {
        this.value = value;
    }

    public byte getValue() {
        return value;
    }

    public int compareTo(NumericValue that) {
        return this.value - that.value;
    }
}
```

**反编译后的code 2:**

```java
 interface Comparable {
  public int compareTo( Object that);
} 

public final class NumericValue
    implements Comparable
{
    public NumericValue(byte value)
    {
        this.value = value;
    }
    public byte getValue()
    {
        return value;
    }
    public int compareTo(NumericValue that)
    {
        return value - that.value;
    }
    public volatile int compareTo(Object obj)
    {
        return compareTo((NumericValue)obj);
    }
    private byte value;
}
```

**code 3:**

```java
public class Collections {
    public static <A extends Comparable<A>> A max(Collection<A> xs) {
        Iterator<A> xi = xs.iterator();
        A w = xi.next();
        while (xi.hasNext()) {
            A x = xi.next();
            if (w.compareTo(x) < 0)
                w = x;
        }
        return w;
    }
}
```

**反编译后的code 3:**

```java
public class Collections
{
    public Collections()
    {
    }
    public static Comparable max(Collection xs)
    {
        Iterator xi = xs.iterator();
        Comparable w = (Comparable)xi.next();
        while(xi.hasNext())
        {
            Comparable x = (Comparable)xi.next();
            if(w.compareTo(x) < 0)
                w = x;
        }
        return w;
    }
}
```

第2个泛型类Comparable <A>擦除后 A被替换为最左边界Object。Comparable<NumericValue>的类型参数NumericValue被擦除掉，但是这直 接导致NumericValue没有实现接口Comparable的compareTo(Object that)方法，于是编译器充当好人，添加了一个**桥接方法**。 第3个示例中限定了类型参数的边界<A extends Comparable<A>>A，A必须为Comparable<A>的子类，按照类型擦除的过程，先讲所有的类型参数 ti换为最左边界Comparable<A>，然后去掉参数类型A，得到最终的擦除后结果。

------

### 四、泛型带来的问题

**一、当泛型遇到重载：**

```java
public class GenericTypes {  

    public static void method(List<String> list) {  
        System.out.println("invoke method(List<String> list)");  
    }  

    public static void method(List<Integer> list) {  
        System.out.println("invoke method(List<Integer> list)");  
    }  
}  
```

上面这段代码，有两个重载的函数，因为他们的参数类型不同，一个是List<String>另一个是List<Integer> ，但是，这段代码是编译通不过的。因为我们前面讲过，参数List<Integer>和List<String>编译之后都被擦除了，变成了一样的原生类型List，擦除动作导致这两个方法的特征签名变得一模一样。

**二、当泛型遇到catch:**

如果我们自定义了一个泛型异常类GenericException，那么，不要尝试用多个catch取匹配不同的异常类型，例如你想要分别捕获GenericException、GenericException，这也是有问题的。

三、当泛型内包含静态变量

```java
public class StaticTest{
    public static void main(String[] args){
        GT<Integer> gti = new GT<Integer>();
        gti.var=1;
        GT<String> gts = new GT<String>();
        gts.var=2;
        System.out.println(gti.var);
    }
}
class GT<T>{
    public static int var=0;
    public void nothing(T x){}
}
```

答案是——2！由于经过类型擦除，所有的泛型类实例都关联到同一份字节码上，泛型类的所有静态变量是共享的。

### 五、总结

1.虚拟机中没有泛型，只有普通类和普通方法,所有泛型类的类型参数在编译时都会被擦除,泛型类并没有自己独有的Class类对象。比如并不存在List<String>.class或是List<Integer>.class，而只有List.class。 2.创建泛型对象时请指明类型，让编译器尽早的做参数检查（**Effective Java，第23条：请不要在新代码中使用原生态类型**） 3.不要忽略编译器的警告信息，那意味着潜在的ClassCastException等着你。 4.静态变量是被泛型类的所有实例所共享的。对于声明为MyClass<T>的类，访问其中的静态变量的方法仍然是 MyClass.myStaticVar。不管是通过new MyClass<String>还是new MyClass<Integer>创建的对象，都是共享一个静态变量。 5.泛型的类型参数不能用在Java异常处理的catch语句中。因为异常处理是由JVM在运行时刻来进行的。由于类型信息被擦除，JVM是无法区分两个异常类型MyException<String>和MyException<Integer>的。对于JVM来说，它们都是 MyException类型的。也就无法执行与异常对应的catch语句。





# 泛型带来的问题

### 一、当泛型遇到重载

```java
public class GenericTypes {  

    public static void method(List<String> list) {  
        System.out.println("invoke method(List<String> list)");  
    }  

    public static void method(List<Integer> list) {  
        System.out.println("invoke method(List<Integer> list)");  
    }  
}  
```

上面这段代码，有两个重载的函数，因为他们的参数类型不同，一个是List<String>另一个是List<Integer> ，但是，这段代码是编译通不过的。因为我们前面讲过，参数List<Integer>和List<String>编译之后都被擦除了，变成了一样的原生类型List，擦除动作导致这两个方法的特征签名变得一模一样。

### 二、当泛型遇到catch

如果我们自定义了一个泛型异常类GenericException，那么，不要尝试用多个catch取匹配不同的异常类型，例如你想要分别捕获GenericException、GenericException，这也是有问题的。

### 三、当泛型内包含静态变量

```java
public class StaticTest{
    public static void main(String[] args){
        GT<Integer> gti = new GT<Integer>();
        gti.var=1;
        GT<String> gts = new GT<String>();
        gts.var=2;
        System.out.println(gti.var);
    }
}
class GT<T>{
    public static int var=0;
    public void nothing(T x){}
}
```

答案是——2！

由于经过类型擦除，所有的泛型类实例都关联到同一份字节码上，泛型类的所有静态变量是共享的。



# 泛型中K T V E ？ object等的含义

E - Element (在集合中使用，因为集合中存放的是元素)

T - Type（Java 类）

K - Key（键）

V - Value（值）

N - Number（数值类型）

？ - 表示不确定的java类型（无限制通配符类型）

S、U、V - 2nd、3rd、4th types

Object - 是所有类的根类，任何类的对象都可以设置给该Object引用变量，使用的时候可能需要类型强制转换，但是用使用了泛型T、E等这些标识符后，在实际用之前类型就已经确定了，不需要再进行类型强制转换。



# 限定通配符和非限定通配符

限定通配符对类型进⾏限制， 泛型中有两种限定通配符：

表示类型的上界，格式为：<？ extends T>，即类型必须为T类型或者T子类 表示类型的下界，格式为：<？ super T>，即类型必须为T类型或者T的父类

泛型类型必须⽤限定内的类型来进⾏初始化，否则会导致编译错误。

⾮限定通配符表⽰可以⽤任意泛型类型来替代，类型为<T>



# 上下界限定符extends 和 super

<? extends T>和<? super T>是Java泛型中的“通配符（Wildcards）”和“边界（Bounds）”的概念。

<? extends T>：是指 “上界通配符（Upper Bounds Wildcards）”，即泛型中的类必须为当前类的子类或当前类。

<? super T>：是指 “下界通配符（Lower Bounds Wildcards）”，即泛型中的类必须为当前类或者其父类。

先看一个列子：

```java
public class Food {}
public class Fruit extends Food {}
public class Apple extends Fruit {}
public class Banana extends Fruit{}

public class GenericTest {

    public void testExtends(List<? extends Fruit> list){

        //报错,extends为上界通配符,只能取值,不能放.
        //因为Fruit的子类不只有Apple还有Banana,这里不能确定具体的泛型到底是Apple还是Banana，所以放入任何一种类型都会报错
        //list.add(new Apple());

        //可以正常获取
        Fruit fruit = list.get(1);
    }

    public void testSuper(List<? super Fruit> list){

        //super为下界通配符，可以存放元素，但是也只能存放当前类或者子类的实例，以当前的例子来讲，
        //无法确定Fruit的父类是否只有Food一个(Object是超级父类)
        //因此放入Food的实例编译不通过
        list.add(new Apple());
//        list.add(new Food());

        Object object = list.get(1);
    }
}
```

在testExtends方法中，因为泛型中用的是extends，在向list中存放元素的时候，我们并不能确定List中的元素的具体类型，即可能是Apple也可能是Banana。因此调用add方法时，不论传入new Apple()还是new Banana()，都会出现编译错误。

理解了extends之后，再看super就很容易理解了，即我们不能确定testSuper方法的参数中的泛型是Fruit的哪个父类，因此在调用get方法时只能返回Object类型。结合extends可见，在获取泛型元素时，使用extends获取到的是泛型中的上边界的类型(本例子中为Fruit),范围更小。

在使用泛型时，存取元素时用super,获取元素时，用extends。

频繁往外读取内容的，适合用上界Extends。经常往里插入的，适合用下界Super。

本文来源：https://juejin.im/post/5c653fe06fb9a049e3089d88



# List和原始类型List之间的区别?

原始类型List和带参数类型List<Object>之间的主要区别是，在编译时编译器不会对原始类型进行类型安全检查，却会对带参数的类型进行检查。

通过使用Object作为类型，可以告知编译器该方法可以接受任何类型的对象，比如String或Integer。

它们之间的第二点区别是，你可以把任何带参数的类型传递给原始类型List，但却不能把List<String>传递给接受 List<Object>的方法，因为会产生编译错误。



# List和List之间的区别是什么?

List<?> 是一个未知类型的List，而List<Object> 其实是任意类型的List。你可以把List<String>, List<Integer>赋值给List<?>，却不能把List<String>赋值给 List<Object>。
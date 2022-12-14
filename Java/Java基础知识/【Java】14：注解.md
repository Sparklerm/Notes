# 元注解

说简单点，就是 定义其他注解的注解 。 比如Override这个注解，就不是一个元注解。而是通过元注解定义出来的。

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.SOURCE)
public @interface Override {
}
```

这里面的 @Target @Retention 就是元注解。

元注解有六个:@Target（表示该注解可以用于什么地方）、@Retention（表示再什么级别保存该注解信息）、@Documented（将此注解包含再javadoc中）、@Inherited（允许子类继承父类中的注解）、@Repeatable（1.8新增，允许一个注解在一个元素上使用多次）、@Native（1.8新增，修饰成员变量，表示这个变量可以被本地代码引用，常常被代码生成工具使用）。

# 自定义注解

除了元注解，都是自定义注解。通过元注解定义出来的注解。 如我们常用的Override 、Autowire等。 日常开发中也可以自定义一个注解，这些都是自定义注解。

# Java中常用注解使用

@Override 表示当前方法覆盖了父类的方法

@Deprecated 表示方法已经过时,方法上有横线，使用时会有警告。

@SuppressWarnings 表示关闭一些警告信息(通知java编译器忽略特定的编译警告)

@SafeVarargs (jdk1.7更新) 表示：专门为抑制“堆污染”警告提供的。

@FunctionalInterface (jdk1.8更新) 表示：用来指定某个接口必须是函数式接口，否则就会编译出错。

### Spring常用注解

@Configuration把一个类作为一个IoC容器，它的某个方法头上如果注册了@Bean，就会作为这个Spring容器中的Bean。

@Scope注解 作用域

@Lazy(true) 表示延迟初始化

@Service用于标注业务层组件

@Controller用于标注控制层组件@Repository用于标注数据访问组件，即DAO组件。

@Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。

@Scope用于指定scope作用域的（用在类上）

@PostConstruct用于指定初始化方法（用在方法上）

@PreDestory用于指定销毁方法（用在方法上）

@DependsOn：定义Bean初始化及销毁时的顺序

@Primary：自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否则将抛出异常

@Autowired 默认按类型装配，如果我们想使用按名称装配，可以结合@Qualifier注解一起使用。如下： @Autowired @Qualifier("personDaoBean") 存在多个实例配合使用

@Resource默认按名称装配，当找不到与名称匹配的bean才会按类型装配。

@PostConstruct 初始化注解

@PreDestroy 摧毁注解 默认 单例 启动就加载





# 注解与反射的结合

注解和反射经常结合在一起使用，在很多框架的代码中都能看到他们结合使用的影子

可以通过反射来判断类，方法，字段上是否有某个注解以及获取注解中的值, 获取某个类中方法上的注解代码示例如下：

```java
Class<?> clz = bean.getClass();
Method[] methods = clz.getMethods();
for (Method method : methods) {
    if (method.isAnnotationPresent(EnableAuth.class)) {
        String name = method.getAnnotation(EnableAuth.class).name();
    }
}
```

通过isAnnotationPresent判断是否存在某个注解，通过getAnnotation获取注解对象，然后获取值。

### 示例

示例参考：https://blog.csdn.net/KKALL1314/article/details/96481557

自己写了一个例子，实现功能如下：

一个类的某些字段上被注解标识，在读取该属性时，将注解中的默认值赋给这些属性，没有标记的属性不赋值

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@Documented
@Inherited
public @interface MyAnno {
    String value() default "有注解";
}
```

定义一个类

```java
@Data
@ToString
public class Person {
    @MyAnno
    private String stra;
    private String strb;
    private String strc;

    public Person(String str1,String str2,String str3){
        super();
        this.stra = str1;
        this.strb = str2;
        this.strc = str3;
    }

}
```

这里给str1加了注解，并利用反射解析并赋值：

```java
public class MyTest {
    public static void main(String[] args) {
        //初始化全都赋值无注解
        Person person = new Person("无注解","无注解","无注解");
        //解析注解
        doAnnoTest(person);
        System.out.println(person.toString());
    }

  private static void doAnnoTest(Object obj) {
        Class clazz = obj.getClass();
        Field[] declareFields = clazz.getDeclaredFields();
        for (Field field:declareFields) {
            //检查该字段是否使用了某个注解
            if(field.isAnnotationPresent(MyAnno.class)){
                MyAnno anno = field.getAnnotation(MyAnno.class);
                if(anno!=null){
                    String fieldName = field.getName();
                    try {
                        Method setMethod = clazz.getDeclaredMethod("set" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1),String.class);
                        //获取注解的属性
                        String annoValue = anno.value();
                        //将注解的属性值赋给对应的属性
                        setMethod.invoke(obj,annoValue);
                    }catch (NoSuchMethodException e){
                        e.printStackTrace();
                    } catch (IllegalAccessException e) {
                        e.printStackTrace();
                    } catch (InvocationTargetException e) {
                        e.printStackTrace();
                    }

                }
            }
            
        }
    }

}
```

运行结果：

```java
Person(stra=有注解, strb=无注解, strc=无注解)

``

当开发者使用了Annotation 修饰了类、方法、Field 等成员之后，这些 Annotation 不会自己生效，必须由开发者提供相应的代码来提取并处理 Annotation 信息。这些处理提取和处理 Annotation 的代码统称为 APT（Annotation Processing Tool)。

注解的提取需要借助于 Java 的反射技术，反射比较慢，所以注解使用时也需要谨慎计较时间成本。
```



# 如何自定义一个注解？

在Java中，类使用class定义，接口使用interface定义，注解和接口的定义差不多，增加了一个@符号，即@interface，代码如下：

```java
public @interface EnableAuth {

}
```

注解中可以定义成员变量，用于信息的描述，跟接口中方法的定义类似，代码如下：

```java
public @interface EnableAuth {
    String name();
}
```

还可以添加默认值：

```java
public @interface EnableAuth {
    String name() default "猿天地";
}
```

上面的介绍只是完成了自定义注解的第一步，开发中日常使用注解大部分是用在类上，方法上，字段上，示列代码如下：

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface EnableAuth {

}
```

Target

用于指定被修饰的注解修饰哪些程序单元，也就是上面说的类，方法，字段

Retention

用于指定被修饰的注解被保留多长时间，分别SOURCE（注解仅存在于源码中，在class字节码文件中不包含）,CLASS（默认的保留策略，注解会在class字节码文件中存在，但运行时无法获取）,RUNTIME（注解会在class字节码文件中存在，在运行时可以通过反射获取到）三种类型，如果想要在程序运行过程中通过反射来获取注解的信息需要将Retention设置为RUNTIME

Documented

用于指定被修饰的注解类将被javadoc工具提取成文档

Inherited

用于指定被修饰的注解类将具有继承性



# Spring常用注解

@Configuration把一个类作为一个IoC容器，它的某个方法头上如果注册了@Bean，就会作为这个Spring容器中的Bean。

@Scope注解 作用域

@Lazy(true) 表示延迟初始化

@Service用于标注业务层组件、

@Controller用于标注控制层组件@Repository用于标注数据访问组件，即DAO组件。

@Component泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。

@Scope用于指定scope作用域的（用在类上）

@PostConstruct用于指定初始化方法（用在方法上）

@PreDestory用于指定销毁方法（用在方法上）

@DependsOn：定义Bean初始化及销毁时的顺序

@Primary：自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否则将抛出异常

@Autowired 默认按类型装配，如果我们想使用按名称装配，可以结合@Qualifier注解一起使用。如下：

@Autowired @Qualifier("personDaoBean") 存在多个实例配合使用

@Resource默认按名称装配，当找不到与名称匹配的bean才会按类型装配。

### Spring中的这几个注解有什么区别：@Component 、@Repository、@Service、@Controller

1. @Component指的是组件，

@Controller，@Repository和@Service 注解都被@Component修饰，用于代码中区分表现层，持久层和业务层的组件，代码中组件不好归类时可以使用@Component来标注

1. 当前版本只有区分的作用，未来版本可能会添加更丰富的功能
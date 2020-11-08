# Java 学习笔记

冲鸭！

## 1 Java数据结构

### 1.1 基本数据类型

#### 1.1.1 8种基本数据类型

4种整数类型，2种浮点数类型，1种字符类型，1种布尔类型。

整型：

- byte

  8位有符号数，以二进制补码形式表示

  最小值` -128 `，最大值` 127 `，默认值` 0 `。

- short

  短整型，16位有符号数，以二进制补码形式表示

  最小值` -2^15 `，最大值` 2^15 - 1`，默认值` 0 `。

- int

  整型，32位有符号数，以二进制补码形式表示

  最小值` -2^31 `，最大值` 2^31 - 1 `，默认值` 0 `。
  
- long

  长整型，64位有符号数，以二进制补码形式表示

  最小值` -2^63 `，最大值` 2^63 - 1 `， 默认值` 0 `。

浮点型：

- float

  单精度浮点数，32位，默认值` 0.0f `。

- double

  双精度浮点数，64位，默认值` 0.0d `。

字符型

- char

  16位unicode字符，可存储任意字符，最小值` \u0000 `（0），最大值 ` \uffff `（65535），默认值` \u0000 `。

布尔型

- boolean

  两种取值，` true `和` false `，默认值` false `。

#### 1.1.2 基本数据类型的包装类

每种基本数据类型都有其对应的包装类

  

  

### 1.1 HashSet

HashSet基于HashMap实现，是一个**不允许重复元素**的集合。

允许有` null `值；

无序集合，不会记录元素插入的顺序；

线程不安全；



## 2 Java 函数式编程

### 1.1 Lambda表达式

Java Lambda是jdk 1.8引入的新特性，允许把函数作为一个方法的参数。其语法形式如下：

```java
(parameters) -> expression
```

或

```
(parameters) -> { statements; }
```

其中：

- 参数类型不需要特别声明，可由编译器识别；
- 当参数只有一个时，无需定义圆括号，多个参数时需使用圆括号；
- 如果主体只包含了一个语句，不需要使用大括号；
- 如果使用了大括号（多个语句），需要指明返回表达式。

#### 1.1.1 Lambda表达式替换匿名内部类

对于单方法接口，可使用Lambda表达式替换匿名内部类的实现方式。

示例：

```java
public static void main(String[] args) {
    MathOperation addition = Integer::sum;
    System.out.println(addition.operation(10,5));

    MathOperation subtraction = (a, b) -> a - b;
    System.out.println(subtraction.operation(10,5));

    MathOperation multiplication = (a, b) -> a * b;
    System.out.println(multiplication.operation(10,5));

    MathOperation division = (a, b) -> a / b;
    System.out.println(division.operation(10,5));

    GreetingService greetingService = (System.out::println);
    greetingService.sayMessage("Hello!");
}

interface MathOperation {
    int operation(int a, int b);
}

interface GreetingService {
    void sayMessage(String message);
}
```

本质上，Lambda表达式完成的工作就是取代了原本的匿名内部类，如上述代码中的：

```java
MathOperation subtraction = (a, b) -> a - b;
```

等效于：

```java
MathOperation subtraction = new MathOperation() {
    @Override
    public int operation(int a, int b) {
        return a - b;
    }
};
```

类似的，例如在开启线程时，可用Lambda表达式替换Runnable匿名内部类：

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("xxx");
    }
}).start();
```

可替换为：

```java
new Thread(() -> System.out.println("xxx")).start();
```

再例如，调用Arrays.sort()时，需要传入Comparator实例：

```java
String[] array = {"a", "b", "c", "d"};
Arrays.sort(array, new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});
```

可替换为：
```java
String[] array = {"a", "b", "c", "d"};
Arrays.sort(array, (s1, s2) -> s1.compareTo(s2));
```

或使用方法引用：

```java
String[] array = {"a", "b", "c", "d"};
Arrays.sort(array, String::compareTo);
```

#### 1.1.2 Lambda表达式对List进行迭代

示例：

```java
List<String> features = Arrays.asList("a", "b", "c", "d");
for (String feature: features) {
    System.out.println(feature);
}
```

可替换为：

```java
List<String> features = Arrays.asList("a", "b", "c", "d");
features.forEach(System.out::println);
```

### 1.2 方法引用 ::



## 3 JVM



## 2 Java 注解 (Annotation)

### 2.1 基本概念

注解是一种用于标注的“元数据”，可放在Java源码的类、方法、字段、参数前。Java中所有的注解本质上均是继承自Annotation接口。

注解可分为三类：

1. 由编译器使用的注解，如

   `@Override`：让编译器检查该方法是否正确实现了重写；

   `@SuppressWarnings`：使编译器忽略此处代码产生的警告；

   编译完成后被会编译器丢弃，不会存在于`.class`文件中。

2. 由工具处理`.class`文件时使用的注解。这类注解会被编译进入`.class`文件，但会被VM丢弃。一般被部分底层库使用。

3. 在程序运行期间能够读取的注解，在加载后一直存在于JVM中。因此可通过反射机制读取注解的信息。如配置了`@PostConstruct`的方法会在构造方法被调用后自动被调用。

定义注解时可以定义配置参数，配置参数包括：

- 所有基本类型
- String
- Class
- enum
- Annotation（即注解可以嵌套）
- 以上类型的数组

注解的配置参数可以有默认值，缺少某个配置参数时将使用默认值。如果只写注解，则相当于全部使用默认值。

注解的配置参数不能有不确定的值，也就是，参数要么具备并使用默认值，要么在使用注解时提供参数的值。此外，对于非基本类型的参数，无论是在源代码中声明，还是在注解接口中定义，都不能以`null`作为值！

### 2.2 定义注解

使用`@interface`语法来定义注解，基本格式如：

```java
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
}
```

使用`default`为注解的参数设定默认值，同时将最常使用的参数命名为`value`。

#### **元注解**

元注解是用于修饰其他注解的注解，Java提供的元注解定义在java.lang.annotation包下面。在jdk 1.8之前共有4种元注解：

1. **@Target**

   使用`@Target`元注解可定义该注解能被应用源码的哪些位置，具体常用的参数值有：

    	`ElementType.TYPE`：作用于类、接口和枚举；

    	`ElementType.FIELD`：作用于字段；

   ​	 `ElementType.METHOD`：作用于方法；

   ​	 `ElementType.CONSTRUCTOR`：作用于构造方法；

   ​	 `ElementType.PARAMETER`：作用于方法参数；

   此外，`ElementType`类中还定义了其他几种参数值：

   ​	 `ElementType.LOCAL_VARIABLE`：作用于局部变量；

   ​	 `ElementType.ANNOTATION_TYPE`：作用于注解；

   ​	 `ElementType.PACKAGE`：作用于包；

   例如，要使注解`@Report`可作用于方法上，需要添加`@Target(ElementType.METHOD)`：

   ```java
   @Target(ElementType.METHOD)
   public @interface Report {
    int type() default 0;
       String level() default "info";
       String value() default "";
   }
   ```

   如果要使其可用在方法和字段上，则可将`@Target`注解的参数变为数组：

   ```java
   @Target({
       ElementType.METHOD,
       ElementType.FIELD
   })
   public @interface Report {
    int type() default 0;
       String level() default "info";
       String value() default "";
   }
   ```

   实际上`@Target`定义的`value`是一个`ElementType[]`数组，当只有一个元素时，可缺省数组写法。

   当注解未指定`@Target`的值时，该注解可用于上述任意类型。

2. **@Retention**

   使用`@Retention`元注解可定义注解的生命周期，具体参数值有：

   `RetentionPolicy.SOURCE`：仅编译期；

   `RetentionPolicy.CLASS`：仅Class文件；

   `RetentionPolicy.RUNTIME`：运行时；

   若没有使用`@Retention`注解，则默认注解的生命周期为`CLASS`。通常自定义的注解需要在`RUNTIME`生效，因此务必添加`@Retention`。

   例如：

   ```java
   @Retention(RetentionPolicy.RUNTIME)
   public @interface Report {
       int type() default 0;
       String level() default "info";
       String value() default "";
   }
   ```

3. **@Document**

   使用`@Document`元注解可定义该注解是否被包含到`javadoc`中。

4. **@inherited**

   使用`@inherited`元注解可定义该注解能否被子类从父类处继承。注意`@Inherited`元注解仅针对`@Target(ElementType.TYPE)`类型的注解有效，且只针对`class`的继承，对`interface`的继承无效。

   例如：

   ```java
@Inherited
   @Target(ElementType.TYPE)
   public @interface Report {
       int type() default 0;
       String level() default "info";
       String value() default "";
   }
   ```
   
   类Parent使用了该注解：
   
   ```java
   @Report(type=1)
   public class Parent {...}
   ```
   
   则Parent的所有子类也定义了该注解：
   
   ```java
   public class Child extends Parent {...}
   ```

#### jdk 1.8注解增强

jdk 1.8中对注解的改进主要有两方面：类型注解和重复注解。

1. 类型注解

   类型注解是一种可以放在任何使用类型的位置上的注解。例如：

   new 语句：

   ```java
   new@Interned MyObject();
   ```

   类型转换：

   ```java
   myString = (@NonNull String) str;
   ```

   implements 语句：

   ```java
   class UnmodifiableList<T> implements@Readonly List<@ReadOnly T> {...}
   ```

   throw exception 语句：

   ```java
   void myMethod() throws@Critical MyException {...}
   ```

   注意！java 8提供的类型注解并没有实际作用，需要由框架和工具开发者实际上使用它们，否则它们只起到注释的作用。

   在代码实现上，java 8为ElementType类新增了两条字段：

   `ElementType.TYPE_PARAMETER` ：允许注解被用在类型变量（泛型）的声明中；

   `ElementType.TYPE_USE`：允许注解可以被用在任何类型使用的地方（如：声明语句、泛型、强制转换语句）。

2. 重复注解

   java 8新增了两种元注解：

   `@native`：只能作用于field，表明该字段为固定值，从native代码中引用；

   `@Repeatable`：表明该注解可在同一位置被重复使用。

   例如，在java 8之前，要重复使用上文中的`@Report`注解，需要定义一个容器注解`@Reports`：

   ```java
   @Target(ElementType.TYPE)
   public @interface Reports {
       Report[] value();
   }
   
   @Target(ElementType.TYPE)
   @Retention(RetentionPolicy.RUNTIME)
   public @interface Report {
       int type() default 0;
       String level() default "info";
       String value() default "";
   }
   ```

   在使用处：

   ```java
   @Reports({@Report(type="1", level="debug"), @Report(type=2, level="warning")})
   class MyObject {...}
   ```

   在java 8后，通过使用`@Repeatable`元注解，可改为以下形式：

   ```java
   @Repeatable(Reports.class)
   @Target(ElementType.TYPE)
   @Retention(RetentionPolicy.RUNTIME)
   public @interface Report {
       int type() default 0;
       String level() default "info";
       String value() default "";
   }
   
   @Target(ElementType.TYPE)
   @Retention(RetentionPolicy.RUNTIME)
   public @interface Reports {
       public Report[] value();
   }
   ```

   而在使用时：

   ```java
   @Report(type=1, level="debug")
   @Report(type=2, level="warning")
   class MyObject {...}
   ```

   注意，仍然需要定义容器注解`@Reports`，不同的是，创建重复注解`@Report`时，加上`@Repeatable`，指向存储注解`@Reports`，在使用时直接可以重复使用`Report`注解，增强了可读性。

#### 注解的定义过程总结

1. 使用` @interface `定义注解
2. 添加参数及默认值，最常用的参数定义为` value() `，尽可能为所有参数设置默认值
3. 

### 2.3 处理注解
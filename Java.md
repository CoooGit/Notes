# Java 笔记

## 1 基础知识

### 1.1 Java面向对象的三大特性

封装、继承、多态。

#### 封装

将对象的状态和行为看作统一的整体，存放在一个独立的模块中，如“类”的概念。同时实现细节隐藏，把不想对外公开的实现细节隐藏起来，保证调用者安全访问，避免因越界出现问题。

Java提供的访问权限修饰符有四种，控制程度从严格到宽松分别是：

- private：类访问权限。表示类私有，只能在当前类中访问；

- 缺省：不加修饰符，属于包访问权限。表示包私有，调用者的包必须和当前类的包相同才能访问；

- protected：子类访问权限。使用protected修饰的类、方法、字段，同包中的类可以访问，不同包但存在继承关系（子类），也可以访问；

- public：全局的公共访问权限。当前项目中的任何地方都可以访问。接口中的抽象方法默认都是public的。

#### 继承

Java中使用关键字extends来表示子类和父类的继承关系，子类可以通过继承获得父类原有的字段和方法，也可以重写（Override）其中允许被子类覆盖的部分。同时子类也可以新增父类所没有的字段和方法。

Java中类之间的继承关系只允许单继承，一个类只能有一个直接父类，但多重继承是允许的。

子类进行方法重写的原则：

- 方法签名必须相同，包括方法名、参数列表、参数类型、参数个数、参数顺序，都必须一致；

- 子类方法的返回值类型必须和父类方法的返回类型相同或者是其子类；

- 子类方法声明抛出的异常类型和父类方法声明抛出的异常类型相同或者是其子类，子类方法可同时声明抛出多个属于父类方法声明抛出的异常类的子类的异常（RuntimeException类型除外）；

- 子类方法的访问权限与父类方法相同或更大；

进行重写的方法需要使用@Override注解修饰，若不使用该注解，则当重写的方法满足重写条件时，JVM可以自动进行识别，但如果重写的方法不符合规范，则会被当做单独的方法使用。因此最好加上该注解。

this和super：this指当前对象，super指当前对象的父类对象。在创建子类对象之前会先创建父类对象，也就是说在调用子类构造器之前，需要先调用父类的构造器，如果在子类的构造方法中显示调用父类的构造方法，那么JVM默认会调用父类的无参构造方法。因此，父类必须存在可以被子类访问的构造器，且如果父类没有提供无参构造器，那么子类就必须显式地通过super语句调用父类的带参构造器。

Java中的Object是基类，其他所有的类都是Object的直接或间接子类。Object类中的常用方法有：

- finalize()：对象被回收时，JVM会检查该方法是否被重写；

- getClass()：返回当前对象的真实类型；

- hashCode()：返回当前对象的哈希码值，表示对象在哈希表中的存储位置；

- equals()：用于当前对象和传入的对象参数的比较，Object类中的equals方法比较的是内存地址。建议有比较需求的类重写该方法，不要比较内存地址，而是比较内容数据。

- toString()：把一个对象转换为字符串，Object类中的该方法返回的是对象的十六进制的hashCode值，有打印需求的类应当重写该方法，返回真正有意义的数据。

#### 多态

Java的多态可以理解为，同一个事件发生在不同的对象上有不同的效果（同一个行为具有不同表现形式或形态的能力）。多态的三个必要条件是：

- 继承
- 重写
- 父类引用指向子类的对象/接口引用指向实现类的对象

程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，而是在程序运行期间才确定，即一个引用变量到底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定。

### 1.2 接口和抽象类的区别





## 2 JVM

### JVM模型

1. 堆 Heap：存储所有的对象实例和数组。堆是线程共有的，JVM的垃圾回收主要发生在该区域；

   从结构上，堆被划分为新生代和老年代；而新生代又分为Eden区、To Survivor区、From Survivor区，大小比例为8:1:1。

   当堆中没有内存可供完成实例分配，且堆也无法再扩展时，将会抛出OutOfMemoryError异常。

2. 方法区 Method Area：存储类的静态信息、常量、静态变量、即时编译器（JIT）编译产生的代码；方法区同样是线程共有的。

   由于HotSpot虚拟机将GC算法拓展到了该区域，因此方法区有时也被称为永久代，Java8之后已不存在永久代的概念，取而代之的是元空间metaspace。

   当方法区无法满足内存分配需求时，将抛出OutOfMemoryError异常。

3. 虚拟机栈 JVM Stack：存储局部变量、对象指针、操作栈、动态链接、方法出口；虚拟机栈是线程私有的；

   每个线程会有一个私有的栈。每个线程中方法的调用又会在本栈中创建一个栈帧。在方法栈中会存放编译期可知的各种基本数据类型和对象引用。局部变量表所需的内存空间在编译期间完成分配，当进入一个方法时，这个方法需要在帧中分配多大的局部变量空间是完全确定的，在方法运行期间不会改变局部变量表的大小。

   JVM为这个区域规定了两种异常状况：

   - StackOverflowError：线程请求的栈深度大于虚拟机所允许的深度时抛出；
   - OutOfMemoryError：虚拟机栈无法申请到足够的内存时抛出。

4. 本地方法栈 Native Method Stack：为虚拟机使用到的Native方法服务；本地方法栈是线程私有的；

   功能上本地方法栈与虚拟机栈是类似的，同样，在该区域JVM规定了StackOverflowError和OutOfMemoryError异常。

5. 程序计数器 Program Counter Register：当前线程所执行的字节码的行号指示器，指向下一条要执行的命令；线程私有。

   该区域没有规定任何异常。

### JVM版本的更新

jdk1.7版本中，将字符串常量池从方法区移动到了堆中，避免方法区内存有限从而出现OOM错误。而jdk1.8版本则将方法区从运行时内存移动到了本地内存中，方法区不再与堆相连。

**为什么jdk1.8之后彻底移除了永久代的概念？**

由于HotSpot虚拟机将GC的范围拓展到了方法区，因此，相对于新生代、老年代，方法区也被称为永久代（PermGen）。从逻辑上来说，方法区和堆区是分开的，但从物理层面来说，方法区和堆区使用的是一块儿连续的内存。因此可以这样说，方法区跟堆区本质上是一样的，只是适用了JVM中不同的规范。

在Java8版本后，方法区不在与堆区连续，而是存在于元空间（Metaspace）。元空间存在于本地内存中。本地内存不会触发GC，通常情况下存在于本地内存中的方法区是不会出现空间不够的问题的。默认情况下元空间可以无限使用本地内存，同时JVM也提供了参数来限制它的使用。

因此Java8使用元空间替换永久代的概率主要是由于以下两个原因：

- 避免OOM异常。虽然在jdk1.8之前可使用PermSize和MaxPermSize设置永久代的大小和上限，但不是总能知道合适的设置值，而使用默认值很容易遇到OOM错误。而使用元空间时，可以加载多少类的元数据就不再由MaxPermSize控制, 而由系统的实际可用空间来控制。
- 另一个原因是要合并HotSpot和JRockit的代码。JRockit没有所谓的永久代概念。

### Java类加载

#### 什么是类加载

类加载的概念：JVM将类的`.class `文件中的二进制数据读入到内存中，将其放在方法区内，然后在堆区创建一个 `java.lang.Class`对象。 `Class`对象封装了类在方法区内的数据结构，并提供访问方法区内的数据结构的接口。

#### 类加载的时机

类加载的时机：JVM规范允许类加载器在预料某个类将要被使用时就预先加载它，不需要等到某个类被“首次主动使用”时再加载。如果在预先加载的过程中遇到了` .class `文件缺失或存在错误，类加载器必须在程序首次主动使用该类时才报告错误（LinkageError错误）如果这个类一直没有被程序主动使用，那么类加载器就不会报告错误。

#### 类加载的过程

类加载包括3个阶段：加载（Load）、链接（Link）、初始化（Init），其中链接又分为3个具体步骤。

1）加载

查找并加载类的二进制数据。

- 通过一个类的全限定名来获取其定义的二进制字节流。
- 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构。
- 在Java堆中生成一个代表这个类的 `java.lang.Class`对象，作为对方法区中这些数据的访问入口。

可以使用系统提供的类加载器或自定义自己的类加载器来完成加载。

2）验证

确保被加载的类的正确性，确保` .Class `文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。

- 文件格式验证：验证字节流是否符合Class文件格式的规范；
- 元数据验证：对字节码描述的信息进行语义分析以保证其描述的信息符合Java语言规范的要求；
- 字节码验证：通过数据流和控制流分析，确定程序语义是合法的、符合逻辑的；
- 符号引用验证：确保解析动作能正确执行。

验证阶段非常重要但不是必须的，它对程序运行期没有影响。如果所引用的类经过反复验证，可以考虑采用 `-Xverifynone`参数来关闭大部分的类验证措施，以缩短虚拟机类加载的时间。

3）准备

为类的静态变量分配内存，并将其初始化为默认值。这些内存都在方法区中分配。

- 该阶段进行内存分配的仅包括static变量，不包括实例变量，实例变量会在对象实例化时随着对象分配在Java堆中；
- 该阶段设置的初始值通常情况下是数据类型默认的零值，而不是在Java代码中被显式地赋予的值；
- 如果存在 `ConstantValue`属性（同时被final和static修饰），那么在该阶段变量就会被初始化为指定的值。

4）解析

把类中的符号引用转换为直接引用。

符号引用就是一组符号来描述目标，可以是任何字面量。

直接引用就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄。

5）初始化

为类的静态变量赋予正确的初始值。步骤如下：

- 假如该类还没有被加载和连接，则程序先加载并连接该类；
- 假如该类的直接父类还没有被初始化，则先初始化其直接父类；
- 假如类中有初始化语句，则系统依次执行这些初始化语句。

#### 类初始化的时机

类初始化时机：只有当对类进行主动使用的时候才会导致类的初始化。类的主动使用包括以下六种：

- 创建类的实例（new）；
- 访问某个类或接口的静态变量，或者对该静态变量赋值；
- 调用类的静态方法；
- 反射；
- 初始化某个类的子类，则其父类也会被初始化；
- Java虚拟机启动时被标明为启动类的类（ `JavaTest`），直接使用 `java.exe`命令来运行某个主类。

#### Java中的类加载器

类加载器包括：

- Bootstrap ClassLoader 启动类加载器
- ExtClassLoader 扩展类加载器
- AppClassLoader 应用类加载器
- User ClassLoader 自定义类加载器

从JVM的角度而言，类加载器只分为两种：启动类加载器和其他类加载器。启动类加载器由C++实现，是JVM的一部分（Hotspot虚拟机），其他所有类加载器都由Java实现，独立于虚拟机之外，继承自抽象类` java.lang.ClassLoader `，需要由启动类加载器加载到内存后才能去加载其他类。

具体地：

启动类加载器负责加载存放在 `\jre\lib`下的类库（如所有的java.开头的类）。启动类加载器无法被Java程序直接引用。

扩展类加载器负责加载 `\jre\lib\ext`目录中的所有类库（如javax.开头的类）。开发者可以直接使用扩展类加载器。

应用程序类加载器负责加载用户类路径（ClassPath）所指定的类，开发者可以直接使用该类加载器。如果应用程序中没有自定义类加载器，一般情况下这就是程序中默认的类加载器。

#### JVM类加载机制

- **全盘负责**，当一个类加载器负责加载某个Class时，该Class所依赖的和引用的其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入；
- **父类委托**，先让父类加载器试图加载该类，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类；
- **缓存机制**，缓存机制保证所有加载过的Class都会被缓存，当程序中需要使用某个Class时，类加载器先从缓存区寻找该Class，只有缓存区不存在，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓存区。当修改了Class后，必须重启JVM，程序的修改才会生效。

#### 类加载的方式

- 启动应用时候由JVM初始化加载
- 通过Class.forName()方法动态加载
- 通过ClassLoader.loadClass()方法动态加载

Class.forName()和ClassLoader.loadClass()的区别？

- `Class.forName()`除了将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块；
- `ClassLoader.loadClass()`只将.class文件加载到jvm中，不会执行static中的内容，只有在newInstance()创建实例时才会去执行static块。
- `Class.forName(name,initialize,loader)`通过传入参数也可控制是否加载static块。

#### 双亲委派机制？

如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载时，子加载器才会尝试自己去加载该类。

双亲委派机制意义：

- 防止内存中出现多份同样的字节码；
- 保证Java程序安全稳定运行。如，即使重写` java.lang.System `类，在类加载的过程中，启动类加载器还是会加载Java提供的System类，避免System类遭到恶意修改。

#### Java反射

通常情况下，Java中使用某个类时，需要先知道它是什么类，对外暴露了哪些方法，然后创建类的实例，之后通过类的对象进行操作。所谓反射就是并不知道要初始化的类是什么，而是通过类的全限定名，调用class.forName()方法获取类，然后再通过类对象的getMethod()方法获取要调用的方法的对象，再通过类的getConstructor()方法获取类的构造器，最后通过Constructor的getInstance()方法创建类的实例，并通过方法对象的invoke()方法调用真正想要使用的方法。总结地讲，反射就是在运行时才知道要操作的类是什么，并且可以在运行时获取类的完整构造，并调用对应的方法。

Java反射的基本流程大致如下：

```java
Class clz = Class.forName("com.xxx.xxx");
Method method = clz.getMethod("methodName", int.class);
Constructor constructor = clz.getConstructor();
Object object = constructor.newInstance();
method.invoke(object, 1);
```



### JVM垃圾回收

#### JVM如何判断对象存活？

1. 引用计数法

   为每个对象设置一个引用计数器，每当有引用时，计数器+1，引用失效时计数器-1。当对象引用为0时，判断对象失效。

   优点：实现简单，判定效率高；

   缺点：难以解决对象之间循环引用的问题。

2. 可达性分析法

   从GC Roots出发，与Roots直接或间接关联的对象就是有效对象，反之就是无效对象。

   可作为GC Roots的对象包括：

   - 虚拟机栈中引用的对象
   - 方法区中类静态属性引用的对象；
   - 方法区中常量引用的对象；
   - 本地方法栈中JNI引用的对象。

#### Java中引用的类型

在JDK1.2之后，Java中存在4种引用类型，从强到弱包括：

强引用、软引用、弱引用、虚引用

- 强引用 Strong Reference

  Java中的默认引用声明就是强引用，如new instance语句和显示赋值语句等。只要强引用存在，垃圾回收器永远不会回收被引用的对象，即使内存不足时，JVM也会直接抛出OutOfMemoryError，而不会回收对象。可通过将引用显示赋值为null来中断强引用。

- 软引用 Soft Reference

  用于描述一些非必需但有用的对象，可通过` java.lang.ref.SoftReference `来使用软引用，如：
  
  ```java
  SoftReference<Object> obj = new SoftReference<>();
  ```
  
  在内存足够的时候，软引用对象不会被回收；内存不足时，JVM则会回收软引用对象。如果回收了软引用对象之后仍然没有足够的内存，JVM才会抛出OutOfMemoryError。
  
  软引用的特性可以很好地解决OOM问题，适用于很多缓存场景，如网页缓存、图片缓存等。
  
- 弱引用 Weak Reference

  弱引用的强度比软引用要更弱，无论内存是否足够，只要 JVM 开始进行垃圾回收，那被弱引用关联的对象都会被回收。可通过` java.lang.ref.WeakReference ` 来使用弱引用，如：
  
  ```java
  WeakReference<Object> obj = new WeakReference<>();
  ```
  
- 虚引用 Phantom Reference

  最弱的引用类型，如果一个对象仅持有虚引用，那么它等同于没有持有任何引用。
  
  ```java
  PhantomReference<Object> obj = new PhantomReference<>();
  ```
  
  无法通过虚引用获得对象。虚引用必须与引用队列配合使用。虚引用的实际应用场景为当对象被回收时，收到系统通知。

**引用队列**：

引用队列可以与软引用、弱引用以及虚引用一起配合使用，当垃圾回收器准备回收一个对象时，如果发现它还有引用，那么就会在回收对象之前，把这个引用加入到与之关联的引用队列中去。程序可以通过判断引用队列中是否已经加入了引用，来判断被引用的对象是否将要被垃圾回收，这样就可以在对象被回收之前采取一些必要的措施。  

#### 被标记为失效的对象是否一定会被回收？

被标记为失效的对象被回收前会经历如下步骤：

1. JVM判断对象是否重写了finalize()方法：

   - 若重写了finalize()方法，则将其放入F-Queue队列中；
   - 若未重写，则直接回收。

2. 执行队列中的finalize()方法：

   JVM自动创建一个优先级较低的线程执行队列中的finalize()方法，只负责触发，不保证执行完毕。若finalize()方法执行了耗时操作，则JVM会停止执行方法并立刻回收对象。

3. 对象销毁/重生：

   若finalize()方法中将this赋值给了某个引用，则该对象会重生，否则会被回收。

#### 几种基础的GC算法

1. 标记-清除

   算法分为两个阶段：标记阶段和清除阶段；标记阶段算法会标记所有失效对象，然后在清除阶段中将这些对象清除。

   优点：实现简单；

   缺点：由于被清除的对象在内存中是不连续的，标记-清除算法会产生大量内存碎片。

2. 复制算法

   将内存分为两块，一次只使用其中一块，当一块内存用完时，将还存活的对象复制到另一块内存上，然后一次性清除第一块内存，再将对象复制回第一块内存。缺点是会浪费一半的内存。

3. 标记-整理

   在清除对象的时候将可回收对象移动到一端，然后清除掉端边界以外的对象，避免产生内存碎片。

复制算法和标记整理算法都有各自适合的场景：

复制算法适合每次回收后存活下来的对象很少的场景，这样可减少复制量，因此复制算法适合**新生代**；

标记整理算法适合每次回收后存活对象较多的场景，回收完毕后产生的内存碎片较少，碎片整理的代价较小，因此标记整理算法主要适合**老年代**。

#### Minor GC和Full GC

HotSpot虚拟机中的GC可分为两种：Partial GC和Full GC。

- Full GC：收集整个堆，包括新生代，老年代，永久代（jdk1.8之前）所有部分的模式；
- Partial GC：对堆的一部分进行垃圾收集，具体又可分为：
  - Young GC：对堆中的新生代进行垃圾收集，即所谓的**Minor GC**；
  - Old GC：对堆中的老年代进行垃圾收集，只有CMS垃圾收集器的concurrent collection是这个模式；
  - Mixed GC：收集整个新生代以及部分老年代。只有G1垃圾收集器有这个模式；

##### Minor GC

HotSpot将新生代分为三个部分，Eden、From Survivor、To Survivor，其空间占比比例为8:1:1。新生代中的对象在Eden空间中分配，当Eden区空间耗尽时，便会触发Minor GC，经过Minor GC后存活下来的对象，会被送到Survivor区。

两个Survivor区中，To指针指向的Survivor区始终是空的。经过一次Minor GC后，Eden区和From指向的Survivor区中的存活对象会被复制到To指向的Survivor区中，然后交换From和To指针，以保证下一次Minor GC时，To指向的 Survivor区还是空的。

##### Survivor区对象晋升位老年代对象的条件

Java虚拟机会记录 Survivor区中的对象一共被来回复制了几次。**如果一个对象被复制的次数为 15 (对应虚拟机参数 -XX:+MaxTenuringThreshold)，那么该对象将被晋升为至老年代**(15次的原因是 HotSpot会在对象头的中的标记字段里记录年龄，分配到的空间只有4位，最多只能记录到15)。此外，**如果单个 Survivor 区已经被占用了 50% (对应虚拟机参数: -XX:TargetSurvivorRatio)，那么较高复制次数的对象也会被晋升至老年代。**

PS：除去以上提到的两种Survivor区对象正常晋升到老年代的情况外，还存在**过早晋升**的情况，即To Survivor区的空间不足以容纳Eden和另一个Survivor中的存活对象时，多余的对象将被移到老年代，这会导致老年代中短期存活的对象增长，可能会引发严重的性能问题。如果老年代满了而无法容纳更多的对象，则Minor GC之后通常就会进行Full GC，这称为**提升失败**。

##### Minor GC如何避免全堆扫描

由于**老年代的对象可能引用新生代的对象**，在标记存活对象的时候，需要扫描老年代的对象，如果该对象拥有对新生代对象的引用，那么这个引用也会被作为 GC Roots。这相当于就做了**全堆扫描**。

HotSpot虚拟机通过**卡表**技术避免Minor GC触发全堆扫描。具体策略是将老年代的空间分成大小为 512B的若干张卡，并且维护一个卡表。卡表本身是字节数组，数组中的每个元素对应着一张卡，本质上就是维护一个标识位，这个标识位代表对应的卡是否可能存有指向新生代对象的引用，如果可能存在，那么这张卡就是所谓的**脏卡**。

在进行Minor GC的时候，只需要在卡表中寻找脏卡，并将脏卡中的老年代指向新生代的引用加入到GC Roots中，当完成所有脏卡的扫描之后，将所有脏卡的标识位清零。

##### Full GC的触发

Full GC可通过以下几种方式触发：

1. 手动调用System.gc，会不断地执行Full GC；
2. 在Minor GC之后，若JVM判断老年代的连续内存空间已少于先前每次Minor GC结束后晋升至老年代的对象总大小的平均值，则JVM会进行Full GC（具体的判断逻辑随着收集器不同有所区别）；
3. 方法区的空间不足时。

#### 主流的垃圾收集器

主流的作用于新生代的垃圾收集器有：

- Serial 串行垃圾回收器：采用**复制算法**进行回收，是一种用单线程进行垃圾回收的回收器。每次回收时，串行回收器只有一个工作线程。

  优点：对于并行计算能力较弱的单CPU计算机来说，串行收集器的性能表现较好；

  缺点：Serial 收集器工作时，需要stop-the-world，程序会暂停运行，等待垃圾收集完毕。

  由于Serial收集器的简单高效，它依然是虚拟机运行在Client模式下默认新生代收集器，对于运行在Client模式下的虚拟机来说是一个很好的选择。

- ParNew 并行垃圾回收器：可以理解为Serial回收器的多线程版，同样使用**复制算法**。

  ParNew默认开启的线程数与CPU数量相同，在CPU核数很多的机器上，可以通过参数`-XX:ParallelGCThreads`来设置线程
  ParNew回收器同样存在Stop The World问题。
  
- Parallel Scavenge（ParallelGC） 回收器：同样是支持多线程的垃圾回收器，使用**复制算法**。ParallelGC回收器更多地关注系统的吞吐量。

  可通过` -XX:MaxGCPauseMillis `参数设置最大垃圾收集停顿时间，把GC停顿的时间控制在MaxGCPauseMillis范围内，如果希望减少GC停顿时间，则可以将MaxGCPauseMillis设置的很小，但是会导致GC频繁，从而增加了GC的总时间，降低吞吐量，因此需要根据实际情况选择。

  此外可通过`-Xx:GCTimeRatio`参数设置吞吐量大小，还可以指定`-XX:+UseAdaptiveSizePolicy`打开自适应模式。

主流的作用于老年代的垃圾收集器有：

- SerialOld垃圾回收器：可理解为Serial回收器的老年代版本，同样是一个单线程回收器，使用的是**标记-整理算法**。其作用主要有：

  - 在JDK1.5及之前的版本中与Parallel Scavenge收集器搭配使用；
  - 作为CMS收集器的后备预案，如果CMS出现Concurrent Mode Failure，则SerialOld将作为后备收集器进行垃圾回收；
  - JVM使用的实际上是基于SerialOld改进的PS MarkSweep收集器。

- ParallelOldGC回收器：类似新生代的Parallel Scavenge，也是一种多线程的回收器，关注的重点同样在于吞吐量。使用了**标记-整理算法**进行垃圾回收。

- CMS回收器：

  CMS即Concurrent Mark Sweep，并发标记清除，使用的是**标记-清除算法**，主要关注系统停顿时间。

此外，还有G1回收器，这是一种作用于整个堆的垃圾回收器。

#### CMS收集器

CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。CMS收集器工作时，GC工作线程与用户线程可以并发执行，以此达到降低收集停顿时间的目的。CMS在jdk1.5中引入。

CMS收集器仅作用于**老年代**的收集，基于**标记-清除算法**，其运作过程分为4个步骤：

1. 初始标记（CMS initial mark）

2. 并发标记（CMS concurrent mark）

3. 重新标记（CMS remark）

4. 并发清除（CMS concurrent sweep）

其中，步骤1和3仍然需要stop-the-world。

步骤1初始标记仅仅标记一下GC Roots能直接关联到的对象，速度很快；

步骤2并发标记阶段就是进行GC Roots Tracing的过程；

步骤3重新标记阶段则是为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始阶段长，但也远比并发标记的时间短；

步骤4完成垃圾对象的回收。

CMS是并发的收集器，因此在CMS回收的过程中，应用程序又会有新的垃圾产生，所以在使用CMS的过程中应该确保应用程序的内存足够可用。CMS不会等到内存空间饱和时才进行回收，而是在其达到某一阀值时开始，回收阀值可通过参数`-XX:CMSInitiatingoccupancyFraction`来指定，默认为68，即当老年代的空间使用率达到68%的时候，会执行CMS回收。

如果内存使用率增长很快，在CMS执行的过程中，已经出现了内存不足的情况，此时CMS回收就会失败，虚拟机将启动SerialOld回收器，这会导致应用程序暂停，直到垃圾回收完成后才会正常工作。

CMS的优点在于其并发收集的特点，系统停顿时间短；而其主要的缺点在于CMS是基于标记-清除算法的，这意味着它会产生大量的不连续的内存碎片，这对新生代是不能接受的，因此CMS并未提供新生代版本。通过设置参数`-XX:+UseCMSCompactAtFullCollecion`可以使CMS回收完成之后进行一次碎片整理。

#### G1收集器

G1收集器出现的目的是为了取代jdk1.5中引入的CMS收集器，可以理解为CMS的升级版，在jdk9中，G1收集器成为默认的垃圾收集器，CMS相关的部分被移除。

G1收集器与传统收集器最大的区别在于，传统的垃圾收集器都将JVM的堆区分为固定大小的连续的两块内存空间：新生代和老年代。而G1收集器则是将堆分为2048块儿大小相等的heap region，这些region在逻辑上是连续的。每块region都会被打上唯一的分代标识（eden/survivor/old），因此，逻辑上依然保留了Eden空间、Survivor空间和Old空间。

G1收集器的作用范围是整个JVM堆，其不同的GC模式有不同的工作流程。主要存在两种GC模式，YGC和MIXGC。G1收集器没有Full GC的模式，但类似CMS，当老年代的内存清理速度跟不上内存空间的分配速度时，G1收集器也会启用SerialOld收集器进行老年代的垃圾收集工作。

- YGC：当Eden空间被占满之后，就会触发G1收集器的YGC。G1依然采用复制算法实现YGC，将Eden空间的存活对象复制到Survivor空间，当对象的存活年龄满足晋升条件时，把对象提升到老年代的region中。

  G1收集器通过动态改变新生代Region的个数达到控制YGC开销的效果。G1的YGC过程仍然需要STW，但其使用了多线程进行对象复制，减少了GC停顿时间。

  类似其他新生代回收算法，G1收集器进行YGC时，也需要考虑是否有老年代对象引用了新生代对象的问题。G1收集器基于卡表的思路，采用一种叫做**Remembered Set**的数据结构处理该问题。RSet是在Card Table的基础上实现的，每个region拥有自己的RSet，其中会记录下别的Region有无指向自己的指针，并标记这些指针分别在哪些Card的范围内。 RSet的结构是一个Hash Table，Key是别的region的地址，Value是一个集合，里面的元素是Card Table的Index。总结就是：RSet是points-into结构（谁引用了我的对象），而Card Table是points-out（我引用了谁的对象）的结构。G1在进行YGC时，通过扫描RSet就可得知哪些对象被老年代引用。

- MIXGC：G1收集器会选定所有新生代region和计算得出的部分收益较高的老年代region，在用户指定的开销范围内尽可能选择收益高的老年代region进行回收。G1收集器进行MIXGC时，通过全局并发标记（concurrent global marking phase）的过程，确定堆中对象的存活情况。该过程类似CMS，主要可分为5个步骤：

  - 初始标记：标记GC Roots直接可达的对象，将它们的字段压入扫描栈中等待后续扫描，该过程需要STW；

  - 根区域扫描：该过程主要是确定老年代中是否存在对新生代对象的引用。从Survivor区出发，标记被引用到老年代的中对象，并将它们的字段压入扫描栈中等待后续扫描，该过程可以并发执行；

  - 并发标记：从扫描栈中取出引用，递归扫描整个堆中的对象，每扫描到一个新的对象就会对其标记，然后将其压入栈中，重复扫描过程直到栈清空，该过程可以并发执行；

  - 最终标记：根据STAB算法，重新标记在步骤3中因为并发扫描而导致存活状态出现错误的对象。STAB算法在GC开始时会对堆中的对象进行逻辑快照，只要被快照到的对象是存活的，那么在整个GC过程中其都被认为是存活的。因此STAB算法可以保证真正存活的对象不会被误回收，但同时也造成了部分可回收对象逃过了GC，产生了浮动垃圾。该步骤需要STW。

    该步骤显著快于CMS收集器的''重新标记"步骤，CMS在重新标记阶段需要脏页和整个根集合，而G1收集器的最终标记过程只需要扫描STAB buffer。

  - 清除：清点出有存活对象的region和没有存活对象的Region，然后更新RSet，这两步操作需要STW；然后对于将所有空的region和计算得到的垃圾收集收益较高的region，生成一个"可分配region队列"（collection set），这一步可以并发执行。

  得到collection set后，G1收集器同样会多线程执行复制和清除的操作。

  总体而言，G1收集器效率高的原因在于其回收的区域变得精确，范围较小。G1从整体来看是基于**标记-整理算法**实现的，从局部（两个Region之间）上来看是基于**复制**算法实现的。但无论如何，这都意味着G1运作期间不会产生内存空间碎片，收集后能提供规整的可用内存。这种特性有利于程序长时间运行，分配大对象时不会因为无法找到连续内存空间而提前触发下一次GC。

#### JVM默认的回收器选择

在jdk1.7和1.8版本中，默认的垃圾回收器为Parallel Scavenge + ParallelOldGC。而在jdk1.9版本后，默认的垃圾回收器是G1回收器。

可通过JVM参数手动指定使用的回收器：

- -XX: UseSerialGC：使用Serial + SerialOld
- -XX: UseParNewGC：使用ParNew + SerialOld
- -XX: UseConcMarkSweepGC：使用ParNew + CMS + SerialOld（SerialOld作为CMS的后备收集器）
- -XX: UseParallelGC：使用Parallel Scavenge + SerialOld
- -XX: UseParallelOldGC：使用Parallel Scavenge + ParallelOld
- -XX: UseG1GC：使用G1收集器

注：在jdk1.7之后使用UseParallelGC参数会默认开启UseParallelOldGC。

#### ZGC收集器

Java11版本引入了一个新的垃圾收集器ZGC，这是一种复杂垃圾收集器，其设计目的是为了支持TB级内存容量，暂停时间低，对整个程序吞吐量的影响小。

### Java内存模型

Java Memory Model（JMM）是一种规范，规定了以下两点：

1. 一个线程如何以及何时可以看到其他线程修改过后的共享变量的值（共享变量的可见性）；
2. 如何在需要的时候对共享变量进行同步。

**JMM定义了Java虚拟机（JVM）在计算机内存（RAM）中的工作方式。**

并发编程中的两个关键问题就是这两条标准的体现：**线程之间如何通信和线程之间如何同步**。

在命令式的编程中，线程之间的通信和同步机制有两种：**共享内存和消息传递**。

- 通信机制：

  在共享内存的并发模型里，线程之间共享程序的公共状态，线程之间通过读-写内存中的公共状态来隐式进行通信。典型的共享内存通信方式就是通过**共享对象**进行通信。

  在消息传递的并发模型里，线程之间没有公共状态，线程之间必须通过明确的发送消息来显示进行通信，在Java中典型的消息传递方式就是` wait() `和` notify() `。

- 同步机制：

  同步是指程序用于控制不同线程之间操作发生相对顺序的机制。

  在共享内存的并发模型里，同步是显示进行的，必须显示指定某个方法或某段代码需要在线程之间互斥进行。

  在消息传递的并发模型里，由于消息的发送必须在消息的接受之前，因此同步是隐式进行的。

Java的并发采用的是共享内存模型，Java线程之间的通信总是隐式进行的。

在JMM中，线程之间的共享变量存储在主内存（main memory）中，每个线程都有一个私有的本地内存（local memory），本地内存中存储了共享变量的副本。本地内存是JMM中的抽象概念，并不真实存在。



## 3 数据结构

### 基本数据类型

Java中提供了8种基本数据类型，包括4种整数类型，2种浮点数类型，1种字符类型，1种布尔类型。

整型：

- byte：8位有符号数，以二进制补码形式表示；最小值` -128 `，最大值` 127 `，默认值` 0 `。

- short：短整型，16位有符号数，以二进制补码形式表示；最小值` -2^15 `，最大值` 2^15 - 1`，默认值` 0 `。

- int：整型，32位有符号数，以二进制补码形式表示；最小值` -2^31 `，最大值` 2^31 - 1 `，默认值` 0 `。

- long：长整型，64位有符号数，以二进制补码形式表示；最小值` -2^63 `，最大值` 2^63 - 1 `， 默认值` 0 `。


浮点型：

- float：单精度浮点数，32位，默认值` 0.0f `。

- double：双精度浮点数，64位，默认值` 0.0d `。


字符型：

- char：16位unicode字符，可存储任意字符，最小值` \u0000 `（0），最大值 ` \uffff `（65535），默认值` \u0000 `。


布尔型：

- boolean：两种取值，` true `和` false `，默认值` false `。


### HashMap

HashMap中的重要参数有：

- loadFactor : 负载因子，默认为0.75；

- initialCapacity : 初始化容量16，最大是（1 << 30）1073741824；
- table : Entry<K,V>[] table 用来存储数据的数组，table的索引在逻辑上被称为“桶”
- Entry<K,V> HashMap的内部类，存储key-value，是链表/树的节点结构；HashMap允许key为null，key为null的条目固定存放在table[0]的位置；

#### HashMap的数据结构

在jdk1.8之前，HashMap的实现是数组+链表，当不同的条目在数组中的位置出现hash冲突时，拉出一条链表维护当前位置上的全部条目；

在jdk1.8之后，HashMap的实现改变为数组+链表+红黑树，在HashMap类中定义了两个阈值常量TREEIFY_THRESHOLD = 8和UNTREEIFY_THRESHOLD = 6，表明当数组中统一位置的元素达到8时，会将链表转换为红黑树，而元素数量下降到6时，红黑树会退化为链表。

> 使用红黑树的原因：
>
> BST在元素插入时会导致树倾斜，插入顺序会影响树的高度，而树的高度直接影响了BST的查找效率，在极端情况下BST会退化成链表（所有节点都在一条斜线上）。红黑数具有性质：任一节点到其子树中的每个叶子节点的路径都包含相同数量的黑色节点，可推导出从红黑树的根节点到叶子节点的最长可能路径不超过最短可能路径的两倍，即红黑树可以保持其大致是平衡的。因此红黑树可以保持在最坏情况下都是相对高效的。作为代价，红黑树的插入删除操作可能会引起树的旋转。
>
> 为什么不直接使用红黑树：
>
> 由于TreeNodes占用空间是普通Nodes的两倍（相较于链表结构，链表只有指向下一个节点的指针，二叉树则需要左右指针，分别指向左节点和右节点），因此使用红黑树取代链表需要以额外的空间为代价。只有当节点较多时，才有转换的必要。

链表转红黑树的阈值被设置为8的原因，一般有两种解释：

1. 当选用的hash算法离散性很好时，数组中同一位置出现碰撞的概率很低，几乎不会出现达到阈值的情况；然而，当采用随机hash算法时，离散性可能会变差，理想情况下随机hash算法计算出的位置分布遵循泊松分布，根据概率统计，同一位置的元素达到8个概率只有大约1亿分之6，几乎是不可能事件。若这种小概率事件都发生了，说明HashMap的元素个数相当多，有提高查找效率的必要。
2. 另一种解释是链表查找的平均查找次数是n/2，而红黑树的平均查找次数是log(n)，8/2=4而log(8)=3，3<4因此有必要转换，但这种说法存在问题，如果是这个理由那应该选择在节点数达到5时就转换，5/2同样大于log(5)。

而树退化的阈值被设为6而不是7，主要是为了避免频繁的树结构的转换，减少系统开销。

#### HashMap的初始化

HashMap的构造法方法在执行时会初始化一个数组table，大小为0。而put方法在执行时，首先会判断table的大小是否为0，如果为0，则会进行真正的初始化，也被称为延迟加载。当进行真正初始化时，数组的默认大小为16。如果创建HashMap实例时调用的是有参的构造方法，则会根据传入的initialCapacity，创建一个长度为大于该值的最小的2的次幂的数组。

#### HashMap的扩容

扩容的时机：当HashMap中的元素个数超过**数组大小\*loadFactor**时，就会进行数组扩容。loadFactor的默认值为0.75，也就是说，默认情况下，数组大小为16，那么当HashMap中元素个数超过16\*0.75=12的时候，就把数组的大小扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知HashMap中元素的个数，那么预设元素的个数能够有效的提高HashMap的性能。

简单总结HashMap的扩容，可分为两步：

- 扩容：创建一个新的Entry空数组，长度是原数组的2倍。
- ReHash：遍历原Entry数组，把所有的Entry重新Hash到新数组。

需要ReHash的原因是：HashMap计算数组下标的逻辑是**index = HashCode(Key) & (Length - 1)**，相当于取HashCode的后N位，在数组长度发生变化后，显然index值会发生变化，因此需要重新计算下标，而不是直接迁移。

#### HashMap节点插入的方式

在jdk1.8之前，在向HashMap中一个已存在元素的数组位置尝试插入新的节点时，使用的是头插法，头插法的设计思想是：后插入的新值往往有更高的访问频率，将新值放在头部可以避免对链表进行遍历。实现思路是：将新节点插在链表的头部。此时新节点就是当前链表的头节点，然后把头节点移动到数组位置即可。

但头插法存在一个非常严重的问题：在并发环境下，HashMap进行扩容操作时，链表有可能会形成环，从而导致在访问时进入无限循环。原因是，在进行扩容时，使用头插法将数据迁移到新的数组中，会导致链表倒序的情况。假设现在有两个线程，线程1和线程2，都尝试向HashMap中插入新的条目，并触发了HashMap的扩容，两个线程都新建了新的扩容数组nextTable。假设在某位置，存在一个链表，其顺序为A-->B-->C，线程1在执行到头节点A时就挂起了，但此时对线程1而言，A的next节点是B。在这同时线程2执行了完整的迁移操作，扩容后新的链表顺序为C-->B-->A。随后线程1恢复，但由于其挂起时，已经将当前位置指向了节点A，而next节点指向了B，此时就出现了loop，A-->B-->A。无论是将线程1创建的数组还是线程2创建的数组返回作为扩容后的新数组，该loop都会存在。

jdk1.8之后，除了将链表结构改为链表+红黑树的形式外，链表插入节点的方式也由头插法改为了尾插法，

#### HashMap为什么线程不安全

HashMap的线程不安全主要体现在其put方法中。假设在HashMap中只有一个元素A。此时线程1向HashMap中添加一个元素B。经过Hash运算后，元素B的索引值与A重复，因此要使用头插法向A位置的链表插入B。在执行插入的过程中，线程2也开始向HashMap中添加一个C。同样，在经过Hash运算，元素C的索引值也与A重复。但线程2执行插入元素C的过程对线程1是不可见的。

因此当线程1执行完插入操作后，线程1的链表B-->A确实已经存在了，但是紧接着就被线程2的C->A所覆盖。这就是HashMap线程不安全的根本原因。

#### 为什么HashMap的数组长度要保持为2的次幂

HashMap中确定元素在数组中的存放位置的方式是，将数组长度-1与要放入的Entry的key的hashCode值进行与运算（&），2的次幂-1后，其每位的值都是1，也就是说key的hashCode进行的与运算，相当于直接取了hashCode的后N位值。因此，只要hashCode是均匀的，其在数组中的位置分布就是均匀的。

简而言之就是为了实现均匀分布。

#### 为什么重写equals()方法一定要重写hashCode()方法

equals()和hashCode()方法都是Object()类中提供的方法，equals()用于判断两个对象是否**相等**，在Object()类中，equals()比较的是两个对象的内存地址，所以如果我们自定义一个类并创建两个不同的实例，即使其内部所有的类的属性值都相同，Object()类的equals()方法也会将它们判断为不相等，因此，例如String和Integer类都对其equals方法进行了重写。如果我们要使用自定义类的对象作为Entry的key，那么就有必要重写equals()方法，确定判断相等的规则。

但重写equals()方法后又会产生新的问题，在HashMap中判断key是否相等大部分情况下是由于要查找在指定的数组位置有没有同样的key存在（如果有同样的key则更新value，否则新插入一条Entry），然而，如果我们使用的是Object类的hashCode()方法，它也是基于内存地址算出的一个散列值，那么两个key对象（即使在我们自定义的equals()方法下它们被判断为相等）的hashCode值很有可能是不一样的，这样它们就会被存入不同的数组位置，根本不会触发equals()方法的判断。因此，要想正确地更新数据，除了重写equals()方法之外，我们还要重写hashCode()方法，保证"相等"的key会被分配到相同的位置。

重写的hashCode()方法和equals()方法需要满足：

1. 如果两个对象相同（即用equals比较返回true），那么它们的hashCode值一定要相同；
2. 如果两个对象的hashCode相同，它们并不一定equals；

### ConcurrentHashMap

jdk1.8中ConcurrentHashMap的实现原理发生了较大的变化。在jdk1.8版本之前，ConcurrentHashMap使用分段锁机制实现对并发操作的支持，其内部包含两个核心静态内部类：Segment和HashEntry。其中Segment类继承ReentrantLock用来充当锁的角色。每个Segment对象守护每个散列映射表的若干个桶，而每个桶是由若干个HashEntry对象链接起来的链表。

在jdk1.8之后，ConcurrentHashMap抛弃了分段锁的机制，改用CAS+Synchronized来保证并发安全，底层数据结构与HashMap相同，采用数组+链表+红黑树。

类似HashMap，ConcurrentHashMap中的重要变量有：

- table：默认为null，延迟初始化，第一次插入操作时初始化，默认大小为16的数组，size总是2的幂次方，用来存储Node节点数据，扩容时大小总是2的幂次方；
- nextTable：默认为null，扩容时新生成的数组，其大小为原数组的两倍；
- Node：保存key，value及key的hash值的数据结构；
- sizeCtl：table数组的容量阀值，默认为0，用来控制table的初始化和扩容操作；
- ForwardingNode：一个特殊的Node节点，hash值为-1，其中存储nextTable的引用。只有table发生扩容的时候，ForwardingNode才会发挥作用，作为一个占位符放在table中表示当前节点为null或已经被移动。

**初始化操作的线程安全：**

table初始化操作会延缓到第一次put行为。在ConcurrentHashMap在构造函数中只会初始化sizeCtl值，并不会直接初始化table，sizeCtl的默认初始值为0，若构造方法传入了自定义的initialCapacity值，那么sizeCtl的值默认为大于initialCapacity的最小的2的次幂。但是put操作是可以并发执行的，在put操作中进行了如下处理：

尝试执行put操作的线程会判断table是否已经被初始化，若table尚未被初始化，则尝试进行初始化操作。初始化方法中，线程会执行Unsafe.compareAndSwapInt方法修改sizeCtl为-1，并且只有一个线程可以执行成功，其余的线程如果进来发现sizeCtl=-1，那么就会执行Thread.yield()让出CPU时间片等待table初始化完成。

**put操作的线程安全：**

执行put操作的线程在判断table已经初始化的前提下，还会对put操作的目标位置Node进行如下判断：

- 若目标位置的Node为null，则说明该位置第一次插入数据，利用Unsafe.compareAndSwapObject安全插入数据，两种可能结果：
  - CAS成功，则说明操作成功，退出；
  - CAS失败，则说明其他线程抢先一步插入了数据，进行自旋，再次尝试插入数据，后续逻辑与目标位置已存在数据的逻辑相同；
- 若目标位置的Node为ForwardingNode，表明有其他线程在进行扩容操作，当前线程会帮助扩容；
- 若目标位置已存在普通Node，则将新的Node节点按链表或红黑树的方式插入到合适的位置，这个过程采用Synchronized实现并发；

**数组扩容的线程安全：**

当table数组的元素个数达到容量阀值sizeCtl的时候，需要对table进行扩容，扩容氛围两个阶段：

- 构建一个新的nextTable，容量为原先table的两倍；

- 把原先table中的数据进行迁移；


第一步nextTable的构建只能单线程进行操作，同样是通过对sizeClt值进行CAS操作，确保只有一个线程能初始化nextTable成功。

第二步中的逻辑则类似put操作，对每个尝试复制数据的位置进行CAS操作，若失败则说明已有别的线程对该位置进行扩容，后续使用Synchronized锁进行并发的迁移操作。

### HashTable和HashMap的区别

HashTable相较于HashMap更“重量级”，主要有以下几点不同：

**继承关系不同**

Hashtable继承自Dictionary类（已弃用），HashMap继承自AbstractMap抽象类，AbstractMap抽象类又实现了Map接口：

```java
public class Hashtable<K, V> extends Dictionary<K, V> implements Map<K, V>, Cloneable, Serializable {...}
```

```java
public class HashMap<K, V> extends AbstractMap<K, V> implements Cloneable, Serializable {...}
public abstract class AbstractMap<K, V> implements Map<K, V> {...}
```

**线程安全不同**

HashTable中的方法是线程安全的，其实现方式是通过synchronized关键字，而HashMap作为一个更轻量级的实现，其中的方法是非线程安全的。以各自的put()方法为例：

HashTable：

```java
public synchronized V put(K key, V value) {
    ...
}
```

HashMap:

```java
@Override public V put(K key, V value) {
    ...
}
```

**是否允许null值**

HashTable中，key和value都不允许出现null值，否则会抛出NullPointerException异常。 

而在HashMap中，允许存在**一个**为null的键，固定存放在数组中的第一个位置（index为0）。可以有**一个或多个**键所对应的值为null。当get()方法返回null值时，**既可以表示HashMap中没有该键，也可以表示该键所对应的值为null**。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个键，而应该用containsKey()方法来判断。

**hash值的计算方法不同**

HashMap直接使用了对象的hashCode值，然后对（hashCode值）和（数组长度的值-1）进行与运算（由于HashMap的数组长度固定为2的次幂，因此该操作相当于取hashCode的后N位），得到数组位置。

HashTable会对对象的hashCode值进行二次hash操作，具体为：先获取key的hashCode()值，然后与int最大值进行与（&）操作，最后直接对数组长度取余，得到数组下标。

**默认数组长度和扩容方式不同**

HashTable中的数组初始大小是11，扩容的方式是old\*2+1。HashMap中数组的默认大小是16，固定为2的次幂，扩容的方式是old\*2。

**处理hash冲突的方式不同**

HashTable处理hash冲突的方式为：在数组的冲突位置拉出一个链表，以头插法的方式向链表中插入冲突的节点。

HashMap在jdk1.8之前，处理hash冲突的方式与HashTable相同，但在jdk1.8中，HashMap将单纯的链表处理改为了链表+红黑树的形式，且链表的插入方式由头插法改为了尾插法。

### HashSet

HashSet基于HashMap实现，是一个**不允许重复元素**的集合。从继承关系上看，HashSet继承自AbstractSet抽象类，该抽象类实现了Set接口。查看其源码的构造方法可以发现，HashSet内部维护了一个HashMap，其实现原理、底层结构完全与HashMap相同。

HashSet保证元素不重复的原理其实是利用了HashMap中的key不允许重复的特性，将需要put进set的元素作为HashMap的key进行校验。

类似HashMap，HashSet存在以下特性：

- 允许有` null `值；

- 无序集合，不会记录元素插入的顺序；

- 线程不安全；

### LinkedHashMap

使用HashMap存放数据时，迭代HashMap的顺序并不是HashMap放置的顺序，也就是说HashMap内部是无序的。当需要维护Map元素在其内部的顺序时，可使用HashMap的子类LinkedHashMap。LinkedHashMap以增加时间和空间开销为代价，维护一个运行于所有条目的双向链表以维护元素迭代的顺序。

LinkedHashMap同样是线程不安全的。

LinkedHashMap中定义的关键对象有：

- header：Entry类型的对象，双向链表的头部节点；

- accessOrder：boolean类型的变量，为true则开启LRU，按照最近访问的顺序维护条目的顺序；为false则按照插入顺序维护。

LinkedHashMap沿用了HashMap的大部分操作数据结构的方法，主要区别在于它们的基本数据结构上，相比较于HashMap的Entry，LinkedHashMap的Entry中多了两个属性before、after。HashMap的next属性用于维护HashMap指定table位置上连接的Entry的顺序，而此处的before和after是用于维护Entry插入的先后顺序的。

简单地讲，LinkedHashMap中，以HashMap的方式维护数据结构，以LinkedList的方式维护数据插入顺序/访问顺序。

#### LRU缓存算法

使用LinkedHashMap可以很方便地实现LRU缓存算法

LinkedHashMap通过其维护的accessOrder属性指定维护条目的顺序，若要实现LRU算法，则该属性需要设置为true。相关的构造方法为:

```java
LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder);
```

在使用get()方法获取value访问条目时，会调用LinkedHashMap中重写的afterNodeAccess()方法，将目标Entry移动到链表最后；

在使用put()方法时，首先同样调用afterNodeAccess()方法，将新添加的或修改过的Entry移动到链表尾；然后会调用afterNodeInsertion()方法，判断是否需要移除最近最少使用的Entry，该判断通过removeEldestEntry()实现，LinkedHashMap中定义的该方法始终返回false，要实现LRU算法，只需要在自定义的类中重写该方法。

示例代码如下：

```java
public class LRUCache {
    // 继承LinkedHashMap类，重写removeEldest
    public class LRUMap<K, V> extends LinkedHashMap<K, V> {
        private int capacity;
        public LRUCache(int capacity, float loadFactor) {
            super(capacity, loadFactor, true);
            this.capacity = capacity;
        }
				@Override
    		protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
      		  return size() > capacity;
    		}
		}

		LRUMap<Integer, Integer> lruMap;
		public LRUCache(int capacity, float loadFactor) {
   		 lruMap = new LRUMap<>(capacity, loadFactor)
		}

		public int get(int key) {
   		 return lruMap.get(key) == null ? -1 : lruMap.get(key);
		}

		public void put(int key, int value) {
   		 lru.put(key, value);
		}
}
```
除了通过继承LinkedHashMap的方式，也可以手动使用双向链表+HashMap，创建自定义的数据结构实现LRU算法，只需要模仿LinkedHashMap中的逻辑即可，具体的，主要需要进行以下工作：

- 创建自定义的内部类Node，Node中除了真实的value外，还需要维护其在链表中的前后节点；

- 实现添加元素到队尾的方法；

- 实现将队列中的元素移动到队尾的方法；

- 实现删除队列头节点的方法；

```java
public class LRUCache {
    private int capacity;
    private HashMap<Integer, Node> hashMap; 
    private Node head;
    private Node tail;
    
    class Node {
        int key;
        int value;
        Node pre;
        Node next;
    
        public Node(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    LRUCache(int capacity) {
        this.capacity = capacity;
        hashMap = new HashMap<>();
    }
    
    /*
     * 从hashMap中寻找key对应的node，若不存在则直接返回-1
     * 若存在，在返回结果之前需要将此次访问的节点置于链表尾部
     * 由于节点在链表中的位置变化，其维护的pre和next属性会变化，因此需要更新hashMap中对应的Entry
     */
    public int get(int key) {
        Node node = hashMap.get(key);
        if (node == null) {
            return -1;
        }
        removeFromList(node);
        addToTail(node);
        hashMap.put(key, node);
        return node.value;
    }
  
    /*
     * 判断put执行的是更新还是新增操作
     * 若是新增，则新构造一个节点对象，然后判断此时hashMap是否已满，若已满则需要移除最近最少使用的条目
     * 若是更新，则从hashMap中找到对应的节点对象，然后先调用removeFromList()方法，再修改其维护的value
     * 无论是新增还是更新，最后都需要将节点添加到链表尾，然后添加/更新hashMap中的Entry。
     */
    public void put(int key, int value) {
        Node node;
        if (!hashMap.containsKey(key)) {
            node = new Node(key, value);
            if (hashMap.size() == this.capacity) {
                removeEldest();
            }
        } else {
            node = hashMap.get(key);
            removeFromList(node);
            node.value = value;
            
        }
        addToTail(node);
        hashMap.put(key, node);
    }

    /*
     * 从链表中移除指定的节点
     * 判断待移除的节点是否为head/tail节点，若是，则需要维护类的head和tail成员变量，让它们指向新的head和tail
     * 若不是head或tail，则将节点的前后节点相连接
     * 最后需要将节点维护的pre和next都置空，避免在调用addToTail()方法后链表形成环。
     */
    public void removeFromList(Node node) {
        if (node == head) {
            head = head.next;
            if (head != null) {
                head.pre = null;
            }   
        } else if (node == tail) {
            tail = tail.pre;
            if (tail != null) {
                tail.next = null;
            }
        } else {
            node.pre.next = node.next;
            node.next.pre = node.pre;
        }
        node.pre = null;
        node.next = null;
    }

    /*
     * 将节点添加到链表尾
     * 若tail指向的对象为null，则说明此时类还没有维护链表，需要初始化head和tail
     * 否则直接将节点设置为新的tail节点，记得设置原tail的next和新tail的pre
     */
    public void addToTail(Node node) {
        if (tail == null) {
            head = tail = node;
            head.next = tail;
        } else {
            tail.next = node;
            node.pre = tail;
            tail = node;
        }
    }
  
    /* 
     * 删除最近最少访问的节点，即链表的head点
     * 首先从hashMap中删除对应的Entry
     * 然后将head指针指向原本head节点的next节点
     * 若新的head节点不为空，记得将其维护的pre节点置为空
     */
    public void removeEldest() {
        hashMap.remove(head.key);
        head = head.next;
        if (head != null) {
            head.pre = null;
        }       
    }
}
```
改变链表结构时，需要注意指针的指向以及判空。

### 双端队列Deque

双端队列继承自Queue，一般可通过创建LinkedList类的对象来创建一个双端队列。可用于实现栈和队列的数据结构。

#### 栈 Stack

栈（Stack）：先进后出，后进先出（FILO）的数据结构。Java中的栈类Stack已废弃，可创建双端队列Deque接口类的对象作为栈使用。Demo：

```java
Deque<Object> stack = new LinkedList<>();
```

双端队列Deque实现了` push() `，` pop() `，` peek() `方法用于栈操作。对应到双端队列本身的方法是：

| 栈方法 | 双端队列方法  |
| ------ | ------------- |
| push() | addFirst()    |
| pop()  | removeFirst() |
| peek() | peekFirst()   |

#### 队列 Queue

队列（Queue）：先进先出（FIFO）的数据结构。同样可创建双端队列Deque类的对象作为队列使用。Demo：

```java
Deque<Object> queue = new LinkedList<>();
```

双端队列Deque实现了` offer() `，` poll() `，` peek() `方法用于队列操作。对应到双端队列本身的方法是：

| 队列方法  | 双端队列方法  |
| --------- | ------------- |
| offer()   | offerLast()   |
| add()     | addLast()     |
| poll()    | pollFirst()   |
| remove()  | removeFirst() |
| peek()    | peekFirst()   |
| element() | getFirst()    |

一些功能类似的方法的区别：

- ` offer() `和` add() `的区别：功能类似，在队列尾添加元素，区别在于在队列容量满时，` offer() `返回false而` add() `会抛出异常。
- ` poll() `和` remove() `的区别：功能类似，移除队列头的元素，区别在于当队列为空时，` poll() `返回null而` remove() `会抛出异常。
- ` peek() `和` element() `的区别：功能类似，获取头部元素（不移除），区别在于当队列/栈为空时，` peek() `返回null而` element() `会抛出异常。

#### 双端队列的常用操作方法

|      | 头部          |              | 尾部         |             |
| ---- | ------------- | ------------ | ------------ | ----------- |
|      | 抛出异常      | 特殊值       | 抛出异常     | 特殊值      |
| 插入 | addFirst()    | offerFirst() | addLast()    | offerLast() |
| 删除 | removeFirst() | pollFirst()  | removeLast() | pollLast()  |
| 检查 | getFirst()    | peekFirst()  | getLast()    | peekLast()  |

### 优先队列 PriorityQueue

PriorityQueue类实现了Queue，可以对其中元素进行排序，支持基本类型的包装类或自定义的类。对于基本类型的包装类，优先队列中元素的默认排列顺序是**升序**；对于自定义类来说，需要自定义比较类（传入自定义的Comparator对象或通过匿名内部类的方式重写compare()方法）。

#### 匿名内部类实现方式（降序）

```java
PriorityQueue<Integer> priorityQueue = new PriorityQueue<>(new Comparator<>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1);
    }
});
```

> **compareTo()方法**
>
> compareTo()方法用于将数字类型对象与方法的参数进行比较。可用于比较Byte/Long/Integer等，也可用于比较String（字典序）。自定义类可通过实现Comparable接口并重写compareTo()方法来使用。
>
> - 如果指定的数与参数相等返回0。
> - 如果指定的数小于参数返回 -1。
> - 如果指定的数大于参数返回 1。
>
> **Comparator类的campare()方法**
>
> 若compare()方法返回了大于0的值，Comparator就把前一个数和后一个数交换，即升序；否则不改动相对位置。

可替换为lambda表达式：

```java
PriorityQueue<Integer> priorityQueue = new PriorityQueue<>((o1, o2) -> o2.compareTo(o1));
```

这种简单的比较也可以直接替换为：

```java
PriorityQueue<Integer> priorityQueue = new PriorityQueue<>(Comparator.reverseOrder());
```

#### 自定义Comparator对象的实现方式（降序）

```java
Comparator<Integer> comparator = new Comparator<>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o2.compareTo(o1);
    }
};

PriorityQueue<Integer> priorityQueue = new PriorityQueue<>(comparator);
```

#### PriorityQueue的实现原理

Java中的优先队列实际上是一个小顶堆/大顶堆，其数据结构是一棵二叉树，通过数组实现。在向数组中插入/删除元素时，会调整整棵树的结构，使其持续满足堆的性质。

PriorityQueue不允许空值。跟队列类似，优先队列同样提供了offer()和poll()方法，用于添加和删除元素，但不是遵循FIFO的规则而是根据优先级排序。

### ArrayList/LinkedList

- **ArrayList**

  从继承关系上看，ArrayList实现了List接口，继承自AbstractList抽象类，其底层是基于数组实现的。此外，ArrayList还实现了RandomAccess接口，用于支持快速随机访问，即不需要通过遍历，根据下标直接访问到内存地址。

  由于ArrayList是基于数组实现的，为了实现动态扩容，在ArrayList添加元素时，如果发现数组已满，则会按照原数组长度的1.5倍创建新的扩容数组，扩容后再将原来的数组复制到新分配的内存地址上。

- **LinkedList**

  从继承关系上看，LinkedList是一个继承自AbstractSequentialList的双向链表，它可以被作为栈、队列或双端队列使用。

  相比较ArrayList，LinkedList没有实现RandomAccess接口，因为LinkedList是基于链表的实现，在内存中是不连续的，因此不支持随机访问。

由于ArrayList和LinkedList的底层实现的差异，他们的增加元素、删除元素、遍历元素的方法性能存在差异，具体的：

- 对增加操作来讲，如果插入的位置在头部，那么LinkedList的性能明显优于ArrayList，因为ArrayList需要复制插入位置后的所有元素并移位。如果插入的位置在靠近尾部，则ArrayList的性能要优于LinkedList，因为链表新增节点需要新建对象并调整前后指针的指向。但如果涉及了数组扩容，那么ArrayList的性能表现就会较差。
- 对删除操作来讲，类似新增操作，删除的位置越靠近头部，ArrayList需要的开销越大。
- 对遍历操作来讲，LinkedList应该尽量避免使用for循环遍历，而是使用迭代器。使用迭代器的时候两者性能接近。



## 4 Java 多线程

### 线程的创建方式

- 继承Thread类

  自定义一个类继承Thread类，并重写其run()方法。然后创建自定义类的对象，调用start()方法开启线程。

  ```java
  public class MyThread extends Thread {
      //Override
      public void run() {
          //具体逻辑
      }
  }
  ```

  ```java
  public void demo () {
      MyThread myThread = new MyThread();
      myThread.start();
  }
  ```

- 实现Runnable接口

  自定义一个类实现Runnable接口，并实现其run()方法。然后创建Thread类的对象，将Runnable对象作为参数传入，调用Thread类对象的start()方法开启线程。

  ```java
  public void MyRunnable implements Runnable {
      //Override
      public void run() {
          //具体逻辑
      }
  }
  ```

  ```java
  public void demo() {
      Thread thread = new Thread(new MyRunnable());
      thread.start();
  }
  ```

  相比较第一种方式，通过实现Runnable接口的线程创建方式实现了线程对象和线程任务的分离，有利于代码解耦。

- 匿名内部类方式

  当线程启动次数较少时，以上两种方法都可以通过匿名内部类的方式，减少代码量。以上两个示例可替换为：

  ```java
  public void demo() {
      Thread thread = new Thread(new Runnable() {
          @Override
          public void run() {
              //具体逻辑
          }
      });
      thread.start();
  }
  ```

  ```java
  public void demo() {
      Thread thread = new Thread() {
          @Override
          public void run() {
              //具体逻辑
          }
      };
      thread.start();
  }
  ```

- 实现Callable接口

  通过Runnable接口定义任务执行的内容并开启线程的方式无法得到返回值，也无法让执行端捕获到异常。可通过Callable接口，使任务在完成后返回指定类型的值或抛出异常。

  由于Callable任务是异步执行的，且不能明确是得到了返回值还是捕获了异常，因此需要对其进一步封装，即Future接口。Future接口封装了对任务执行情况的获取。单独启动一个Thread时，可使用FutureTask接口二次封装Callable对象，然后将FutureTask对象作为参数创建Thread实例，通过FutureTask对象的get()方法获取返回值；通过线程池启动Thread时，可调用线程池对象的submit()方法传入Callable对象，然后通过submit()方法返回的Future对象的get()方法获取返回值。

  - FutureTask + Callable

    FutureTask实现了RunnableFuture接口，表示FutureTask本质上也是表征了一个任务。FutureTask的构造方式有两种：

    直接传入一个Callable对象：
    
    ```java
    public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;
  }
    ```

    传入一个Runnable对象和一个指定的result，然后将其适配为Callable对象：
    
    ```java
    public FutureTask(Runnable runnable, V result) {
        this.callable = Executors.callable(runnable, result);
        this.state = NEW;
  }
    ```

    本质上两种方法是一致的。FutureTask内部维护了任务执行的各种情况，可以让执行段获得返回值。
    
    匿名内部类构建FutureTask对象并开启线程获取返回值：
    
    ```java
    public void demo() {
        FutureTask<String> futureTask = new FutureTask<>(new Callable<String>() {
            @Override
            public String call() throws Exception {
                return "ok";
            }
        });
        Thread thread = new Thread(futureTask);
        thread.start();
        try {
            System.out.println(futureTask.get());
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }    
    }
    ```

  - Future + Callable + ThreadPool

    在任务的执行处接收Future，通过Future来获取结果：

    ```java
    public void demo() {
        ExecutorService threadPool = Executors.newCachedThreadPool();
        Future<String> result = threadPool.submit(new Callable<String>() {
            @Override
            public String call() throws Exception {
                return "ok";
            }
        });
        try {
            System.out.println(result.get());
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
    ```

### 线程的生命周期

Java中线程的状态有以下几种：

- NEW ：初始状态。新创建了一个线程对象，但还没有调用start()方法；
- RUNNABLE：运行状态。Java线程中将就绪（ready）和运行中（running）两种状态统称为“运行”。线程对象创建后，其他线程(比如main线程）调用了该对象的start()方法。该状态的线程位于可运行线程池中，等待被线程调度选中，获取CPU的使用权，此时处于就绪状态（ready）。就绪状态的线程在获得CPU时间片后变为运行中状态（running）；
- BLOCKED：阻塞状态。表示线程阻塞在进入synchronized关键字修饰的方法或代码块（获取锁）时的状态。
- WAITING：等待状态。进入该状态的线程不会被分配CPU时间片，需要等待其他线程显式唤醒（通知或中断）；
- TIMED_WAITING：超时等待状态。进入该状态的线程不会被分配CPU时间片，但不同于WAITING，线程可以在指定的时间后自行返回；
- TERMINATED：终止状态。表示该线程已经执行完毕，当线程的run()方法完成时，或者主线程的main()方法完成时，线程就被认为是终止的。调用一个终止状态的线程会报出java.lang.IllegalThreadStateException异常。

#### 有关线程状态的几种方法的比较

- Thread.sleep(long millis)：由**当前线程**调用此方法，当前线程放弃获取的CPU时间片，进入TIMED_WAITING状态，**但不释放对象锁**，millis后线程自动苏醒进入就绪状态。作用：给其它线程执行机会。
- Thread.yield()：由**当前线程**调用此方法，当前线程放弃获取的CPU时间片，**但不释放锁资源**，由运行状态变为**就绪状态**，让OS再次选择线程。 作用：让相同优先级的线程轮流执行，但并不保证一定会轮流执行。实际中无法保证yield()达到让步目的，因为让步的线程还有可能被线程调度程序再次选中。Thread.yield()**不会导致阻塞**。该方法与sleep()类似，只是不能由用户指定暂停多长时间。
- thread.join()/thread.join(long millis)：在**当前线程**里调用其它线程T的join方法，**当前线程**进入WAITING/TIMED_WAITING状态，当前线程不会释放已经持有的对象锁。**线程T执行完毕或者millis时间到，当前线程一般情况下进入RUNNABLE状态，也有可能进入BLOCKED状态（因为join是基于wait实现的）。**
- Object.wait()/wait(long timeout)：**当前线程**调用**对象的**wait()方法，当前线程**释放对象锁**并进入WAITING/TIMED_WAITING状态，**当前线程**进入等待队列。依靠notify()/notifyAll()唤醒或者timeout时间到自动唤醒。

#### 同步队列

- 当前线程想调用对象A的同步方法时，发现对象A的锁被别的线程占有，此时当前线程进入同步队列。简言之，同步队列里面放的都是想争夺同一对象锁的线程。
- 当一个线程1被另外一个线程2唤醒时，1线程进入同步队列，去争夺对象锁。
- 同步队列是在同步的环境下才有的概念，一个对象对应一个同步队列。
- 线程等待时间到了或被notify/notifyAll唤醒后，会进入同步队列竞争锁，如果获得锁，进入RUNNABLE状态，否则进入BLOCKED状态等待获取锁。

### Java线程池

#### 什么是线程池

线程池的思想是一种对象池，在程序启动时就开辟一块儿内存空间用于存放线程对象。线程的执行调度由线程池管理器处理，当有线程任务时，从池中取一个线程对象完成任务，执行完成后线程对象返回池内，以避免反复创建线程对象带来的性能开销，节省系统资源。

使用线程池主要有以下优点：

- 减少线程创建和销毁的次数，每个工作线程都可以被重复利用；

- 可有效控制线程的最大并发数，防止消耗过多的内存资源；

- 实现对线程的一些简单管理，如线程的延时执行、定时循环执行的策略等；

#### 线程池的组件

基础的线程池包括以下4个基本组成部分：

线程池管理器 ThreadPool：负责线程池的创建与销毁，添加新任务等操作；

工作线程 WorkThread：负责任务的执行；

任务接口 Task：每个任务对象都必须实现的接口，规定了任务入口、任务的执行状态、任务完成后的收尾工作等；

任务队列 TaskQueue：存放暂时没有得到处理的任务，是线程池的缓存机制；

#### 线程池的关键类

线程池的关键类为ThreadPoolExecutor，继承关系为：ThreadPoolExecutor-->AbstractExecutorService-->ExecutorService。该类实现了execute、shutdown等方法，定义了TaskQueue以及内部类Worker等核心组件。

#### 创建线程池的关键参数

共7个关键参数

- corePoolSize：核心线程池的大小，也可理解为线程池的基本大小。当线程池中正在工作的线程数小于corePoolSize时，若有新任务提交，则线程池会分配一个新的线程来执行任务；若当前正在工作的线程数已经达到corePoolSize的大小，则新提交的任务会进入任务队列，若任务队列已满则会进一步判断maximumPoolSize；

- maximumPoolSize：线程池最大大小，线程池允许创建的最大线程数目。当有任务新提交时，若当前工作线程数已达corePoolSize的限制，且任务队列已满，则进一步判断当前工作线程数有没有达到maximumPoolSize，若没有则创建一个新的线程来执行任务，否则会执行拒绝策略。注意，如果使用了无界的任务的队列，则该参数会失去意义；

- runnableTaskQueue：任务队列，用于保存等待执行的任务的阻塞队列；

- ThreadFactory：创建线程的工厂，可为每个工作线程设置线程名等参数；

- RejectedExecutionHandler：拒绝策略。JDK内置的4种拒绝策略为：

  - AbortPolicy：拒绝任务，抛出异常；
  - DiscardPolicy：拒绝任务，不触发任何动作；
  - DiscardOldestPolicy：丢弃队列最前面的任务（最旧的任务），然后重新尝试执行任务；

  - CallerRunsPolicy：由提交任务的当前线程处理该任务，一般用于不允许失败的、对性能要求较低、并发量较小的场景；

  JVM也支持自定义拒绝策略。

- keepAliveTime：线程活动保持时间。工作线程空闲后，保持存活的时间；

- TimeUnit：时间单位。

#### 线程池支持的三种阻塞队列

Java线程池支持的阻塞队列BlockingQueue可分为三种：有界队列、无界队列、同步移交队列

- 无界队列

  队列大小无限制，常用的为无界的LinkedBlockingQueue；

- 有界队列

  常用的有两类，一类是遵循FIFO原则的队列如ArrayBlockingQueue，另一类是优先级队列如PriorityBlockingQueue；

- 同步移交队列

  当不希望任务在队列中等待而是将任务直接移交给工作线程时，可使用SynchronousQueue作为等待队列。SynchronousQueue不是一个真正的队列，而是一种线程之间移交的机制。要将一个元素放入SynchronousQueue中，必须有另一个线程正在等待接收这个元素；

#### Java创建线程池的方式

Java中创建线程池有两种方法：通过Executors工厂类提供的方法创建4种默认支持的线程池之一，或通过创建ThreadPoolExecutor类的实例自定义线程池。

Executor类提供的4种方法为：

- Executors.newCachedThreadPool()：创建一个可缓存的线程池，若线程数超过处理所需，缓存一段时间后会回收，若线程数不够，则新建线程；

  该方法创建的线程池使用的是SynchronousQueue队列，该队列不会保持任务而是将任务直接提交给线程，相当于队列大小为0。核心线程数为0而最大线程数被设置为Integer.MAX_VALUE，因此当线程数不足时，会持续创建新的线程。

- Executors.newFixedThreadPool(n)：创建一个固定大小的线程池，不能及时的处理的任务会在队列中等待；

  该方法创建的线程池使用的是LinkedBlockingQueue队列，而核心线程数与最大线程数设置为相同，所以线程池的线程数是固定的，相反，阻塞队列是没有限制长度的，因此不能及时处理的任务会进入队列中排队。

- Executors.newSingleThreadPool()：创建一个单线程的线程池，保证所有任务按照指定顺序（FIFO/LIFO/优先级）执行；

  类似newFixedThreadPool()方法，区别在于最大线程和核心线程数都固定为1。

- Executors.newScheduledThreadPool(n)：创建一个周期性的线程池，支持定时及周期性执行任务；

  该方法创建的线程池使用的是DelayedWorkQueue队列，最大线程数被设置为Integer.MAX_VALUE。特别地，使用该方法时，线程池对象的类型为ScheduledExecutorService，而执行任务的方法不是execute()而是schedule()。

自定义线程池方法则需要传入上文提到的7个关键参数。

实际上，在选择使用线程池时，应当尽量通过手动创建ThreadPoolExecutor类的实例来实现，除了可以更好地控制线程池的表现外，还因为Executors工厂类提供的这四种方法存在以下问题：

- 对于newCachedThreadPool和newScheduledThreadPool，由于其最大线程数被设置为了Integer.MAX_VALUE，存在线程大量创建从而OOM的风险；
- 对于newFixedThreadPool和newSingleThread，由于其队列长度是无限的，存在请求大量堆积从而OOM的风险。

#### 线程池执行任务的方式

ThreadPoolExecutor提供了两种向线程池提交任务的方法：execute()和submit()。

- execute()：没有返回值，因此无法判断任务是否被成功执行；

- submit()：返回一个future对象，可通过Future对象的get()方法获取返回值，可配合Callable对象使用；

#### 线程池的关闭

ThreadPoolExecutor提供了两种关闭线程池的方法：shutdown()和shutdownNow()。

- shutdown()：将线程池的状态设置为SHUTDOWN以阻止新任务提交。然后中断所有不在执行任务的线程。已在队列中的任务依然会被按顺序执行；
- shutdownNow()：将线程池的状态设置为STOP以阻止新任务提交。遍历工作线程，逐个调用线程的interrupt()方法来中断线程，同时队列中的任务会被移除，并添加到列表中返回；

### 线程方法

#### run()和start()的区别

要真正开启一个线程，需要调用其start()方法。若调用run()方法，则只是在当前线程中使用了线程对象的方法，后续代码依然需要等待run()方法执行完毕才能进行，也就是并没有开启新的线程。

#### wait()和sleep()的区别

- 方法所属的类不同

  - sleep()是Thread类的方法，因为sleep是让当前线程休眠，不涉及到对象类，也不需要获得对象的锁；
  - wait()是基类Object类的方法，因为wait是让获得对象锁的线程实现等待，那么前提就是要获得对象的锁；

- 是否让出锁

  - 调用sleep()方法不会失去对任何监视器（monitor）的所有权，不会释放锁，仅仅会让出CPU的执行权；
  - 调用wait()方法不仅会让出CPU的执行权，还会释放锁（即monitor的所有权），并且进入wait set。直到其他线程调用notify()或者notifyAll()方法，或者指定的timeout到了，才会从wait set中出来，并重新竞争锁；

  sleep 会让出CPU执行时间且强制上下文切换，而wait则不一定，wait后可能还是有机会重新竞争到锁继续执行的。

- 使用方式

  - 使用sleep()方法需要捕获InterruptedException异常：

    ```java
    try {
        Thread.sleep(3000L);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    ```

  - 使用wait()方法则必须放在synchronized代码块里面，同样需要捕获InterruptedException异常，并且需要获取对象的锁。负责唤醒的nofity()和nofityAll()方法也同样需要放到synchronized代码块中：

    ```java
    synchronized (lock){
        try {
            lock.wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    ```

    ```java
    synchronized (lock) {
        // 随机唤醒
        lock.notify();
        // 唤醒全部
        lock.notifyAll();
    }
    ```

- 使用场景

  - sleep：一般用于当前线程休眠，或者轮循暂停操作；

  - wait：多用于多线程之间的通信；

#### join()方法

在当前线程中调用另一个线程的Thread.join()方法，会使当前线程进入WAIT/TIMED_WAIT状态，直到被调用方法的线程运行完毕。因此，要保证n个线程按顺序执行，可以在主线程中依次调用它们的join()方法，如：

```java
...
Thread A = new Thread(...);
Thread B = new Thread(...);
Thread C = new Thread(...);

A.start();
A.join();

B.start();
B.join();

C.start();
C.join();
...
```

### 生产者消费者模式

生产者-消费者模式是一个经典的多线程设计模式，它为多线程间的协作提供了良好的解决方案。这个模式中，通常有两类线程，即若干个生产者线程和若干个消费者线程。生产者线程负责提交用户请求，消费者线程则负责具体处理生产者提交的任务。生产者和消费者之间通过共享内存缓存区进行通信，这样就避免了生产者和消费者直接通信，从而将生产者和消费者解耦。不管是生产高于消费，还是消费高于生产，缓存区的存在可以确保系统的正常运行。这个模式有以下几种角色：

- 生产者：用于提交用户的请求，提取用户任务，装入内存缓冲区。
- 消费者：在内存缓冲区中提取并处理任务。
- 内存缓冲区：缓存生产者提交的任务或数据，供消费者使用。
- 任务：生产者向内存缓冲区提交的数据结构。
- Main：使用消费者和生产者的客户端。

当存储空间为空时，消费者阻塞，当存储空间满时，生产者阻塞。

生产者消费者模式可通过以下方式实现：

1. wait()+notify()方法

   最简单最基础的实现，缓冲区满和为空时都调用wait()方法等待，当生产者生产了一个产品或者消费者消费了一个产品之后会唤醒所有线程。

2. 可重入锁ReentrantLock的实现

   





## 5 Java 锁机制

### 5.1 synchronized关键字

#### 实现原理

synchronized关键字是Java提供的原子性内置锁，是一种监视器锁（内置且使用者看不到）。使用synchronized关键字会在编译后的同步代码块前后加上monitorenter和monitorexit字节码指令，依赖操作系统底层的互斥锁实现。synchronized关键字的主要作用是实现原子性操作和解决共享变量的内存可见性问题。

执行monitorenter指令时会尝试获取对象锁，如果对象未加锁或者线程已获得了锁，锁的计数器+1；此时其他尝试竞争锁的线程会进入等待队列中。执行monitorexit指令时会将计数器-1，当计数器归零时锁会被释放，处于等待队列中的线程则会尝试竞争锁。

从内存语义来说，加锁的过程会清除工作内存中的共享变量，再从主内存中读取；释放锁的过程则是将工作内存的变量写回主内存。

#### 加锁方式

synchronized关键字有三种使用方式：

- 修饰实例方法，作用于**当前实例**，进入同步代码前，要获得**当前实例**的锁，即锁的作用域是this；
- 修饰静态方法，作用于**类对象**，进入同步代码前，要获得**当前类对象**的锁，即锁的作用域是class；
- 修饰代码块，指定加锁对象，对指定的对象加锁，进入同步代码块前要获得指定对象的锁。可以指定当前实例，也可以指定类对象，也可以指定其他对象；

#### 可重入性

由synchronized底层原理可知，若当前线程已经持有锁对象，在其再次申请锁时，锁的计数器会+1，当前线程依然可以获取锁。因此，从逻辑上讲，synchronized关键字属于可重入锁。

#### 线程中断

对于synchronized关键字来说，如果一个线程在等待锁，那么结果只有两种，要么它获得这把锁继续执行，要么保持等待，即使调用中断线程的方法，也不会生效。

#### 等待唤醒机制

在使用notify()、notifyAl()l和wait()方法时，必须处于synchronized代码块或者synchronized方法中，否则会抛出IllegalMonitorStateException异常，这是因为调用这几个方法前必须拿到当前对象的监视器monitor对象。

#### 执行过程

线程进入synchronized代码块前后，执行过程如下：

- 线程获得互斥锁；
- 线程清空工作内存；
- 线程从主内存拷贝共享变量最新的值到工作内存成为副本；
- 线程执行同步代码；
- 线程将修改后的副本的值刷新回主内存中；
- 线程释放锁；

### 5.2 Lock

Java内置的synchronized关键字锁相对比较重量级，且使用时存在以下局限：

- 当一个线程获取了对应的锁，并执行同步代码块时，其他线程只能一直等待，等待获取锁的线程释放锁，而获取锁的线程释放锁只会有两种情况：

  - 获取锁的线程执行完了代码块，线程释放对锁的占有；
  - 线程执行发生异常，JVM自动释放锁；

  如果这个获取锁的线程由于要等待IO或者其他原因（如调用sleep方法）被阻塞了，但是又没有释放锁，其他线程便会长时间地等待下去。等待中的线程**不会响应中断**。

- 如果多个线程都只是进行读操作，当一个线程在进行读操作时，其他线程也只能等待无法进行读操作。

- synchronized机制无法感知线程是否成功获取了锁。

因此Java中提供了Lock接口，用于在部分场景下取代synchronized关键字，进行更轻量级的实现。但注意，采用Lock方式获取必须主动去释放锁，且Lock在发生异常时，不会自动释放锁。因此一般来说，使用Lock必须在try-catch块中进行，并且将释放锁的操作放在finally块中，以保证锁一定被被释放，防止死锁的发生。

lock()方法和unlock()方法间的代码块即为加锁的代码块。Lock只有代码块加锁的方式，不能对方法加锁。

#### lock()方法

获取锁。如果锁已被其他线程获取，则进行等待。

示例：

```java
Lock lock = new ReentrantLock();
lock.lock();
try {
    // 处理任务
} catch(Exception e) {
    
} finally {
    lock.unlock(); // 释放锁
}
```

注意，lock()方法的调用在try之前，原因是加锁操作可能会抛出异常，如果加锁操作在try块之前，那么出现异常时try-finally块不会执行，程序直接因为异常而中断执行；如果加锁操作在try块中，由于已经执行到了try块，那么finally在try中出现异常时仍然会执行，此时try中的加锁操作出现异常，finally依然会执行解锁操作，而此时并没有获取到锁，执行解锁操作会抛出另外一个异常。虽然都是抛出异常结束，但是此时finally解锁抛出的异常信息会将加锁的异常信息覆盖，导致信息丢失。因此应该将加锁操作放在try块之前执行。

#### tryLock()方法

tryLock()方法是有返回值的，它表示尝试获取锁，如果获取成功，则返回true，如果获取失败（即锁已被其他线程获取），则返回false。该方法会立即返回，在拿不到锁时不会一直等待。

tryLock(long time, TimeUnit unit)方法和tryLock()方法是类似的，区别在于传入时间参数时，tryLock()方法在拿不到锁后会等待一定的时间，在时间期限之内如果还拿不到锁，就返回false。如果一开始拿到锁或者在等待期间内拿到了锁，则返回true。

示例：

```java
Lock lock = new ReentrantLock();
if (lock.tryLock()) {
     try {
         // 处理任务
     } catch(Exception e){
         
     } finally{
         lock.unlock();   // 释放锁
     } 
} else {
    // 如果不能获取锁，则直接做其他事情
}
```

#### 可重入锁ReentrantLock

ReentrantLock实现了Lock接口。可重入锁即表示可反复进入的锁，但仅限于当前线程。当前线程可以反复加锁，但也需要释放同样加锁次数的锁，即重入了多少次，就要释放多少次，不然也会导入锁不被释放。

**几个重要方法：**

1. lock()

   获取锁，有三种情况：

   - 锁空闲：直接获取锁并返回，同时设置锁持有者数量为：1；
   - 当前线程持有锁：直接获取锁并返回，同时锁持有者数量递增1；
   - 其他线程持有锁：当前线程会休眠等待，直至获取锁为止；

2. tryLock()

   尝试获取锁，获取成功返回：true，获取失败返回：false。同样有以下三种情况：

   - 锁空闲：直接获取锁并返回：true，同时设置锁持有者数量为：1；
   - 当前线程持有锁：直接获取锁并返回：true，同时锁持有者数量递增1；
   - 其他线程持有锁：获取锁失败，返回：false；

3. unlock()

   释放锁，每次锁持有者数量递减 1，直到 0 为止。

### 5.3 CAS

CAS即**Compare And Swap**。CAS机制当中使用了3个基本操作数：内存地址V，旧的预期值A（V的一个copy），计算后要修改的新值B。在线程尝试更新一个变量的时候，只有当变量的预期值A和内存地址V当中的实际值相同时，才会将内存地址V对应的值修改为B。否则线程会重新尝试，直到更新成功。从思想上看，CAS属于乐观锁的概念。

CAS可能存在的问题：

1. ABA问题：如果在线程1尝试更新之前，线程2将要修改的值从A改为了B又改为了A，那么线程1使用CAS进行检查时会发现它的值没有发生变化，但是实际上却变化了。

   解决方法：使用版本号或时间戳。

2. 循环时间长开销大：自旋CAS如果长时间不成功，会给CPU带来较大的执行开销。

3. 只能保证一个共享变量的原子操作：对多个共享变量操作时，CAS无法保证操作的原子性。

   解决方法：使用jdk1.5引入的AtomicReference类来保证引用对象之间的原子性，把多个变量放在一个对象里来进行CAS操作。或放弃CAS，改为使用锁。

### 5.4 Java锁优化机制

JDK1.6版本引入了锁优化机制。锁优化机制包括：自适应锁、自旋锁、锁消除、锁粗化、轻量级锁和偏向锁。

锁状态从低到高依次为：无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁。

相关概念的含义为：

- 自旋锁：大部分情况下锁占用的时间不会太长，共享变量的锁定时间很短，因此没有必要挂起线程，进行用户态和内核态的来回上下文切换。自旋的概念就是让线程执行一个忙循环，防止从用户态进入内核态，自旋的默认次数时10次。

- 自适应锁：自适应锁就是自旋锁的自适应版本，其自旋的时间不是固定的，而是由前一次在同一个锁上的自旋时间和锁的持有者状态来决定。

- 锁消除：JVM检测到一些同步代码块完全不存在数据竞争时，会进行锁消除操作，不对其加锁。

- 偏向锁：当使用偏向锁时，线程访问同步代码块获取锁会在对象头和栈帧的锁记录中存储偏向锁的线程ID，之后该线程再次进入同步方法块时就不需要CAS来加锁和解锁了。偏向锁永远偏向第一个获得锁的线程，若后续没有别的线程获得过这个锁，持有锁的线程就永远不需要进行同步，反之，其他线程竞争偏向锁时，持有偏向锁的线程就会释放锁。

- 轻量级锁：JVM的对象的对象头中包含锁的标志位，代码进入同步块的时候，JVM使用CAS的方式来尝试获取锁，如果更新成功会把对象头中的状态位标记为轻量级锁，如果更新失败则尝试自旋来获取锁。

简单地讲，偏向锁就是通过对象头的偏向线程ID来比对，甚至不需要CAS；轻量级锁主要是通过CAS修改对象头的锁记录和自旋来实现；重量级锁则是除了当前拥有锁的线程，其他线程全部阻塞。

### 5.5 Java对象头

Java对象在内存中的实际包含3个部分：对象头、实例数据、对齐填充。

对象头则包括两部分内容：

- Mark Word，包括对象的hashcode、分代年龄、轻量级锁指针、重量级锁指针、GC标记、偏向锁线程ID、偏向锁时间戳。

- 存储类型指针，指向类的元数据的指针，用于确定对象是哪个类的实例。

如果是数组对象则还包括数组的长度。

5.6 volatile关键字





## 6 Java设计模式

- 使用设计模式的目的：复用代码，提高代码的可扩展性和可维护性，降低代码的耦合度；

- 设计模式的原则

  - 开闭原则：软件应该对扩展开放，而对修改关闭；即新功能的实现尽量通过新增代码实现，而不是修改原有代码；

  - 里氏替换原则：如果调用一个父类的方法可以成功，那么替换为子类调用也应当可以运行。

- 设计模式分类

  - 创建型模式：关注点是如何创建对象，核心思想是将对象的创建与使用相分离；

  - 结构型模式：主要涉及如何组合各种对象以便获得更好、更灵活的结构。结构型模式不仅仅简单地使用继承，而更多地通过组合与运行期的动态组合来实现更灵活的功能；

  - 行为型模式：主要涉及算法和对象间的职责分配。通过使用对象组合，行为型模式可以描述一组对象应该如何协作来完成一个整体任务。

### 6.1 创建型模式

#### 单例模式 Singleton Pattern

保证一个类仅有一个实例，并提供一个访问它的全局访问点。

单例模式的实现要素有以下几点：

1. 只有private构造方法，确保外部无法实例化；
2. 通过private static变量持有唯一实例，确保全局唯一性；

3. 通过public static方法返回此唯一实例，使外部方法获取实例；

延迟加载（lazy loading）的概念：不在类加载时就实例化全局唯一实例，而是在调用方第一次调用getInstance()方法时才创建。

单例模式的实现方式：

**懒汉式**

```java
public class Singleton {
    private static Singleton instance;
    private Singleton(){}
  
    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton(){};
        }
        return instance;
    }
}    
```

显然，基础版本的懒汉式实现了延迟加载，但存在一个严重问题：不支持多线程环境。在多线程环境下，使用该方法有可能造成实例对象被反复创建。因此严格意义上这不能算是单例模式。

**懒汉式-线程安全**

在基础版本的懒汉式上对getInstance方法使用synchronized进行加锁即可满足多线程工作的要求。 

```java
public class Singleton {
    private static Singleton instance;
    private Singleton(){}
    
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
```
这种方式既具备延迟加载的性能，也能支持多线程的工作环境，缺点是效率相对较低，绝大多数情况下加锁是浪费的。适用于getInstance()方法的性能对应用程度不太关键的情景。

**饿汉式**

区别于懒汉式，饿汉式直接在类加载时就完成实例对象的初始化工作，利用JVM的classLoader机制避免了多线程的同步问题，执行效率会很高。缺点是容易产生垃圾对象，浪费内存。

```java
public class Singleton {
    private static Singleton instance = new Singleton();
    private Singleton(){}
    
    public static Singleton getInstance() {
        return instance;
    }
}
```
**双重校验锁**

采用双锁机制，支持延迟加载和多线程工作环境，且性能很高；适用于getInstance()方法对应用程序的性能影响较大的场景。

```java
public class Singleton {
    private volatile static Singleton instance;
    private Singleton(){}
  
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }    
}
```
对比懒汉式，双重校验锁的改动在：判断instance为空后，并不直接调用类的构造方法，而是先尝试获取类的synchronized锁，然后再次判断instance是否为空（防止在获取锁的过程中其他线程已经完成了类的唯一实例的创建），然后才调用构造方法创建对象。

注意：双重校验锁需要在jdk1.5之后的版本中才能正确运行，且需要用volatile关键字修饰类的实例对象，防止JVM的指令重排序。

**登记式/静态内部类**

```java
public class Singleton {
    private static class SingletonHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
    private Singleton(){}
  
    public static final Singleton getInstance() {
        return SingletonHolder.INSTANCE;
    }
}
```

静态内部类方式是饿汉式的改良版，同样利用了classLoader的机制避免了多线程同步的问题，同时将Singleton的实例封装为静态内部类的constant变量。因此，在Singleton类加载时，不会直接创建实例对象，而是在调用getInstance()方法时，加载内部静态类，初始化其静态常量，从而实现Singleton类实例的创建，达到了延迟加载的效果。

一般情况下，不会使用方法1、2，建议使用方法3饿汉式，如果有明确的延迟加载的要求，则可以使用方法5静态内部类的方式。一般也不使用双重校验锁。

#### 工厂模式 Factory Pattern

工厂模式是Java中最常使用的设计模式之一，其目的是定义一个用于创建对象的接口，由子类决定实例化哪一个类。创建过程延迟到子类进行。

工厂模式的优点：

- 调用者想创建对象时，只需要知道其名称，不需要了解创建的逻辑；

- 扩展性高，想增加产品时，只需要扩展一个工厂类即可；

- 屏蔽了产品的具体实现，调用者只关心接口；

缺点：增加产品时需要增加具体类和对象实现工厂，增加了系统的复杂度。

工厂模式适合于复杂对象的创建，而简单对象，例如可以直接通过new完成创建的对象，一般情况下无需使用工厂模式。

如：接口类为Shape，具体的实现类为Rectangle、Triangle、Circle，工厂类为ShapeFactory，示例代码如下：

**接口类Shape.java**

```java
public interface Shape {
    void draw();
}
```

**实现类Rectangle.java、Triangle.java、Circle.java**

```java
public class Rectangle implements Shape {
    //Override
    public void draw() {
        //具体实现
    }
}
```

```java
public class Triangle implements Shape {
    //Override
    public void draw() {
        //具体实现
    }
}
```

```java
public class Circle implements Shape {
    //Override
    public void draw() {
        //具体实现
    }
}
```

**工厂类ShapeFactory.java**

```java
public class ShapeFactory {
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIngoreCase("RCETANGLE")) {
            return new Rectangle();
        }
        if (shapeType.equalsIngoreCase("TRIANGLE")) {
            return new Triangle();
        }
        if (shapeType.equalsIngoreCase("CIRCLE")) {
            return new Circle();
        }
        return null;
    }
}
```

需要使用实现类的对象时，可以使用以下的方式：

```java
ShapeFactory shapeFactory = new ShapeFactory();
Shape shape1 = shapeFactory.getShape("RECTANGLE");
shape1.draw();
Shape shape2 = shapeFactory.getShape("TRIANGLE");
shape2.draw();
Shape shape3 = shapeFactory.getShape("CIRCLE");
shape3.draw();
```

#### 抽象工厂模式 Abstract Factory Pattern

抽象工厂模式类似工厂模式，但其解决的问题更复杂。不但工厂是抽象的，产品是抽象的，而且有多个产品需要创建，因此，抽象工厂会对应到多个实际工厂，而每个实际工厂负责创建多个实际产品。

抽象工厂模式实现的关键点是定义工厂接口和产品接口，但如何实现工厂与产品本身则需要留给具体的子类实现，客户端只和抽象工厂与抽象产品打交道。

基于上述的工厂模式，添加抽象工厂类为AbstractFactory，工厂类为ShapeFactory和ColorFactory以及工厂制造类FactoryProducer：

产品接口Shape，产品实例Rectangle、Triangle、Cricle同上；

再新建一组不同的产品，产品接口Color，产品实例Red、Blue、Yellow：

**产品接口Color.java**

```java
public interface Color {
    void fill();
}
```

**产品实例Red.java、Blue.java、Yellow.java**

```java
public class Red implements Color {
    @Override
    public void fill() {
        //具体实现
    }
}
```

```java
public class Blue implements Color {
    @Override
    public void fill() {
        //具体实现
    }
}
```

```java
public class Yellow implements Color {
    @Override
    public void fill() {
        //具体实现
    }
}
```

此外，还需要以下关键类：

**抽象工厂类AbstractFactory.java**

```java
public abstract class AbstractFactory {
    public abstract Color getColor(String color);
    public abstract Shape getShape(String shape) ;
}
```

Shape和Color的工厂类都必须继承自该抽象工厂类，因此抽象工厂类中需要预先定义各自的get方法。

**Shape产品的工厂类ShapeFactory.java**

```java
public class ShapeFactory extends AbstractFactory {
    
    @Override
    public Shape getShape(String shapeType){
        if(shapeType == null){
            return null;
        }        
        if(shapeType.equalsIgnoreCase("CIRCLE")){
            return new Circle();
        } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
            return new Rectangle();
        } else if(shapeType.equalsIgnoreCase("SQUARE")){
            return new Square();
        }
        return null;
    }

    @Override
    public Color getColor(String colorType) {
        return null;
    }
}
```

**Color产品的工厂类ColorFactory.java**

```java
public class ColorFactory extends AbstractFactory{
    
    @Override
    public Color getColor(String colorType) {
        if (colorType == null) {
            return null;
        }
        if (colorType.equalsIngoreCase("RED")) {
            return new Red();
        }
        if (colorType.equalsIngoreCase("BLUE")) {
            return new Blue();
        }
        if (colorType.equalsIngoreCase("YELLOW")) {
            return new Yellow();
        }
        return null;
    }
    
    @Override
    public Shape getShape(String shapeType) {
        return null;
    }
}
```

**工厂生成器类FactoryProducer.java**

```java
public class FactoryProducer {
    public static AbstarctFactory getFactory(String factoryType) {
        if (factoryType == null) {
            return null;
        }
        if (factoryType.equalsIngoreCase("SPAPE")) {
            return new ShapeFactory();
        }
        if (factoryType.equalsIngoreCase("COLOR")) {
            return new ColorFactory();
        }
    }
}
```

需要生成产品对象的实例时，可使用如下逻辑：

```java
//通过工厂生产器类的静态方法获取工厂类的对象
ShapeFactory shapeFactory = FactoryProducer.getFactory("SHAPE");
ColorFactory colorFactory = FactoryProducer.getFactory("COLOR");

//创建Shape类型的产品并调用其方法
Shape shape1 = shapeFactory.getShape("RCETENGLE");
shape1.draw();
Shape shape2 = shapeFactory.getShape("TRIANGLE");
shape2.draw();
Shape shape3 = shapeFactory.getShape("CIRCLE");
shape3.draw();

//创建Color类型的产品并调用其方法
Color color1 = colorFactory.getColor("RED");
color1.fill();
Color color2 = colorFactory.getColor("BLUE");
color2.fill();
Color color3 = colorFactory.getColor("YELLOW");
color3.fill();
```

抽象工厂模式可以理解在工厂模式的架构上又覆盖了一层工厂模式。

#### 建造者模式 Builder Pattern

建造者模式使用多个简单的对象一步一步构建成一个复杂的对象，将一个复杂的构建与其表示相分离，使得同样的构建过程可以创建不同的表示。主要解决在软件系统中，有时候面临着"一个复杂对象"的创建工作，其通常由各个部分的子对象用一定的算法构成；由于需求的变化，这个复杂对象的各个部分经常面临着剧烈的变化，但是将它们组合在一起的算法却相对稳定。

#### 原型模式 Prototype Pattern

原型模式指创建新对象的时候，根据现有的一个原型来创建。简单理解就是在类中提供复制的方法。原型模式一般有两种实现方式：

- 使用Java Object类提供的clone()方法，需要类实现Cloneable接口。该方式存在的问题是clone()方法定义在object类中，因此返回的类型也是object，需要对返回结果进行强制类型转换；
- 定义类自己的copy方法，明确返回的类型；

原型模式的应用相对局限，若类的实例持有类似文件、Socket之类资源，是无法复制给另一个对象共享的，只有存储简单类型的“值”对象可以复制。

### 结构型模式

#### 适配器模式 Adapter Pattern

适配器模式用于将一个类的接口转换成用户希望的另外一个接口，使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。

编写一个Adapter的步骤如下：

1. 实现目标接口；
2. 内部持有一个待转换接口的引用；
3. 在目标接口的实现方法内部，调用待转换接口的方法。

以将Callable接口转换为Runnable接口为例，假设有一个类Task实现了Callable接口：

```java
public class Task implements Callable<String> {
    public Task(){}
  
    public String call() throws Exceptions {
        return "ok";
    }
}
```

显然，Task类的对象不能作为Thread类的构造参数，因为Thread类接收的是Runnable类型的变量；若希望通过构造Thread类对象开启线程执行该方法，则可以通过创建一个RunnableAdapter实现。示例代码如下：

```java
public class RunnableAdapter implements Runnable {
    private Callable<?> callable;
    
    public RunnableAdapter(Callable callable) {
        this.callable = callable;
    }
    
    @Override
    public void run() {
        try {
            callable.call();
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
}
```

定义了该Adapter类后，我们就可以通过它来实现接口类型的转换：

```java
Thread thread = new Thread(new RunnableAdapter(callable));
thread.start();
```

Java的标准库中有大量适配器模式的应用，如ArrayList.asList()方法就可将一个数组类型的对象转换为实现了List接口的对象。

#### 装饰器模式 Decorator Pattern

动态地给一个对象添加一些额外的职责。使用装饰器模式相比生成子类更为灵活。

Java标准库中使用装饰器模式的例子，典型的有：

给FileInputStream增加缓冲和解压缩功能，用Decorator模式写出来如下：

```java
// 创建原始的数据源
InputStream fis = new FileInputStream("test.gz");
// 增加缓冲功能
InputStream bis = new BufferedInputStream(fis);
// 增加解压缩功能
InputStream gis = new GZIPInputStream(bis);
```

或直接一次性写成：

```java
InputStream input = new GZIPInputStream( // 第二层装饰
                        new BufferedInputStream( // 第一层装饰
                            new FileInputStream("test.gz") // 核心功能
                        ));
```

简单地将，装饰器模式就是通过对对象的层层封装，每层都附带上更多的新功能。

若要自己实现完整的适配器模式，则需要自定义一个**顶层接口**，每次封装的类都要实现该接口，并定义新的功能。

#### 外观模式 Facade Pattern

该模式比较简单，基本思想是：使用一个统一的中介，客户端（调用者）只跟中介交互，中介再跟各个子系统交互。多用于web程序内部。

#### 享元模式 Flyweight Pattern

该模式的核心思想很简单：如果一个对象实例一经创建就不可变，那么反复创建相同的实例就没有必要，直接向调用方返回一个已缓存的共享的实例就行，这样即节省内存，又可以减少创建对象的过程，提高运行速度。

Java标准库中有很多享元模式的应用，最典型的有：

- Integer.valueOf()方法创建Integer类型的对象，当传入的值在-128到127之间时，方法返回到就是预先缓存好的Integer对象。
- Byte.valueOf()方法创建Byte类型的对象，由于Byte对象一共只有256个状态，因此该方法返回的全部是预先缓存好的实例。

#### 代理模式 Proxy Pattern

代理模式和适配器Adapter模式很类似，区别在于，适配器将A接口转换为B接口，而代理模式将A接口仍然转换为A接口。代理模式存在的意义在于，它可以将不同类之间的职责区分开，并简化测试过程（一次只测一个功能）。例如，要实现权限检查的功能，我们可以构造如下的代理类：

```java
public AProxy implements A {
    private A a;
    public AProxy(A a) {
        this.a = a
    }
    public void aWork() {
        if (getCurrentUser().isRoot()) {
            this.a.aWork();
        } else {
            throw new SecurityException("Forbidden");
        }
    }
}
```

如此我们就实现了，仅当权限检查通过时，才会执行aWork()方法。

此外，代理模式还广泛应用于远程代理、保护代理等。

### 行为型模式

#### 责任链模式 Chain of Responsibility Pattern

这是一种处理请求的模式，它让多个处理者都有机会处理该请求，直到其中某个处理成功为止。责任链模式把多个处理者串成链，然后让请求在链上传递。通常每个处理者都包含对另一个处理者的引用。如果一个对象不能处理该请求，那么它会把相同的请求传给下一个处理者，依此类推。

责任链模式并不复杂，但需要注意的是，处理者添加的顺序很重要，如果顺序不对，处理的结果可能不符合预期。

此外，责任链模式有很多变种。例如，有些责任链模式中，每个处理者都有机会处理请求，通常这种责任链被称为拦截器（Interceptor）或者过滤器（Filter），它的目的不是找到某个处理者处理请求，而是让每个处理者都执行一部分处理工作。

#### 解释器模式 Interpreter Pattern

给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。

典型的实现有正则匹配表达式和SQL的解释器。

#### 迭代器模式 Iterator Pattern

提供一种方法顺序访问一个聚合对象中的各个元素，而又不需要暴露该对象的内部表示。

迭代器模式在Java的集合类中广泛使用。以List为例，要遍历ArrayList，即使我们知道它的内部存储了一个Object[]数组，也不应该直接使用数组索引去遍历，因为这样需要了解集合内部的存储结构。使用Iterator遍历List的示例如下：

```java
List<String> list = ...
for (Iterator<String> it = list.iterator(); it.hasNext(); ) {
    String s = it.next();
}
```

#### 观察者模式 Observer Pattern

观察者模式定义了对象间的一种**一对多**的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并被自动更新。它是一种通知机制，让发送通知的一方（被观察方）和接收通知的一方（观察者）能彼此分离，互不影响。

以电商网站为例。假设一个电商网站有多种Product，而Customer和Admin对商品上架、价格改变都感兴趣，希望能第一时间获得通知。于是，Store类可以这么写：

```java
public class Store {
    Customer customer;
    Admin admin;

    private Map<String, Product> products = new HashMap<>();

    public void addNewProduct(String name, double price) {
        Product p = new Product(name, price);
        products.put(p.getName(), p);
        // 通知用户:
        customer.onPublished(p);
        // 通知管理员:
        admin.onPublished(p);
    }

    public void setProductPrice(String name, double price) {
        Product p = products.get(name);
        p.setPrice(price);
        // 通知用户:
        customer.onPriceChanged(p);
        // 通知管理员:
        admin.onPriceChanged(p);
    }
}
```

上述Store类显然存在问题：

- 直接引用了Customer和Admin的对象；
- 对多个Customer或Admin的情形难以支持；
- 如果要加一个新的观察者类型，Store类就必须改动；

此时可使用观察者模式，分离被观察者和观察者之间的耦合关系。具体的实现通过引用ProductObserver接口实现，任何人想要观察Store，只要实现该接口，并且把自己注册到Store即可：

```java
public class Store {
    private List<ProductObserver> observers = new ArrayList<>();
    private Map<String, Product> products = new HashMap<>();

    // 注册观察者:
    public void addObserver(ProductObserver observer) {
        this.observers.add(observer);
    }

    // 取消注册:
    public void removeObserver(ProductObserver observer) {
        this.observers.remove(observer);
    }

    public void addNewProduct(String name, double price) {
        Product p = new Product(name, price);
        products.put(p.getName(), p);
        // 通知观察者:
        observers.forEach(o -> o.onPublished(p));
    }

    public void setProductPrice(String name, double price) {
        Product p = products.get(name);
        p.setPrice(price);
        // 通知观察者:
        observers.forEach(o -> o.onPriceChanged(p));
    }
}
```

改动后，观察者类型和数目就可以无限扩充了，且这种模式支持创建匿名观察者。





## Java IO模型

### I/O模型的基本概念

#### 同步/异步

- 同步I/O：每个请求必须逐个地被处理，一个请求的处理会导致整个流程的暂时等待，这些事件无法并发地执行。用户线程发起I/O请求后需要等待或者轮询内核I/O操作完成后才能继续执行。

- 异步I/O：多个请求可以并发地执行，一个请求或者任务的执行不会导致整个流程的暂时等待。用户线程发起I/O请求后仍然继续执行，当内核I/O操作完成后会通知用户线程，或者调用用户线程注册的回调函数。

#### 阻塞/非阻塞

- 阻塞：某个请求发出后，由于该请求操作需要的条件不满足，请求操作一直阻塞，不会返回，直到条件满足。

- 非阻塞：请求发出后，若该请求需要的条件不满足，则立即返回一个标志信息告知条件不满足，而不会一直等待。一般需要通过循环判断请求条件是否满足来获取请求结果。

需要注意的是，阻塞并不等价于同步，而非阻塞并非等价于异步。事实上这两组概念描述的是I/O模型中的两个不同维度。

同步和异步着重点在于多个任务执行过程中，后发起的任务是否必须等先发起的任务完成之后再进行。而不管先发起的任务请求是阻塞等待完成，还是立即返回通过循环等待请求成功。

而阻塞和非阻塞重点在于请求的方法是否立即返回（或者说是否在条件不满足时被阻塞）。

### BIO、NIO和AIO

#### BIO

BIO：同步阻塞I/O模式，数据的读取写入必须阻塞在一个线程内等待其完成。服务器实现模式为一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不作任何事情会造成不必要的线程开销。

BIO是面向流的，每次从流（InputStream/OutputStream）中读一个或多个字节，直到读取完所有字节，它们没有被缓存在任何地方。另外，它不能前后移动流中的数据，如需前后移动处理，需要先将其缓存至一个缓冲区。

#### NIO

NIO：同步非阻塞I/O模式，服务器实现模式为一个线程处理多个请求(连接)，即客户端发送的连接请求会被注册到多路复用器上，多路复用器轮询到有I/O请求就会进行处理。NIO是非阻塞的，如：一个线程从某通道发送请求读取数据，它仅能得到目前可用的数据；如果目前没有可用数据时，则返回无法获取，而不是保持线程阻塞；直到数据变为可以读取之前，工作线程可以做其他事情。非阻塞写入同理。

NIO 有三个核心部分：Channel（管道）、Buffer（缓冲区）、Selector（选择器）。

NIO 是面向**缓冲区**编程的。数据读取到了一个它稍微处理的缓冲区，需要时可在缓冲区中前后移动，增加了处理过程中的灵活性，使用它可以提供非阻塞的高伸缩性网络。但与此同时在处理缓冲区前需要检查该缓冲区中是否包含有所需要处理的数据，并需要确保更多数据读入缓冲区时，不会覆盖缓冲区内尚未处理的数据。

NIO的底层设计结构：

- 每个Channel对应一个Buffer。

- Selector对应一个线程，一个线程对应多个Channel。

- 程序切换到哪个Channel是由**事件（Event）**决定的。

- Selector根据不同的事件，在各个通道上切换。

- Buffer是一个内存块，底层是一个数组。

- 数据的读取和写入通过Buffer，可使用**flip()**切换读写模式。而BIO是单向的，要么输入流要么输出流。



#### AIO

AIO：异步非阻塞I/O模式，无需一个线程去轮询所有IO操作的状态改变，在相应的状态改变后，系统会通知对应的线程来处理。AIO引入了异步通道的概念，采用了Proactor模式，简化了程序编写，有效的请求才启动线程，它的特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用。

#### BIO和NIO的区别





##  函数式编程

### Lambda表达式

Java Lambda是jdk 1.8引入的新特性，允许把函数作为一个方法的参数。其语法形式如下：

```java
(parameters) -> expression
```

或

```java
(parameters) -> { statements; }
```

其中：

- 参数类型不需要特别声明，可由编译器识别；
- 当参数只有一个时，无需定义圆括号，多个参数时需使用圆括号；
- 如果主体只包含了一个语句，不需要使用大括号；
- 如果使用了大括号（多个语句），需要指明返回表达式。

Lambda表达式最常用的场景为：对于单方法接口/类，使用Lambda表达式替换匿名内部类的实现方式。

示例：

```java
public static void main(String[] args) {
    MathOperation subtraction = (a, b) -> a - b;
}

interface MathOperation {
    int operation(int a, int b);
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

### 方法引用 ::



## 注解 Annotation

### 基本概念

注解是一种用于标注的“元数据”，可放在Java源码的类、方法、字段、参数前。Java中所有的注解本质上均是继承自Annotation接口。

定义注解时可以定义配置参数，配置参数包括：

- 所有基本类型
- String
- Class
- enum
- Annotation（即注解可以嵌套）
- 以上类型的数组

注解的配置参数可以有默认值，缺少某个配置参数时将使用默认值。如果只写注解，则相当于全部使用默认值。

注解的配置参数不能有不确定的值，也就是，参数要么具备并使用默认值，要么在使用注解时提供参数的值。此外，对于非基本类型的参数，无论是在源代码中声明，还是在注解接口中定义，都不能以`null`作为值！

### 定义注解

使用**@interface**语法来定义注解，基本格式如：

```java
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
}
```

使用**default**为注解的参数设定默认值，同时将最常使用的参数命名为**value**。

### 元注解

元注解是用于修饰其他注解的注解，Java提供的元注解定义在java.lang.annotation包下面。在jdk 1.8之前共有4种元注解：

#### @Target

使用元注解可定义该注解能被应用源码的哪些位置，具体常用的有：

- ElementType.TYPE：作用于类、接口和枚举；

- ElementType.FIELD：作用于字段；

- ElementType.METHOD：作用于方法；

- ElementType.CONSTRUCTOR：作用于构造方法；
- ElementType.PARAMETER：作用于方法参数；

此外，ElementType类中还定义了其他几种类型：

- ElementType.LOCAL_VARIABLE：作用于局部变量；

- ElementType.ANNOTATION_TYPE：作用于注解；

- ElementType.PACKAGE：作用于包；

例如，要使注解可作用于方法上，需要添加**@Target(ElementType.METHOD)**：

```java
@Target(ElementType.METHOD)
public @interface Report {
 int type() default 0;
    String level() default "info";
    String value() default "";
}
```

如果要使其可用在方法和字段上，则可将@Target注解的参数变为数组：

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

当注解未指定@Target的值时，该注解可用于上述任意类型。

#### @Retention

使用@Retention元注解可定义注解的生命周期，具体参数值有：

- RetentionPolicy.SOURCE：仅编译期；

- RetentionPolicy.CLASS：仅Class文件；

- RetentionPolicy.RUNTIME：运行时；

若没有使用@Retention注解，则默认注解的生命周期为CLASS。通常自定义的注解需要在RUNTIME生效，因此务必添加**@Retention(RetentionPolicy.RUNTIME)**。

例如：

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
}
```

#### @Document

使用@Document元注解可定义该注解是否被包含到javadoc中。

#### @Inherited

使用@Inherited元注解可定义该注解能否被子类从父类处继承。仅针对@Target(ElementType.TYPE)类型的注解有效，且只针对类继承，对接口的实现无效。

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

### 注解增强

jdk 1.8中对注解的改进主要有两方面：类型注解和重复注解。

#### 类型注解

类型注解是一种可以放在任何使用类型的位置上的注解。例如：

new语句：

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

注意，类型注解并没有实际作用，需要由框架和工具开发者实际上使用它们，否则它们只起到注释的作用。

在代码实现上，jdk1.8为ElementType类新增了两条字段：

- ElementType.TYPE_PARAMETER ：允许注解被用在类型变量（泛型）的声明中；

- ElementType.TYPE_USE：允许注解可以被用在任何类型使用的地方（如：声明语句、泛型、强制转换语句）。

#### 重复注解

jdk1.8新增了两种元注解：

- @native：只能作用于field，表明该字段为固定值，从native代码中引用；

- @Repeatable：表明该注解可在同一位置被重复使用。

例如，在jdk1.8之前，要重复使用上文中的@Report注解，需要定义一个容器注解@Reports：

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

通过使用@Repeatable元注解，可改为以下形式：

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

注意，仍然需要定义容器注解@Reports，不同的是，创建重复注解时，只需要加上@Repeatable并指向存储注解@Reports，在使用时直接可以重复使用Report注解，增强代码可读性。

### 处理注解


## JDK、JRE和JVM的关系
- JDK（Java Development Kit），它是功能齐全的 Java SDK，是提供给开发者使用，能够创建和编译 Java 程序的开发套件。它包含了 JRE，同时还包含了编译 java 源码的编译器 javac 以及一些其他工具比如 javadoc（文档注释工具）、jdb（调试器）、jconsole（基于 JMX 的可视化监控⼯具）、javap（反编译工具）等等。
- JRE（Java Runtime Environment） 是 Java `运行时`环境。它是运行已编译 Java 程序所需的所有内容的集合，主要包括 Java 虚拟机（JVM）、Java 基础类库（Class Library）。

![[Pasted image 20240314122731.png]]

## JAVA是编译与解释并存的语言

Java 语言既具有编译型语言的特征，也具有解释型语言的特征。因为 Java 程序要经过先编译，后解释两个步骤，由 Java 编写的程序需要先经过编译步骤，生成字节码（`.class` 文件），这种字节码必须由 Java 解释器来解释执行。

我们需要格外注意的是 `.class->机器码` 这一步。在这一步 JVM 类加载器首先加载字节码文件，然后通过解释器逐行解释执行，这种方式的执行速度会相对比较慢。而且，有些方法和代码块是经常需要被调用的(也就是所谓的热点代码)，所以后面引进了 **JIT（Just in Time Compilation）** 编译器，而 JIT 属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。而我们知道，机器码的运行效率肯定是高于 Java 解释器的。

![[Pasted image 20240314123221.png]]

> HotSpot 采用了惰性评估(Lazy Evaluation)的做法，根据二八定律，消耗大部分系统资源的只有那一小部分的代码（热点代码），而这也就是 JIT 所需要编译的部分。JVM 会根据代码每次被执行的情况收集信息并相应地做出一些优化，因此执行的次数越多，它的速度就越快。

### AOT

JDK9引入的新的编译模式 **AOT(Ahead of Time Compilation)**

和 JIT 不同的是，这种编译模式会在程序被执行前就将其编译成机器码，属于静态编译（C、 C++，Rust，Go 等语言就是静态编译）。

优势：
- 启动速度快（不需要预热
- 能减少内存占用
- 增强 Java 程序的安全性（不容易被反编译和修改
- 更适合云原生场景，对微服务架构的支持也比较友好

缺陷：
- 无法支持 Java 的一些动态特性，如反射、动态代理、动态加载、JNI（Java Native Interface）

## `final` 关键字

`final` 关键字修饰的：
- 类不能被**继承**
- 方法不能被**重写**
- 变量是基本数据类型则值不能**改变**

## 基础类型

Java 中有 8 种基本数据类型，分别为：

- 6 种数字类型：
    - 4 种整数型：`byte`、`short`、`int`、`long`
    - 2 种浮点型：`float`、`double`
- 1 种字符类型：`char`
- 1 种布尔型：`boolean`。

![[Pasted image 20240324205315.png]]

>对于 `boolean`依赖于 JVM 厂商的具体实现。逻辑上理解是占用 1 位，但是实际中会考虑计算机高效存储因素。

### 包装类

^525f8b

- 成员变量包装类型不赋值就是 `null`，而基本类型有默认值且不是 `null`

包装类缓存机制： ^617e0b
- `Byte`,`Short`,`Integer`,`Long` 这 4 种包装类默认创建了数值 **[-128，127]** 的相应类型的缓存数据，`Character` 创建了数值在 **[0,127]** 范围的缓存数据，`Boolean` 直接返回 `True` or `False`。
- 两种浮点数类型的包装类 `Float`,`Double` 并没有实现缓存机制。
- 如果超出对应范围仍然会去创建新的对象，缓存的范围区间的大小只是在性能和资源之间的权衡。

```java
Integer i1 = 40;
Integer i2 = new Integer(40);
System.out.println(i1==i2); // false, `Integer i2 = new Integer(40)` 会直接创建新的对象
```


## 变量初始值

只有成员变量才有默认值，而局部变量必须要赋初值。

使用`new`创建在堆上的数组元素均有默认值

## 浅拷贝、深拷贝和引用拷贝

| 类型   | 拷贝机制                                | 实现方法                                                                                                                            | 补充说明                                                                                                 |
| ---- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| 引用拷贝 | 将指向对象的`地址`复制                        | 变量名赋值即可                                                                                                                         | 无                                                                                                    |
| 浅拷贝  | 创建一个`新对象`，新对象与原对象不等，但`新对象的属性和老对象相同` | 在需要拷贝的类上实现`Cloneable接口并重写其clone()`方法。                                                                                           | 如果新对象或老对象的属性改变了指向，另一个的属性不会改变                                                                         |
| 深拷贝  | 创建的新对象的`属性内容`和老对象相同，但是`完全独立`于老对象    | - `重写clone()方法`，要将类中所有自定义引用变量的类也去实现Cloneable接口实现clone()方法。<br>- `序列化`，自定义的类**需要实现Serializable接口**，在需要深拷贝的类中定义一个函数返回该类对象，之后调用该函数 | 序列化后：将二进制字节流内容写到一个媒介（文本或字节数组），然后是从这个媒介读取数据，原对象写入这个媒介后拷贝给clone对象，原对象的修改不会影响clone对象，因为clone对象是从这个媒介读取。 |
|      |                                     |                                                                                                                                 |                                                                                                      |
![[Pasted image 20240314171711.png]]

![[Pasted image 20240314171722.png]]

![[Pasted image 20240314171732.png]]

## Native method

- 本地方法：java代码里面写的`native`方法，它没有方法体。是为了调用`C/C++`代码而写的。在JNI程序里面使用。
- 使用原因：
	- JVM的实现，为了与操作系统底层进行交互，就使用了本地方法
	- JVM自己的代码，有一部分使用C实现的，这些代码的使用也需要使用本地方法

## 为什么重写 equals() 时必须重写 hashCode() 方法

因为两个相等的对象的 `hashCode` 值必须是相等。也就是说如果 `equals` 方法判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等。

如果重写 `equals()` 时没有重写 `hashCode()` 方法的话就可能会导致 `equals` 方法判断是相等的两个对象，`hashCode` 值却不相等。

## String类型

### String类型为何不可变

- 保存字符串的数组被 `final` 修饰且为私有的，并且`String` 类没有提供/暴露修改这个字符串的方法。
- `String` 类被 `final` 修饰导致其不能被继承，进而避免了子类破坏 `String` 不可变。

> 在 Java 9 之后，`String`、`StringBuilder` 与 `StringBuffer` 的实现改用 `byte` 数组存储字符串。

### String通过 `+`拼接原理

字符串对象通过“+”的字符串拼接方式，实际上是通过 `StringBuilder` 调用 `append()` 方法实现的，拼接完成之后调用 `toString()` 得到一个 `String` 对象 。

### 字符串常量池

**字符串常量池** 是 JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建。

## 常量折叠

对于编译期可以确定值的字符串，也就是常量字符串 ，jvm 会将其存入字符串常量池。并且，字符串常量拼接得到的字符串常量在编译阶段就已经被存放字符串常量池，这个得益于编译器的优化。

常量折叠会把常量表达式的值求出来作为常量嵌在最终生成的代码中，这是 Javac 编译器会对源代码做的极少量优化措施之一(代码优化几乎都在即时编译器中进行)。

## SPI #重要 
> [Java SPI 机制详解 | JavaGuide](https://javaguide.cn/java/basis/spi.html)

SPI 即 Service Provider Interface ，字面意思就是：“服务提供者的接口”，可以理解为：专门提供给服务提供者或者扩展框架功能的开发者去使用的一个接口。

![[Pasted image 20240314200618.png]]

###  ServiceLoader

想要使用 Java 的 SPI 机制是需要依赖 `ServiceLoader` 来实现的，JDK 官方给的注释：**一种加载服务实现的工具。**



## 序列化与反序列化 #重要 

> 详情参见[Java 序列化详解 | JavaGuide](https://javaguide.cn/java/basis/serialization.html)

- **序列化**：将数据结构或对象转换成二进制字节流的过程
- **反序列化**：将在序列化过程中所生成的二进制字节流转换成数据结构或者对象的过程

常见应用场景：网络传输、存储到文件、存储到数据库、存储到内存

### 序列化协议对应于 TCP/IP 4 层模型的哪一层？

位于OSI 七层协议模型中的表现层，TCP/IP 四层模型中的应用层

### 不进行序列化

对于不想进行序列化的变量，使用 `transient` 关键字修饰。

`transient` 关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被 `transient` 修饰的变量值不会被持久化和恢复。

`static`变量不会被序列化，因为其不属于任何对象（Object）

### 常见的序列化协议

- JDK自带的——不推荐
	- **不支持跨语言调用**
	- **性能差**
	- **存在安全问题**
- Kryo 是专门针对 Java 语言序列化方式并且性能非常好
-  Protobuf、 ProtoStuff、hessian都是跨语言的序列化方式，如果有跨语言需求的话可以考虑使用
## IO流

Java IO 流的 40 多个类都是从如下 4 个抽象类基类中派生出来的。

- `InputStream`/`Reader`: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
- `OutputStream`/`Writer`: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

### I/O 流为什么要分为字节流和字符流呢?

问题本质想问：**不管是文件读写还是网络发送接收，信息的最小存储单元都是字节，那为什么 I/O 流操作要分为字节流操作和字符流操作呢？**

个人认为主要有两点原因：

- 字符流是由 Java 虚拟机将字节转换得到的，这个过程还算是比较耗时；
- 如果我们不知道编码类型的话，使用字节流的过程中很容易出现乱码问题。

## 反射 #重要 

- 框架：半成品软件。可以在框架的基础上进行软件开发，简化编码
- 反射：将类的各个组成部分封装成其他对象，这就是反射机制
	- 好处：
		1. 可以在程序运行过程中，操作这些对象
		2. 可以解耦，提高程序的可拓展性

![JavaClass](JavaClass.png "javaclass")

### Class对象的获取方式

1. `Class.forname("全类名")`：包名+类名  将字节码文件加载进内存，返回class对象
	- 多用于配置文件，将类名定义在配置文件中，读取文件，加载类
2. 类名.class:通过类名的属性class获取
	- 多用于参数的传递
3. 对象.getClass():getClass()方法在Object类中定义着。
	- 多用于对象的获取字节码的方式
4. 通过类加载器`xxxClassLoader.loadClass()`传入类路径获取:
	- 通过类加载器获取 Class 对象不会进行初始化，意味着不进行包括初始化等一系列步骤，静态代码块和静态对象不会得到执行

> 同一个字节码文件(.class)在一次程序运行中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个

### Class对象功能

- 获取功能：
	1. 获取成员变量们

	```java
	Field[] getFields()   :获取所有public修饰的成员变量
	Field getField(String name)    :获取指定名字的public修饰的成员变量
	Field getDeclaredFields() :获取所有的成员变量，不考虑修饰符（可以获取私有变量，不过要忽略访问权限修饰符的安全检查--field.setAccesible(true);//暴力反射）
	Field getDeclaredField(String name)
	```

	2. 获取构造函数们

	```java
	Constructor<?>[] getConstructors()
	Constructor<T> getConstructor(类<?>... parameterTypes)
	Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)
	Constructor<?>[] getDeclaredConstructors()
	```

	3. 获取成员方法们

	```java
	Method[] getMethods()
	Method getDeclaredMethod(String name, 类<?>... parameterTypes)
	Method[] getDeclaredMethods()
	Method getDeclaredMethod(String name, 类<?>... parameterTypes)
	```

	4. 获取类名
	`String getname(); //全类名`

### Field：成员变量

- 操作
	1. 设置值
		- void set(Object obj, Object value)
	2. 获取值
		- get(Object obj)
	3. 忽略访问权限修饰符的安全检查
		- field.setAccesible(true)l;//暴力反射

### Constructor：构造方法

- 创建对象：
	- T newInstance(Object... initargs)
	- 如果使用空参构造方法创建对象，操作可以简化：class对象的newInstance
	- 忽略访问权限修饰符的安全检查
		- field.setAccesible(true)l;//暴力反射

### Method：成员方法

- 执行方法：
	- Object invoke(Object obj, Object.. args)
	- 忽略访问权限修饰符的安全检查
		- field.setAccesible(true)l;//暴力反射
- 获取方法名称：
	- String getName：获取方法名

### 案例

- 需求：写一个“框架”，在不改变该类的任何代码的前提下，可以帮助我们创建任意类的对象，并且执行其中任意的方法
	- 实现：
		1. 配置文件
		2. 反射
	- 步骤：
		1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件(.properties)中

		2. 在程序中加载读取配置文件（使用**静态代码块**来读取配置文件）

		```java
		//1. 创建Properties集合类
		Properties pro = new Properties();
		//获取src路径下的文件的方式--->ClassLoader(类加载器)
		ClassLoader classloader = JDBCUtils.class.getClassLoader();
		URL res = classlaoder.getResource("Filepath"); //相对路径：./ = src/ 
		String path = res.getPath();
		//2. 加载文件
		//pro.load(new FileReader("Filename"));
		pro.load(new FileReader("path"));
		//3. 获取数据，赋值
		value = pro.getProperty("value");
		```

		3. 使用反射技术来加载类文件进内存

		4. 创建对象

		5. 执行方法
]]

## BigDecimal  #重要 

> [BigDecimal 详解 | JavaGuide](https://javaguide.cn/java/basis/bigdecimal.html)

**浮点数 `float` 或 `double` 运算的时候会有精度丢失的风险**，为了避免精度丢失，可以使用 `BigDecimal` 来进行浮点数的运算。

通常情况下，大部分需要浮点数精确运算结果的业务场景（比如涉及到钱的场景）都是通过 `BigDecimal` 来做的。

`BigDecimal` 的实现利用到了 `BigInteger` （用来操作大整数）, 所不同的是 `BigDecimal` 加入了小数位的概念。

### BigDecimal常见方法

#### 创建

在使用 `BigDecimal` 时，为了防止精度丢失，推荐使用它的`BigDecimal(String val)`构造方法或者 `BigDecimal.valueOf(double val)` 静态方法来创建对象。

> `BigDecimal(double val)`的方式存在精度损失风险

#### 加减乘除

- 加：`add`
- 减：`subtract` 
- 乘：`multiply` 
- 除：`divide` 

>在使用 `divide` 方法的时候尽量使用 3 个参数版本，并且`RoundingMode` 不要选择 `UNNECESSARY`，否则很可能会遇到 `ArithmeticException`（无法除尽出现无限循环小数的时候），其中 `scale` 表示要保留几位小数，`roundingMode` 代表保留规则。

#### 大小比较

`a.compareTo(b)` : 返回 -1 表示 `a` 小于 `b`，0 表示 `a` 等于 `b` ， 1 表示 `a` 大于 `b`。

> `BigDecimal` 使用 `equals()` 方法进行等值比较会出现问题，这是因为 `equals()` 方法不仅仅会比较值的大小（value）还会比较精度（scale），而 `compareTo()` 方法比较的时候会忽略精度。

#### 保留小数位数

通过 `setScale`方法设置保留几位小数以及保留规则。

## 动态代理 #重要 

> [Java 代理模式详解 | JavaGuide](https://javaguide.cn/java/basis/proxy.html#_3-1-jdk-%E5%8A%A8%E6%80%81%E4%BB%A3%E7%90%86%E6%9C%BA%E5%88%B6)

**从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的。**

动态代理只能代理`接口`
### JDK动态代理

**`InvocationHandler` 接口和 `Proxy` 类是核心。**

`Proxy` 类中使用频率最高的方法是：`newProxyInstance()` ，这个方法主要用来生成一个代理对象。

#### 使用步骤

1. 定义一个接口及其实现类；
2. 自定义 `InvocationHandler` 并重写`invoke`方法（可以采用lambda匿名类实现），在 `invoke` 方法中我们会调用原生方法（被代理类的方法）并自定义一些处理逻辑；
3. 通过 `Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)` 方法创建代理对象；

#### 函数说明

#### `Proxy` 类的`newProxyInstance()`

```java
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,    InvocationHandler h) throws IllegalArgumentException{
	......
}
```

1. **loader** :类加载器，用于加载代理对象。—— [[类加载器]] 
2. **interfaces** : 被代理类实现的一些接口；—— 通过接口指定生成的代理有哪些方法
3. **h** : 实现了 `InvocationHandler` 接口的对象；—— 指定生成的代理对象要干什么事

#### `InvocationHandler` 接口中的`invoke()`

1. **proxy** :动态生成的代理类——是`newProxyInstance()`生成的代理对象
2. **method** : 与代理类对象调用的方法相对应——传入真实对象需要代理的方法
3. **args** : 当前 method 方法的参数

## Java 魔法类 Unsafe #重要 

> [Java 魔法类 Unsafe 详解 | JavaGuide](https://javaguide.cn/java/basis/unsafe.html)

### 介绍

`Unsafe` 是位于 `sun.misc` 包下的一个类，主要提供一些用于执行低级别、不安全操作的方法，如直接访问系统内存资源、自主管理内存资源等，这些方法在提升 Java 运行效率、增强 Java 语言底层资源操作能力方面起到了很大的作用。但由于 `Unsafe` 类使 Java 语言拥有了类似 C 语言指针一样操作内存空间的能力，这无疑也增加了程序发生相关指针问题的风险。在程序中过度、不正确使用 `Unsafe` 类会使得程序出错的概率变大，使得 Java 这种安全的语言变得不再“安全”，因此对 `Unsafe` 的使用一定要慎重。

另外，`Unsafe` 提供的这些功能的实现需要依赖本地方法（Native Method）。你可以将本地方法看作是 Java 中使用其他编程语言编写的方法。本地方法使用 **`native`** 关键字修饰，Java 代码中只是声明方法头，具体的实现则交给 **本地代码**。

为什么要使用本地方法：
- 需要用到 Java 中不具备的依赖于操作系统的特性，Java 在实现跨平台的同时要实现对底层的控制，需要借助其他语言发挥作用。
- 对于其他语言已经完成的一些现成功能，可以使用 Java 直接调用。
- 程序对时间敏感或对性能要求非常高时，有必要使用更加底层的语言，例如 C/C++甚至是汇编。

### 如何使用Unsafe这个类

1. 利用反射获得 Unsafe 类中已经实例化完成的单例对象 `theUnsafe`
2. 从`getUnsafe`方法的使用限制条件出发，通过 Java 命令行命令`-Xbootclasspath/a`把调用 Unsafe 相关方法的类 A 所在 jar 包路径追加到默认的 bootstrap 路径中，使得 A 被引导类加载器加载，从而通过`Unsafe.getUnsafe`方法安全的获取 Unsafe 实例。

### Unsafe 功能

1. 内存操作
2. 内存屏障
3. 对象操作
4. 数据操作
5. CAS 操作
6. 线程调度
7. Class 操作
8. 系统信息

## 类加载器

> [一看你就懂，超详细java中的ClassLoader详解-CSDN博客](https://blog.csdn.net/briblue/article/details/54973413)

### JAVA类加载器

- **Bootstrap ClassLoader** 最顶层的加载类，主要加载核心类库，%JRE_HOME%\lib下的rt.jar、resources.jar、charsets.jar和class等。另外需要注意的是可以通过启动jvm时指定-Xbootclasspath和路径来改变Bootstrap ClassLoader的加载目录。比如`java -Xbootclasspath/a:path`被指定的文件追加到默认的bootstrap路径中。我们可以打开我的电脑，在上面的目录下查看，看看这些jar包是不是存在于这个目录。
- **Extention ClassLoader** 扩展的类加载器，加载目录%JRE_HOME%\lib\ext目录下的jar包和class文件。还可以加载`-D java.ext.dirs`选项指定的目录。
- **Appclass Loader也称为SystemAppClass**，加载当前应用的classpath的所有类。
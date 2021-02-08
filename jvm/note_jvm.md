# 一、JVM 概述

## 1.1 什么是虚拟机

虚拟机（Virtual Machine）指一台虚拟的计算机，具体来说是一款用来执行一系列虚拟计算机指令的**软件**。

虚拟机大体上可以分为两类：

* ==系统虚拟机==

  如 Visual Box，VMware等我们经常使用的虚拟机就属于系统虚拟机，它们**完全是对物理计算机的仿真**，提供了一个可运行完整操作系统的软件平台。

* ==程序虚拟机==

  典型代表：Java 虚拟机，它**专门为执行单个计算机程序而设计**，在 Java 虚拟机中执行的指令我们称为 Java 字节码指令。

无论是系统虚拟机还是程序虚拟机，在上面运行的软件都被限制于虚拟机提供的资源中。

## 1.2 什么是  Java 虚拟机

Java 虚拟机（Java Virtual Machine，JVM）是**一台执行 Java 字节码的虚拟计算机**，具体来说就是二进制字节码的运行环境，它负责**装载字节码到其内部，解释 / 编译为对应平台上的机器指令**执行。

每条 JVM 指令，JVM 规范中都有详细定义，如怎么取操作数，怎么处理操作数，处理结果应该放在哪等。

* [官方规范文档](https://docs.oracle.com/javase/specs/index.html)

### 1.2.1 JVM 的特点

* 一次编译，到处运行；
* 自动内存管理；
* 自动垃圾回收功能；

这些特点有其优点也有其缺点，缺点例如无法像c++开发者一样拥有强大的垃圾管理能力。

### 1.2.2 JVM 的位置

Java 虚拟机运行操作系统之上，与硬件没有直接交互。

<img src="E:\MyLearning\study_notes\jvm\pictures\概述\JVM的位置.jpg" alt="JVM的位置" style="zoom: 33%;" />

在 Java 的技术体系中，JVM 所处的位置如下图

<img src="E:\MyLearning\study_notes\jvm\pictures\概述\java技术体系.jpg" alt="java技术体系" style="zoom: 50%;" />

JDK（Java Development Kit）包括：Java 程序设计语言、Java虚拟机、Java类库，它是用于支持 Java程序开发的最小环境。

JRE（Java Runtime Environment）包括：Java 类库 API 中的 Java SE 子集和 Java 虚拟机，它是支持 Java 程序运行的标准环境。

更直观地：

<img src="E:\MyLearning\study_notes\jvm\pictures\概述\jdk、jre、jvm关系图.jpg" alt="jdk、jre、jvm关系图" style="zoom: 67%;" />

### 1.2.3 JVM 的中立特性

#### 1️⃣平台无关性

应该注意到，JVM 实际上是在不同系统上构建了一个统一的系统平台，而且不同的系统安装的 JVM 是有区别的， 如下图 Java 程序的运行过程，这也就不难理解为什么 “Write Once，Run Anywhere” 是 JVM 的特点之一了。

>  .java（java源文件）👉 .class（字节码文件，通过javac（前端编译器）编译完成） 👉 jvm

<img src="E:\MyLearning\study_notes\jvm\pictures\概述\jvm的中立特性-平台无关性.jpg" alt="jvm的中立特性-平台无关性" style="zoom:33%;" />

#### 2️⃣语言无关性

Java 虚拟机的起始原材料：**字节码文件（class file）**。

字节码文件可由**不同的编程语言经不同的编译器编译**后生成，如Kotlin、Jython、JavaScript等。

> Java语言中的各种语法、关键字、常量变量和运算符号的语义最终都会由多条字节码指令组合来表达，这决定了字节码指令所能提供的语言描述能力必须必Java语言本身更加强大才行。因此，有一些Java语言本身无法有效支持的语言特性并不代表在字节码中也无法有效表达出来，这为其他程序语言实现一些有别于Java的语言特性提供了发挥空间。

Java 虚拟机**只关心字节码文件是否符合它的规范**。只要编程语言的编译结果满足并包含 Java 虚拟机的内部指令集、符号表以及其它的辅助信息，那这个编译后生成的文件就是一个有效的字节码文件，就能够被 JVM 所识别并装载运行。

<img src="E:\MyLearning\study_notes\jvm\pictures\概述\jvm的中立特性-语言无关性.jpg" alt="jvm的中立特性-语言无关性" style="zoom:33%;" />

## 1.3 JVM 的整体结构

以 HotSpot VM 为例，它采用**解释器与即时编译器并存（体现：执行引擎）**的架构：

<img src="E:\MyLearning\study_notes\jvm\pictures\概述\Jvm架构的简图.jpg" alt="Jvm架构的简图" style="zoom: 50%;" />

* 类加载器子系统：用于把字节码文件装载到内存，即运行时数据区（过程：加载->链接->初始化）；
* 运行时数据区：将类装载器子系统传进来的东西生成一个大的class对象；
* 执行引擎：包含解释器、JIT编译器（后端编译器，可把<u>热点代码（需反复执行的代码）</u>直接地编译为机器指令）和垃圾回收器，用于**将高级语言翻译为机器语言**；

![Java代码执行流程【具体】](E:\MyLearning\study_notes\jvm\pictures\概述\Java代码执行流程【具体】.jpg)

## 1.4 JVM 的架构模型

JVM 的架构模型可分为两种：

* ==基于栈的指令集架构==

  Hot Spot 虚拟机就基于这种架构，具体来说是 **Java 编译器输入的指令流基本上就是这种架构**。

  特点：

  * **设计和实现更简单**，基本上就是（方法执行）入栈和（方法结束）出栈的操作，适用于资源受限的系统（嵌入式的一些设备，如机顶盒、打印机等）；

  * 避开了寄存器的分配难题：**使用零地址指令方式分配**，可以说基于栈式架构的指令集是以零地址指令为主的；

    >  一般完成一个指令的执行需要两个部分：地址+操作数，零地址就是指没有地址。
    >
    > 这里没有地址的原因是基于栈式架构的编译器主要对栈顶元素进行操作就已足够。

  * 不需要硬件支持，可移植性更好，**更好实现跨平台**；

  * 由于零地址指令的对齐方式是每 8 位单字节对齐，而寄存器是 16 位双字节的，故其**指令集更小，编译器容易实现**；

* ==基于寄存器的指令集架构==

  典型的应用是 x86 的二进制指令集：比如传统的 PC 以及 Android 的 Davlik 虚拟机；

  特点：

  * 指令集架构则**完全依赖硬件，可移植性差**；
  * **性能优秀和执行更高效**；
  * 在大部分情况下，基于寄存器架构的**指令集**往往都以一地址指令、二地址指令和三地址指令为主；
  * 可以**花费更少的指令**去完成一项操作；

### 举例：对比寄存器架构和栈式架构执行同样代码所需指令数

编译 2+3 这个逻辑操作的代码：

```java
int i = 2;
int j = 3;
int k = i + j;
```

编译后得到一个 StackStruTest.class 的字节码文件，在 Terminal 中进入到这个字节码文件的位置，输入**反编译**的命令：`javap -v StackStruTest.class`

在控制台输出的基于栈式架构的 JVM 中所执行的指令计算流程如下：

```
0: iconst_2	// 生成一个常量2
1: istore_1	// 保存到操作数栈（索引1）
2: iconst_3	// 生成一个常量3
3: istore_2	// 保存到操作数栈（索引2）
4: iload_1	// 加载i
5: iload_2	// 加载j
6: iadd		// 执行加法操作
7: istore_3	// 保存结果到操作数栈（索引3）
8: return
```

而在基于寄存器架构的 JVM 中所执行的指令计算流程如下：

```
mov eax,2 //将eax寄存器的值设为1
add eax,3 //使eax寄存器的值加3
```

### 总结

由于跨平台性的特性，Java 的指令都是根据栈来设计的。同时不同平台的 CPU 架构不同，所以不能设计为基于寄存器架构。

## 1.5 JVM 的生命周期

### 1️⃣ 虚拟机的启动

Java 虚拟机的启动是通过引导类加载器（bootstrap class loader）创建一个初始类（initial class）来完成的，这个类是由虚拟机的具体实现指定的。

### 2️⃣ 虚拟机的执行

* 一个运行中的 Java 虚拟机有着一个清晰的任务：**执行 Java 程序**；
* 程序开始执行时 JVM 运行，程序结束时 JVM 停止；
* 执行一个 Java程序 的时候，真正在执行的是一个叫做 **Java 虚拟机的进程**；

### 3️⃣ 虚拟机的退出

常见的退出情况如下：

* 程序正常执行结束；

* 程序在执行过程中遇到了异常或错误而终止；

* 由于操作系统出现错误而导致 Java 虚拟机进程终止；

* 某线程调用 Runtime 类或 System 类的 exit 方法，或者是直接调用 Runtime 类的 halt 方法，并且 Java 安全管理器也允许这次 exit 或 halt 操作；

  这种情况最终都是调用 Shutdown 类里的 `static native void halt0(int status)` 这个方法来结束进程。

* 除此之外，JNI（Java Native Interface）规范描述了用 JNI Invocation API 来加载或卸载 Java 虚拟机时，Java 虚拟机的退出情况。

# 二、类加载机制

把描述类的数据从 Class 文件加载到内存，并对数据进行校验、转换解析和初始化，最终形成可以被虚拟机直接使用的 Java 类型，这个过程被称作虚拟机的类加载机制。

类是在运行期间第一次使用时动态加载的，而不是一次性加载所有类，因为如果一次性加载，那么会占用很多内存。

实际上，在 Java 中，类的加载、连接和初始化过程都是在程序运行期间完成的，这样虽然不好进行提前编译，且会让类加载产生一定的性能开销，但却为 Java 应用提供了极高的扩展性、灵活性，Java 可以动态扩展的语言特性就是依赖运行期动态加载和动态连接这个特点实现的。

## 2.1 类的生命周期

一个类的完整生命周期如下：

![类的生命周期](E:\MyLearning\study_notes\jvm\pictures\类加载机制\类的生命周期.jpg)

注意：加载、验证、准备、初始化和卸载这五个阶段的顺序是确定的，类或接口的加载过程必须按照这种顺序按部就班地开始（强调 “开始” 而不是 “进行” 或 “完成” 是因为这些阶段通常都是互相交叉地混合进行的，会在一个阶段执行的过程中调用、激活另一个阶段），而解析阶段则不一定：它在某些情况下可以在初始化阶段之后再开始，这是为了支持Java语言的运行时绑定特性（也称动态绑定或晚期绑定）。

## 2.2 类加载的过程

在类加载机制中首先进行的就是类的加载过程，包含加载、验证、准备、解析和初始化这 5 个阶段。如图：

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\类加载器子系统.jpg" alt="类加载器子系统" style="zoom:67%;" />

可以把 JVM 对 Class 文件进行 `加载 → 连接 → 初始化` 这些操作的代码虚拟化为一个模块，称为类加载器子系统。

> ==注意==：`加载 → 连接 → 初始化` 中的加载（ Loading ）阶段是整个类加载（ Class Loading ）过程中的一个阶段。
>
> ==另外==：这个 Class 文件并不特指某个存在于具体磁盘中的文件，而应为一串二进制字节流，其存在形式包括但不限于磁盘文件、网络、数据库、内存或者动态产生等。

### 1️⃣ 加载阶段

在加载阶段，Java 虚拟机需完成以下三件事：

1. 通过一个类的全限定名来获取定义此类的二进制字节流（文件加载到内存中的必经之路）；
2. 将这个字节流所代表的的静态存储结构转化为方法区（虚拟概念，jdk 7 之前为永久代，jdk 7 之后为源空间，它们都可称为方法区）的运行时数据结构；
3. **在内存中生成一个代表这个类的 java.lang.Class 对象**（即生成了一个大的 Class 文件的实例），作为方法区这个类的各种数据的访问入口；

由于《 Java 虚拟机规范 》对以上三点只是作了一个笼统的要求，虚拟机实现与 Java 应用的灵活度还是相当大的。

比如，具体加载 Class 文件的方式可以有：

- 从本地系统中直接加载；
- 通过网络获取，典型场景：Web Applet；
- 从 ZIP 压缩包中读取，成为日后 JAR、EAR、WAR 格式的基础（第三方jar包中存储的其实就是字节码文件）
- 运行时计算生成，使用最多的技术是：动态代理技术；
- 由其它文件生成，典型场景：JSP应用；
- 从专有数据库中提取.class文件，比较少见；
- 从加密文件中获取，典型的防Class文件被反编译的保护措施；

### 2️⃣ 验证阶段

==目的==：**确保 Class 文件的字节流中包含信息符合当前虚拟机要求，保证被加载类的正确性，不会危害虚拟机自身安全**。

从整体上看，验证阶段大致会完成四个阶段的验证动作：

* **文件格式验证**；

  验证的是字节流是否符合 Class 文件格式的规范，并且能被当前版本的虚拟机处理，如不符合，就应当抛出一个 java.lang.Verify Error 异常或其子类异常。

  其主要目的是保证输入的字节流能正确地解析并存储于方法区内，格式上符合描述一个 Java 类型信息的要求。

  基于二进制字节流进行，通过验证后这段字节流才能进入 Java 虚拟机内存的方法区中进行存储，后面的三个验证阶段都不再直接读取、操作字节流（基于方法区的存储结构进行）。

  包括的验证点如：

  * 是否以魔数 0xCAFEBABE 开头；
  * 主、次版本号是否在当前 Java 虚拟机接受范围之内；
  * ......

* **元数据验证**；

  对字节码描述的信息进行语义分析，参考的是《 Java语言规范 》的要求。

  其主要目的是对类的元数据信息（数据类型、...）进行语义校验。

  包括的验证点如：

  * 此类是否有父类；
  * 此类的父类是否继承了不允许被继承的类（被 final 修饰的类）；
  * ......

* **字节码验证**；

  验证阶段最复杂的一个子阶段。

  其主要目的是通过数据流分析和控制流分析，确定程序语义是合法的、符合逻辑的、不危害虚拟机安全的。

  主要是对类的方法体（ Class 文件的 Code 属性）进行校验，包括的验证点如：

  * 保证任意时刻操作数栈的数据类型与指令代码序列都能配合工作，如不会出现类似于 “ 在操作栈放置了一个 int 类型的数据，使用时却按 long 类型来加载入本地变量表中 ” 这样的情况；
  * 保证任何跳转指令都不会跳转到方法体以外的字节码指令上；
  * ......

  另外要注意的是，通过程序去校验程序逻辑是无法做到绝对准确的，不可能用程序来准确判定一段程序是否存在 Bug ，这涉及到了 “ 停机问题 ”。

* **符号引用验证**；

  验证该类是否缺少或者被禁止访问它依赖的某些外部类、方法、字段等资源。

  发生在虚拟机将符号引用转化为直接引用的时候，这个转化动作将在连接的第三阶段——解析阶段中发生。

  其主要目的是确保解析行为能被正常执行，若无法通过符号引用验证，会抛出 java.lang.IncompatibleClassChangeError 子类异常，如 java.lang.NoSuchMethodError 等。

  包括的验证点如：

  * 符号引用中的类、字段、方法的可访问性（private、protected、public、<package>）是否可被当前类访问；
  * 符号引用中通过字符串描述的全限定名是否能找到对应的类；
  * ......

### 3️⃣ 准备阶段

类变量是被 static 修饰的变量，准备阶段为类变量分配内存并设置初始值，使用的是方法区（逻辑上的概念）的内存。

实例变量不会在准备阶段分配内存，它会在对象实例化时随着对象一起分配在 Java 堆中。应该注意到，实例化不是类加载的一个过程，类加载发生在所有实例化操作之前，并且类加载只进行一次，实例化可以进行多次。

初始值通常是数据类型的零值，例如下面的类变量 value 在准备阶段分配内存并初始化为 0 而非赋值为 123。

```java
private static int value = 123;
```

如果类变量是常量，那么它将在编译时分配内存，在准备阶段初始化为表达式所定义的值而不是 0。例如下面的常量 value 被初始化为 123 而不是 0。

```java
private static final int value = 123;
```

Java 基本数据类型的零值：

* byte：(byte) 0；
* short：(short) 0；
* int：0；
* long：0L；
* float：0.0f；
* double：0.0d；
* char：\u0000；
* boolean：false；
* reference（引用类型）：null；

### 4️⃣ 解析阶段

Java 虚拟机将常量池内的符号引用替换为直接引用的过程。

解析过程在某些情况下可以在初始化阶段之后再开始，这是为了支持 Java 的动态绑定。

### 5️⃣ 初始化阶段

初始化阶段真正开始执行类中定义的 Java 程序代码，实际上就是执行类构造器方法：`<clinit>()` 的过程。

`<clinit>()` 类构造器方法无需定义，它是 javac 编译器自动收集类中的**所有类变量**的赋值动作和**静态代码块**中的语句合并而来的。编译器收集的顺序是由语句在源文件中出现的顺序决定的。

编译此类：

```java
public class ClassInitTest {
	private static int a = 1;
	public static void main(String[] args) {
		System.out.println(ClassInitTest.a);
	}
}
```

用 jclasslib 查看它的字节码文件，可以看到类构造器方法 `<clinit>()` 出现了：

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\clinit.jpg" alt="clinit" style="zoom: 80%;" />

重新编译：

```java
public class ClassInitTest {
	private static int a = 1;

	static{
		a = 2;
		num = 20;	//	可以赋值但不能调用，否则报错：非法的前向引用
		System.out.println(a);
	}
	//linking的prepare阶段：num = 0 -->initial时执行clinit方法（按顺序）：num = 20 --> num = 10
	private static int num = 10;
	public static void main(String[] args) {
		System.out.println(ClassInitTest.a);
		System.out.println(num);
	}
}
```

用 jclasslib 查看它的字节码文件，查看其类构造器方法 `<clinit>()`，可以看到指令是按语句在源文件中出现的顺序执行的：

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\clinit初始化顺序.jpg" alt="clinit初始化顺序" style="zoom:80%;" />

`<clinit>()` 方法不同于类的构造函数（即虚拟机视角下的 `<init>()` 方法）。

编译此类：

```java
public class ClinitTest {
	//  任何一个类声明后，内部至少存在一个类的构造器
	//  唯一没有构造器的类：匿名内部类
	private int a = 2;
	public static void main(String[] args) {
		int b = 1;
	}
}
```

用 jclasslib 查看它的字节码文件，发现它并没有 `<clinit>()` 方法，但是却有构造器 `<init>` ，这是因为此类中没有静态变量或静态代码块，无可赋值操作 ，且任何一个类声明后，内部至少存在一个类的构造器。

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\init.jpg" alt="init" style="zoom:80%;" />

若该类具有父类，JVM会保证子类的 `<clinit>()` 执行前，父类的 `<clinit>()` 已经执行完毕，因此在 JVM 中第一个被执行的 `<clinit>()` 方法的类型肯定是 java.lang.Object 。

编译此类：

```java
public class ClinitTest1 {
	//  静态内部类 | 父类
	static class Father{
		public static int A = 1;
		static {
			A = 2;
		}
	}

	// 静态内部类 | 子类
	static class Son extends Father{
		public static int B = A;
	}

	public static void main(String[] args) {
		//  先按序加载父类的clinit，再加载子类的clinit；
		System.out.println(Son.B);	// 2
	}
}
```

用 jclasslib 查看它的字节码文件，查看其类构造器方法 `<clinit>()`，发现先执行父类的类构造器方法，再执行子类的类构造器方法：

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\clinit父子类.jpg" alt="clinit父子类" style="zoom:80%;" />

Java 虚拟机必须保证一个类的 `<clinit>()` 方法在多线程下被同步加锁，如果多个线程同时初始化一个类，只会有一个线程执行这个类的 `<clinit>()` 方法，其它线程都会阻塞等待，直到活动线程执行 `<clinit>()` 方法完毕（值得注意的是，如果执行 `<clinit>()` 方法的那条线程退出 `<clinit>()` 方法后，其他线程唤醒后则不会再次进入 `<clinit>()` 方法。同一个类加载器下，一个类型只会被初始化一次。）。

因为 `<clinit>（）` 方法是带锁线程安全，所以在多线程环境下进行类初始化的话可能会引起死锁，并且这种死锁很难被发现。

```java
public class DeadThreadTest {
	public static void main(String[] args) {
		Runnable r = () -> {
			System.out.println(Thread.currentThread().getName() + "开始");
			DeadThread dead = new DeadThread();
			System.out.println(Thread.currentThread().getName() + "结束");
		};

		Thread t1 = new Thread(r,"线程1");
		Thread t2 = new Thread(r, "线程2");

		t1.start();
		t2.start();
	}
}
class DeadThread{
	// 模拟clinit方法在多线程下被虚拟机同步加锁过程
	static {
		if(true){
			//  只会执行一次
			System.out.println(Thread.currentThread().getName() + "初始化当前类");
			while (true){

			}
		}
	}
}
```

接口中不可以使用静态语句块，但仍然有类变量初始化的赋值操作，因此接口与类一样都会生成 `<clinit>()` 方法。但接口与类不同的是，执行接口的 `<clinit>()` 方法不需要先执行父接口的 `<clinit>()` 方法。只有当父接口中定义的变量使用时，父接口才会初始化。另外，接口的实现类在初始化时也一样不会执行接口的 `<clinit>()` 方法。

特别注意的是，静态语句块只能访问到定义在它之前的类变量，定义在它之后的类变量只能赋值，不能访问。例如以下代码：

```java
public class Test {
    static {
        i = 0;                // 给变量赋值可以正常编译通过
        System.out.print(i);  // 这句编译器会提示“非法向前引用”
    }
    static int i = 1;
}
```

## 2.3 类加载器

==类加载器实际上是一段代码==，这段代码实现了==把类加载阶段中的 “ 通过一个类的全限定名来获取描述该类的二进制字节流 ” 这个动作放到 Java 虚拟机外部去实现，以便让应用程序自己决定如何去获取所需的类==这个动作。

### 扮演的角色

类加载器（ClassLoader）在 ` Class 文件 → JVM → DNA 元数据模板 ` 这个过程中扮演着一个运输工具的角色，DNA 元数据模板其实是加载到 JVM 中的 .class 文件的称号，我们可以根据这个元数据模板实例化出 n 个一模一样的实例。

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\类加载器角色.jpg" alt="类加载器角色" style="zoom: 80%;" />

ClassLoader **只负责 Class 文件的加载**（即加载到内存中生成一个大的 Class 实例对象），至于它是否可运行，则由 Execution Engine 决定。

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\类的加载过程.jpg" alt="类的加载过程" style="zoom: 50%;" />

加载的类信息存放于一块称为**方法区**的内存空间。除了类的信息外，方法区还会存放运行时常量池信息，可能还包括字符串字面量和数字常量（这部分常量池信息是 Class 文件中常量池部分的内存映射，即当 Class 文件被加载到内存后，这部分信息便成为运行时常量池信息）。

### 类与类加载器

比较两个类相等的条件是类本身相等，同时使用的类加载器是同一个。这是因为每个类加载器都拥有一个独立的类名称空间。

这里的相等，包括类的 Class 对象的 equals() 方法、isAssignableFrom() 方法、isInstance() 方法的返回结果为 true，也包括使用 instanceof 关键字做对象所属关系判定结果为 true。

### 类加载器分类

在 Java 虚拟机规范中类的加载器被分为两类：

* 引导（启动）类加载器（Bootstrap ClassLoader），使用 C/C++ 实现，是虚拟机自身的一部分，因此无法通过其子类加载器获取到，无父类加载器。
* 所有其它的类加载器，使用 Java 实现，独立于虚拟机，继承自抽象类 java.lang.ClassLoader。

从 Java 开发人员的角度看，类加载器可以划分得更细致一些：

* 引导（启动）类加载器（Bootstrap ClassLoader）：此类加载器负责将**存放在 <JRE_HOME>\lib 目录中的，或者被 -Xbootclasspath 参数所指定的路径中的，并且是虚拟机识别的（仅按照文件名识别，如 rt.jar，名字不符合的类库即使放在 lib 目录中也不会被加载）类库**加载到虚拟机内存中（**即只加载 Java 的核心类库，用于提供 JVM 自身需要的类**）。引导类加载器无法被 Java 程序直接引用，用户在编写自定义类加载器时，如果需要把加载请求委派给引导类加载器，直接使用 null 代替即可。作为扩展类加载器和应用程序类加载器的父加载器，负责加载这两个加载器。出于安全考虑，Bootstrap 启动类加载器只加载包名为 java、javax、sun 等开头的类。
* 扩展类加载器（Extension ClassLoader）：这个类加载器是由 ExtClassLoader（sun.misc.Launcher$ExtClassLoader）实现的。它负责将 <JAVA_HOME>/lib/ext 或者被 java.ext.dir 系统变量所指定路径中的所有类库加载到内存中，开发者可以直接使用扩展类加载器。如果用户创建的 JAR 放在此目录下，也会自动由扩展类加载器加载。
* 应用程序类加载器（Application ClassLoader）：这个类加载器是由 AppClassLoader（sun.misc.Launcher$AppClassLoader）实现的。由于这个类加载器是 ClassLoader 中的 getSystemClassLoader() 方法的返回值，因此一般称为系统类加载器。它负责加载环境变量 classpath 或系统属性 java.class.path 指定路径下的类库，开发者可以直接使用这个类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。



几个类加载器的包含关系如图：

<img src="E:\MyLearning\study_notes\jvm\pictures\类加载机制\几个类加载器包含关系.jpg" alt="几个类加载器包含关系" style="zoom:50%;" />

试图获取扩展类加载器的父类加载器，但获取不到，因为其父类加载器是引导类加载器：

```java
ClassLoader bootstrapClassLoader = extClassLoader.getParent();
System.out.println(bootstrapClassLoader);//null
```

试图 String 类的类加载器，但获取不到，因为其加载器是引导类加载器：

```java
ClassLoader classLoader1 = String.class.getClassLoader();
System.out.println(classLoader1);//null
```

获取系统类加载器的父类加载器，得到扩展类加载器：

```java
ClassLoader extClassLoader = systemClassLoader.getParent();
//输出：全限定名$类型@对象地址
System.out.println(extClassLoader);//	sun.misc.Launcher$ExtClassLoader@1b6d3586
```

获取系统类加载器：

```java
ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
System.out.println(systemClassLoader);//sun.misc.Launcher$AppClassLoader@18b4aac2
```

对于用户自定义类来说，默认使用系统类加载器进行加载：

```java
ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
System.out.println(classLoader);//sun.misc.Launcher$AppClassLoader@18b4aac2
```

### 自定义类加载器

开发者需要自定义类加载器的可能原因有如下几个：

* 隔离加载类（框架中使用中间件时有时需隔离中间，进行类的仲裁，防止类冲突….）；
* 修改类加载的方式；
* 扩展加载源（其它字节码的来源如数据库、电视机机顶盒、...）；
* 防止源码泄漏（对字节码文件加密后自己要用得解密，就可以用自定义类加载器）；

实现方法：

自定义自己的类加载器继承自抽象类 java.lang.ClassLoader ，将自定义的类加载逻辑写在 findClass() 方法里。如果没有过于复杂的需求，可以直接继承 URLClassLoader 类，这样就可以避免自己去编写 findClass() 方法及其获取字节码流的方式，使自定义类加载器编写更加简洁。

实现的大致参考步骤：

```java
public class CustomClassLoader extends ClassLoader{
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        try{
            //  根据指定路径将对应的类以二进制字节流的方式读取
            byte[] result = getClassFromCustomPath(name);
            if (result == null){
                throw new FileNotFoundException();
            }else {
                return defineClass(name, result, 0, result.length);
            }
        }catch (FileNotFoundException e){
            e.printStackTrace();
        }
        throw new ClassNotFoundException(name);
    }

    private byte[] getClassFromCustomPath(String name){
        //  从自定义路径中加载指定类，细节略。
        //  如果指定路径的字节码文件进行了加密，则需要在此解密。
        return null;
    }

    public static void main(String[] args) {
        CustomClassLoader customClassLoader = new CustomClassLoader();
        try{
            Class<?> clazz = Class.forName("One", true, customClassLoader);
            Object obj = clazz.newInstance();
            System.out.println(obj.getClass().getClassLoader());
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}
```

### 获取 ClassLoader 的几种途径

获取当前类的 ClassLoader：

```java
ClassLoader classLoader = Class.forName("java.lang.String").getClassLoader();
```

获取当前线程上下文的 ClassLoader：

```java
ClassLoader classLoader = Thread.currentThread().getContextClassLoader();
```

获取系统类加载器：

```java
ClassLoader classLoader = ClassLoader.getSystemClassLoader();
```

获取调用者的 ClassLoader：

```java
DriverManager.getCallerClassLoader();
```
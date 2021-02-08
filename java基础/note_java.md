# 一、 面向对象

## 引用与对象

Java 里（几乎）一切皆对象，操纵对象可以通过标识符进行，这个标识符实际上是对象的一个 “ 引用 ” 。

引用并不一定需要有对象与它关联，例如：

``````java
String s;
``````

就表明创建了一个 String 引用，但此时这个**引用没有与某个对象相关联**，直接使用的话会返回**运行时错误**，所以比较安全的做法是：**创建引用的同时进行初始化**，例如：

```java
String s = new String("abcd");
```

## 对象的作用域

以下说明不适用于基本类型，基本类型有自己的生命周期。

在下方代码中，引用 s 在作用域终点消失，即 `}` ，但 s 指向的 String 对象仍继续占据内存空间，尽管无法继续用 s 访问它。

```java
{
	String s = new String("a string");
}	//	End of scope
```

Java 中的垃圾回收器会监视用 new 创建的所有对象，并辨别出那些不会再被引用的对象后释放这些对象的内存空间，以供其他新对象使用，从而消除了 “ 内存泄漏 ” 问题。

## 高内聚、低耦合

高内聚：模块内部的代码相互之间联系性高，尽可能独立地完成自己的功能，不依赖于其它模块的代码。

低耦合：各模块之间相互联系的紧密程度低，模块与模块之间的接口，尽量的少而简单。如果某两个模块间的关系比较复杂的话，最好首先考虑进一步的模块划分。这样有利于修改和组合。

* [3分钟Tips：用大白话告诉你什么是低耦合|高内聚](https://y1ran.blog.csdn.net/article/details/79489180?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control)
* [面向对象原则：高内聚、低耦合。多聚合、少继承](https://blog.csdn.net/kingscoming/article/details/78836229)

# 二、数据类型

## 基本类型与包装器类型

| 基本类型 | 大小 |     最小值      |          最大值          | 包装器类  |
| :------: | :--: | :-------------: | :----------------------: | :-------: |
|   byte   | 8位  |      -128       |           +127           |   Byte    |
|   char   | 16位 |    Unicode o    | Unicode 2<sup>16</sup>-1 | Character |
|  short   | 16位 | -2<sup>15</sup> |    +2<sup>15</sup>-1     |   Short   |
|   int    | 32位 | -2<sup>31</sup> |    +2<sup>31</sup>-1     |  Integer  |
|  float   | 32位 |     IEEE754     |         IEEE754          |   Float   |
|   long   | 64位 | -2<sup>63</sup> |    +2<sup>63</sup>-1     |   Long    |
|  double  | 64位 |     IEEE754     |         IEEE754          |  Double   |
|   void   |  —   |        —        |            —             |   Void    |
| boolean  |  —   |        —        |            —             |  Boolean  |

boolean 只有两个值：true、false，可以用 1 bit 来存储，但是**具体大小没有明确规定**。另外，JVM 会在编译时将 boolean 类型的数据转化为 int，使用 1 来表示 true，0 表示 false。JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。

- [Primitive Data Types](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)
- [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se8/jvms8.pdf)



基本类型对应的包装器类如上表，基本类型与其对应的包装类之间的赋值使用自动装箱与拆箱完成。

装箱：将原始值（例如`int`）转换为对应的包装器类（`Integer`）的对象

拆箱：将包装器类型（`Integer`）的对象转换为其对应的原始（`int`）值

```java
Integer x = 2;	//	装箱 运行时调用了 Integer.valueOf(2)
int y = x;		//	拆箱 运行时调用了 X.intValue()
```

* [Autoboxing and Unboxing](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)

## 高精度数字

两个用于高精度计算的类：BigInteger 和 BigDecimal，无对应的基本类型。

* ==BigInteger==：支持任意精度的整数，在运算中可以**准确地表示任何大小的整数值，而不会丢失任何信息**；

* ==BigDecimal==：支持任何精度的定点数，可以用它进行**精确的货币运算**；

## 基本类型与对象

**基本类型的变量**属于比较小、简单的变量，通过 new 将对象存储在堆里往往不是很有效，所以 Java 对于这种类型的对象采用创建一个不是引用的 “ 自动 ” 变量的方法，这个变量**直接存储 “ 值 ”，并置于堆栈中**，因此**更加高效**。

可以创建基本类型对应的包装器类来在堆中创建一个非基本对象，用来表示对应的基本类型。

## 缓存池

new Integer(123) 与 Integer.valueOf(123) 的区别：

* new Integer(123) 每次都会新建一个对象；
* Integer.valueOf(123) 会用缓存池中的对象，多次调用会取得同一个对象的引用；

```java
Integer a = new Integer(123);
Integer b = new Integer(123);
System.out.println(a == b);		//	false

Integer c = Integer.valueOf(123);
Integer d = Integer.valueOf(123);
System.out.println(c == d);		//	true
```

Integer.valueOf() 方法的实现比较简单，每次取值时会先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

```java
public static Integer valueOf(int i){
	if(i >= IntegerCache.low && i <= IntegerCache.high)
		return IntegerCache.cache[i + (-IntegerCache.low)];
	return new Integer(i);			
}
```

在 Java 8 中，Integer 缓存池的大小默认为 -128 ~ 127。

```java
static final int low = -128;
static final int high;
static final Integer cache[];

static {
    // high value may be configured by property
    int h = 127;
    String integerCacheHighPropValue =
        sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
    if (integerCacheHighPropValue != null) {
        try {
            int i = parseInt(integerCacheHighPropValue);
            i = Math.max(i, 127);
            // Maximum array size is Integer.MAX_VALUE
            h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
        } catch( NumberFormatException nfe) {
            // If the property cannot be parsed into an int, ignore it.
        }
    }
    high = h;

    cache = new Integer[(high - low) + 1];
    int j = low;
    for(int k = 0; k < cache.length; k++)
        cache[k] = new Integer(j++);

    // range [-128, 127] must be interned (JLS7 5.1.7)
    assert IntegerCache.high >= 127;
}
```

编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

基本类型对应的缓存池如下：

* boolean values true and false
* all byte values
* short values between -128 and 127
* int values between -128 and 127
* char in the range \u0000 to \u007F

在使用这些基本类型对应的包装类型时，如果该数值范围在缓存池范围内，就可以直接使用缓存池中的对象。

在 jdk 1.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是 127，但是这个上界是可调的，在启动 JVM 的时候，通过 -XX:AutoBoxCacheMax=<size> 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。

[StackOverflow : Differences between new Integer(123), Integer.valueOf(123) and just 123](https://stackoverflow.com/questions/9030817/differences-between-new-integer123-integer-valueof123-and-just-123)

# 三、字符串

## 概述

String 对象不可变，查看 JDK 文档可知，String 其实是被声明为 final ，因此它不可被继承。（Integer 等包装类也不能被继承）

在 Java 8 中，String 内部用 char 数组存储数据：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

在 Java 9 之后，String 类的实现改用 byte 数组存储字符串，同时使用 `coder` 来标识使用了哪种编码：

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```

改成 byte 数组存储字符串的目的是**节省空间**，我们知道一个 char 字符占 16 位，2 个字节，而一个 byte 只占 8 位，1 个字节，故在存储单字节编码内的字符（占一个字节的字符）的情况下可减少一半的内存，只有当一个char字符大小超过0xFF时，才会将byte数组变为原来的两倍，用两个字节存储一个char字符。

新属性 coder 的作用是，在计算字符串长度或者使用 indexOf() 函数时，我们需要根据这个字段，判断如何计算字符串长度。coder 属性默认有 0 和 1 两个值，0 代表 Latin-1(单字节编码)，1 代表 UTF-16。如果 String 判断字符串只包含了 Latin-1，则 coder 属性值为 0，反之则为 1。

value 数组被声明为 final，说明 value 数组被初始化后便不能再引用其它数组，并且 String 内部没有改变 value 数组的方法，故可以保证 String 的不可变性。



## 用于 String 的 “+” 和 “+=”

用于字符串拼接的 “ + ” 与 “ += ” 是 Java 中仅有的两个重载过的操作符，同时注意：Java 不允许程序员重载任何操作符。

由于 String 对象的不可变性，String 对象的拼接带来了一定的效率问题（注意，不可变性不仅只带来拼接的效率问题）。

```java
public class Concatenation {
    public static void main(String[] args) {
        String mango = "mango";
        String s = "abc" + mango + "def" + 47;
        System.out.println(s);
    }
}
```

反编译以上代码，得到（只抽取重要部分）：

```
  public static void main(java.lang.String[]);
    Code:
       0: ldc           #2                  // String mango
       2: astore_1
       3: new           #3                  // class java/lang/StringBuilder
       6: dup
       7: invokespecial #4                  // Method java/lang/StringBuilder."<init>":()V
      10: ldc           #5                  // String abc
      12: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      15: aload_1
      16: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      19: ldc           #7                  // String def
      21: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      24: bipush        47
      26: invokevirtual #8                  // Method java/lang/StringBuilder.append:(I)Ljava/lang/StringBuilder;
      29: invokevirtual #9                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      32: astore_2
      33: getstatic     #10                 // Field java/lang/System.out:Ljava/io/PrintStream;
      36: aload_2
      37: invokevirtual #11                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      40: return
```

在这其中，编译器创建了一个 StringBuilder 对象，并为每个字符串调用一次 append() 方法，共四次。最后调用 toString() 生成最终结果，并存为 s （对应命令：aload_2）。

可以看到虽然源代码没有使用 StringBuilder 类，但编译器在 String 对象进行拼接时使用了它，原因是它更高效。

但是通过编译器来自动优化性能并不是一个一劳永逸的办法，我们应该更深入地看到编译器能优化到什么程度，是否还能提高更多的性能。

对比多个 String 和 StringBuilder 生成一个 String：

```java
public class WhitherStringBuilder {
    public String implicit(String[] fileds){
        String result = "";
        for (int i = 0; i < fileds.length; i++) {
            result += fileds[i];
        }
        return result;
    }
    public String explicit(String[] fileds){
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < fileds.length; i++) {
            result.append(fileds[i]);
        }
        return result.toString();
    }
}
```

反编译以上代码，得到（只抽取重要部分）：

* implicit() 方法对应字节码 

```
public java.lang.String implicit(java.lang.String[]);
    Code:
       0: ldc           #2                  // String
       2: astore_2
       3: iconst_0
       4: istore_3
       5: iload_3
       6: aload_1
       7: arraylength
       8: if_icmpge     38
      11: new           #3                  // class java/lang/StringBuilder
      14: dup
      15: invokespecial #4                  // Method java/lang/StringBuilder."<init>":()V
      18: aload_2
      19: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/Strin
gBuilder;
      22: aload_1
      23: iload_3
      24: aaload
      25: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/Strin
gBuilder;
      28: invokevirtual #6                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      31: astore_2
      32: iinc          3, 1
      35: goto          5
      38: aload_2
      39: areturn
```

* explicit() 方法对应字节码 

```
public java.lang.String explicit(java.lang.String[]);
    Code:
       0: new           #3                  // class java/lang/StringBuilder
       3: dup
       4: invokespecial #4                  // Method java/lang/StringBuilder."<init>":()V
       7: astore_2
       8: iconst_0
       9: istore_3
      10: iload_3
      11: aload_1
      12: arraylength
      13: if_icmpge     30
      16: aload_2
      17: aload_1
      18: iload_3
      19: aaload
      20: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/Strin
gBuilder;
      23: pop
      24: iinc          3, 1
      27: goto          10
      30: aload_2
      31: invokevirtual #6                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      34: areturn
```

在 implicit() 方法对应的字节码中，第 8 行到第 35 行构成一个循环体，重点注意 StringBuilder 是在这个循环体内构造的，这意味着每循环一次就会创建一个新的 StringBuilder 对象。

而在 explicit() 方法对应的字节码中，只生成了一个 StringBuilder 对象，这样一对比就知道 explicit() 方法的开销会更小一些。如果已经知道最终字符串的大小，那么还可以预先指定 StringBuilder 的大小，避免多次重新分配缓冲。

注意，多个 StringBuilder 对象用 append() 语句一点点拼接和类似这种直接用 append(a + ":" + c) 的写法拼接多个对象的性能是不一样的，后者编译器会为你另外创建一个 StringBuilder 对象处理括号内的字符串操作。

如果想清楚编译器会为你做什么以辨别使用哪种方式能提高性能，要学会用 ` javap -c 字节码文件名 ` 这个反编译命令来查看编译器的操作。
# 一、什么是 JUC

jdk 中包 java.util.concurrent 的缩写，实际上由三部分组成：

* java.util.concurrent：在并发编程中使用的工具包；
* java.util.concurrent.atomic：并发原子包；
* java.util.concurrent.locks：并发locks包；

## 进程与线程

进入到内存中的程序称为进程（也可以说是后台运行），它会占用一些内存来执行。

线程属于进程中的一个执行单元，是多线程操作系统能进行运算调度的最小单位，也是进程中实际运行的单位。

## 并发与并行

* 并发：

  两个或多个事件在同一时间段发生（交替执行）

* 并行：

  两个事件在同一时刻同时执行（各自同时执行）

并行速度更快；

# 二、多线程

## 创建线程的方法

### 匿名内部类

### 继承 Thread 类

### 实现 Runnable 接口



## synchronized 与 Lock


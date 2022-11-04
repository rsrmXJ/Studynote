# Java 面试题

### 基础

##### 第一章 Java 概述

**1.** java语言的特点是什么？

- 面向对象性：两个基本概念：类、对象；三大特性：封装、继承、多态

- 健壮性：吸收了 C/C++ 语言的优点，但去掉了其影响程序健壮性的部分（如指针、内存的申请与释放等），提供了一个相对安全的内存管理和访问机制

- 跨平台性：通过Java语言编写的应用程序在不同的系统平台上都可以运行。**“Write once , Run Anywhere”**

**2.** ystem.out.println()和System.out.print()什么区别呢？

- System.out.println();输出后，换行。 

- System.out.print();输出后，不换行。

**3.** 一个".java"源文件中是否可以包括多个类（不是内部类）？有什么限制？

可以。但最多只有一个类名可以被 public 修饰，且文件名相同必须与其相同。

**4.** Something 类的文件名可以叫 OtherThing.java？

```java
class Something {
    public static void main(String[] something_to_do) {        
        System.out.println("Do something ...");
    }
}
```

可以。没有规定说 Java 的 class 名字必须和其文件名相同。但 public 修饰 class 的名字必须和文件名相同。

**5.** 为什么要设置环境变量？

为了在任何文件路径下，都可以调用 jdk 指定目录下的指令。

**6.** JDK,JRE和JVM的关系是什么？

- JDK包含JRE，JRE包含JVM.
- JDK = JRE + 开发工具集

- JRE = JVM + Java SE标准类库

JVM Java Virtical Machine: Java 虚拟机，建立在操作系统上，不同的操作系统有不同的 JVM；Java 程序在 JVM 中运行

JRE Java Run everiment : Java 运行环境；包含了 JVM 

JDK Java Develop Kits: Java 开发工具包。包含 JRE + 开发工具包

**7.** 源文件名是否必须与类名相同？如果不是，那么什么情况下，必须相同?

源文件不一定要与类名相同。但是当类名用 public  class 修饰时，源文件名必须与类名相同。(一个源文件中至多有一个类可以用 public 修饰)

**8.** 程序中若只有一个 public 修饰的类，且此类含main方法。那么类名与源文件名可否不一致？

必须一致

**9.** Java的注释方式有哪几种，格式为何

- 单行注释：//

- 多行注释:/**/

- 文档注释: 

  ```
  /**
    *
    *
    *
    */
  ```

**10.** 自己使用 java 文档注释的方式编写程序，并用javadoc 命令解析

**11.** GC是什么? 为什么要有GC?

GC是垃圾收集的意思（Gabage Collection）,内存处理是编程人员容易出现问题的地方，忘记或者错误的内存回收会导致程序或系统的不稳定甚至崩溃，Java 提供的 GC 功能可以自动监测对象是否超过作用域从而达到自动回收内存的目的，Java 语言没有提供释放已分配内存的显示操作方法。

**12.** 垃圾回收器的基本原理是什么？垃圾回收器可以马上回收内存吗？有什么办法主动通知虚拟机进行垃圾回收?

对于 GC 来说，当程序员创建对象时，GC就开始监控这个对象的地址、大小以及使用情况。通常，GC采用有向图的方式记录和管理堆(heap)中的所有对象。通过这种方式确定哪些对象是"可达的"，哪些对象是"不可达的"。

当GC确定一些对象为"不可达"时，GC就有责任回收这些内存空间。可以。程序员可以手动执  System.gc()，通知GC运行，但是Java语言规范并不保证GC一定会执行。

**13.** 输出一个心形

##### 第二章 基本语法

**1.** 标识符的命名规则有哪些？

- 可以包含 26 个英文字母，数字 0~9.$和_
- 不能以数字开头，不可以使用关键字和保留字，但可以包含关键字和保留字
- 严格区分大小写，标识符中不能含有空格，长度无限制

 **2.** 基本数据类型有哪几类？包含String吗？

- 整型：byte short int long
- 浮点型：float double
- 字符型：char
- 布尔型：boolean

不包含 String 

**3.** 写出基本数据类型自动转化的流程图?

byte/char/short --> int --> long --> float --> double

**4.** 整型默认的是什么类型，浮点型（实数型）默认的是什么类型？

整型默认是 int，浮点型默认是 double

**5.** 对于包名，类名接口名，变量名和函数名，常量名我们习惯如何格式来命名？

- 包名：所有字母小写

- 类名、接口名：首字母大写

- 变量名和函数名：第一个单词首字母大写，其余首字母大写

- 常量名：所有字母大写，若是由多个字母字母组成，中间用下划线隔开。

  注：起名字时要尽量见名知意

**6.** 定义一个变量需要注意什么？

1. 变量必须先声明，后调用
2. 首次使用变量之前一定要初始化(赋值)
3. 变量只有在其作用域内有效
4. 在同一作用域内或嵌套的代码中不能使用同名的变量。
5. 可以使用变量名来访问访问和修改这块内存空间中的数据。

**7.** 强制类型转化可能出现的问题

强制数据类型转换可能导致精度损失，可能会导致数据溢出

**8.** char型变量中能不能存贮一个中文汉字?为什么?

是能够定义成为一个中文的，因为 java 中以 unicode 编码，一个 char 占 16 个二进制位，所以放一个中文是没问题的

**9.** 定义float f=3.4;是否正确?

不正确。精度不准确,应该用强制类型转换，如下所示：float f=(float)3.4

**10.** String是最基本的数据类型吗？

基本数据类型包括byte、int、char、long、float、double、boolean和short。

java.lang.String是java中定义的一个类，类都属于引用数据类型。

**11.** Java 有没有 goto?

java中的保留字，现在没有在 java 中使用

**13.** 用最有效的的方法算出 2 乘以 8 等于几

`2<<3`

**14.** 根据运算符的功能，我们把运算符分成哪几类？

- 算术运算符
- 赋值运算符
- 比较运算符(关系运算符、逻辑运算符)
- 逻辑运算符
- 三目运算符(三元运算符)
- 位运算符

**15** 练习

```java
short s  = 3;
s = s+2; ①
s += 2; ②
```

①和②有什么区别？

当指令为s=s+2进行编译时,会报错.

原因数字 2 是 int 类型的常量，s+2 会自动转换为 int 类型，将一个 int 型赋给一个 short 型的 s，自然编译会出错。

为什么 s+=2 不会报错呢?

因为编译器自动将+=运算符后面的操作数***\*强制转换为前面变量的类型\****,所以s+=2不会报错.

同时类似的还有: `-= \*= /= %=`

```java
int i = 1;
i *= 0.1; 
System.out.println(i);//0
i++;
System.out.println(i);//1
```

因为 0.1 是 double 类型的常量，编译器会自动把*= 赋值运算符后面的数转换为 int 0。那么 `i\*=0` , 因此 i

**16.** 下面程序的输出结果：

```java
       	int no = 10;
        String str = "abcdef";
        String str1 = str + "xyz" + no;//abcefxyz10

        str1 = str1 + 123;//abcefxyz10123
        char c = '国';
        double pi = 3.1416;
        str1 = str1 + pi;//abcefxyz101233.1416
        boolean b = false;
        str1 += b;//abcefxyz101233.1416false
        str1 += c;//abcefxyz101233.1416false国
        System.out.println(str1);
```

**17.**  & 和 && 的异同

相同：运算结果相同

不同：& 无论符号左边是否为 false 都会执行符号右边的表达式。既是逻辑运算符，又是位运算符

​				&& 符号左边的为 true 执行符号右边的表达式，如果符号左边为 false 就不会执行符号右边的表达式

**18.**  switch 后面使用的表达式可以是什么数据类型？

int、byte、short、char、枚举、字符串

**19.** 交换两个整数：

```java
int num1 = 123;
int num2 = 342;
num1 ^= num2;
num2 ^= num1;
num1 ^= num2;
```

**20.** 循环结构是如何最后退出循环的，有哪些不同的情况请说明。

① 循环条件返回 false

② 在循环体内，一旦执行到 break，跳出循环

**21.** 说明break和continue使用上的相同点和不同点

 break:s witch-case 和 循环结构（结束当前循环），其后不可以声明执行语句

continue: 循环结构（结束当次循环），其后不可以声明执行语句

### 面向对象

**1.** == 和 equals 的区别

== 运算符

1. 可以使用在比较基本数据类型变量和引用数据类型变量中。
2. 如果比较的时基本数据类型，比较两个变量报数的数据值。<br/>
   如果比较的时引用数据类型，比较两个对象的地址值是否相同。

注：== 要保证两边的变量类一致。

equals 方法的使用

1. 是一个方法，而非运算符
2. 只能使用与引用数据类型。
3. Object类中equals的定义：   

```
pulic boolean equals(Object obj){
    return this == obj;
}
```

Object 类中定义的 equals 和 == 的作用是相同的。

1. 像 String、Date、File、包装类等都重写了 equals 的方法。（比较两个对象的“实体内容”是否相同）
2. 我们自定义的类使用 equals,通常是想比较两个对象的"实体内容"是否相同，如何对其进行重写。

**2.**  什么是封装？什么是继承？什么是多态？

封装就是将内部实现细节隐藏起来，然后对外公开接口，便于外界调用。从而提高系统的可扩展性和可维护性。

封装性的体现需要权限修饰符的配合。

继承：一旦子类 A 继承 B 以后，子类 A 中就获取了父类 B 中的结构 (属性、方法)

多态：父类引用指向子类的对象，在运行时，仍然保持子类的特征。(将子类对象赋值父类类型)

**3.** 一个数如果恰好等于它的因子之和，这个数就称为"完数"。例如6=1＋2＋3。编程 找出1000以内的所有完数。（因子：除去这个数本身的其它约数）

**4.** 写出一维数组初始化的两种方式

```java
int[] arr = new int[5];//动态初始化

String[] arr1 = new String[]{"Tom","Jerry","Jim"};//静态初始化

数组一旦初始化，其长度就是确定的。arr.length
数组长度一旦确定，就不可修改。
```

2.写出二维数组初始化的两种方式

```java
int[][] arr = new int[4][3];//动态初始化1
int[][] arr1 = new int[4][];//动态初始化2

int[][] arr2 = new int[][]{{1,2,3},{4,5,6},{7,8}};//静态初始化
```

3.如何遍历如下的二维数组

```java
int[] arr = new int[][]{{1,2,3},{4,5},{6,7,8}};

for(int i = 0;i < arr.length;i++){
	for(int j = 0;j < arr[i].length;j++){
		System.out.print(arr[i][j] + "\t");
	}
	System.out.println();
}
```

4.不同类型的一维数组元素的默认初始化值各是多少

整型 : 0
浮点型：0.0
char:0
boolean :false
引用类型：null

5.一维数组的内存解析：

```java
String[] strs = new String[5];
strs[2] = “Tom”;
strs = new String[3];
```

### 高级

**集合**

### 集合面试题

**1.** 集合 Collections 中存储的自定义类的对象，我们需要重写那个方法为什么？

equals() 方法。在 List 中虽然可以存储重复的数据但是当我们需要执行Collection 接口中的一些方法： contians、remove、 retainsAll等方法的时候就会调用 equals 方法。而在 Set 中需要存储不可重复的数据，HashSet、LinkedSet在执行 add、addAll 等方法时候会调用 equals 和 hashCode contains remove方法。TreeSet 根据的就是 compareTO() compare()。当我们调用remove、contains 调用的也是 compare() compareTo() 方法。因此我们泛泛的需要定义 equals 方法。

**2.** ArrayList、LinkedList、Vector 三者的区别？

相同点：都实现了 List 接口。都存储有序、且不可重复的数据

不同点：

ArrayList: 效率高、线程不安全。用于替代数组的使用。(根据索引)查找速度。底层使用数组来存储。 扩容 1.5 倍

Vector: 古老的实现类、线程安全。扩容为 2 倍

LinkedList: 适用于频繁的插入、删除。底层使用链表来存储。

注：拿ArrayList 和 Vector 比，然后拿 ArrayList 和 LinkedList 比

**3.** List 接口中方法？(增、删、改、查、插、遍历)、

增：add(Object obj)

删：remove(Object obj)\remove(int index)

改：set(int index,Object obj)

查：get(int index)

插：add(int index，Object obj);

长度：size()

遍历：iterator();foreach 和 普通 for

**4.** 使用 iterator 和增强 for 循环来遍历 List 举例说明？

**5.** set 存储数据的特点是什么？常见的实现类是什么？说以下他们彼此的特点？

无序、不可重复

HashSet 线程不安全、效率高

LinkedHashSet HashSet 的子类，在遍历内部数据时，可以按照添加的顺序进行遍历

TrreeSet 可以按照添加所对象指定的属性进行排序

**Map 面试题**

**6.** HashMap 的底层实现原理？

**7.** HashMap 和 HashTable 异同？

相同点: 都实现了 Map 解耦

HashMap 主要实现类。线程安全，效率高。

HashTable 古老实现类。线程不安全、效率低。

**8.** CurrentHashMap 与 HashTalbe 的异同?

**9.** 谈谈你对 HashMap 中 put/get 方法的认识？如果理解再谈谈 HashMap 的扩容机制？默认大小是多少，什么是负载因子，什么是临界吞吐值(阈值、threshold)

### 面试阶段——面试题


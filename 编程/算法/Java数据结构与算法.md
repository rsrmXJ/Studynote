# Java数据结构与算法

###### 1. 数据结构与算法的重要性

1\. 算法是程序的灵魂，优秀的程序在做海量数据计算时，依然保持高速计算

2\. 一般来讲，程序使用内存计算框架(spark)和缓存技术(Redis)等来优化程序。(深入思考这些计算框架和缓存技术的核心功能是那个部分)

3\. 目前程序员的面试门槛越来越高，很多一线 IT 公司(大厂),都会有数据结构与算法的面试题

4\. 如果你不想永远都是代码工人,那就花时间来研究下数据结构和算法

### 第一章 数据结构与算法的概述

##### 1. 数据结构与算法的关系:

1. 数据结构 (data structure) 是**一门研究组织数据方式**的学科，有了编程也就有了数据结构。学好数据结构可以编写更加漂亮，更加有效率的代码。
2. **程序 = 数据结构 + 算法**
3. **数据结构是算法的基础**，换言之想要学好算法，就必须先把把数据结构学到位

> 数据结构主要分为：
>
> - 线性结构
> - 非线性结

##### 2. 线性结构与非线性结构

**线性结构**

1.  线性结构是最常用的数据结构，特点是**数据元素之间存在一对一的线性关系**。例如：`arr[0]=30`；
2. 线性结构有两种不同的存储结构，顺序存储结构和链式存储结构
   1. 顺序存储结构：顺序存储的线性表称为顺序表，顺序表中存储的元素是连续的（即在内存中是连续的）。
   2. 链式存储结构：链式存储的线性表称为链式表。链式表中存储的结构不一定是连续的（即在内存中不一定是连续的）。元素节点中存储数据元素以及相邻元素的地址信息
3. 线性结构常见的有：数组、队列、链表和栈

**非线性结构**

非线性结构包括：二维数组，多维数组，广义表，树结构，图结构 

### 第三章 稀疏数组和队列

##### 3.1 稀疏数组 sparsearray

实际需求：五子棋

**基本介绍：**

当一个数组中大部分元素为 0，或者为同一个值的数组，那么可以使用稀疏数组来保存该数组

稀疏数组的处理方法：

1. 第一行记录数组共有几行几列，共有多少个不同的值
2. 把具有不同值的元素的行列及值记录在在一个小规模的数组中，从而缩小程序的规模

二维数组转稀疏数组的思路：

1. 遍历原始的二维数数组，得到有效数据的个数 sum
2. 根据 sum 的值，就可以创建稀疏组数 sparseArr int\[sum+1\]\[3\]
3. 将二维数组的有效数据存入到稀疏数组

稀疏数组转原始的二维数组的思路：

1. 先读取稀疏数组的第一行，根据第一行的数据，声明原始的二维数组
2. 再读取稀疏数组后几行的数据，并赋给原始的二维数组即可

课后练习：

1. 在前面的基础上，将稀疏数组保存到磁盘上如 map.data
2. 恢复原来的数组时，读取 map.data 进行恢复

```java
//创建一个原始的数组 11*11
// 0 表示没有妻子，1 表示黑子，2 表示蓝子
int[][] chessArr = new int[11][11];
chessArr[1][2] = 1;
chessArr[2][3] = 2;
chessArr[4][5] = 2;
//输出原始的二维数组
System.out.println();
for (int[] row : chessArr) {
    for (int data : row) {
        System.out.printf("%d\t",data);
    }
    System.out.println();
}
//将二维数组转稀疏数组的思路
//1. 线遍历二维数组，得到非 0 数据的个数
int sum = 0;
for (int i = 0; i < chessArr.length; i++) {
    for (int j = 0; j < chessArr[i].length;j++) {
        if(chessArr[i][j] != 0){
            sum++;
        }
    }
}
//创建对应的稀疏数组
int sparseArr[][] = new int[sum+1][3];
//给各稀疏数组赋值
sparseArr[0][0] = chessArr.length;
sparseArr[0][1] = chessArr[0].length;
sparseArr[0][2] = sum;
//遍历二维数组，将非 0 的值存放到 sparseArr 中
int count = 0;
for (int i = 0; i < chessArr.length; i++) {
    for (int j = 0; j < chessArr[i].length;j++) {
        if(chessArr[i][j] != 0){
            sparseArr[++count][0] = i;
            sparseArr[count][1] = j;
            sparseArr[count][2] = chessArr[i][j];
        }
    }
}
//输出稀疏数组的形式
System.out.println("\n得到的稀疏数组为----------------");
for (int i = 0; i < sparseArr.length; i++) {
    System.out.printf("%d\t%d\t%d\n",sparseArr[i][0],sparseArr[i][1],sparseArr[i][2]);
}
System.out.println();
//将稀疏数组恢复为二维数组
//1. 先读取稀疏数组的第一行，根据第一行的数据创建原始的二维数组
//2. 再读取稀疏数组后几行的数据，并给原始的二维数即可
int[][] chessArr2 = new int[sparseArr[0][0]][sparseArr[0][1]];
for (int i = 1; i < sparseArr.length; i++) {
    chessArr2[sparseArr[i][0]][sparseArr[i][1]]=sparseArr[i][2];
}
//输出恢复后的二维数组
System.out.println();
System.out.println("恢复的二维数组为");
for (int[] row : chessArr2) {
    for (int data : row) {
        System.out.print(data+"\t");
    }
    System.out.println();
}
```

##### 3.2 队列

1. 队列是一个有序列表，可以用**数组或链表**来实现
2. 遵循先入先出的原则。即：先存入队列的数据，要先取出，后存入队列的数据后取出。

**数组模拟队列**

1. 队列本身是有序列表，若使用数组的结构来存储队列的数据。则队列声明如下，其中maxSize 是该队列的最大容量
2. 因为队列的输出、输入分别从前后端处理的，因此需要两个变量 front 和 rear 分别记录队列前后端的下标。front 会随着的数据输出而改变，rear 会随着数据输入而改变。

我们将数据存入队列时称为 "addQueque",addQueque 的处理有两个步骤：**思路分析**

1. 将尾指针后移：rear+1，当 front == rear 【空】

2. 若尾指针 rear 小于队列的最大下标 maxSize-1, 则将数据存入 rear 所指的数组元素中，否则无法存入数据，rear == maxSize-1【队列墙】

   > 添加数据时，要先进行判断，队列是否为空，或是否为满的。如果满了就不可以添加数据了。

3. 代码实现：

4. 问题分析并优化

注：

- front 指向队列头部，front 就是指向队列头的前一个位置 (不包含队列首部)。如果要从队列中取出数据，那么 front 就要先自增 1。
- rear 指向队列尾，指向队列尾的数据（包含队列尾）

初始化为：`front = rear = -1;`

想法：front包不包含队首，rear 包不包含队尾。影响的仅仅是从队列中添加或取出数据的的操作的方式。也可以将其视为约定的规则。

```java
public class ArrQueue{
    private int maxSize;//表示数组的最大容量
    private int front;//队列首的索引
    private int rear;//队列尾
    private int[] arr;//该数组用于存放数据模拟队列
    //使用泛型
    
    //创建队列的构造器
    public ArrQueue(int maxSize){
        this.maxSize = maxSize;//标识最大容量
        arr = new int[maxSize];//根据maxSize 创建队列
        front = -1;//指向队列头部，front 指向队首的前一个位置
        rear = -1;//指向队列尾的数据
    }
    //判断队列是否满
    public boolean isFull(){
        return rear == maxSize - 1;
    }
    //判断队列是否为空
    public boolean isEmpty(){
        return rear == front;
    }
    //front 指向队首的前一个数据，而 rear 指向队尾。也就是如果有数据 front != rear;而如果队列中没有数据，front 是随着数据的取出而改变，rear 是随着数据的添加而改变。当队尾数据也被取出时，front == rear;
    //添加数据到队列
   	public void addQueue(int n){
        if(isFull()){
            System.out.println("队列满，不能添加数据");
			return;
        }
        arr[++rear] = n;
    }
	//获取队列的数据出队列
    public int getQueue(){
        if(isEmpty()){
            //通过异常来处理，如果我们直接返回 -1，那么 -1 也是一个数据
            throw new RuntimeException(“队列空，不能取数据”);
        }
        front++;//front后移
        return arr[front];
    }
    //显示队列的所有数据
    public void showQueue(){
        //遍历
        if(isEmpty()){
            //为什么这里要这样写，而没有直接抛出异常来处理
            System.out.println("队列为空，没有数据");
            return;
        }
        for(int i = 0;i < arr.length;i++){//如果将arr.length 改为rear 则是从数组的开端到最后一个有效数据。同时如果将 i = front 则是显示所有有效数据
            System.out.printf("arr[%d]=%d,\n",i,arr[i]);
        }
    }   
}
/**
  *添加数据时，要先判断队列是否已满，取数据时，要先判断队列是否为空
  *
  */
```

使用泛型

```java
public class ArrQueue<E> {
    private Class<E> clazz;
    private int maxSize;
    private int front;
    private int rear;
    //front 指向队列的第一个的前一个数据，而 rear 指向队列的最后一个数据。
    /**
     * 意味每次添加新元素都要往前移动一位，而每取出一个就
     */
    private E[] array;

    public ArrQueue(Class<E> clazz, int maxSize) {
        this.maxSize = maxSize;
        this.rear = this.front = -1;
        array = (E[]) Array.newInstance(clazz, maxSize);
    }

    public boolean isFull() {
        return rear == maxSize - 1;
    }
    public boolean isEmpty(){
        return rear == front;
    }
    public void addQueue(E ele){
        if(isFull()){
            System.out.println("队列已满，不可以添加数据");
            return;
        }
        array[++rear] = ele;
    }
    public E getQueue(){
        if(isEmpty()){
            throw new RuntimeException("队列为空，没有数据");

        }
        return array[++front];

    }
    public void showQueue(){
        if(isEmpty()){
            throw new RuntimeException("队列为空，没有数据");
        }
        for (int i = 0; i < array.length; i++) {
            System.out.printf("array[%d]=%d\n",i,array[i]);
        }
    }
    public E showHeadQueue() {
        // 判断
        if (isEmpty()) {
            throw new RuntimeException("队列空的，没有数据~~");
        }
        return array[front + 1];
    }
    public E headQueue() {
        // 判断
        if (isEmpty()) {
            throw new RuntimeException("队列空的，没有数据~~");
        }
        return array[front + 1];
    }

}
  


public static void main(String[] args) {
        ArrQueue<Integer> queue = new ArrQueue<>(Integer.class, 3);
        char key = ' '; //接收用户输入
        Scanner scanner = new Scanner(System.in);//
        boolean loop = true;
        System.out.println("s(show): 显示队列");
        System.out.println("e(exit): 退出程序");
        System.out.println("a(add): 添加数据到队列");
        System.out.println("g(get): 从队列取出数据");
        System.out.println("h(head): 查看队列头的数据");
        //输出一个菜单
        while(loop) {
            key = scanner.next().charAt(0);//接收一个字符
            switch (key) {
                case 's':
                    try {
                        queue.showQueue();
                    } catch (Exception e) {
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'a':
                    System.out.println("输出一个数");
                    int value = scanner.nextInt();
                    queue.addQueue(value);
                    break;
                case 'g': //取出数据
                    try {
                        int res = queue.getQueue();
                        System.out.printf("取出的数据是%d\n", res);
                    } catch (Exception e) {
                        // TODO: handle exception
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'h': //查看队列头的数据
                    try {
                        int res = queue.headQueue();
                        System.out.printf("队列头的数据是%d\n", res);
                    } catch (Exception e) {
                        // TODO: handle exception
                        System.out.println(e.getMessage());
                    }
                    break;
                case 'e': //退出
                    scanner.close();
                    loop = false;
                    break;
                default:
                    break;
            }
        }

        System.out.println("程序退出~~");
    }
```

抛出异常来终止方法和return 来终止方法的区别是什么？

rerun 的作用：

1. 终止当前方法
2. 返回数据
3. 在循环中即终止后续循环，也终止当前方法

throw：

程序在正常执行的过程中，一旦出现异常，就会在异常代码除生成一个异常类对象，然后将对象抛出。一旦抛出对象以后，对象以后的代码就不会执行了。

**问题分析并优化**

1. 数组目前使用一次就不能使用了，没有达到复用的效果
2. 将这个数组使用算法，改进成一个环形的队列

***数组模拟环形队列***

**思路如下：**

变量含义调整：

1. front ：front 指向队首元素。也就是说 arr[front] 就是队列的首元素；front 的初始值 = 0
2. rear ：rear 指向队尾元素的后一个元素。希望空出一个位置为约定，rear 的初始值 = 0
3. 队列满条件是 `(rear + 1) % maxSize  =front`【满】
4. 队列空的条件是 `rear = front` 【空】
5. 当我们这样分析，队列中有效数据的个数：`(rear + maxSize - front) % maxSize`

rear 代表前面有的元素个数 front 代表前面有多少个空格，将有内容的也视之为空格。因此共有 (rear + maxSize - front) % maxSize 个元素

代码实现

```java
public class CircleArrayQueque {
	private int front;
	private int rear;
	private int maxSize;
	private int[] arr;
	
	public CircleArrayQueque(int maxSize) {
		super();
		this.maxSize = maxSize;
		arr = new int[maxSize];
		front = rear = 0;//可以不写
	}
	
	public boolean isFull() {
		return (rear + 1) % maxSize == front;
		/**
		 * rear 指向队尾元素的后一个元素。假设将队列存满那么 rear 指向 maxSize,那么 maxSize + 1
		 */
	}
	
	public boolean isEmpty() {
		return rear == front;
	}
	
	public void addQueue(int n) {
		if(isFull()) {
			//这里要使用 throw 抛出异常，否则会导致变量 rear增加,从而导致但判断的不准确
			throw new RunntimeException("队列 满，不可以添加数据");
		}
		arr[rear++] = n;
		rear = rear % maxSize;
	}
	
	public int getQueue() {
		if(isEmpty()) {
			throw new RuntimeException("队列为空，不可以取出数据");
		}
		/**
		 *1. 先把 front 索引对应的值的保存到一个临时变量
		 *2. 把 front 后移，考虑取模
		 *3. 将临时保存的变量返回
		 */
		int value = arr[front];
		front = (front + 1) % maxSize;
		return value;
	}
	
	public void showQueue() {
		if(isEmpty()) {
			System.out.println("队列为空，没有数据");
			return;
		}
		/**
		 * 思路从 front 开始遍历，遍历多少个元素
		 */
		for(int i = front;i < front + size();i++) {
			System.out.printf("arr[%d]=%d\n",i % maxSize,arr[i % maxSize]);
		}
	}
	
	public int getHeadQueue(){
		if(isEmpty()) {
			throw new RuntimeException("队列为空，无法取出数据");
		}
		return arr[front];
	}
	/**
	 * 求当前队列有效数据的个数
	 * @return
	 */
	public int size() {
		return (rear + maxSize - front) % maxSize;
	}
}
```

##### 3.3 单链表

链表是有序的列表。

总结：

1. 链表是以节点的方式来存储的。
2. 每个节点包含 data 域：存储数据，和 next 域：指向下一个节点
   1. 链表的各个节点不一定是连续存放的
3. 链表分带头节点的链表和无头节点的链表。根据实际需求来确定

+++

head 节点：

1. 不存储具体数据
2. 作用就是表示单链表头
3. next 指向第一个节点

创建

1. 先创建一个 head 头节点，作用就是表示单链表的头
2. 我们后面没添加一个节点，就直接加入到链表的尾部
3. 最后一个节点 Nord 的 next 总是指向 null

遍历

1. 通过一个辅助变量，帮助遍历整个单链表

通过带头的单链表对梁山好汉进行排序：

具体面试题和笔记详见 linkedList/SingleLinkedListDemo.java\笔记

按照编号的顺序进行添加：

1. 首先要找到新添加节点的位置
2. 新节点的 next 指向原来的temp.next.next
3. 然后将 temp.next 指向新节点

从单链表中删除一个节点的思路：

1. 我们先找到需要删除这个节点的前一个节点temp
2. 然后直接点将temp.next 指向temp.next.next

面试题：

1. 获取有效节点的个数

2. 获取倒数第 n 个节点

3. 将单向链表进行反序

4.  从尾到头打印单链表

   1. 思路：

      1. 方式一：将单链表进行反转操作，然后遍历即可，这样做的问题是会破坏原来的单链表的结构不建议
      2. 方式二：利用栈，将各个节点压入栈中，然后利用栈的先进后出的特点就是先了逆序打印的效果。

5. 将两个有序单向链表进行合并

   思路：

   1. 进行判断如果一个其中有一个单向链表为空，那么直接返回另一个单向链表。如果两个都为空那么直接返回null

      ```java
      if(head1.getNext() == null || head2.getNext() == null){
          return head1.getNext() != null ? head1:head2;
      }
      /**
        *进行这个条件只有三种情况：
        1. head1 为空直接返回 head2;
        2. 如果 head2 为空直接返回 head1;
        3. 如果 head1 和 head2 皆为空那么直接返回 null
        综上所述，上面那个表达式完全复合要求
      ```

   2. 定义两个变量，A,B 分别表示 head1 和 head 2 中的节点。定义一个临时变量C 来对其进行接受

      按照增序排列

      A < B，将 A 添加到 C 链表的后面，然后将 A 的指针后移一位。

      A = B，将 A 添加到 C 的后面，然后将 A 和 B 的指针都后移一位

      A > B， 将 B 添加到 C 链表的后米娜，将 B 的指针后移一位。

      注我们比较的一直都是 A 或 B 的值。

   3. 若 A 遍历完毕，B 未遍历完毕，则将 B 链表剩余的部分连接新链表的末尾

   4. 若 B 遍历完，而 A 未遍历完毕，则将 A 链表的剩余的部分连接到新链表的末尾

   是否返回值根据参数决定。

##### 3.4 双向链表

单向链表的缺点分析：

1. 单向链表的查找方向只能是一个，双向链表可以向前或向后查找
2. 单向链表不能自我删除，需要辅助节点，而双向链表可以自我删除，所以前面我们单链表删除时节点，总是找到 temp 的下一个节点(要删除的节点)来删除。即：要找到删除节点的前一个结点来进行删除

##### 3.5 单向环形链表

Josephu 约瑟夫问题

用一个不带头节点的循环链表处理 Josephu 问题：线构成一个有 n 个节点的单向循环链表，然后节点从1 开始计数，计到 m 时，对应结点从链表中删除有，然后从被删除结点的下一个结点从 1 开始结束，直到最后一个结点从链表中删除算法结束

构建一个单向环形链表的思路：

1. 先创建一个结点，让 first 指向该结点，并形成环形
2. 最后当我们每创捷一个新的结点，就把该结点，加入到以后的环形链表中

遍历环形链表：

1. 先让一个辅助指针(变量) curBoy, 指向 first 结点
2. 然后通过一个 while 循环遍历该环形链表即可 curBoy.next == first 结束

##### 3.6 栈 Stack

1. 栈时限制线性表中元素的插入和删除只能在一端进行的一种特殊线性表。允许插入和删除的一端，为变化的一端，成为栈顶（Top）。另一端固定的一端，称为栈底 （Botton）
2. 根据栈的定义可知，最先放入的元素在栈底，最后放入的元素在栈顶，删除元素刚好相反，最后放入的元素最先删除，最先放入的元素最后删除

**栈的应用场景**：

1. 子程序的调用，在跳往子程序前，会先将子程序首个指令地址存到堆栈中，直到子程序执行完毕后再将地址取出，就会原来的程序中。return 关键字
2. 处理递归调用：和子程序的调用类似，只是除了下个执行的地址外，也将参数，区域变量等数据数据存入堆栈中。
3. 表达式的转换 [中缀表达式转后缀表达式]与求职
4. 二叉树的遍历
5. 图形的深度优先(deepth--first)搜索法

**实现栈的思路分析**

1. 使用数组来模拟栈
2. 定义一个 top 来表示栈顶，初始化为 -1
3. 入栈的操作，当有数据加入到栈时，top++,stack[top] = data;
4. 出栈的操作，int val = stack[top];top --; return val

**栈实现综合计算器**

```java
  /**
             * 思路分析
             * 1. 将字符串中的字符逐个取出
             *    如果取出的是符号：
             *      1.首先判断符号栈是否为空，
             *          - 如果为空，那么直接入栈
             *          - 符号不为空，那么判断该符号，与符号栈中前一个符号的优先级
             *              - 如果该符号的优先级小于于或等于前一个符号的优先级，则数栈抛出两个数字，符号栈抛出一个字符，
             *              然后将计算的结果存入数栈中
             *              - 如果该符号的优先级大于前一个符号的优先级，那么直接入符号栈
             *    如果取出的是数字：
             *    1. 先用一个临时的字符串变量对这个字符进行拼接。
             *    2. 然后判断该字符后一个字符是否是数字，
             *      如果是不是数字，那么直接入数栈，并将临时变量清空
             *      如果是数字，那么跳过
             *
             *   我们将字符逐个取出然后进行计算。
             * 注：默认 +- 的优先级为 0，* / 的优先级为1
             */
```



节点

```java
package structure;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * @author rsrmXJ
 * @create 2021-04-07 20:44
 * LinkedListNode
 */
public class LinkedListNode<E> {
    private E data;//存放泛型数据，但指向下一个节点的仍然是个节点
    private LinkedListNode<E> next;
    private static final Logger LOGGER = LoggerFactory.getLogger(LinkedListNode.class);
    public LinkedListNode(LinkedListNode<Integer> integerLLNode) {
    }

    public E getData() {
        return data;
    }

    public void setData(E data) {
        this.data = data;
    }

    public LinkedListNode() {

    }

    public LinkedListNode(E data) {
        this.data = data;
    }

    public LinkedListNode<E> getNext() {
        return next;
    }

    public void setNext(LinkedListNode<E> next) {
        this.next = next;
    }
}
```

栈

```
package structure;

import org.junit.jupiter.api.Test;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * @author rsrmXJ
 * @create 2021-04-07 20:46
 */
public class LinkedListStack<E> {
    /**
     * 我们只能在链表的末尾进行push或pop
     */
    private LinkedListNode<E> headNode = null;
    private static final Logger LOGGER = LoggerFactory.getLogger(LinkedListStack.class);

    public LinkedListStack() {
        headNode = new LinkedListNode<E>();
    }
    public boolean isEmpty(){
        return headNode == null;//链表中最后一个节点指向 null
    }

    public void push(E data){
    /**
 * 是否可以放 null 值可以根据需要来定，下面的代码不注释，就是不能放 null 值。
 */
        if(data == null){
           LOGGER.warn("数据不合法");
           return;
        }
        if(headNode.getData() == null){//第一个放进去的值，将放在头节点，
            headNode.setData(data);
        }else if(headNode == null){
            headNode = new LinkedListNode<E>(data);
        }else{//新加进来的放在第一个
            LinkedListNode<E> newnode = new LinkedListNode<E>(data);
            newnode.setNext(headNode);
            headNode = newnode;
        }
    }

    /**
     * 取出来的应该是数据,而不是某个节点
     * @return data
     */
    public E pop(){
        E data = null;
        if(isEmpty()){
            if(LOGGER.isWarnEnabled()){
                LOGGER.warn("栈为空");
                return null;
            }
        }
        data = headNode.getData();
        headNode = headNode.getNext();
        return data;
    }

    public E top(){
        E data = null;
        if(isEmpty()){
            if(LOGGER.isWarnEnabled()){
                LOGGER.warn("栈为空,返回值为 0");
                return null;
            }
        }
        data = headNode.getData();
        return data;
    }
    public int size(){
        int count = 0;
        LinkedListNode<E> helpNode = headNode;
        if(isEmpty() || helpNode.getData() == null){
            count = 0;
        }else{
            while(helpNode != null){
                count++;
                helpNode = helpNode.getNext();
            }
        }
        return count;
    }

    public void LLStackTest(){
        LinkedListStack<Integer> integerLLStack = new LinkedListStack<>();
        integerLLStack.push(1);
        integerLLStack.push(2);
        integerLLStack.push(3);
        int size = integerLLStack.size();
        LOGGER.info("栈里面值的个数为"+size);
    }
}

```

   ##### 

##### 3.7 前缀、中缀、后缀表达式

**前缀表达式**又称波兰表达式，前缀表达式的运算符为与操作数之前。

举例：(3+4)*5-6,对应的表达式是 - * + 3 4 5 6

**前缀表达式的求值**

从左至右扫描表达式，遇到数字压入栈中，遇到运算符，则从栈中弹出两个数，用运算符对他们做相应的计算，然后将计算结果压入栈中；重复上述步骤，直到表达式最左端，最后运算得出的值为为表达式的结果

**中缀表达式**

1. 中缀表达式就常见的运算表达式
2. 中缀表达式的求值是我们人最熟悉的，但是计算机来说却不好操作，因此在计算结果时，往往会将中缀表达式转换为其它表达式来进行计算(一般转换为后缀表达式)

**后缀表达式(逆波兰表达式)**

1. 与前缀表达式相似，只不过是运算符位于操作数之后

2. 举例：`(3+4)x5-6` 的后缀表达式 `3 4 + 5 x 6 -`

3. 其它例子

   | 正常的表达式 | 逆波兰表达式  |
   | :----------: | :-----------: |
   |     a+b      |     a b +     |
   |   a+(b-c)    |   a b c - +   |
   |  a+(b-c)*d   | a b c -d * +  |
   |  a+d*(b-c)   | a d b c - * + |
   |   a = 1+ 3   |   a 1 3 + =   |

   **后缀表达式的求值**

   从左至右扫描表达式，遇到数字时，将数字压入栈中，遇到运算符时，在弹出两个数，用运算符对他们进行计算，将计算的结果压入栈中；重复上述过程直到表达式的最有端，最后运算的结果就是表达式的值

使用链表模拟栈

   ##### 3.8 逆波兰计算器

   思路分析：

一个算法中语句的执行次数成为语句频度或时间频度。$T(n)$

因为一个算法中语句的执行次数与所花费的时间成正比。时间频度就是一个算法执行所消耗的时间。

时间复杂度。在时间频度中，n 称为问题的规模，当 n 不断变化时，时间频度 T(n) 也会随之改变。

常见的时间复杂度：

- 常数阶 $O(1)$
- 对数阶 $O(log_2n)$
- 线性阶$$
- 线性对数阶
- 平方阶
- 立方阶
- k 次方阶
- 指数阶
- 阶乘阶
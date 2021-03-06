---

title: Python数据结构与算法
date: 2020-09-30 11:37:57
tags: Python
top: 0
---

## 一、数据结构和算法

数据：能够被计算机处理的符号或符号集

数据元素（节点、记录）：数据的基本单位。例：学生统计表

数据项（字段、域）：组成数据元素的最小单位。例：编号、姓名、性别等

<!--more-->

### 1  数据结构

**相互之间存在的一种或多种特定的数据元素的集合用于计算机存储、组织数据的方式**

![](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/1.jpg)

#### 1.1  逻辑结构

**数据元素间的抽象关系（如近邻关系、从属关系等），分为线性结构与非线性结构，**抽象出来的，与机器无关

![](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/2.png)

| 结构类型 | 数据元素间的相互关系 |
| :------: | :------------------: |
|   集合   |          无          |
|   线性   |        一对一        |
|   树形   |        一对多        |
|    图    |        多对多        |

#### 1.2  （存储）物理结构

**数据在计算机中的存放方式，反应数据元素间的逻辑关系**

- 顺序存储（向量存储）：把数据元素存放在一组存储地址连续的存储单元里，逻辑关系和物理关系是一致的
- 链式存储（动态存储）：通过指针反应数据间的逻辑关系
- 索引存储：建立一个附加的索引表
- 散列存储：根据节点的关键字直接计算出该节点的存储地址

> 同一逻辑结构可以对应不同的存储结构，算法的设计取决于数据的逻辑结构，而算法实现依赖于指定的存储结构

### 2  算法

**解决特定问题的一种方法或一种描述**

#### 2.1  数据结构和算法的联系

程序=算法+数据结构

- 算法设计: 取决于选定的逻辑结构
- 算法实现: 依赖于采用的存储结构

区别：

- 数据结构关注的是**数据的逻辑结构、存储结构的基本操作**
- 算法关注的是**在数据结构的基础上解决实际问题**

算法的五大特性：

1. 可行性：解决具体问题，完成期望功能
2. 确定性：每一步在一定条件下只有一条执行路径
3. 有穷性： 算法执行的步骤（或规则）是有限的
4. 输入：具有零或多个输入
5. 输出：至少有一个或多个输出

评价算法好坏的方法：

- 正确性：运行正确是一个算法的前提。
- 可读性：容易理解、容易编程和调试、容易维护。
- 健壮性：考虑情况全面，不容以出现运行错误。
- 时间效率高：算法消耗的时间少。
- 储存量低：占用较少的存储空间。

#### 2.2  时间复杂度O(n)

计算规则

1. 基本操作，只有**常数项O(1)**
2. 顺序结构，按**加法计算**
3. 循环结构，按**乘法计算**
4. 分支结构，**取最大值**

#### 2.3  空间复杂度S(n)

**一个算法在运行过程中临时占用存储空间大小的度量**

## 二、线性数据结构

特点：在数据元素的非空有限集合中

- 存在唯一的（第一个--最后一个）数据元素
- 且第一个元素没有直接前驱元素，最后一个元素没有直接后继元素，其他元素都有唯一的前驱和后继元素

### 1  线性表

- 线性表有顺序存储结构和链式存储结构。

#### 1.1  顺序存储结构（顺序表）

**是指将线性表中的各个元素依次存放在一组地址连续的存储单元中，通常将这种方法存储的线性表称为顺序表。**
顺序表的优缺点：

- 优：无须关心表中元素之间的关系，所以不用增加额外的存储空间；可以快速地取表中任意位置的元素。
- 缺：插入和删除需要移动大量元素

#### 1.2  线性表的链式存储（链表）

一组任意的连续或非连续存储单元存储线性表的元素，存储元素本身（数据域）和后继元素地址（指针域)	

- 链表与顺序表的对比

  |        |    存储方式    |           时间性能           |       空间性能       |
  | :----: | :------------: | :--------------------------: | :------------------: |
  | 顺序表 | 连续的存储单元 | 查找：O(1)；插入、删除：O(n) |     预先分配空间     |
  | 单链表 | 任意的存储单元 | 查找：O(n)；插入、删除：O(1) | 不用估计预用空间大小 |

循环单链表：首尾相连的一种单链表，最后一个结点称为：尾指针：rear

双向链表： 链表中的每个结点有两个指针域，一个指向直接前驱结点，另一个指向直接后继结点。 data -- prior -- next 

### 2  栈和队列

#### 2.1  栈

**限制在表一端进行插入（入栈）和删除（出栈）操作的线性表**

- 栈顶：允许操作端

- 栈底：固定端

> 先进后出

##### 2.1.1  栈的存储结构

栈是运算受限的线性表，线性表的存储结构对栈也适用

1. 顺序栈
2. 链栈

#### 2.2  队列

**插入（队尾）在表一段，删除（对头）在表的另一端**

> 先进先出

##### 2.2.1  队列的存储结构

队列也是一种运算受限的线性表

- 顺序队列
- 链队列

小结：

1. 链式栈的栈顶应在链头，插入与删除操作都在链头进行。
2. 循环队列要注意对空条件和对满条件；而对于链队列，需要特别注意出队仅对队头指针操作，当只有一个元素时，出队需要修改队尾指针。
3. 递归实质上是通过栈来实现函数调用，只不过是调用自身而已。

### 3  字符串和数组

#### 3.1  字符串

 串（String）是由零个或多个任意字符串组成的字符序列。 

##### 3.1.1  串的存储结构

- 定长顺序存储结构（顺序串）
  - 定长是指按预定义的大小为每一个串变量分配固定长度的存储区。 最大不能超过256
- 堆分配存储结构

#### 3.2 数组

 **结构中的元素本身可以是具有某种结构的数据，但属于同一数据类型，一般线性表的扩充**

是一个具有固定格式和数量的数据有续集，一旦被定义就不再改变，两种基本运算：

1. 取值操作：给定一组下标，读其对应的数据元素。
2. 赋值操作：给定一组下标，存储或修改与其相对应的数据元素。

##### 3.2.1  数组的存储结构

一维：顺序存储结构，直接按其下标顺序分配存储空间

多维：按某种次序将数组中元素排成一个线性序列，再作存储

存二维数组：

1. 先行后列
2. 先列后行

##### 3.2.2  稀疏矩阵

**矩阵中大多数元素为零元素的矩阵，按常规分配方法浪费内存**

三元组表存储

- 非零元素所在行、列及值构成一个三元组（i,j,v）

## 三、非线性结构

### 1  树和二叉树

前驱唯一而后继不唯一，一对多

定义：树（tree）是n（n>=0）个结点的有限集合。当n=0时，该集合满足以下条件：

- 只有一个特殊节点称为根节点（root）

- 当n>1时，其余n-1个结点被分成m(m>0)个互相不相交的集合，称为根节点的**子树**

  树的定义用了递归概念，其算法也常常使用递归

#### 1.1  二叉树

二叉树是每个节点最多有两个子树的树结构。通常子树被称作“左子树”（left subtree）和“右子树”（right subtree）

满二叉树：除了叶结点外每一个结点都有左右子叶且叶子结点都处在最底层的二叉树。 

##### 1.1.1  存储结构

顺序存储结构：按照二叉树结点从上至下、从左到右的顺序存储。

链式存储结构：每个节点由三个域组成：数据域和左、右指针域。

![](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/%E4%BA%8C%E5%8F%89%E6%A0%91.jpeg)

##### 1.1.2  二叉树遍历

**深度优先一般用递归，广度优先一般用队列。一般情况下能用递归实现的算法大部分也能用堆栈来实现。**

1. 深度优先遍历
   1. 先序遍历	` 根节点->左子树->右子树 `
   2. 中序遍历    ` 左子树->根节点->右子树 `
   3. 后序遍历    ` 左子树->右子树->根节点 `
2. 广度优先遍历（层次遍历）
   -  从树的root开始，从上到下从从左到右遍历整个树的节点 

### 2  图

任意两个结点之间都可能相关，即结点之间的邻接关系可以是任意的。

## 四、排序和搜索

### 1  冒泡排序

**重复遍历数列，一次比较两个元素**

![冒泡排序](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/%E5%86%92%E6%B3%A1%E6%8E%92%E5%BA%8F.gif)

### 2  选择排序

**找到最大或最小的元素，把它排在队前或队尾，循而往复， 放到已排序序列的末尾**

![选择排序](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/%E9%80%89%E6%8B%A9%E6%8E%92%E5%BA%8F.gif)

### 3  插入排序

**通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。**

![插入排序](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F.gif)

### 4  希尔排序

**希尔排序是插入排序的一种，将数据分成几组，分别使用插入排序**

![希尔排序](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F.jpg)

### 5  快速排序

**从序列中挑出一个元素（基准），把小于（大于）基准的元素放在基准前边（后边）**

![快速排序](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F.gif)

### 6  归并排序

 **分治法，先递归分解数据，在合并数组；比较两个数组最前边的数，谁小谁先取**

![归并排序](https://tupian-1300728887.cos.ap-chengdu.myqcloud.com/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F.gif)

## 五、搜索

### 1  顺序查找

### 2  二分法查找

### 3  二叉树查找

### 4  哈希查找
# 一、数据结构

## 线性结构和非线性结构

数据结构分为线性数据结构和非线性数据结构。

线性结构比较常用，常见的有数组、栈、队列和链表。

线性结构中有两种不同的存储结构：

1. 顺序存储结构（数组）

   这种结构的线性表也称为顺序表，其特点是**存储的元素是连续**的

2. 链式存储结构（链表）

   这种结构的线性表也称链表，其中**存储的元素不一定连续**，元素的节点存放数据元素以及相邻元素的地址信息

线性结构具有的特点是数据元素之间存在一对一的线性关系（例如数组中一个下标对应一个元素）。

非线性数据结构常见的有二（多）维数组、广义表、树、图。

## 稀疏数组

当一个二维数组中有很多值都为 0 或都相等时，有必要考虑**使用稀疏数组来压缩这个二维数组**。

这个稀疏数组首先需要记录原数组共有几行、几列、有多少个不同的值（因此稀疏数组一般行数不定，列数为 3 ），往下就开始记录具有不同值的元素的行列及值记录，从而把原来更有价值的东西在一个小规模的数组中。

例如：

<img src="E:\MyLearning\study_notes\数据结构与算法\pictures\笔记\稀疏数组示例图.png" alt="稀疏数组示例图" style="zoom:80%;" />

### 压缩：实现二维数组转稀疏数组

思路：

1. 遍历原始二维数组 `oldArray[m][n]`，得到其中有效值个数 validNums；
2. 创建稀疏数组 `sparseArr[sum + 1][3]`；
3. 将原始数组的行数 m、列数 n、有效值个数 validNums 依次存入稀疏数组 `sparseArr` 的第一行，接着把原始数组有效值的行列信息及值信息存入稀疏数组中；

实现：

```java
public int[][] parseToSparseArray(int[][] oldArray){
	int validNums = 0;
	for(int[] a : oldArray){                    //	记录原数组非 0 数值的数目
    	for(int b : a){
        	if(b != 0){
            	validNums++;
            }
        }
    }
    int[][] sparseArray = new int[validNums + 1][3];  //创建稀疏数组
    //  初始化稀疏数组
    sparseArray[0][0] = oldArray.length;
    sparseArray[0][1] = oldArray[0].length;
    sparseArray[0][2] = validNums;
    //  存储有效值信息
    int tempRow = 0;
    for (int i = 0; i < oldArray.length; i++) {
    	for (int j = 0; j < oldArray[0].length; j++) {
        	if(oldArray[i][j] != 0){
            	tempRow++;
                sparseArray[tempRow][0] = i;
                sparseArray[tempRow][1] = j;
                sparseArray[tempRow][2] = oldArray[i][j];
            }
        }
    }
    return sparseArray;
}
```

### 解压缩：实现稀疏数组转二维数组

思路：

1. 读取稀疏数组第一行的数据得到原始数组的行数 m、列数 n；
2. 创建二维数组 `oldArray[m][n]`；
3. 读取稀疏数组后几行的值及其在二维数组中的行列信息，存入二维数组的对应位置即可。

实现：

```java
public int[][] parseToOldArray(int[][] sparseArray){
    int[][] oldArray = new int[sparseArray[0][0]][sparseArray[0][1]];
    int validNums = sparseArray[0][2];
    for (int i = 1; i <= validNums; i++) {
        oldArray[sparseArray[i][0]][sparseArray[i][1]] = sparseArray[i][2];
    }
    return oldArray;
}
```

### 结果：

```
原二维数组如下：
0	0	0	0	0	0	0	0	0	0	0	
0	0	2	0	0	0	0	0	0	0	0	
0	0	0	4	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
转换为稀疏数组如下：
11	11	2	
1	2	2	
2	3	4	
将稀疏数组转换为原始二维数组如下：
0	0	0	0	0	0	0	0	0	0	0	
0	0	2	0	0	0	0	0	0	0	0	
0	0	0	4	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
0	0	0	0	0	0	0	0	0	0	0	
```

## 链表

链表在内存中存储的物理结构：

<img src="E:\MyLearning\study_notes\数据结构与算法\pictures\笔记\链表在内存中的存储.jpg" alt="链表在内存中的存储" style="zoom:80%;" />

链表的特征：

* 链表是有序的列表，以节点的方式存储，属于**链式存储**；
* 每个节点包含一个 data 域和一个 next 域（指向下一个节点）；
* 链表的**每个节点不一定是连续存储**；
* 链表分**带头节点的链表和不带头节点**的链表，根据实际需求来确定；

（带头节点）单链表的逻辑结构：

<img src="E:\MyLearning\study_notes\数据结构与算法\pictures\笔记\（带头节点）单链表的逻辑结构.jpg" alt="（带头节点）单链表的逻辑结构" style="zoom:80%;" />

### 单链表的应用实例

完成对一个带头节点的单向链表的增删改查操作的思路：

* 遍历：
  1. head 头结点不能动，需要添加一个辅助变量来帮助遍历整个链表

* 增加节点功能：

  1. 先创建一个 head 节点，作用是表示单链表的头；
  2. 后面每添加一个节点，就直接加入到链表的最后；

  当有要求按某种顺序添加时，需要先找到要添加的节点的位置，这个实现是通过辅助变量 temp 的遍历来实现的，而后将 `新的节点.next = temp.next`，再把 `temp.next = 新的节点`。

* 修改节点

  1. 遍历，根据node查找到准确位置；
  2. 若找到，则修改；

* 删除

  1. 遍历，找到要删除节点的前一个节点；
  2. `temp.next = temp.next.next`，成功删除；
  3. 被删除的节点不会用其它引用指向，它会被垃圾回收机制回收；

实现：

* 链表结构：

  ```java
  class HeroNode{
      public int node;
      public String name;
      public String nickName;
      public HeroNode next;       //  next 默认为 null
  
      public HeroNode(int node, String name, String nickName) {
          this.node = node;
          this.name = name;
          this.nickName = nickName;
      }
  
      @Override
      public String toString() {
          return "HeroNode{" +
                  "node=" + node +
                  ", name='" + name + '\'' +
                  ", nickName='" + nickName + '\'' +
                  '}';
      }
  }
  ```

* 添加节点到单链表（不考虑编号顺序）：

  ```java
  public void add(HeroNode heroNode){
      //1. 找到当前链表的最后节点
      //因为头节点不能动，所以添加一个辅助节点来进行遍历
      HeroNode temp = head;
      while (true){
          if(temp.next == null){
              break;
          }
          temp = temp.next;
      }
      //2. 将最后这个节点的 next 指向 新的节点
      temp.next = heroNode;
  }
  ```

* 添加节点到单链表（按照给定位置插入）：

  ```java
  public void addByOrder(HeroNode heroNode){
      // 头结点不能动，添加一个辅助节点temp来进行遍历
      HeroNode temp = head;
      // 用于标志英雄是否已存在
      boolean flag = false;
      // 遍历链表
      while (true){
          if(temp.next == null){//直接将新节点插在最后
              break;
          }
          if(temp.next.node == heroNode.node){//节点已存在
              flag = true;
              break;
          }
          if(temp.next.node > heroNode.node){//找到了节点应该插入的位置
              break;
          }
          temp = temp.next;
      }
      // 判断节点是否已存在
      if(flag == true){
          System.out.printf("英雄 %s 已存在，不能重复插入！\n", heroNode.name);
          return;
      }
      // 插入节点
      heroNode.next = temp.next;
      temp.next = heroNode;
  }
  ```

* 修改某个节点信息：

  ```java
  public void update(HeroNode newHeroNode){
      if(head == null){// 链表为空
          System.out.println("链表为空");
          return;
      }
      // 头结点不能动，添加一个辅助节点temp来进行遍历
      HeroNode temp = head.next;
      // 判断节点是否存在
      boolean flag = false;
      while (true){
          if(temp == null){// 节点不存在
              break;
          }
          if(temp.node == newHeroNode.node) {//找到要修改的节点
              flag = true;
              break;
          }
          temp = temp.next;
      }
      if(flag == true){
          temp.name = newHeroNode.name;
          temp.nickName = newHeroNode.nickName;
      }else {
          System.out.printf("找不到该节点，无法修改");
      }
  }
  ```

* 删除某个节点：

  ```java
  public void delete(int node){
      // head头节点不能动，添加一个辅助变量用于遍历链表
      HeroNode temp = head;
      // 用于判断节点是否存在
      boolean flag = false;
      while (true){
          if(temp.next == null){ //找不到该节点，无法删除
              break;
          }
          if(temp.next.node == node){// 找到要删除的节点
              flag = true;
              break;
          }
          temp = temp.next;
      }
      if(flag == true){
          temp.next = temp.next.next;
      }else {
          System.out.println("节点不存在，无法删除");
      }
  }
  ```


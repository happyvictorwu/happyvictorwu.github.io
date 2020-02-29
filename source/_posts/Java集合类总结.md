---
title: Java集合类总结
date: 2018-12-19 21:57:46
categories:
- [Java, 集合类]
- [算法, 技巧]
tags:
- 编程
---


# Java 刷题集合类

## 快速查看

| 类 | 方法 |
| :-: | :-: |
| String | charAt toCharArray split substring indexOf lastIndexOf replace length|
| List | add remove get size subList |
| Stack | push pop peek isEmpty size |
| Queue | offer poll peek isEmpty size|
| PriorityQueue | offer poll peek isEmpty size |
| Set | add remove contains isEmpty size first(TreeSet) last(TreeSet)|
| Map | put get getOrDefault containsKey containsValue keySet values isEmpty size|



## 静态数组 Static Array
性质：初始化后长度不能变了
### 初始化
#### 一维
```java
// Type[] names = new Type[capacity];
int[] a = new int[10];
String[] s = new String[3];

// Type[] names = new Type[]{Tpye ...a};
int[] a = new int[]{1, 2, 3, 4};    // int[] a = {1, 2, 3, 4};
char[] b = new char[]{'a', 'b'};    // char[] b = {'a', 'b'};
String[] s = new String[]{"hello", "world"};

// 创建
Set<String> set = new Set[105]; // 每一个指向一个null
for (int i = 0; i < 105; i++) jud[i] = new HashSet<>();  // 每个Set现在才被创建出来
```
#### 二维
```java
// 二维
int[][] c = new int[10][10];
```
### instance属性
#### length
`.length` *记得是属性而不是方法 `arr.length` 没有()*

### 技巧
#### Arrays.sort从小到大排序
`Arrays.sort(int[] arr)` `Arrays.sort(int[] arr, int fromIndex, int toIndex)` 数组类型只要实现了Comparable接口就行(基本数据类型int也可以)
```java
Arrays.sort(int[] arr, int fromIndex, int toIndex, 比较器); //一定是需要泛型

Arrays.sort(arr, (o1, o2) -> o2 - o1); //数组全部 从大到小排序 跟Collections.sort()一样

Arrays.sort(arr, 0, 3, (o1, o2) -> o2 - o1); //从大到小排序，只排序[0, 3)
```

#### Arrays.fill填满一个数组
```java
int[] a = new int[5];
Arrays.fill(a, 1);
```
#### Arrays.copyOf / arr.clone()复制一个数组(二维数组也可以)
```java
int[] a = new int[5];
int[] newA = Array.copyOf(a, 5);
// or
int[][] a = {{1}, {1,2}, {1,2,3}, {1,2,3,4}, {1,2,3,4,5}}; // 不是5*5，第一维1 2 3 4 5
int[][] newa = a.clone(); // 不是5*5矩阵
```
## 动态数组 List & Dynamic Array 
性质: 可以动态扩容的数组
### 初始化
#### 常规 - ArrayList更常用
```java
List<Integer> array = new ArrayList<>();    // 数组
List<Integer> list = new LinkedList<>();    // 链表
```
#### 接受一个Stack、Set等容器为参数 - 以Set举例
```java
// Set<Integer> a = new HashSet....
List<Integer> b = new ArrayList<>(a);
```

### instance方法（List接口方法）
*方法：get, size, add, remove, subList*
#### get
```java
.get(int index)    // 返回元素位置在index的元素e --- array O(1), list O(n)
```

#### size
```java
.size()    // 返回数组长度 --- O(1)
```

#### add
``` java
.add(E e)    // 在尾部添加一个元素e --- O(1)
.add(int index, E e)    // 在index位置插一个元素e --- O(n)
```

#### remove
```java
.remove(int index)    // 删除位于index的元素，并返回删除元素e --- 删除最后元素为O(1)， 其余为O(n)
//删除最后元素 list.remove(list.size() - 1);
```
#### subList
```java
.subList(int from, int to)    // 相当于返回原数组的一个片段,但不要对其进行改动，改动会影响原数组 --- O(1)
// List<Integer> list, 对原来的list和返回的list做的“非结构性修改”(non-structural changes)，
//都会影响到彼此对方. 如果你在调用了sublist返回了子list之后，如果修改了原list的大小，那么之前产生的子list将会失效，变得不可使用
```

### 技巧
`Collections.sort(list);` 从小到大排序
`Collections.sort(list, (o1, o2) -> o2 - o1);` 从大到小排序， 第二个参数为一个比较器
``

## 栈 Stack
性质： 先进后出
### 初始化 (唯一初始化方式)
```java
Stack<Integer> stack = new Stack<>();
```
### instance方法
*方法：push, pop, peek, isEmpty, size*
#### push
```java
.push(E e);    // 入栈元素e， 返回值为元素e --- O(1)
```
#### pop
```java
.pop();    // 出栈一个元素，返回出栈元素e --- O(1)
```
#### peek
```java
.peek();    // 查看栈顶元素， 返回值为栈顶元素e --- O(1)
```
#### isEmpty
```java
.isEmpty()    // 若栈空返回true， 否则返回false --- O(1)
```
#### size
```java
.size()    // 返回栈中元素个数 --- O(1)
```

## 队列 Queue
性质：先进先出
通过实现实现队列接口的LinkedList<>();
### 初始化
#### 使用LinkedList实现Queue接口初始化
```java
Queue<Integer> q = new LinkedList<>();
```
#### 把集合如Stack、Set、List等Collection作为参数
```java
// Set<Integer> set = new HashSet<>();
Queue<Integer> q = new LinkedList<>(set);
```

### instance方法 (Queue接口)
*方法：offer, poll, peek, isEmpty, size*
#### offer
```java
.offer(E e);    // 队尾加入元素e。 若成功入队返回值true，否则返回false --- O(1)
```
#### poll
```java
.poll();    // 出队首，返回出队元素e --- O(1)
```
#### peek
```java
.peek();    // 查看队首元素， 返回值队首元素e --- O(1)
```
#### isEmpty
```java
.isEmpty()    // 若队空返回true， 否则返回false --- O(1)
```
#### size
```java
.size()    // 返回队中元素个数 --- O(1)
```

## 优先队列 PriorityQueue (Heap)
性质：底层是一颗数， 以小根堆为例。对于任意结点来说，该节点的值比其左右孩子的值都要小。 （就是最上面的结点最小）。 大根堆类似，最上面结点最大
### 初始化
#### 小根堆
```java
Queue<Integer> minH = new PriorityQueue<>();    // 小根堆，默认大小为11 相当于  new PriorityQueue<>(11)
Queue<Integer> minH = new PriorityQueue<>(100);  // 定义一个默认容量有100的小根堆。在当中增加元素会扩容，只是开始指定大小。不是size，是capacity
```
#### 大根堆
```java
Queue<Integer> maxH = new PriorityQueue<>((i1, i2) -> i2 - i1);    // 大根堆，默认大小为11 相当于  new PriorityQueue<>(11, (i1, i2) -> i2 - i1)
Queue<Integer> maxH = new PriorityQueue<>(100, (i1, i2) -> i2 - i1);    // 定义一个默认容量有100的大根堆。在当中增加元素会扩容，只是开始指定大小
```

### instance方法 (Queue接口方法)
*方法：offer, poll, peek, isEmpty, size*
#### offer
```java
.offer(E e);    // 在堆中加入元素e，并调整堆。若成功入堆返回值true，否则返回false --- O(logN)
```
#### poll
```java
.poll();    // 弹出堆顶元素，并重新调整堆，返回出队元素e --- O(logN)
```
#### peek
```java
.peek();    // 查看堆顶元素， 返回值堆顶元素e --- O(1)
```
#### isEmpty
```java
.isEmpty()    // 若队空返回true， 否则返回false --- O(1)
```
#### size
```java
.size()    // 返回队中元素个数 --- O(1)
```

### 技巧
#### 从小到大（或从大到小弹出元素）
```java
while (!pq.isEmpty()) {}
```

## 集合 Set - HashSet
性质：Set中没有重复元素，重复添加的元素抛弃
### 初始化
#### 默认构造函数
```java
Set<Integer> set = new HashSet<>();
```
#### 把集合如Stack、Queue、List等Collection作为参数
```java
// List<Integer> list = new ArrayList<>....;
// Set<Integer> set = new HashSet<>(list);
```

### instance方法 (Set接口方法)
*方法：add, remove, contains, isEmpty, size*
#### add
```java
.add(E e);    // 在集合中添加元素E e， 若成功添加则返回true，若集合中有元素e则返回false --- O(1)
```
#### remove
```java
.remove(E e);    // 在集合中删除元素e，若删除成功返回true；若集合中没有元素e，返回false --- O(1)
```
#### contains
```java
.contains(E e);    // 若存在元素e，则返回true，否则返回false --- O(1)
```
#### isEmpty
```java
.isEmpty()    // 若集合为空返回true， 否则返回false --- O(1)
```
#### size
```java
.size()    // 返回集合中中元素个数 --- O(1)
```
#### first (TreeSet)
```java
.first()    // 返回集合里的最小值（若给了比较器从大到小则是返回最大值）
```

#### last (TreeSet)
```java
.last()    // 返回集合里的最大值（若给了比较器从大到小则是返回最小值）
```

## 散列表 HashMap
性质：使用健值对的方式存储数据 <Key,Value>
### 初始化
#### <Key, Value> key和value是任何Collection或任何Object
```java
Map<Characters, Integer> map = new HashMap<>();
```
### instance方法 (Map接口方法)
*方法：put, get, getOrDefault, containsKey, containsValue, keySet, values, isEmpty, size*
#### put
```java
.put(K key, V value);    // 在Map中加入键值对<key, value>。返回value值。如果Map中有key，则replace旧的value --- O(1)
```
#### get
```java
.get(K key);    // 返回Map中key对应的value。若Map中没有该key，则返回null --- O(1)
```
#### getOrDefault
```java
.getOrDefault(K key, V defaultValue);    // 返回Map中key对应的value。若Map中没有该key，则返回defaultValue --- O(1)

// For example:
// Map<Character, Integer> map = new HashMap<>();
// if (...)    // 如果发现k，则k在Map中的值加1。没一开始没有k，则从0开始加1。（相当于给了key在Map中的一个初试值）
    map.put('k', map.getOrDefault('k', 0) + 1);
```

#### containsKey
```java
.containsKey(Key key);    // 在Map中若存在key，则返回true，否则返回false --- O(1)

.get(x) == null // 可以代替改用法
```

#### containsValue
```java
.containsValue(V value);    // 在Map中若存在value，则返回true，否则返回false --- O(1)
```
#### keySet
```java
.keySet();    // 返回一个Set,这个Set中包含Map中所有的Key --- O(1)

// For example:
// We want to get all keys in Map
// Map<Character, Integer> map = new HashMap<>();
for (Character key : map.keySet()) {
    // Operate with each key
}
```

#### values
```java
.values();    // 返回一个Collection<v>,里面全是对应的每一个value --- O(1)

// For example:
// We want to get all values in Map
// Map<Character, Integer> map = new HashMap<>();
for (Integer value : map.values()) {
    // Operate with each values
}
```

#### isEmpty
```java
.isEmpty()    // 若Map为空返回true， 否则返回false --- O(1)
```
#### size
```java
.size()    // 返回Map中中键值对<K, V>的个数 --- O(1)
```

## 字符串 
### String
性质：不可变量(相当于只读final修饰)
每个位置元素是个char
#### 初始化
##### 字符串复制初始化
```java
String s = "abc";
```
##### 基于另外一个字符串
```java
// s = "abc"
String s2 = new String(s);
```
##### 基于char[]
```java
// s = "abc";
// char[] c = s.toCharArray();
String s3 = new String(c);

// 可以偏移
// public String(char value[], int offset, int count)
String s4 = new String(c, 1, 2);    // [offset, offset + count) [)

// 把char[] 变成字符串
char[] ch = {'a', 'b', 'c'};
String.valueOf(ch);
```
#### 类方法
`String.valueOf( 一个参数Object/基本数据类型 )` 返回传入参数obj的toString(),若为空返回字符串"null"。 若为基本类型调用其 包装类的toString方法（`Integer.toString(i)`）
#### instance方法
*方法: charAt, length, substring, equals, indexOf, lastIndexOf, replace, toCharArray, trim, split, toLowerCase, toUpperCase*
##### charAt
```java
.charAt(int index);    // 返回index位置的char --- O(1)
```
##### length
```java
.length();    // 返回字符串长度 --- O(1)
```
##### substring
```java
.substring(int beginIndex, int endIndex);    // 返回字符片段[beginIndex, endIndex) --- O(n)

.substring(int beginIndex);    // 返回字符片段[beginIndex, end_of_String) 就是从beginIndex开始后面的 ---- O(n)
```
##### indexOf 是（暴力查找字符串，不是KMP）
```java
.indexOf(String str)    // 返回str第一个出现的位置(int)，没找到则返回-1。 --- O(m * n) m为原串长度， n为str长度
// (假如要找一个字符char c，str可以表示成String.valueOf(c),然后作为参数传进去.

s.indexOf(String str, int fromIndex);    // 同上，但从fromIndex开始找 --- O(m * n)
```
##### lastIndexOf
```java
.lastIndexOf(String str);    // 返回str最后出现的位置(int)，没找到则返回-1。 --- O(m * n) m为原串长度， n为str长度
// (假如要找一个字符char c，str可以表示成String.valueOf(c),然后作为参数传进去.

.lastIndexOf(String str, int fromIndex);    // 同上，
//但从fromIndex开始从后往前找 [0 <- fromIndex] --- O(m * n)
```
##### replace 只能换char
```java
.replace(char oldChar, char newChar);    // 返回一个新字符串String，其oldChar全部变成newChar --- O(n)
```

##### toCharArray
```java
.toCharArray();    // 返回char[] 数组。 把String编程字符数组 --- O(n)
```
##### trim 去除前后空格
```java
.trim();    // 返回去除前后空格的新字符串 --- O(n)
```
##### split 以什么分开
```java
.split(String regex);    // 返回 String[]，以regex(正则表达式)分隔好的字符换数组。 ---- O(n)

// For example
// 从非"/"算起 若"/a/c" -> 会变成"" "a" "c"
String[] date = str.split("/");     // date[0]:1995 date[1]:12 date[2]:18 --- O(n)
```
##### toLowerCase, toUpperCase 转换大小写
```java
s = s.toLowerCase();    // 返回一个新的字符串全部转成小写 --- O(n)
s = s.toUpperCase();    // 返回一个新的字符串全部转成大写 --- O(n)
```
#### 技巧
通过`+`连接其他字符串， 但是是两个组成一个新的字符串，有开销。最好用StringBuilder

### StringBuilder
#### 初始化
```java
StringBuilder sb = new StringBuilder();
```
#### instance方法
*方法: append, charAt, length, setCharAt, insert, deleteCharAt, delete, reverse, toString*

##### charAt
```java
.charAt(int index);    // 返回index位置的char --- O(1)
```
##### length
```java
.length();    // 返回缓冲字符串长度 --- O(1)
```
##### setCharAt
```java
.setCharAt(int index, char ch);    // 设置index位置的char为ch --- O(1)
```
##### insert
```java
.insert(int offset, String str);    // 在offer位置的插入字符串str--- O(m + n)
```
##### deleteCharAt
```java
.deleteCharAt(int index);    // 删除index位置的char --- O(n)

.deleteCharAt(sb.length() - 1);    // 删除最后一个char --- O(1)
```
##### delete
```java
.delete(int start, int end);    // 删除[start, end)位置的char --- O(n)
```
##### reverse
```java
.reverse();    // 反转缓存字符串 --- O(n)
```
##### toString
```java
.toString();    // 返回一个与构建起或缓冲器内容相同的字符串 --- O(n)
```
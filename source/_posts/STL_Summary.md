---
title: STL Summary
date: 2018-12-18 15:22:38
categories:
- [C++, STL]
tags:
- 编程
---

# STL & C++刷题技巧

| 容器 | 头文件 | 方法 | 备注 |
| :-: | :-: | :-: | :-: |
| **vector** | vector | `push_back/pop_back` `resize` `erase` `front/back` `begin/ end` `size/clear/empty` `[]`| resize的参数跟初始化一样 |
| **list** | list | `push_back/pop_back` `front/back` `erase` `begin/ end` `size/clear/empty` `[]`| |
| **string** | string | `substr` `+=/push_back` `find` `c_str` `replace` `size/length/clear/empty` `[]` | getline(cin,s6); 读取字符到遇到换行，空格可读入，知道‘\n’结束. `c_str()`可以用printf %s输出 `to_string() / stoi()/stod()`在这个头文件中。  char转成字符串 `string(1, c)`|
| **stack** | stack | `push` `pop` `top` `size/empty`| **没有**`clear`， 要一个个弹出 |
| **queue** | queue | `push` `pop` `top` `front/back` `size/empty`| **没有**`clear`， 要一个个弹出 |
| **priority_queue** | queue | `push/pop` `size/empty`| 默认大根堆 小根堆`priority_queue<int, vector<int>, greater<int> > q;` 小于的定义是反的|
| **deque** | deque | `push_back/pop_back` `front/back` `begin/ end` `size/clear/empty` `[]`| deque容器虽然支持随机访问，但是其**随机访问**速度慢于vector容器。插(删)头尾O(1) |
| **set** | set | `insert` `count / find` `erase` `begin/ end` `size/clear/empty`| 自动去重升序排序 `O(logn)` `构造函数vector->set(v.begin(), v.end())` |
| **unordered_set** | unordered_set | `insert` `count / find` `erase` `begin/ end` `size/clear/empty`| `O(1)` |
| **map** | map | `insert` `count / find` `[]` `erase` `begin/ end` `size/clear/empty` | 基于平衡树`O(logn)` 可以使用初始化列表|
| **unordered_map** | unordered_map | `insert` `count / find` `erase` `size/clear/empty`| 基于哈希表`O(1)` |
| **其他** | | `max_element` `lower_bound` `next_permutation` |  ！！！ 用`.size()`不要拿它做减法，返回的是`unsigned int`无符号整数做减法不会小于0 |


# 常用
## 保留几位小数
```c++
printf("%.6f", k);  // 保留6位小数   (double - printf - %f) (doubel - scanf - %lf)
```

## 输入字符串包括空格
```c++
getline(cin,str);   // str 为 string
```

## 输入直到文字流结束 （ctrl + z）
```c++
// scanf
while (scanf("%d %d",&a, &b) != EOF) {
    printf("%d\n", a + b);
}

// cin
while (!cin.eof()) {
    cin >> a >> b;
    cout << a + b << endl;
}
```

## to_string() 函数 (把数字转成字符串的相互转换，负数也可以)

```c++
double f = 23.43
string f_str = to_string(f);    // 23.430000

double f_double = stod(f);   //转换回double  23.43
```

__PS： char转成字符串 `string(1, c)`, 不能用to_string(c),这样是在转换成c的ASCII码__

## pair

* pair对于排序来说是以first为第一关键字排序， second为第二关键字排序
* 赋值的时候可以使用花括号代替`{1, 2}`

## memset
只能做这3个值
每4个字节赋值， Ox3f3f3f3f 大约是10^9次方多一点点
```c++
memset(h, -1, sizeof h);
memset(h, 0, sizeof h);
memset(h, 0x3f, sizeof h);  // memset这个函数是按字节来赋值的，int有4个字节，所以把每个字节都赋值成0x3f以后就是0x3f3f3f3f。
```


## sort的比较函数的几种写法 （默认从小到大）
```c++
// 对vector排序
sort(v.begin(), v.end(), [](const int& a, const int& b){return a > b;});

// 对数组排序
sort(arr, arr + 100);
```

## lower_bound
找到一个大于等于这个数的位置的迭代器
```c++
pos = lower_bound( number, number + 8, 3) - number  // number数组的下标为0的位置开始

// 容器只可以返回迭代器， 然后使用distance函数判断跟begin()的距离
int pos = distance(v.begin(), lower_bound(v.begin(), v.end(), 3));
```

## 一维 <--> 二维
```
第k个元素 -> n * m的矩阵a (都从0开始)

// 一维转二维 k -> [x][y]
 x = k / m;
 y = k % m;
 
 // 二维转一维 [x][y] -> k
 k = x * m + y;
```


## 自定义结构体并重载小于号 
* 结构体`=`为赋值操作，是整个结构体赋值过去。 而类只是浅拷贝
* LeetCode中要写在类的外面
* 写在类中LeetCode需要在前面加friend。[参考](http://www.voidcn.com/article/p-okwlzxpt-zm.html)
* 不要对pair进行重载运算符，最好自己定义结构体
* 遇到`priority_queue`的时候需要相反定义 (认为大的越小， 小的越大)

```c++
struct Node {
    int p1, p2;
    Node(int p1, int p2) : p1(p1), p2(p2) {}
};

// 推荐方式 !!!!!!! 
bool operator<(const Node& a, const Node& b)
{
    if (a.p1 == b.p1) return a.p2 < b.p2;
    return a.p1 < b.p1;
}

// 以p1为第一关键字， p2为第二关键字排序  && 优先级比 || 高
bool operator<(const Node& a, const Node& b)
{
    // return a.p1 < b.p1 || !(b.p1 < a.p1) && a.p2 < b.p2;
    return a.p1 < b.p1 || a.p1 == b.p1 && a.p2 < b.p2;
}

Node n1(1, 2);
```

* 为什么重载<号要这样写，而不写成`return a.p1 < b.p1 || a.p1 == b.p1 && a.p2 < b.p2`？因为p1和p2是int，他们虽然有重载`==`。但是对于没有重载`==`来说,只须重载小于运算符. [参考文献](https://blog.csdn.net/dpfordor/article/details/1958077)

## 堆之标准比较器构造 (对于指针来说可以使用)

```c++

typedef pair<string, int> Node;
typedef function<bool(const Node&, const Node&)> Compare;

int main()
{
    // ...
    
    Compare comparator = [](const Node& a, const Node& b) {
        if (a.second == b.second)
            return a.first < b.first;    // order by alphabet  ASC
        return a.second > b.second;    // order by freq DESC
    };
    
    // Min heap by frequency
    priority_queue<Node, vector<Node>, Compare> q(comparator);
    
    // ...
}

```

关于指针的写法
```c++
    typedef function<bool(const ListNode*, const ListNode*)> Compare;
    
    
    
    Compare cmp = [](const ListNode *a, const ListNode *b) {
            return a->val > b->val;
    };
    // 大根堆
    priority_queue<ListNode*, vector<ListNode*>, Compare> q(cmp);

```

## cin优化
```
// 两句话一起写
ios::sync_with_stdio(false);
cin.tie(0);
```

## swap交换两个vector
* O(1)时间，实质是指针交换

```c++
swap(v1, v2);
```

## max函数多参数
* C++ 11之后支持传入参数初始化列表

```c++
int mx = max({a, b, c, d});    // a, b, c, d的最大值
```

## 位运算
* 数的最高位是符号位
* 左移去掉最高位，然后左边补0。 
* 右移根据正数还是负数决定。 正数最高位补0， 负数补1
* （记）计算机以补码表示： 除符号位越大，数越大
* 2的n次方 `1 << n` (n <= 30)    (1后面几个0就是 2的几次方)
* `INT_MAX == 1后面31个零     INT_MAX == 0后面31个1`
* 取反操作 `~`


## 生成随机数
`a <= x <= b`

* 方法一：两者差值不能超过INT_MAX

```c++
srand(time(0));  // 先设置随机种子

int randX(int a, int b) {   // a b数的差值不能超过INT_MAX
    return a + (rand() % (b - a + 1));
}

// 生成方法
int n = randX(1, 100);   // n ∈ [1, 100]
```

* 方法二：int间的任何数

```c++
typedef long long ll;
int myrand(int mod) { return ((ll)rand()<<32^(ll)rand()<<16^rand())%mod; }
#define random(a, b)((a) + myrand((b) - (a) + 1)) // Integer[a,b]

// 生成方法
int n = random(INT_MIN, INT_MAX);  // n ∈ [-2147483648, 2147483647]
```

## 通过迭代器来初始化容器 hash表 < -- > vector

* set(map) <-> vector <-> string
* 总之有迭代器的都可以。 stack 和 queue没有

```c++
vector<int> a = {3, 2, 1};
set<int> se(a.begin(), a.end()); // vector -> set
vector<int> a2(se.begin(), se.end()); // set -> vector

vector<pair<int, int>> a = {{3, 1}, {2, 1}, {1, 1}};
unordered_map<int, int> mp(a.begin(), a.end()); //vector -> map
vector<pair<int, int>> a2(mp.begin(), mp.end());  // map -> vector

unordered_set<char> se(word.begin(), word.end()); //word 为 string
```
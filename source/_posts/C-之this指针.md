---
title: C++之this指针
date: 2020-02-29 22:04:25
categories:
- [C++]
tags:
- 编程
---

### `this` pointer(指针) in C++
* 每一个类的对象来说，成员属性是在每个对象是一个是副本。
* 而对于每个对象是共享一个类的成员函数。（this是解决区分谁调用了这个成员函数）

通俗来说，this指针是用来区分哪一个对象。this指针作为隐藏参数传递给所有非静态成员变量函数调用，而且可以用作所有**非静态函数体**内部的局部变量。

*静态函数是不能访问有this指针的，他是属于类的。*

#### 作用1
初始化对象的成员变量(本地变量与成员变量重名的时候，当然不重名的时候也可以使用)
```c++
#include<iostream> 
using namespace std; 
  
/* local variable is same as a member's name */
class Test 
{ 
private: 
   int x; 
public: 
   void setX (int x) 
   { 
       // The 'this' pointer is used to retrieve the object's x 
       // hidden by the local variable 'x' 
       this->x = x; 
   } 
   void print() { cout << "x = " << x << endl; } 
}; 
  
int main() 
{ 
   Test obj; 
   int x = 20; 
   obj.setX(x); 
   obj.print();   // 输出结果：x = 20
   return 0; 
}
```

#### 作用2
返回调用对象的引用（从而可以链式设置等）
```c++
/* Reference to the calling object can be returned */ 
Test& Test::func () 
{ 
   // Some processing 
   return *this; 
}  
```
注意这里成员函数`func`返回的引用 `&`
```c++
#include<iostream> 
using namespace std; 
  
class Test 
{ 
private: 
  int x; 
  int y; 
public: 
  Test(int x = 0, int y = 0) { this->x = x; this->y = y; } 
  Test &setX(int a) { x = a; return *this; }  // 返回对象引用
  Test &setY(int b) { y = b; return *this; }  // 返回对象引用
  void print() { cout << "x = " << x << " y = " << y << endl; } 
}; 
  
int main() 
{ 
  Test obj1(5, 5); 
  
  // Chained function calls.  All calls modify the same object 
  // as the same object is returned by reference 
  obj1.setX(10).setY(20);   // 链式设置
  
  obj1.print();  // 输出结果：x = 10 y = 20
  return 0; 
} 
```

#### 作用3
删除对象
```c++
#include<iostream> 
using namespace std; 
  
class Test 
{ 
private: 
  int x; 
  int y; 
public: 
  Test(int x = 0, int y = 0) { this->x = x; this->y = y; } 
  void setX(int a) { x = a; } 
  void setY(int b) { y = b; } 
  void destroy()  { delete this; }  // 删除这个对象调用各个成员变量的析构方法
  void print() { cout << "x = " << x << " y = " << y << endl; } 
}; 
  
int main() 
{ 
  Test obj; 
  obj.print();   // print()一定要在destroy之前
  obj.destroy();   // 删除掉了obj所指的对象
  return 0; 
} 
```

#### 刷LeetCode时候规范用法
把函数参数赋值给`this->成员属性`，这样可以再任何地方使用成员属性，就不用作为引用参数传入了。

```c++
class Solution {
private:
    vector<vector<int>> a;  // 自己定义成员变量
    int n, m;  // // 自己定义成员变量
    
    void solve() {
        a[0][0] = 1;
        // ... 正常使用a, m, n
    }
    
public:  
    int getMaximumGold(vector<vector<int>>& a) {
        this->a = a;    // 把本地变量赋值给同名的数据成员(data member)
        this->n = a.size(), this->m = a[0].size();  // // 把本地变量赋值给同名的数据成员(data member)
        
        solve();
        // 操作逻辑...

        //return ans;
    }
};
```

#### 参考文献

[geeksforgeeks](https://www.geeksforgeeks.org/this-pointer-in-c/) 
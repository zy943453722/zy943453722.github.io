---
title: c++中类的大小
date: 2017-10-22 16:34:26
tags: c++
categories: 编程语言
comments: true
---
*** 类所占内存的大小是有成员变量（静态变量除外）决定的，成员函数是不计算在内的，他们只是名义上在类里，其实同一个类的多个对象共享函数代码，而我们访问类的成员函数是通过类中的自引用指针实现的，所以我们访问成员函数是间接获得地址的。 ***
### 空类
空类的大小为1：

目的标示这个类，c++要求每个实例在内存中都具有独一无二的地址。
### 普通类(不继承，不含虚函数）
跟struct的原理一致，内存对齐原则，但静态变量不占用内存，原因是编译器将其放在了全局变量区。
### 含有虚函数的类
c++类中有虚函数的时候有一个指向虚函数的指针(vptr),因此此指针要占用字节空间，**虚函数个数和大小无关。**
### 继承的类
子类的大小是本身成员变量的大小加上父类的大小，若父类本身就含有虚函数，则子类无论有没有虚函数都不能算上。
**当继承空类时，不加上空类的大小，而是只算本身子类的大小。**
### 实例（注：笔者是在64位操作系统下操作得出的结果
```
#include<iostream>
using namespace std;
#include<stdlib.h>
#include<stdio.h>
class cBase
{

}; //标识存在为1
class cBase1
{
    int a;
    char p;
};//内存对齐，4个字节和1个字节，1字节按4字节算
class cBase2
{
  public:
      cBase2();
      virtual ~cBase2();//virtual开辟一块指针，因此为8
  private:
     int a;//4，因内存对齐则为8
    char *p;//8
};
class c:public cBase
{
   virtual void fun() = 0;//纯虚函数
};
class b{};
class d:public b,public c{};//共享虚函数的指针
int main()
{
    cout << "cBase类的大小:" << sizeof(cBase) << endl;
    cout << "cBase1类的大小:" << sizeof(cBase1) << endl;
    cout<< "cBase2类的大小:" << sizeof(cBase2) << endl; 
    cout<< "c类的大小:" << sizeof(c) <<endl; 
    cout << "d类的大小:" << sizeof(d) <<endl;
    return 0;
}
```
结果为：<br>
cBase类的大小：1<br>
cBase1类的大小：8<br>
cBase2类的大小：24<br>
c类的大小：8<br>
d类的大小：8<br>
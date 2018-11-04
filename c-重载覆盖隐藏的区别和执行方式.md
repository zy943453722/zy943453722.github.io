---
title: c++重载覆盖隐藏的区别和执行方式
date: 2017-09-14 22:53:03
tags: [c++]
categories: 技术
comments: true 
---
## 成员函数被重载（overload）的特征
* 相同的范围（在同一个类中）
* 函数名字相同
* 参数不同
* virtual关键字可有可无
```
class A{
  private: 
     int a
  public：
      void print();
      void print(int aa);
  };
  ```
  ## 覆盖（override）的特征
  * 不同的范围（分别于派生类和基类）
  * 函数名字相同
  * 参数相同
  * 基类函数必须有virtual关键字
  
  ***覆盖是指派生类函数覆盖基类函数***
  ```
  class A{
    public:
      virtual void print();
   };
   class B:public A
   {
      public:
        void print();
    };
   ```
   ## 隐藏（redefining）的规则
   * 派生类函数与基类函数同名，但是参数不同，此时，不论有无virtual关键字，基类的函数都会被隐藏。
   * 若派生类函数与基类函数同名且参数相同，但是基类函数没有virtual，基类的函数会被隐藏。
   
   ***隐藏是指派生类的函数屏蔽了与其同名的基类函数。***
   ```
   class A{
   public:
     void print();
    };
    class B:public A{
    private:
     int a;
    public:
      void print(int aa);
      };
      -----------------------------------------------------
      class A{
      public:
        void print();
     };
     class B:public A{
     public:
       void print();
     };
      ```
      ## 3种情况怎么执行
      1. 重载：看参数
      2. 隐藏：看指针类型
      3. 覆盖：看实体对象类型
    
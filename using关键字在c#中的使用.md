---
title: 'using关键字在c#中的使用'
date: 2017-06-06 22:50:36
tags: [c++,函数]
categories: 编程语言
comments: true
---
# using关键字在C#中的3种用法

**using 关键字有两个主要用途：**

  (一).作为指令，用于为命名空间创建别名或导入其他命名空间中定义的类型。
  
  (二).作为语句，用于定义一个范围，在此范围的末尾将释放对象。


## (一).作为指令

1、用在命名空间里    

using + 命名空间名字，这样可以在程序中直接用命令空间中的类型，而不必指定类型的详细命名空间，类似于Java的import，这个功能也是最常用的，几乎每个cs的程序都会用到。 
例如：using System; 一般都会出现在*.cs中。

2、为命名空间或类型创建别名，using + 别名 = 包括详细命名空间信息的具体的类型。

这种做法有个好处就是当同一个cs引用了两个不同的命名空间，但两个命名空间都包括了一个相同名字的类型的时候。当需要用到这个类型的时候，就每个地方都要用详细命名空间的办法来区分这些相同名字的类型。而用别名的方法会更简洁，用到哪个类就给哪个类做别名声明就可以了。注意：并不是说两个名字重复，给其中一个用了别名，另外一个就不需要用别名了，如果两个都要使用，则两个都需要用using来定义别名的。

```
using System; 
using aClass = NameSpace1.MyClass; 
using bClass = NameSpace2.MyClass; 
namespace NameSpace1 { 
         public class MyClass { 
                  public override string ToString() { 
                         return "You are in NameSpace1.MyClass"; 
                         } 
                     } 
                 } 
namespace NameSpace2 { 
     class MyClass { 
           public override string ToString() { 
                  return "You are in NameSpace2.MyClass"; 
                  } 
           } 
     } 
 namespace testUsing { 
       using NameSpace1; 
       using NameSpace2; 
       class Class1 { 
             [STAThread] static void Main(string[] args)              { 
                 aClass my1 = new aClass();
                 Console.WriteLine(my1); bClass my2 = new bClass(); 
                 Console.WriteLine(my2); Console.WriteLine("Press any key"); Console.Read();
                 }
             } 
    }
```
(二).作为语句

* using 语句允许程序员指定使用资源的对象应当何时释放资源。
* using 语句中使用的对象必须实现 IDisposable 接口。此接口提供了 Dispose 方法，该方法将释放此对象的资源。

    ①可以在 using 语句之前声明对象。
　　    ```
        Font font2 = new Font(“Arial”, 10.0f);
        using (font2)
        {
        // use font2;
        }
      
    ②可以在 using 语句之中声明对象。
　　    ```
      using (Font font2 = new Font(“Arial”, 10.0f))
      {
      // use font2;
      }
    ③可以有多个对象与 using 语句一起使用，但是必须在 using 语句内部声明这些对象。
       ```
       using (Font font3=new Font(“Arial”,10.0f), font4=new Font(“Arial”,10.0f)){
       // Use font3 and font4. 
       }

***使用规则:***

①using只能用于实现了IDisposable接口的类型，禁止为不支持IDisposable接口的类型使用using语句，否则会出现编译错误；

②using语句适用于清理单个非托管资源的情况，而多个非托管对象的清理最好以try-finnaly来实现，因为嵌套的using语句可能存在隐藏的Bug。内层using块引发异常时，将不能释放外层using块的对象资源；

③using语句支持初始化多个变量，但前提是这些变量的类型必须相同，例如：
        ```
        using(Pen p1 = new Pen(Brushes.Black), p2 = new Pen(Brushes.Blue)){
        // 
        }
      ```
④针对初始化多个不同类型的变量时，可以都声明为IDisposable类型，例如：
```
        using (IDisposable font = new Font(“Verdana”, 12), pen = new Pen(Brushes.Black))
　　    {
　　        float size = (font as Font).Size;
　　        Brush brush = (pen as Pen).Brush;
　　    }
      ```

using实质
    在程序编译阶段，编译器会自动将using语句生成为try-finally语句，并在finally块中调用对象的Dispose方法，来清理资源。所以，using语句等效于try-finally语句，例如：

```
using (Font f2 = new Font(“Arial”, 10, FontStyle.Bold)）
{
     font2.F();
}
```
被编译器翻译为：
```
    Font f2 = new Font(“Arial”, 10, FontStyle.Bold);　
    try{ 
        font2.F();　
    }
    finally　{ 
       if (f2 != null) ((IDisposable)f2).Dispose();
       }
```

转载自 
http://www.cnblogs.com/xiaobiexi/p/6179127.html
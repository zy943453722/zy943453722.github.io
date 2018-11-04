---
title: 探寻psr标准
date: 2018-01-02 09:47:01
tags: [php,编程基础]
categories: 编程语言
comments: true 
---
## 前言
**笔者学习symfony框架，看源码之时，发现好多函数带有psr的后缀，心想这是个什么东东。。。于是乎google了一番，发现这正是“心仪已久”的PHP开发的标准规范啊！！！**<br>
但是好多东西在对应的官方文档[PSR标准规范文档](https://psr.phphub.org/)都有，因此笔者在此只做代码的示范，用代码来表示规范。
## 简介
PSR 是 PHP Standard Recommendations 的简写，由 PHP FIG 组织制定的 PHP 规范，是 PHP 开发的实践标准。<br>
目前已表决通过了 6 套标准，已经得到大部分 PHP 框架的支持和认可。<br>
**标准目录如下：<br>**
![图1](探寻psr标准/1.png)

## 详解
### 基本代码规范和编程风格规范
```
<?php 
/**
 * psr标准中的大部分语言规范
 * 
 * @author zy
 */
//php代码文件必须以这个标签或者<?= 开始
//每行的字符数控制在80~120之间
namespace Vender\Package;//命名空间块后都有一个空行，命名空间和子命名空间必须和相应的文件基目录(文件绝对路径)匹配。
namespace Symfony\Component\Routing;//完整的类名必须有个顶级命名空间和一个或多个子命名空间，末尾的类名与对应的.php文件同名

use Symfony\Component\Routing\Route;
use FooInterface;//use语句后面也要有一行空行,use务必声明在namespace之后

//类名必须遵循StudlyCaps大写驼峰命名法即多个单词组成名字每个单词首字母大写
class FooFunction extends Bars implements FooInterface,
    \ArrayAccess,
    \Countable
{//多实现可以多行显示，但是要有缩进，且每个接口自成一行
 //类的前后花括号均自成一行
    public $superStar;//每个属性都要添加访问修饰符
    public $commonPeople;//类的普通属性用哪种命名方式均可，但要统一
    protected $files = null;//null,true,false这些关键字都小写
    const MAX_COUNT = 120;//const定义的变量必须大写且单词间用下划线分隔
    //方法名遵循camelCase式的小写驼峰命名法即多个单词组成名字时首单词小写，之后的每个单词首字母大写
    public function sampleFunction($a, $b = null)
    {//方法的前后花括号也均自成一行
        //缩进时不能使用tab要用4个空格进行缩进
        if ($a === $b) {
            //控制结构诸如if-else，switch-case，while，for，foreach等等书写时这些关键字后要有一个空格
            //前括号之前也要有一个空格，且前括号在关键字同一行，后括号自成一行
            bar();
        } elseif ($a > $b) {//变量与运算符之间要有空格
            $foo->bar($arg1);
        } else {
            BazClass::bar($arg2, $arg3);//方法的参数每个逗号后必须要有一个空格，有默认值的放到参数列表末尾
        }
    }
    //参数列表可以单独一行,结束括号必须和方法前花括号自成一行,final、abstract必须在访问修饰符前面，static在访问修饰符之后
    final public static function aVeryLongMethod(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    ){
        switch ($expr) {//case相对switch要有缩进
            case 0: 
                echo 'xxxxxxx';//执行语句相对case也要缩进
                break;
            case 1://遇到这种case没有break的直穿语句，要有no break的注明 
                echo 'second';
                //no break
            case 2:
            case 3:
                echo 'third';
                break;
            default:
                echo "default case";
                break;
        }    
    }
    public function simpleFunction()
    {
        while ($exp) {//同样的诸如do-while，try-catch控制结构需要相同的结构
            echo 'zzz';
        }
        $clouseWith = function ($arg1, $arg2) use ($var1, $var2) {
            /*对于闭包函数也就是匿名函数，function后要有一个空格，use的前后都要有一个空格，并且前括号要和关键字在同一行，
            但后括号单独成行*/
        };
    }

}

/*
诸如此类产生副作用的操作，ex：
                 生成输出
                 直接的 require 或 include
                 连接外部服务
                 修改 ini 配置
                 抛出错误或异常
                 修改全局或静态变量
                 读或写文件等
的操作不能和声明类、函数等在同一个php文件下
include "file.php";
echo "<html>\n";
ini_set('error_reporting',E_ALL);
*/
//纯php代码文件最后必须省略“?>”标签
//同时php文件最后必须要有一行空行作为结束
```
### 其他规范
其他规范都是在框架或者项目中需要用的诸如日志、自动加载、缓存规范。因此就不在此赘述，贴出官方的规范：[https://psr.phphub.org/](https://psr.phphub.org/)

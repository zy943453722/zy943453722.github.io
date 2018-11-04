---
title: php中静态类、抽象类和接口类的区别
date: 2018-01-11 14:30:20
tags: [php基本语法]
categories: 编程语言
comments: true
---
## 前言
**最近笔者学习了php的基本知识，其基本语法像极了c语言，但是取消了指针等特性，且操作起来更加简便。下面就浅析一下php面向对象中的特性：静态类、抽象类和接口以及很好用的代码复用技术trait。**

## 静态类

### 静态类定义：
   ***类中含有static修饰的方法或者属性的类。***

### 静态类的特性和易错点
1. 静态类中的成员不用实例化对象访问。用类名：：属性名/方法名 访问。
2. 实例化的对象只能访问静态方法，不可访问静态属性。
3. 继承时子类可以继承父类的公有和受保护的方法和属性。
4. 可以说静态方法和属性不属于这个类，所以不能用自引用指针$this去引用，但却可以通过self或者parent访问。

### 实例
```
<?php
class Foo{
    public $my = "zy";
    public static $my_static = 'foo';//初始化附常量
    public function staticValue()
    {
        echo $this->my;
        //echo $this->my_static;这些属性不属于类
        return self::$my_static;
    }
    public static function handle()
    {
        //$this->$my_static;静态方法中没有$this这个伪变量
        echo "这是静态变量\n";
    }
    protected static function func()
    {
       echo "protected static\n";   
    }
}
class Bar extends Foo{
    public function fooStatic(){
        return parent::$my_static;       
    }
    public function funn()
    {
        echo self::func()."\n";//继承了
    }
}
echo Foo::$my_static."\n";
echo Foo::handle()."\n";
$foo = new Foo();
//echo $foo->$my_static."\n";//不可用对象直接访问静态属性
echo $foo->handle()."\n";//用对象可直接访问静态方法
echo $foo->staticValue()."\n";
echo Bar::$my_static."\n";
$bar = new Bar();
echo $bar->fooStatic()."\n";
echo $bar->handle();
$bar->funn();
?>
```

## 抽象类

### 抽象类的定义
*** 被abstract修饰的类。***

### 抽象类的特性和易错点
1. 抽象类不可被实例化
2. 任何一个类，若其中至少有一个抽象方法，那么该类就必须定义为抽象类，类中可以有非抽象的方法或属性。
3. 抽象方法只声明其调用方式，不定义具体功能。
4. 继承某个抽象类后，其子类必须实现所有的抽象方法，且不论函数名、参数都不能改变，但是继承访问控制可以一致或更宽松。
5. 若子类继承的父类抽象方法中参数列表是可选参数，那么也可以与父类参数个数不一致，即子类可以有个默认参数列表。

### 实例：
```
<?php
abstract class AbstractClass{//此类不可实例化对象
    abstract protected function getValue();
    abstract protected function prefixValue($prefix);//只声明，不作具体实现
    abstract public function pre($han);
    const i = 12;
    public $a = 13;
    public function printOut()
    {
        echo $this->a."\n";
        echo $this->getValue()."\n";
    }
} 
class ConcreteClass1 extends AbstractClass
{
    protected function getValue() {
        return "ConcreteClass1";//实现具体实现，必须保持一致
    }

    public function prefixValue($prefix) {
        return "$prefix.ConcreteClass1";
    }
    public function pre($han,$ren = 'a')
    {
       echo $han.$ren."\n";       
    }
}

class ConcreteClass2 extends AbstractClass
{
    public function getValue() {
        return "ConcreteClass2";
    }

    public function prefixValue($prefix) {
        return "{$prefix}ConcreteClass2";
    }
    public function pre($han,$ren = 'a')
    {
       echo $han.$ren."\n";       
    }
}

$class1 = new ConcreteClass1;//继承抽象类的类才可以实例化
$class1->printOut();
echo $class1->prefixValue('FOO_') ."\n";
$class1->pre("zy");//可以打印出默认的参数
$class2 = new ConcreteClass2;
$class2->printOut();
echo $class2->prefixValue('FOO_') ."\n";
echo AbstractClass::i;//同样可以输出常量，无法输出变量
?>
```

## 接口

### 接口的定义
*** interface修饰的一个特殊的抽象类，但不是类。***

### 接口特性和易错点
1. 接口定义的所有方法都是空的
2. 接口中的所有方法都是公有的，这是接口的特性 
3. 接口的定义使用interface，但是接口的实现就要用到implements,实现接口的实际是类
4. 接口实现过程中要实现全部定义的接口 
5. 接口可以继承，用extends实现，与接口的实现不是一个意思,继承之后用类实现时要全部实现
6. 接口中可以声明常量但是不可声明变量
7. 可以说接口是特殊的抽象类，他里面的方法也是不实现功能的抽象类但是为了方便，不写abstract还有定义成interface而非类
8. 一个类虽然是单继承的，但是一个类可以实现多个接口,多个接口之间用逗号隔开

### 实例
```
<?php
interface iTemplate
  {
      const name = "ha";
      public function setVariable($name,$var);//必须是公有的方法
      public function getHtml($template);
  }
  interface inTel{
    public function handle();   
  }
  //一个接口类可以实现多个定义的接口
  class Template implements iTemplate,inTel{
      private $vars = array();
      public function setVariable($name,$var)
      {
          $this->vars[$name] = $var;
      }
      public function getHtml($template)
      {
          foreach($this->vars as $name => $value)
          {
              $template = str_replace($name,$value,$template);//把字符串template中的name字符换成value
          }
          return $template;
      }
      public function handle()
      {
          echo "handle things\n";
      }
  }//用一个类去实现接口  
    //$te = new iTemplate();因为是一种特殊的抽象类，因此也是不能实例化对象的
    $tel = new Template();
    $tel->setVariable('zy','人才');
    echo $tel->getHtml('zyzsddw')."\n";
    $tel->handle();
    echo iTemplate::name;//输出接口中定义的常量
  ?>
```

## trait
### trait的定义
***Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制***

### trait的特性和易错点
1. trait是一种代码复用技术，相当于一种池技术，把好多功能(方法)写在其中，用的时候调用即可。可代替继承技术
2. 他的调用优先级高于继承后的同名方法的优先级
3. 它本身无法进行实例化
4. 可以同时有多个trait，类中use声明时用逗号隔开
5. trait也可以使用多个trait作为成员
6. 不可以声明静态成员，可在方法中定义静态变量

### 实例
```
<?php
class Base{
    public function sayHello(){
        echo 'hello';
    }
}
trait mysay{
    public function mysays()
    {
        echo "this is my says\n";
    }
}
trait SayWorld{//声明方式相当于一个类
    //static public $c = 1;除非定义在方法中
    public $name = "zy";
    public function sayHello(){
      parent::sayHello();
      echo "World\n";
   }  
   public function says(){
       echo "zwdfw\n";
   }
   public static function func()
   {
       static $c = 1;
       echo "$c.this is static\n";
   }
   protected function sayno()
   {
       echo "sayno"."\n";
   } 
   abstract public function getworld();//允许定义抽象方法
}
class Myhello extends Base{
   use SayWorld,mysay;//用这种方式调用trait,相当于把所有的方法都继承到了  
   public function getworld()
   {
       echo "nihao world\n";//使用了trait就要实现抽象方法
   }
}
$foo = new Myhello();
$foo->sayHello();//优先级高于继承来的同名方法
$foo->says();//随意调用其中的方法
//$foo->sayno();//私有方法和受保护的方法无法从trait中获取
$foo->func();
$foo->mysays();
mysay::mysays();//无法实例化trait的对象访问，可以用trait名::方法/变量的形式访问
echo $foo->name."\n";
//echo $foo->c."\n";
?>
```

## 这四种php中oo特性的代表的区别

|      | 静态类 | 抽象类 | 接口  | trait |
|------|:-------:|:-----:|:----:|:-------:|
| 是否可以实例化对象 | 是 | 否 | 否 | 否 |
| 类外访问方式      | 1.类名::属性/方法 <br>   2.对象名->方法 | 子类继承实现，<br>实例化子类，<br>子类对象调用属性/方法 | 子类implements实现，子类实例化对象调用属性/方法|类中use实现，类外实例化对象调用属性/方法|
| 方法是否立即实现 |类内外都可以|子类实现|子类实现|类内外都可以|
|方法的访问控制|都可以|抽象方法公有或者受保护，普通方法都可以|均为公有|都可以|
|实现方式|类内类外都可以|子类继承实现|子类继承实现|内部实现|
|是否可以声明常量/变量|都可以|都可以，但变量无法类外访问|常量可以，变量不可以|都可以|


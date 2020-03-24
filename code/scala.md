# 一、

### 1.程序入口,IDEA中java用psvm，scala用main快捷（异）

~~~
Scala程序的执行入口是main（）函数
~~~

~~~scala
scala: 
object Cc {
  def main(args: Array[String]): Unit = {
    println("cc")
  }
}
//反编译后：
public final class HelloWorld
{
  public static void main(String[] paramArrayOfString)
  {
    HelloWorld$.MODULE$.main(paramArrayOfString);
  }
}

public final class HelloWorld$
{
  public static final  MODULE$;

  static
  {
    new ();
  }

  public void main(String[] args)
  {
    Predef..MODULE$.print("ccc");
  }
  private HelloWorld$() { MODULE$ = this; }

}
//结论：main方法在编译后自动形成了public static void main(...)
//scala在编译源码时，会生成两个字节码文件，静态main方法执行另外一个字节码文件中的成员main方法
//scala是完全面向对象的语言，那么没有静态的语法，只能通过模拟生成静态方法
//编译时将当前类生成一个特殊的类HelloWorld$，然后创建对象来调用这个对象的main方法
//一般加$的类的对象，称为“伴生对象”，伴生对象都可以通过类名访问，来模拟java中静态语法
//伴生对象的语法用object声明
~~~

~~~java
java:
public class Cc {
    public static void main(String[] args) {
		System.out.println("cc");
    }
}
//JVM读取固定程序入口
~~~

### 2.关键字（异同）

~~~java
scala中没有public关键字，默认所有的访问权限都是公共的
scala中没有void关键字，采用Unit来模拟 （它是一个类，完全面向对象）
java中有public关键字
~~~

### 3.方法（异）

scala:       def main(args:Array[String]):Unit={方法体}    

java:          public static void main(String[] args){方法体}

~~~
scala中声是方法用用关键字def
//方法后小括号是方法的参数列表
//scala参数列表中的表明方式和java不一样
java中参数列表主要考虑类型 （类型 参数名）
scala中优先考虑名称  （参数名： 类型）
~~~

~~~java
java中方法声明和方法体直接连接
scala中方法的声明和方法体是通过等号连接
~~~

~~~
scala中将方法的返回值类型放置在方法声明后面使用冒号连接
~~~

### 4.执行流程（异同）

~~~java
scala源文件以 “.scala”为扩展名
java源文件以 “.java”为扩展名
~~~

~~~java
java:编译成.class再运行字节码文件
javac 源文件名.java
java 源文件名
~~~

~~~java
scala有两种方式：
一.编译成.class再运行字节码文件
scalac 源文件名.scala
scala 源文件名
二.编译运行一步到位
scala 源文件名.scala 
~~~

### 5.大小写（同）

~~~java
java和scala都严格区分大小写
~~~

### 6.语句结尾（异）

~~~java
Scala语句结尾不加分号：(同一行写几句的话，前面加分号，尽量一行写一条语句)
print("CC")
Java语句结尾加分号:
System.out.print("CC");
~~~

### 7.打印语句括号里（异同）

~~~java
print("CC"+cc+"DD"+dd) 类似Java
~~~

~~~java
printf("name=%s,age=%d,url=%s \n",name,age,url) 类似C语言
~~~

~~~java
println(s"name=$name,age=$age")//name=cc,age=18 类似PHP
print(s"${name+age}")//cc18
print(f"${name},${age}%.2f")//cc,18.00
print(raw"${name}+${age}%.2f")//cc,18%.2f
推荐此方式
~~~

### 8.注释（同）

Scala和Java一样

~~~java
单行 //
多行 /*     */
文档 /**    */
~~~



# 二、变量

### 1.声明变量

Scala：

变量声明时，必须有初始值（显示初始化）

在方法外部声明的变量用val关键字，等同于使用final修饰，不能再修改。

方法内部也不能改

~~~java
var/val 变量名称 ：变量类型 = 变量的值
var修饰变量可变。val修饰变量不可变但对象的状态（值）是可以改变的如：自定义的对象、数组、集合等
var name : String = "cc"
val name = "cc"//可省String
var name:String(此行错) 必须显示初始化 不能分开写
~~~

Java：

~~~java
变量类型 变量名 = 变量值
String name = "cc";
~~~

### 2.常量

java所谓的常量 就是字面量

# 三、数据类型

### 1、介绍

scala与java有相同数据类型，在Scala中数据类型都是对象，也就是说Scala没有Java中的原生类型

Scala数据类型分为两大类AnyVal(值类型)和AnyRef（引用类型），都是对象

~~~
 val age : Int =20;
 val age : int =20;(此行会报错)
~~~

~~~java
Java有：
byte short int long float double boolean char
Scala有：
val b : Byte =10
val s : Short = 10
val i : Int = 10
val lon : Long =10
val f : Float =10.0f
val d : Double = 10
val bln : Boolean = true
val c : Char = 'c'
//首写字母大写
val ii : Integer = 10
~~~

### 2.使用细节

~~~java
Scala的整型常量/字面量 默认Int型，声明常量Long须后加L或l
浮点型默认Double 声明Float要加F或f

~~~

### 3.相互转换

1.基本类型-->String类型

~~~java
String str1 = true + " ";
String str2 = 5.0 + " ";
String str3 = 90 + " ";
~~~

2.String类型-->基本类型

要确保String类型转成有效的数据比如：“123”可转成整数，但“hellocc”不能

~~~java
s1.toInt
s1.toFloat
s1.toDouble
s1.toByte
s1.toLong
s1.toShort
~~~

# 四、标识符

1.scala中的下划线有特殊用途，不能独立当成变量名来使用

# 五、运算符

Scala中没有++   -- 只能通过+=  -=来实现同样效果

Scala += 有类型提升 和Java不一样

# 六、逻辑控制

scala

~~~scala
//for(i <- 1 to 5) { }
object HelloWorld {
  def main(args: Array[String]): Unit = {
    for (i <- -5 to 5) {
      print(i)
    }
  }
}
//-5-4-3-2-101234
~~~

~~~scala
for(i <- 1 until 5) { }
//不包含5
~~~



java

~~~java
//for(int i; i< 5;i++){  }
public class Cc {
    public static void main(String[] args) {
        for (int i = -5; i < 5; i++) {
            System.out.print(i);
        }
    }
}
//-5-4-3-2-1012345
~~~



2.打印,用scala一行for循环实现如下效果

~~~scala
        *        
       ***       
      *****      
     *******     
    *********    
   ***********   
  *************  
 *************** 
*****************
~~~

~~~scala
object HelloWorld {
  def main(args: Array[String]): Unit = {
    for (i <- Range(1, 18, 2)) {
      println(" " * ((18 - i) / 2) + "*" * i + (" " * ((18 - i) / 2)))
    }
  }
}
~~~

~~~scala
for(i<-Range(1,18,2);j=(18-i)/2){
println(" "*j+"*"*i+" "*j)
}
~~~

3.scala所有表达式都有返回值
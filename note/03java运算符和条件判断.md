# Java运算符和条件判断语气

<nav>
<a href="#一.运算符">一.运算符</a><br/>
<a href="#二.条件判断语气">二.条件判断语气</a><br/>
</nav>

## 一、运算符

运算符是一种特殊的符号，用以表示数据的运算、赋值和比较等。

### 1.1 运算符分类

1.算术运算符

<nav>
<a href="#2.1 算术运算符">1.算术运算符</a><br/>
<a href="#2.2 赋值运算符">2.赋值运算符</a><br/>
<a href="#2.3 比较运算符">3.比较运算符</a><br/>
<a href="#2.4 逻辑运算符">4.逻辑运算符</a><br/>
</nav>



### 2.1 算术运算符

算术运算符 有 +  -  *  /  % （前）++ （后）++ 前（--）后（--）

#### 1. 加法 + 减法 -

~~~java
public class AriTest{
    public static void main(String[] args) {
        int num1 = 12;
        int num2 = 5;
        System.out.println(num1+num2);//17
        System.out.println(num1-num2);//7
    }
}
~~~

#### 2. 乘法 * 除法 /

```scala
public class AriTest{
    public static void main(String[] args) {
        int num1 = 12;
        int num2 = 5;
        System.out.println(num1*num2);//60
        System.out.println(num1/num2);//2
    }
}
```

#### 3. 取模 %

取模以后，结果的符号取决于被模数的符号。

开发中，经常使用此符号表示，是否可以除尽某个数。比如num%2 ==0

```java
public class AriTest{
    public static void main(String[] args) {
        int num1 = 12;
        int num2 = 5;
        int num3 =-12;
        int num 4 =-5;
        System.out.println(num1%num2);//2
        System.out.println(num1%num4);//2
        System.out.println(num3%num2);//-2
        System.out.println(num3%num4);//-2
    }
}
```

#### 4. 前后++

不管前后，最终都会加1 ，前++ 先自增后运算  ；后++先运算后自增

自增不改变原有类型：short s1 = 10; s1++还是short类型

```java
public class AriTest{
    public static void main(String[] args) {
        int j1 =10;
        j1++;
        System.out.println(j1);//11
    }
}
```

#### 5. 前后--

不管前后，最终都会减1 ，前-- 先自减后运算  ；后--先运算后自减

```java
public class AriTest{
    public static void main(String[] args) {
        int num1 = 12;
        int num2 = 5;
        System.out.println(num1*num2);//60
    }
}
```

### 2.2 赋值运算符

=、+=、-=、*=、 /=、%=

如果开发中，针对于数值型变量，想+2的运算，建议+=2，想+1运算，建议++

```java
public class SetValueTest {
    public static void main(String[] args){
        int num1 = 20;
        num1 +=2;
        System.out.println(num1);//22
        num1 %= 3;
        System.out.println(num1)//1
    }
}
```

~~~java
public class Demo01 {
    public static void main(String[] args) {
        int i =1;
        i*=0.1;
        System.out.println(i);//0
        i++;
        System.out.println(i);//1
    }
}
~~~

~~~java
public class Demo02 {
    public static void main(String[] args) {
        int n = 10;
        n+=(n++)+(++n);
        System.out.println(n);//32
    }
}
~~~

### 2.3 比较运算符

==、 !=、 >、 <、 >=、 <=、 instanceof

注意运算结果是boolean类型

注意区分==和=

~~~java
public class CompareTest{
	public static void main(String[] args) {
        int i =10;
        int b = 20;
        System.out.println(i>b);//false
    }
}
~~~

### 2.4 逻辑运算符

符号操作的都是bollean类型的变量，而且结果也是bollean类型

逻辑运算符：逻辑与&、短路&&、逻辑或|、短路或||、逻辑非！、逻辑异或^

1.当符号左边为true时，& 和&&右边的运算都需要执行；||右边不执行

2.当符号左边为false时，&&右边的运算不需要执行；|和|右边都要执行

3.^同为假、异为真，&、&&有假为假，|、||有真为真

小结：开发中建议用短路







## 二.条件判断语气

#### 1、public static void main(String[] args)程序入口单词可变？

 代码中args变量名  可以改变如：

~~~java
public static void main(String[] args){......}
~~~



#### 2、System.out.println（）；括号中可以是什么值

数据类型：字符串、数值类型或数值运算，字符

~~~java
System.out.println(“aa”);
System.out.println(6);
System.out.println(6+7);
System.out.println('\n');
~~~



#### 3、bollean类型占几个字节？

1. boolean类型被编译为int类型，等于是说JVM里占用字节和int完全一样，int是4个字节，于是boolean也是4字节
2. boolean数组在Oracle的JVM中，编码为byte数组，每个boolean元素占用8位=1字节
3. 

#### 4：以下代码编译通过吗？为什么？

~~~java
byte a1 = 5; 
byte a2 =5+1;//6
byte a3 = a1+1;//错
~~~

不通过 因为变量加数值运算自动转成int类型



#### 5：以下代码的输出结果是什么？

‘a’ 、'A'作运算相加不才能成为数字 否则还是a A

~~~java
char c = 'a';
String str = "hello";
System.out.println(c + str );//ahello10
--------------------------------------------------
char c = 'a';
int num = 10;
String str = "hello";
System.out.println(c + num + str);//107hello10
~~~

#### 6：int之下变量（byte、short、char）运算自动转为什么类型？

int

#### 7：二进制相关

计算机算的是补码 

原码--反码--补码 

最高位1是负 0是正

正数 原 反补码都一样

负数反码加1得补码

#### 8：Java的常见转义字符有哪些？

\t  表示tab键按一下

\n  表示回车换行  

 \r   表示回车到当前行行首


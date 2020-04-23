## Hadoop 本地运行模式

<nav>
<a href="#一、前置条件">一、前置条件</a><br/>
<a href="#二、WordCount案例">二、WordCount案例</a><br/>
</nav>



### 一、前置条件

Hadoop本地运行模式的运行依赖 JDK，Hadoop需要预先安装，安装步骤见：

+ [Centos7.6环境下 JDK8 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [Hadoop 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)



### 二、WordCount案例

**1.创建在hadoop-3.1.3文件下面创建一个wcinput文件夹**

~~~shell
mkdir wcinput
~~~

**2.切换到wcinput文件目录下**

~~~shell
cd wcinput
~~~

**3.编辑wc.input文件**

~~~shell
#在文件中输入如下内容,然后保存退出
hadoop yarn
hadoop mapreduce
nogc
nogc
~~~

**4.回到Hadoop目录**

~~~shell
cd /opt/module/hadoop-3.1.3
~~~

**5.执行程序**

~~~shell
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount wcinput wcoutput
~~~

**6.当前hadoop-3.1.3目录下查看结果**

~~~shell
cat wcoutput/part-r-00000
~~~

显示如下：

~~~shell
cc 2
hadoop  2
mapreduce       1
yarn    1
~~~


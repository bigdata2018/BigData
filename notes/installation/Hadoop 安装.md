## Hadoop 安装

<nav>
<a href="#一、下载Hadoop">一、下载Hadoop</a><br/>
<a href="#二、安装Hadoop">二、安装Hadoop</a><br/>
<a href="#三、测试Hadoop是否生效">三、测试Hadoop是否生效</a><br/>
</nav>





### 一、下载Hadoop

**hadoop-3.1.3下载地址：**

~~~shell
https://archive.apache.org/dist/hadoop/common/hadoop-3.1.3/
~~~



### 二、安装Hadoop

**1.创建hadoop安装包目录和解压目录**

~~~shell
mkdir -p /opt/software
mkdir -p /opt/module
~~~

**2.上传hadoop安装包到software目录**

- 第一种:


~~~
[root@hadoop101 software]# rz
~~~

注：要先用yum安装 lrzsz 才可用rz上传命令:

~~~
yum install -y lrzsz  // yum 安装完毕之后可以直接rz尝试使用
~~~

- 第二种:


通过Xftp 上传

**3.解压hadoop安装包到module目录**

~~~shell
[nogc@hadoop101 software]$ tar -zxvf hadoop-3.1.3.tar.gz -C /opt/module/
~~~

**4.配置环境变量**

1. 获取Hadoop安装路径

~~~shell
#先cd进入到解压后的hadoop目录，然后使用如下命令
[nogc@hadoop101 hadoop-3.1.3]$ pwd
~~~

~~~shell
#使用pwd后获如下路径内容
/opt/module/hadoop-3.1.3
~~~

2. 打开/etc/profile文件

~~~shell
sudo vim /etc/profile
~~~

~~~shell
#修改profile如下（包含已配置的jdk环境变量）后保存退出：
JAVA_HOME=/opt/module/jdk1.8.0_212
HADOOP_HOME=/opt/module/hadoop-3.1.3
PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export PATH JAVA_HOME HADOOP_HOME
~~~

~~~shell
#保存退出后记得source
source /etc/profile
~~~



### 三、测试Hadoop是否生效

~~~shell
#输入如下命令查看jdk是否安装成功
hadoop version
~~~

~~~shell
#显示如下，则安装成功
Hadoop 3.1.3
Source code repository https://gitbox.apache.org/repos/asf/hadoop.git -r ba631c436b806728f8ec2f54ab1e289526c90579
Compiled by ztang on 2019-09-12T02:47Z
Compiled with protoc 2.5.0
From source with checksum ec785077c385118ac91aadde5ec9799
This command was run using /opt/module/hadoop-3.1.3/share/hadoop/common/hadoop-common-3.1.3.jar
~~~

注意：重启（如果Hadoop命令不能用再重启）

~~~shell
sudo reboot
~~~
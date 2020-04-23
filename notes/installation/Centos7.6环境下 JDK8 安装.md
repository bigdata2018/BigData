## Centos7.6环境下 JDK8 安装

<nav>
<a href="#一卸载自带openjdk">一、卸载自带openjdk</a><br/>
<a href="#二、下载JDK">二、下载JDK</a><br/>
<a href="#三、安装JDK">三、安装JDK</a><br/>
<a href="#四、测试JDK是否安装成功">四、测试JDK是否安装成功</a><br/>
</nav>




### 一、卸载自带openjdk

##### 1.查看自带的openjdk

~~~shell
[root@hadoop100 ~] rpm -qa | grep openjdk
~~~

java-1.8.0-openjdk-headless-1.8.0.161-2.b14.el7.x86_64
java-1.7.0-openjdk-1.7.0.171-2.6.13.2.el7.x86_64
java-1.8.0-openjdk-1.8.0.161-2.b14.el7.x86_64
java-1.7.0-openjdk-headless-1.7.0.171-2.6.13.2.el7.x86_64

##### 2.卸载自带的openjdk

~~~shell
[root@hadoop100 ~] rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.161-2.b14.el7.x86_64
[root@hadoop100 ~] rpm -e --nodeps java-1.7.0-openjdk-1.7.0.171-2.6.13.2.el7.x86_64
[root@hadoop100 ~] rpm -e --nodeps java-1.8.0-openjdk-1.8.0.161-2.b14.el7.x86_64
[root@hadoop100 ~] rpm -e --nodeps java-1.7.0-openjdk-headless-1.7.0.171-2.6.13.2.el7.x86_64
~~~



### 二、下载JDK

**jdk8下载地址**：

https://www.oracle.com/java/technologies/javase-jdk8-downloads.html



### 三、安装JDK

**1.创建jdk安装包目录和解压目录**

~~~shell
mkdir -p /opt/software
mkdir -p /opt/module
~~~

**2.上传jdk安装包到software目录**

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

**3.解压jdk安装包到module目录**

~~~shell
tar -zxvf jdk-8u212-linux-x64.tar.gz -C /opt/module/
~~~

**4.配置环境变量（任选一种）**

- 第一种：


~~~shell
#新建/etc/profile.d/my_env.sh文件
sudo vim /etc/profile.d/my_env.sh
~~~

~~~shell
#在my_env.sh里添加如下内容
export JAVA_HOME=/opt/module/jdk1.8.0_212
export PATH=$PATH:$JAVA_HOME/bin
~~~

~~~shell
#保存退出后记得source
source /etc/profile
~~~

- 第二种：


~~~shell
#直接将环境变量配置到 /etc/profile文件中,在/etc/profile文件的末尾追加如下内容:
JAVA_HOME=/opt/module/jdk1.8.0_212
PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME
~~~

~~~shell
#保存退出后记得source
source /etc/profile
~~~



### 四、测试JDK是否安装成功

~~~shell
#输入如下命令查看jdk是否安装成功
java -version
~~~

~~~shell
#显示如下，则安装成功
java version "1.8.0_212"
Java(TM) SE Runtime Environment (build 1.8.0_212-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.212-b10, mixed mode)
~~~

注意：重启（如果java -version可以用就不用重启）

~~~shell
sudo reboot
~~~
## Zookeeper分布式安装部署

<nav>
<a href="#一、前置条件">一、前置条件</a><br/>
<a href="#二、分布式安装部署">二、分布式安装部署</a><br/>
   <a href="#三、配置修改">三、配置修改</a><br/>
<a href="#四、操作Zookeeper">四、操作Zookeeper</a><br/>
</nav>


### 一、前置条件

Hadoop本地运行模式的运行依赖 JDK，Hadoop需要预先安装，安装步骤见：

- [虚拟机环境](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)

+ [Centos7.6环境下 JDK8 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [集群分发脚本xsync](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)

拷贝Zookeeper安装包到Linux系统下，解压到指定目录

~~~shell
[nogc@hadoop102 software]$ tar -zxvf zookeeper-3.5.7.tar.gz -C /opt/module/
~~~

### 二、分布式安装部署

**1.集群规划**

在hadoop102、hadoop103和hadoop104三个节点上部署Zookeeper。

2.**配置服务器编号**

1. 在/opt/module/zookeeper-3.5.7/这个目录下创建zkData

   ~~~shell
   [nogc@hadoop102 zookeeper-3.5.7]$ mkdir -p zkData
   ~~~

2. 在/opt/module/zookeeper-3.5.7/zkData目录下创建一个myid的文件

   ~~~shell
   [nogc@hadoop102 zkData]$ touch myid
   ~~~

   添加myid文件，注意一定要在linux里面创建，在notepad++里面很可能乱码

3. 编辑myid文件

   ~~~shell
   [nogc@hadoop102 zkData]$ vi myid
   ~~~

   在文件中添加与server对应的编号

   ~~~
   2
   ~~~

4. 拷贝配置好的zookeeper到其他机器上

   ~~~shell
   [nogc@hadoop102 module ]$ xsync  zookeeper-3.5.7
   ~~~

   并分别在hadoop103、hadoop104上修改myid文件中内容为3、4

**3.配置zoo.cfg文件**

1. 重命名/opt/module/zookeeper-3.5.7/conf这个目录下的zoo_sample.cfg为zoo.cfg

   ~~~shell
   [nogc@hadoop102 conf]$ mv zoo_sample.cfg zoo.cfg
   ~~~

2. 打开zoo.cfg文件

   ~~~shell
   [nogc@hadoop102 conf]$ vim zoo.cfg
   ~~~

   修改数据存储路径配置

   ~~~shell
   dataDir=/opt/module/zookeeper-3.5.7/zkData
   ~~~

   增加如下配置

   ~~~shell
   #######################cluster##########################
   server.2=hadoop102:2888:3888
   server.3=hadoop103:2888:3888
   server.4=hadoop104:2888:3888
   ~~~

3. 同步zoo.cfg配置文件

   ~~~shell
   [nogc@hadoop102 conf]$ xsync zoo.cfg
   ~~~

4. 配置参数解读

   ~~~shell
   server.A=B:C:D
   ~~~

   **A**是一个数字，表示这个是第几号服务器；

   集群模式下配置一个文件myid，这个文件在dataDir目录下，这个文件里面有一个数据就是A的值，Zookeeper启动时读取此文件，拿到里面的数据与zoo.cfg里面的配置信息比较从而判断到底是哪个server。

   **B**是这个服务器的地址；

   **C**是这个服务器Follower与集群中的Leader服务器交换信息的端口；

   **D**是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。

5. **集群操作**

   1.分别启动Zookeeper

   ~~~shell
   [nogc@hadoop102 zookeeper-3.5.7]$ bin/zkServer.sh start
   [nogc@hadoop103 zookeeper-3.5.7]$ bin/zkServer.sh start
   [nogc@hadoop104 zookeeper-3.5.7]$ bin/zkServer.sh start
   ~~~

   2.查看状态

   ~~~shell
   [nogc@hadoop102 zookeeper-3.5.7]# bin/zkServer.sh status
   JMX enabled by default
   Using config: /opt/module/zookeeper-3.5.7/bin/../conf/zoo.cfg
   Mode: follower
   [nogc@hadoop103 zookeeper-3.5.7]# bin/zkServer.sh status
   JMX enabled by default
   Using config: /opt/module/zookeeper-3.5.7/bin/../conf/zoo.cfg
   Mode: leader
   [nogc@hadoop104 zookeeper-3.4.5]# bin/zkServer.sh status
   JMX enabled by default
   Using config: /opt/module/zookeeper-3.5.7/bin/../conf/zoo.cfg
   Mode: follower
   ~~~

   

### 三、配置修改

**1.将/opt/module/zookeeper-3.5.7/conf这个路径下的zoo_sample.cfg修改为zoo.cfg**

~~~shell
[nogc@hadoop102 conf]$ mv zoo_sample.cfg zoo.cfg
~~~

**2.打开zoo.cfg文件，修改dataDir路径**

~~~shell
[nogc@hadoop102 zookeeper-3.5.7]$ vim zoo.cfg
#修改如下内容：
dataDir=/opt/module/zookeeper-3.5.7/zkData
~~~

**3.  在/opt/module/zookeeper-3.5.7/这个目录上创建zkData文件夹**

~~~shell
[nogc@hadoop102 zookeeper-3.5.7]$ mkdir zkData
~~~



### 四、操作Zookeeper

**1.启动Zookeeper**

~~~shell
[nogc@hadoop102 zookeeper-3.5.7]$ bin/zkServer.sh start
~~~

**2.查看进程是否启动**

~~~shell
[nogc@hadoop102 zookeeper-3.5.7]$ jps
4020 Jps
4001 QuorumPeerMain
~~~

**3.  查看状态**

~~~shell
[nogc@hadoop102 zookeeper-3.5.7]$ bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /opt/module/zookeeper-3.5.7/bin/../conf/zoo.cfg
Mode: standalone
~~~

**4.启动客户端**

~~~shell
[nogc@hadoop102 zookeeper-3.5.7]$ bin/zkCli.sh
~~~

**5.退出客户端**

~~~shell
[zk: localhost:2181(CONNECTED) 0] quit
~~~

**6.停止Zookeeper**

~~~shell
[nogc@hadoop102 zookeeper-3.5.7]$ bin/zkServer.sh stop
~~~


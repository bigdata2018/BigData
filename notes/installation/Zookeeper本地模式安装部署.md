## Zookeeper本地模式安装部署

<nav>
<a href="#一、前置条件">一、前置条件</a><br/>
<a href="#二、配置修改">二、配置修改</a><br/>
<a href="#三、操作Zookeeper">三、操作Zookeeper</a><br/>
</nav>




### 一、前置条件

Hadoop本地运行模式的运行依赖 JDK，Hadoop需要预先安装，安装步骤见：

- [虚拟机环境](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)

+ [Centos7.6环境下 JDK8 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [集群分发脚本xsync](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)

拷贝Zookeeper安装包到Linux系统下，解压到指定目录

~~~
[nogc@hadoop102 software]$ tar -zxvf zookeeper-3.5.7.tar.gz -C /opt/module/
~~~



### 二、配置修改

**1.将/opt/module/zookeeper-3.5.7/conf这个路径下的zoo_sample.cfg修改为zoo.cfg**

~~~
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



### 三、操作Zookeeper

**1.启动Zookeeper**

~~~
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


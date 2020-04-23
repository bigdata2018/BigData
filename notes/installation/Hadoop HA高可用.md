## Hadoop HA高可用（开发重点)

<nav>
<a href="#一、前置条件">一、前置条件</a><br/>
<a href="#二、HA概述">二、HA概述</a><br/>
<a href="#三、HDFS-HA手动故障转移">三、HDFS-HA手动故障转移</a><br/>
   <a href="#四、配置HDFS-HA自动故障转移">四、配置HDFS-HA自动故障转移</a><br/>
    <a href="#五、 YARN-HA配置">五、 YARN-HA配置</a><br/>
</nav>




### 一、前置条件

Hadoop本地运行模式的运行依赖 JDK，Hadoop需要预先安装，安装步骤见：

- [虚拟机环境](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)

+ [Centos7.6环境下 JDK8 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [Hadoop 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [集群分发脚本xsync](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)



### 二、HA概述

1. 所谓HA（High Availablity），即高可用（7*24小时不中断服务）。

2. 实现高可用最关键的策略是消除单点故障。HA严格来说应该分成各个组件的HA机制：HDFS的HA和YARN的HA。

3. Hadoop2.0之前，在HDFS集群中NameNode存在单点故障SPOF（Single Points Of Failure）。

4. NameNode主要在以下两个方面影响HDFS集群

   1. NameNode机器发生意外，如宕机，集群将无法使用，直到管理员重启

   2. NameNode机器需要升级，包括软件、硬件升级，此时集群也将无法使用

HDFS HA功能通过配置Active/Standby两个NameNodes实现在集群中对 NameNode的热备来解决上述问 题。如果出现故障，如机器崩溃或机器需要升级维护，这时可通过此种方式将NameNode很快的切换到另外一台机器。



### 三、HDFS-HA手动故障转移

#### 一、配置HDFS-HA集群

**1.在opt目录下创建一个ha文件夹**

~~~
[nogc@hadoop202 opt]$ mkdir ha
~~~

**2.将/opt/module/下的 hadoop-3.1.3拷贝到/opt/ha目录下**

~~~shell
[nogc@hadoop202 opt] cp -r hadoop-3.1.3 ha
~~~

**3.  配置：core-site.xml**

~~~shell
<configuration>
<!-- 把多个NameNode的地址组装成一个集群mycluster -->
		<property>
			<name>fs.defaultFS</name>
        	<value>hdfs://mycluster</value>
		</property>
	
	<!-- 指定hadoop运行时产生文件的存储目录 -->
		<property>
			<name>hadoop.tmp.dir</name>
			<value>/opt/module/ha/hadoop-3.1.3/data/tmp</value>
		</property>
   <!-- 声明journalnode服务器存储目录-->
	<property>
		<name>dfs.journalnode.edits.dir</name>
		<value>file://${hadoop.tmp.dir}/jn</value>
	</property>
</configuration>

~~~

**4.  配置：hdfs-site.xml**

~~~shell
<configuration>
	<!-- 完全分布式集群名称 -->
	<property>
		<name>dfs.nameservices</name>
		<value>mycluster</value>
	</property>
  <!-- NameNode数据存储目录 -->
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file://${hadoop.tmp.dir}/name</value>
  </property>
 <!-- DataNode数据存储目录 -->
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file://${hadoop.tmp.dir}/data</value>
  </property>

	<!-- 集群中NameNode节点都有哪些 -->
	<property>
		<name>dfs.ha.namenodes.mycluster</name>
		<value>nn1,nn2,nn3</value>
	</property>

	<!-- nn1的RPC通信地址 -->
	<property>
		<name>dfs.namenode.rpc-address.mycluster.nn1</name>
		<value>hadoop102:9000</value>
	</property>

	<!-- nn2的RPC通信地址 -->
	<property>
		<name>dfs.namenode.rpc-address.mycluster.nn2</name>
		<value>hadoop103:9000</value>
	</property>
	<!-- nn3的RPC通信地址 -->
	<property>
		<name>dfs.namenode.rpc-address.mycluster.nn3</name>
		<value>hadoop104:9000</value>
	</property>


	<!-- nn1的http通信地址 -->
	<property>
		<name>dfs.namenode.http-address.mycluster.nn1</name>
		<value>hadoop102:9870</value>
	</property>

	<!-- nn2的http通信地址 -->
	<property>
		<name>dfs.namenode.http-address.mycluster.nn2</name>
		<value>hadoop103:9870</value>
	</property>
	<!-- nn3的http通信地址 -->
	<property>
		<name>dfs.namenode.http-address.mycluster.nn3</name>
		<value>hadoop104:9870</value>
	</property>

	<!-- 指定NameNode元数据在JournalNode上的存放位置 -->
	<property>
		<name>dfs.namenode.shared.edits.dir</name>
	<value>qjournal://hadoop102:8485;hadoop103:8485;hadoop104:8485/mycluster</value>
	</property>

	<!-- 配置隔离机制，即同一时刻只能有一台服务器对外响应 -->
	<property>
		<name>dfs.ha.fencing.methods</name>
		<value>sshfence</value>
	</property>

	<!-- 使用隔离机制时需要ssh无秘钥登录-->
	<property>
		<name>dfs.ha.fencing.ssh.private-key-files</name>
		<value>/home/nogc/.ssh/id_rsa</value>
	</property>

	<!-- 访问代理类：client用于确定哪个NameNode为Active -->
	<property>		<name>dfs.client.failover.proxy.provider.mycluster</name>
	<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
	</property>
</configuration>
~~~

**5.  拷贝配置好的hadoop环境到其他节点**

#### 二、启动HDFS-HA集群

1.**在各个JournalNode节点上，输入以下命令启动journalnode服务**

~~~shell
hdfs --daemon start journalnode
~~~

2.**在[nn1]上，对其进行格式化，并启动**

~~~shell
hdfs namenode -format
hdfs --daemon start namenode
~~~

3.**在[nn2]和[nn3]上，同步nn1的元数据信息**

~~~shell
hdfs namenode -bootstrapStandby
~~~

**4.启动[nn2] 和 [nn3]**

~~~shell
hdfs --daemon start namenode
~~~

**5.查看web页面显示，102 103 104 都为standby状态**

**6.启动所有datanode**

~~~shell
hdfs --daemon start datanode
~~~

**7.将[nn1]切换为Active**

~~~shell
hdfs haadmin -transitionToActive nn1
~~~

**8.是否Active**

~~~shell
hdfs haadmin -getServiceState nn1
~~~

**9.kill掉Active的NameNode，进行手动故障转移**



### 四、配置HDFS-HA自动故障转移

**规划集群**

| hadoop102   | hadoop103       | hadoop104   |
| ----------- | --------------- | ----------- |
| NameNode    | NameNode        | NameNode    |
| ZKFC        | ZKFC            | ZKFC        |
| JournalNode | JournalNode     | JournalNode |
| DataNode    | DataNode        | DataNode    |
| ZK          | ZK              | ZK          |
|             | ResourceManager |             |
| NodeManager | NodeManager     | NodeManager |

#### 一、配置Zookeeper集群

**1.集群规划**

在hadoop102、hadoop103和hadoop104三个节点上部署Zookeeper。

**2.解压安装**

1. 解压Zookeeper安装包到/opt/module/目录下

   ~~~shell
   [nogc@hadoop102 software]$ tar -zxvf zookeeper-3.5.7.tar.gz -C /opt/module/
   ~~~

2. 在/opt/module/zookeeper-3.5.7/这个目录下创建zkData

   ~~~shell
   mkdir -p zkData
   ~~~

3. 重命名/opt/module/zookeeper-3.5.7/conf这个目录下的zoo_sample.cfg为zoo.cfg

   ~~~shell
   mv zoo_sample.cfg zoo.cfg
   ~~~

**3.配置zoo.cfg文件**

1. 具体配置

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

**4.集群操作**

1. 在/opt/module/zookeeper-3.5.7/zkData目录下创建一个myid的文件

   ~~~
   touch myid
   ~~~

2. 编辑myid文件

   ~~~shell
   #在文件中添加与server对应的编号：如2
   vi myid
   ~~~

3. 拷贝配置好的zookeeper到hadoop103 hadoop104

   ~~~shell
   [nogc@hadoop102 module] xsync  zookeeper-3.5.7
   ~~~

   并分别修改myid文件中内容为3、4

4. 分别启动zookeeper

   ~~~shell
   [nogc @hadoop102 zookeeper-3.5.7]# bin/zkServer.sh start
   [nogc @hadoop103 zookeeper-3.5.7]# bin/zkServer.sh start
   [nogc @hadoop104 zookeeper-3.5.7]# bin/zkServer.sh start
   ~~~

5. 查看状态

   ~~~shell
   [nogc @hadoop102 zookeeper-3.5.7]# bin/zkServer.sh status
   JMX enabled by default
   Using config: /opt/module/zookeeper-3.4.10/bin/../conf/zoo.cfg
   Mode: follower
   [nogc @hadoop103 zookeeper-3.5.7]# bin/zkServer.sh status
   JMX enabled by default
   Using config: /opt/module/zookeeper-3.4.10/bin/../conf/zoo.cfg
   Mode: leader
   [nogc @hadoop104 zookeeper-3.5.7]# bin/zkServer.sh status
   JMX enabled by default
   Using config: /opt/module/zookeeper-3.4.10/bin/../conf/zoo.cfg
   Mode: follower
   ~~~

   

#### 二、配置HDFS-HA自动故障转移

**1.具体配置**

1. 在hdfs-site.xml中增加

   ~~~
   <property>
   	<name>dfs.ha.automatic-failover.enabled</name>
   	<value>true</value>
   </property>
   ~~~

2. 在core-site.xml文件中增加

   ~~~
   <property>
   	<name>ha.zookeeper.quorum</name>
   	<value>hadoop102:2181,hadoop103:2181,hadoop104:2181</value>
   </property>
   ~~~

**2.启动**

1. 关闭所有HDFS服务

   ~~~
   stop-dfs.sh
   ~~~

2. 启动Zookeeper集群，在每个Zookeeper节点执行

   ~~~
   zkServer.sh start
   ~~~

3. 初始化HA在Zookeeper中状态

   ~~~
   hdfs zkfc -formatZK
   ~~~

4. 启动HDFS服务

   ~~~shell
   start-dfs.sh
   ~~~

**3.web端查看，一个Active的NameNode， 两个Standby的NameNode**

**4.验证**

将Active NameNode进程kill,实现自动故障转移

~~~shell
kill -9 namenode的进程id
~~~



### 五、 YARN-HA配置

**1.规划集群**

| hadoop102       | hadoop103       | hadoop104   |
| --------------- | --------------- | ----------- |
| NameNode        | NameNode        | NameNode    |
| JournalNode     | JournalNode     | JournalNode |
| ZKFC            | ZKFC            | ZKFC        |
| DataNode        | DataNode        | DataNode    |
| ZK              | ZK              | ZK          |
| ResourceManager | ResourceManager |             |
| NodeManager     | NodeManager     | NodeManager |

**2.具体配置**

1. yarn-site.xml

   ~~~shell
   <configuration>
   
       <property>
           <name>yarn.nodemanager.aux-services</name>
           <value>mapreduce_shuffle</value>
       </property>
   
       <!--启用resourcemanager ha-->
       <property>
           <name>yarn.resourcemanager.ha.enabled</name>
           <value>true</value>
       </property>
    
       <!--声明HA resourcemanager的地址-->
       <property>
           <name>yarn.resourcemanager.cluster-id</name>
           <value>cluster-yarn1</value>
       </property>
        <!-- 指定RM的逻辑列表 -->
       <property>
           <name>yarn.resourcemanager.ha.rm-ids</name>
           <value>rm1,rm2,rm3</value>
       </property>
   
   <!-- 指定rm1 的主机名 -->
       <property>
           <name>yarn.resourcemanager.hostname.rm1</name>
           <value>hadoop102</value>
       </property>
       <!-- 指定rm1的web端地址 -->
   <property>
           <name>yarn.resourcemanager.webapp.address.rm1</name>
           <value>hadoop102:8088</value>
   </property>
      <!--  =========== rm1 配置============  --> 
      <!-- 指定rm1的内部通信地址 -->
       <property>
           <name>yarn.resourcemanager.address.rm1</name>
           <value>hadoop102:8032</value>
       </property>
     <!-- 指定AM向rm1申请资源的地址 -->
       <property>
           <name>yarn.resourcemanager.scheduler.address.rm1</name>  
           <value>hadoop102:8030</value>
       </property>
     <!-- 指定供NM连接的地址 -->  
   <property>
           <name>yarn.resourcemanager.resource-tracker.address.rm1</name>
           <value>hadoop102:8031</value>
   </property>
   
   <!--  =========== rm2 配置============  --> 
   
       <property>
           <name>yarn.resourcemanager.hostname.rm2</name>
           <value>hadoop103</value>
   </property>
   
   <property>
           <name>yarn.resourcemanager.webapp.address.rm2</name>
           <value>hadoop103:8088</value>
   </property>
       <property>
           <name>yarn.resourcemanager.address.rm2</name>
           <value>hadoop103:8032</value>
       </property>
       <property>
           <name>yarn.resourcemanager.scheduler.address.rm2</name>
           <value>hadoop103:8030</value>
       </property>
   
   <property>
           <name>yarn.resourcemanager.resource-tracker.address.rm2</name>
           <value>hadoop103:8031</value>
   </property>
   
   <!--  =========== rm3 配置============  --> 
   
       <property>
           <name>yarn.resourcemanager.hostname.rm3</name>
           <value>hadoop104</value>
       </property>
   
   <property>
           <name>yarn.resourcemanager.webapp.address.rm3</name>
           <value>hadoop104:8088</value>
   </property>
       <property>
           <name>yarn.resourcemanager.address.rm3</name>
           <value>hadoop104:8032</value>
       </property>
       <property>
           <name>yarn.resourcemanager.scheduler.address.rm3</name>
           <value>hadoop104:8030</value>
       </property>
   
   <property>
           <name>yarn.resourcemanager.resource-tracker.address.rm3</name>
           <value>hadoop104:8031</value>
   </property>
    
       <!--指定zookeeper集群的地址--> 
       <property>
           <name>yarn.resourcemanager.zk-address</name>
           <value>hadoop102:2181,hadoop103:2181,hadoop104:2181</value>
       </property>
   
       <!--启用自动恢复--> 
       <property>
           <name>yarn.resourcemanager.recovery.enabled</name>
           <value>true</value>
       </property>
    
       <!--指定resourcemanager的状态信息存储在zookeeper集群--> 
       <property>
           <name>yarn.resourcemanager.store.class</name>     <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
   </property>
    
   <!-- 环境变量的继承 -->
     <property>
           <name>yarn.nodemanager.env-whitelist</name>
           <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
       </property>
   
   </configuration>
   ~~~

2. **同步配置文件到其他节点**

   ~~~shell
    [nogc@hadoop202 hadoop]$ xsync yarn-site.xml
   ~~~

**3.启动HDFS**

**4.启动YARN**

1. 在hadoop102中执行

   ~~~
   start-yarn.sh
   ~~~

2. 查看服务状态

   ~~~
   yarn rmadmin -getServiceState rm1
   ~~~

3. web端查看YARN的状态

   


## Hadoop完全分布式运行模式（开发重点)

<nav>
<a href="#一、前置条件">一、前置条件</a><br/>
<a href="#二、集群环境">二、集群环境</a><br/>
<a href="#三、Hadoop集群环境配置">三、Hadoop集群环境配置</a><br/>
   <a href="#四、集群单点启动">四、集群单点启动</a><br/>
    <a href="#五、SSH无密登录配置">五、SSH无密登录配置</a><br/>
    <a href="#六、群起集群">六、群起集群</a><br/>
    <a href="#七、集群启动与停止方式">七、集群启动与停止方式</a><br/>
    <a href="#八、配置历史服务器">八、配置历史服务器</a><br/>
    <a href="#九、配置日志的聚集">九、配置日志的聚集</a><br/>
    <a href="#十、集群时间同步">十、集群时间同步</a><br/>
</nav>




### 一、前置条件

Hadoop本地运行模式的运行依赖 JDK，Hadoop需要预先安装，安装步骤见：

- [虚拟机环境](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)

+ [Centos7.6环境下 JDK8 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [Hadoop 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [集群分发脚本xsync](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)



### 二、集群环境

**从搭好的虚拟机hadoop102克隆出hadoop103和hadoop104**

1. 修改主机名

   ~~~shell
   #hadoop103和hadoop104机子上分别更改主机名为 hadoop103 hadoop104
   sudo vim /etc/hostname
   ~~~

2. 分别更改其它两台不同虚拟机静态IP(按照自己机器的网络设置进行修改)

   ~~~shell
   sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33
   ~~~

   ~~~shell
   #在hadoop103上更改ifcfg-ens33里的内容为：
   DEVICE=ens33
   TYPE=Ethernet
   ONBOOT=yes
   BOOTPROTO=static
   NAME="ens33"
   IPADDR=192.168.117.103
   PREFIX=24
   GATEWAY=192.168.117.2
   DNS1=192.168.117.2
   DNS2=8.8.8.8
   ~~~

   ~~~shell
   #在hadoop104上更改ifcfg-ens33里的内容为：
   DEVICE=ens33
   TYPE=Ethernet
   ONBOOT=yes
   BOOTPROTO=static
   NAME="ens33"
   IPADDR=192.168.117.104
   PREFIX=24
   GATEWAY=192.168.117.2
   DNS1=192.168.117.2
   DNS2=8.8.8.8
   ~~~

### 三、Hadoop集群环境配置

**1.集群部署**

- NameNode和SecondaryNameNode不要安装在同一台服务器

- ResourceManager也很消耗内存，不要和NameNode、SecondaryNameNode配置在同一台机器上。

|      | hadoop102          | hadoop103                    | hadoop104                   |
| ---- | ------------------ | ---------------------------- | --------------------------- |
| HDFS | NameNode  DataNode | DataNode                     | SecondaryNameNode  DataNode |
| YARN | NodeManager        | ResourceManager  NodeManager | NodeManager                 |

**2.  配置：core-site.xml**

~~~shell
cd $HADOOP_HOME/etc/hadoop
vim core-site.xml
~~~

~~~shell
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://hadoop102:8020</value>
    </property>
    <property>
        <name>hadoop.data.dir</name>
        <value>/opt/module/hadoop-3.1.3/data</value>
    </property>
    <property>
        <name>hadoop.proxyuser.nogc.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.nogc.groups</name>
        <value>*</value>
    </property>
</configuration>
~~~

**3.  配置：hdfs-site.xml**

~~~shell
vim hdfs-site.xml
~~~

~~~shell
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file://${hadoop.data.dir}/name</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file://${hadoop.data.dir}/data</value>
  </property>
    <property>
    <name>dfs.namenode.checkpoint.dir</name>
    <value>file://${hadoop.data.dir}/namesecondary</value>
  </property>
    <property>
    <name>dfs.client.datanode-restart.timeout</name>
    <value>30</value>
  </property>
  <property>
    <name>dfs.namenode.secondary.http-address</name>
    <value>hadoop104:9868</value>
  </property>
</configuration>
~~~

**4.  配置：yarn-site.xml**

~~~shell
vim yarn-site.xml
~~~

~~~shell
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>hadoop103</value>
    </property>
    <property>
        <name>yarn.nodemanager.env-whitelist</name>
        <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
    </property>
</configuration>
~~~

**5.  配置：mapred-site.xml**

~~~shell
vim mapred-site.xml
~~~

~~~shell
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
~~~

**6.  集群上分发配置好的hadoop**

~~~shell
xsync /opt/module/hadoop-3.1.3
~~~

### 四、集群单点启动

1. **如果集群是第一次启动，需要格式化NameNode**

   ~~~shell
   hdfs namenode -format
   ~~~

2. **在hadoop102上启动NameNode**

   ~~~shell
   hdfs --daemon start namenode
   ~~~

3. **在hadoop102、hadoop103以及hadoop104上执行如下命令（三台都要执行）**

   ~~~shell
   hdfs --daemon start datanode
   ~~~

- 用jps命令可查看启动情况



### 五、SSH无密登录配置

1. **在hadoop102上生成公钥和私钥**

   ~~~shell
   ssh-keygen -t rsa
   ~~~

   然后敲（三个回车），就会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）

2. **将公钥拷贝到要免密登录的目标机器上**

   ~~~shell
   ssh-copy-id hadoop102
   ssh-copy-id hadoop103
   ssh-copy-id hadoop104
   ~~~

- 分别在hadoop103、hadoop104上执行相同操作
- root账号和nogc账号各执行一轮



### 六、群起集群

1. **配置workers（注hadoop2版本没有workers，而是*salv*es）**

   ~~~shell
   vim /opt/module/hadoop-3.1.3/etc/hadoop/workers
   ~~~

   ~~~shell
   #注意：该文件中添加的内容结尾不允许有空格，文件中不允许有空行
   hadoop102
   hadoop103
   hadoop104
   ~~~

   同步所有节点配置文件

   ~~~shell
   xsync  workers
   ~~~

2. **启动集群**

   1. 如果集群是第一次启动，需要在hadoop102节点格式化NameNode（注意格式化之前，一定要先停止上次启动的所有namenode和datanode进程，然后再删除data和log数据）

      ~~~shell
      #集群是第一次启动格式化NameNode
      hdfs namenode -format
      ~~~

   2. 启动HDFS

      ~~~shell
      sbin/start-dfs.sh
      ~~~

   3. 在配置了ResourceManager的节点（hadoop103）启动YARN

      ~~~shell
      sbin/start-yarn.sh	
      ~~~

3. **集群基本测试**

   1. 上传文件到集群

      ~~~shell
      #上传小文件
      hadoop fs -mkdir -p /user/nogc/input
      hadoop fs -put $HADOOP_HOME/wcinput/wc.input /user/nogc/input
      ~~~

      ~~~shell
      #上传大文件
      hadoop fs -put  /opt/software/hadoop-3.1.3.tar.gz  /
      ~~~

   2. 上传文件后查看文件存放在什么位置

      ~~~shell
      #查看HDFS文件存储路径
      [nogc@hadoop102 subdir0]$ pwd
      ~~~

      /opt/module/hadoop-3.1.3/data/tmp/dfs/data/current/BP-938951106-192.168.10.107-1495462844069/current/finalized/subdir0/subdir0

      ~~~shell
      #查看HDFS在磁盘存储文件内容
      [nogc@hadoop102 subdir0]$ cat blk_1073741825
      hadoop yarn
      hadoop mapreduce 
      nogc
      nogc
      ~~~

   3. 拼接

   ~~~shell
   -rw-rw-r--. 1 nogc nogc 134217728 5月  23 16:01 blk_1073741836
   -rw-rw-r--. 1 nogc nogc   1048583 5月  23 16:01 blk_1073741836_1012.meta
   -rw-rw-r--. 1 nogc nogc  63439959 5月  23 16:01 blk_1073741837
   -rw-rw-r--. 1 nogc nogc    495635 5月  23 16:01 blk_1073741837_1013.meta
   [nogc@hadoop102 subdir0]$ cat blk_1073741836>>tmp.jar
   [nogc@hadoop102 subdir0]$ cat blk_1073741837>>tmp.jar
   [nogc@hadoop102 subdir0]$ tar -zxvf tmp.jar
   ~~~

   4. 下载

      ~~~shell
      [nogc@hadoop102 hadoop-3.1.3]$ bin/hadoop fs -get
       /hadoop-3.1.3.tar.gz ./
      ~~~

   5. 执行wordcount程序

      ~~~shell
      [nogc@hadoop102 hadoop-3.1.3]$ hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /user/nogc/input /user/nogc/output
      ~~~

      

### 七、集群启动与停止方式

1. **分别单个启动/停止HDFS组件**

   ~~~shell
   hdfs --daemon start/stop namenode/datanode/secondarynamenode
   ~~~

2. **分别单个启动/停止YARN**

   ~~~shell
   yarn --daemon start/stop  resourcemanager/nodemanager
   ~~~

- **集体启动/停止（配置ssh是前提）常用方式**

- **整体启动/停止HDFS**

  ~~~shell
  start-dfs.sh/stop-dfs.sh
  ~~~

- **整体启动/停止YARN**

  ~~~shell
  start-yarn.sh/stop-yarn.sh
  ~~~

### 八、配置历史服务器

1. **配置mapred-site.xml**

   ~~~shell
   vi mapred-site.xml
   ~~~

   ~~~shell
   #在该文件里面增加如下配置
   <!-- 历史服务器端地址 -->
   <property>
       <name>mapreduce.jobhistory.address</name>
       <value>hadoop102:10020</value>
   </property>
   
   <!-- 历史服务器web端地址 -->
   <property>
       <name>mapreduce.jobhistory.webapp.address</name>
       <value>hadoop102:19888</value>
   </property>
   ~~~

2. **分发配置**

   ~~~shell
   xsync $HADOOP_HOME/etc/hadoop/mapred-site.xml
   ~~~

3. **在hadoop102启动历史服务器**

   ~~~shell
   mapred --daemon start historyserver
   ~~~

4. **查看历史服务器是否启动**

   ~~~
   jps
   ~~~

5. **查看JobHistory**

   http://hadoop102:19888/jobhistory

### 九、配置日志的聚集

- 日志聚集概念：应用运行完成以后，将程序运行日志信息上传到HDFS系统上。

- 日志聚集功能好处：可以方便的查看到程序运行详情，方便开发调试。

- 注意：开启日志聚集功能，需要重新启动NodeManager 、ResourceManager和HistoryManager。

开启日志聚集功能具体步骤如下：

1. **配置yarn-site.xml**

   ~~~
   vim yarn-site.xml
   ~~~

   ~~~shell
   #在该文件里面增加如下配置
   <property>
   	<name>yarn.log-aggregation-enable</name>
   	<value>true</value>
   </property>
   <property>  
   	<name>yarn.log.server.url</name>          		 <value>http://hadoop102:19888/jobhistory/logs</value>  
   </property>
       <property>
       <name>yarn.log-aggregation.retain-seconds</name>
   	<value>604800</value>
   </property>
   ~~~

2. **分发配置**

   ~~~shell
   xsync $HADOOP_HOME/etc/hadoop/yarn-site.xml
   ~~~

3. **关闭NodeManager 、ResourceManager和HistoryServer**

   ~~~shell
   在103上执行： stop-yarn.sh
   在102上执行： mapred --daemon stop historyserver
   ~~~

4. **启动NodeManager 、ResourceManager和HistoryServer**

   ~~~shell
   在103上执行：start-yarn.sh
   在102上执行：mapred --daemon start historyserver
   ~~~

5. **删除HDFS上已经存在的输出文件**

   ~~~shell
   hdfs dfs -rm -R /user/nogc/output
   ~~~

6. **执行WordCount程序**

   ~~~shell
   hadoop jar  $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /user/nogc/input /user/nogc/output
   ~~~

7. 查看日志

   http://hadoop102:19888/jobhistory

### 十、集群时间同步

1. **时间服务器配置（必须root用户）**

   **在所有节点关闭ntp服务和自启动**

   ~~~shell
   sudo systemctl stop ntpd
   sudo systemctl disable ntpd
   ~~~

2. **修改ntp配置文件**

   ~~~shell
   vim /etc/ntp.conf
   ~~~

   a、修改（授权192.168.1.0-192.168.1.255网段上的所有机器可以从这台机器上查询和同步时间）

   ~~~shell
   #restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap//改为
   restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
   ~~~

   ~~~shell
   #b、修改（集群在局域网中，不使用其他互联网上的时间）
   server 0.centos.pool.ntp.org iburst
   server 1.centos.pool.ntp.org iburst
   server 2.centos.pool.ntp.org iburst
   server 3.centos.pool.ntp.org iburst  //改为
   
   #server 0.centos.pool.ntp.org iburst
   #server 1.centos.pool.ntp.org iburst
   #server 2.centos.pool.ntp.org iburst
   #server 3.centos.pool.ntp.org iburst
   ~~~

   ~~~shell
   #c、添加（当该节点丢失网络连接，依然可以采用本地时间作为时间服务器为集群中的其他节点提供时间同步）
   server 127.127.1.0
   fudge 127.127.1.0 stratum 10
   ~~~

   ~~~shell
   #3、修改/etc/sysconfig/ntpd 文件
   vim /etc/sysconfig/ntpd
   ~~~

   ~~~shell
   #增加内容如下（让硬件时间与系统时间一起同步）
   SYNC_HWCLOCK=yes
   ~~~

   ~~~shell
   #4、重新启动ntpd服务
   systemctl start ntpd
   ~~~

   ~~~shell
   #5、设置ntpd服务开机启动
   systemctl enable ntpd
   ~~~

   


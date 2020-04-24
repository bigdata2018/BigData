# Hive安装

<nav>
<a href="#一、前置环境">一、前置环境</a><br/>
<a href="#二、Hive地址">二、Hive地址</a><br/>
<a href="#三、Hive安装部署">三、Hive安装部署</a><br/>
<a href="#四、Hive元数据配置到MySql">四、Hive元数据配置到MySql</a><br/>
<a href="#五、安装Tez引擎">五、安装Tez引擎</a><br/>
    <a href="#六、启动Hive">六、启动Hive</a><br/>
    <a href="#七、HiveJDBC访问">七、HiveJDBC访问</a><br/>
    <a href="#八、Hive访问">八、Hive访问</a><br/>
</nav>




## 一、前置环境

[Centos7.6下MySql-5.7.28安装](#)

[Centos7.6下Hadoop-3.13安装](#)



## 二、Hive地址

1.Hive官网地址

http://hive.apache.org/

2.文档查看地址

https://cwiki.apache.org/confluence/display/Hive/GettingStarted

3.下载地址

http://archive.apache.org/dist/hive/

4.github地址

https://github.com/apache/hive



## 三、Hive安装部署

**1.把apache-hive-3.1.2-bin.tar.gz上传到linux的/opt/software目录下**

**2.解压apache-hive-3.1.2-bin.tar.gz到/opt/module/目录下面**

~~~shell
[atguigu@hadoop102 software]$ tar -zxvf /opt/software/apache-hive-3.1.2-bin.tar.gz -C /opt/module/
~~~

**3.修改apache-hive-3.1.2-bin.tar.gz的名称为hive**

~~~shell
[atguigu@hadoop102 software]$ mv /opt/module/apache-hive-3.1.2-bin/ /opt/module/hive
~~~

**4.修改/etc/profile.d/my_env.sh，添加环境变量**

~~~shell
[atguigu@hadoop102 software]$ sudo vim /etc/profile.d/my_env.sh
~~~

**5.在my_env.sh添加内容**

~~~shell
#HIVE_HOME，
export HIVE_HOME=/opt/module/hive
export PATH=$PATH:$HIVE_HOME/bin
或
HIVE_HOME=/opt/module/hive
PATH=$PATH:$HIVE_HOME/bin
export PATH HIVE_HOME
~~~

**6.解决日志Jar包冲突**

~~~shell
[atguigu@hadoop102 software]$ mv $HIVE_HOME/lib/log4j-slf4j-impl-2.10.0.jar $HIVE_HOME/lib/log4j-slf4j-impl-2.10.0.bak
~~~



## 四、Hive元数据配置到MySql

**1.拷贝驱动**

将MySQL的JDBC驱动拷贝到Hive的lib目录下

~~~shell
[atguigu@hadoop102 software]$ cp /opt/software/mysql-connector-java-5.1.48.jar $HIVE_HOME/lib
~~~

**2.配置Metastore到MySql**

在$HIVE_HOME/conf目录下新建hive-site.xml文件

~~~shell
[atguigu@hadoop102 software]$ vim $HIVE_HOME/conf/hive-site.xml
~~~

添加如下内容:

~~~shell
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <!-- jdbc连接的URL -->
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://hadoop102:3306/metastore?useSSL=false</value>
</property>

    <!-- jdbc连接的Driver-->
    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
</property>

	<!-- jdbc连接的username-->
    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
    </property>

    <!-- jdbc连接的password -->
    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>123456</value>
    </property>
    <!-- Hive默认在HDFS的工作目录 -->
    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
    </property>
    
   <!-- Hive元数据存储版本的验证 -->
    <property>
        <name>hive.metastore.schema.verification</name>
        <value>false</value>
    </property>
    <!-- 指定存储元数据要连接的地址 -->
    <property>
        <name>hive.metastore.uris</name>
        <value>thrift://hadoop102:9083</value>
    </property>
    <!-- 指定hiveserver2连接的端口号 -->
    <property>
    <name>hive.server2.thrift.port</name>
    
    <value>10000</value>
    </property>
   <!-- 指定hiveserver2连接的host -->
    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>hadoop102</value>
    </property>
    <!-- 元数据存储授权  -->
    <property>
        <name>hive.metastore.event.db.notification.api.auth</name>
        <value>false</value>
    </property>

</configuration>
~~~



## 五、安装Tez引擎

Tez是一个Hive的运行引擎，性能优于MR。为什么优于MR呢？看下图：

![image-20200424111811375](C:\Users\whj\AppData\Roaming\Typora\typora-user-images\image-20200424111811375.png)

用Hive直接编写MR程序，假设有四个有依赖关系的MR作业，上图中，绿色是Reduce Task，云状表示写屏蔽，需要将中间结果持久化写到HDFS。

Tez可以将多个有依赖的作业转换为一个作业，这样只需写一次HDFS，且中间节点较少，从而大大提升作业的计算性能。

**1.将tez安装包拷贝到集群，并解压tar包**

~~~shell
[atguigu@hadoop102 software]$ mkdir /opt/module/tez
[atguigu@hadoop102 software]$ tar -zxvf /opt/software/tez-0.10.1- minimal SNAPSHOT.tar.gz -C /opt/module/tez
~~~

**2.上传tez依赖到HDFS**

~~~shell
[atguigu@hadoop102 software]$ hadoop fs -mkdir /tez
[atguigu@hadoop102 software]$ hadoop fs -put /opt/software/tez-0.10.1-SNAPSHOT.tar.gz /tez
~~~

**3.新建tez-site.xml**

~~~shell
[atguigu@hadoop102 software]$ vim $HADOOP_HOME/etc/hadoop/tez-site.xml
~~~

添加如下内容：

~~~shell
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
<property>
	<name>tez.lib.uris</name>
    <value>${fs.defaultFS}/tez/tez-0.10.1-SNAPSHOT.tar.gz</value>
</property>
<property>
     <name>tez.use.cluster.hadoop-libs</name>
     <value>true</value>
</property>
<property>
     <name>tez.am.resource.memory.mb</name>
     <value>1024</value>
</property>
<property>
     <name>tez.am.resource.cpu.vcores</name>
     <value>1</value>
</property>
<property>
     <name>tez.container.max.java.heap.fraction</name>
     <value>0.4</value>
</property>
<property>
     <name>tez.task.resource.memory.mb</name>
     <value>1024</value>
</property>
<property>
     <name>tez.task.resource.cpu.vcores</name>
     <value>1</value>
</property>
</configuration>
~~~

**4.修改Hadoop环境变量**

~~~shell
[atguigu@hadoop102 software]$ vim $HADOOP_HOME/etc/hadoop/shellprofile.d/tez.sh
~~~

添加Tez的Jar包相关信息

~~~shell
hadoop_add_profile tez
function _tez_hadoop_classpath
{
    hadoop_add_classpath "$HADOOP_HOME/etc/hadoop" after
    hadoop_add_classpath "/opt/module/tez/*" after
    hadoop_add_classpath "/opt/module/tez/lib/*" after
}
~~~

**5.修改Hive的计算引擎**

~~~shell
[atguigu@hadoop102 software]$ vim $HIVE_HOME/conf/hive-site.xml
~~~

加以下内容：

~~~shell
<property>
    <name>hive.execution.engine</name>
    <value>tez</value>
</property>
<property>
    <name>hive.tez.container.size</name>
    <value>1024</value>
</property>
~~~

**6.解决日志Jar包冲突**

~~~shell
[atguigu@hadoop102 software]$ rm /opt/module/tez/lib/slf4j-log4j12-1.7.10.jar
~~~



## 六、启动Hive

#### 一、初始化元数据库

**1.登陆MySQL**

~~~
[atguigu@hadoop102 software]$ mysql -uroot -p000000
~~~

**2.新建Hive元数据库**

~~~
mysql> create database metastore;
mysql> quit;
~~~

**3.初始化Hive元数据库**

~~~
[atguigu@hadoop102 software]$ schematool -initSchema -dbType mysql -verbose
~~~

#### 二、启动metastore和hiveserver2

**1.Hive 2.x以上版本，要先启动这两个服务，否则会报错：**

~~~
FAILED: HiveException java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient
~~~

**1.1启动metastore**

~~~shell
[atguigu@hadoop202 hive]$ hive --service metastore 
2020-04-24 16:58:08: Starting Hive Metastore Server  
注意: 启动后窗口不能再操作，需打开一个新的shell窗口做别的操作
~~~

**1.2启动 hiveserver2**

~~~shell
[atguigu@hadoop202 hive]$ hive --service hiveserver2
which: no hbase in (/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/module/jdk1.8.0_212/bin:/opt/module/hadoop-3.1.3/bin:/opt/module/hadoop-3.1.3/sbin:/opt/module/hive/bin:/home/atguigu/.local/bin:/home/atguigu/bin)
2020-04-24 17:00:19: Starting HiveServer2  
注意: 启动后窗口不能再操作，需打开一个新的shell窗口做别的操作
~~~

**2.编写hive服务启动脚本**

~~~shell
[atguigu@hadoop102 hive]$ vim $HIVE_HOME/bin/hiveservices.sh
~~~

~~~shell
#!/bin/bash
HIVE_LOG_DIR=$HIVE_HOME/logs
if [ ! -d $HIVE_LOG_DIR ]
then
	mkdir -p $HIVE_LOG_DIR
fi
#检查进程是否运行正常，参数1为进程名，参数2为进程端口
function check_process()
{
    pid=$(ps -ef 2>/dev/null | grep -v grep | grep -i $1 | awk '{print $2}')
    ppid=$(netstat -nltp 2>/dev/null | grep $2 | awk '{print $7}' | cut -d '/' -f 1)
    echo $pid
    [[ "$pid" =~ "$ppid" ]] && [ "$ppid" ] && return 0 || return 1
}

function hive_start()
{
    metapid=$(check_process HiveMetastore 9083)
    cmd="nohup hive --service metastore >$HIVE_LOG_DIR/metastore.log 2>&1 &"
    cmd=$cmd" sleep 4; hdfs dfsadmin -safemode wait >/dev/null 2>&1"
    [ -z "$metapid" ] && eval $cmd || echo "Metastroe服务已启动"
    server2pid=$(check_process HiveServer2 10000)
    cmd="nohup hive --service hiveserver2 >$HIVE_LOG_DIR/hiveServer2.log 2>&1 &"
    [ -z "$server2pid" ] && eval $cmd || echo "HiveServer2服务已启动"
}

function hive_stop()
{
    metapid=$(check_process HiveMetastore 9083)
    [ "$metapid" ] && kill $metapid || echo "Metastore服务未启动"
    server2pid=$(check_process HiveServer2 10000)
    [ "$server2pid" ] && kill $server2pid || echo "HiveServer2服务未启动"
}

case $1 in
"start")
    hive_start
    ;;
"stop")
    hive_stop
    ;;
"restart")
    hive_stop
    sleep 2
    hive_start
    ;;
"status")
    check_process HiveMetastore 9083 >/dev/null && echo "Metastore服务运行正常" || echo "Metastore服务运行异常"
    check_process HiveServer2 10000 >/dev/null && echo "HiveServer2服务运行正常" || echo "HiveServer2服务运行异常"
    ;;
*)
    echo Invalid Args!
    echo 'Usage: '$(basename $0)' start|stop|restart|status'
    ;;
esac
~~~

**3.添加执行权限**

~~~shell
[atguigu@hadoop102 hive]$ chmod +x $HIVE_HOME/bin/hiveservices.sh
~~~

**4.启动Hive后台服务**

~~~shell
[atguigu@hadoop102 hive]$ hiveservices.sh start
~~~



## 七、HiveJDBC访问

**1.启动beeline客户端**

~~~
[atguigu@hadoop102 hive]$ bin/beeline -u jdbc:hive2://hadoop102:10000 -n atguigu
~~~

显示如下：

~~~shell
Connecting to jdbc:hive2://hadoop102:10000
Connected to: Apache Hive (version 3.1.2)
Driver: Hive JDBC (version 3.1.2)
Transaction isolation: TRANSACTION_REPEATABLE_READ
Beeline version 3.1.2 by Apache Hive
0: jdbc:hive2://hadoop102:10000>
~~~



## 八、Hive访问

**1.启动hive客户端**

~~~
[atguigu@hadoop102 hive]$ bin/hive
~~~

显示如下：

~~~
which: no hbase in (/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/module/jdk1.8.0_212/bin:/opt/module/hadoop-3.1.3/bin:/opt/module/hadoop-3.1.3/sbin:/opt/module/hive/bin:/home/atguigu/.local/bin:/home/atguigu/bin)
Hive Session ID = 36f90830-2d91-469d-8823-9ee62b6d0c26

Logging initialized using configuration in jar:file:/opt/module/hive/lib/hive-common-3.1.2.jar!/hive-log4j2.properties Async: true
Hive Session ID = 14f96e4e-7009-4926-bb62-035be9178b02
hive>
~~~

**2.打印 当前库 和 表头**

在hive-site.xml中加入如下两个配置: 

~~~shell
<property>
    <name>hive.cli.print.header</name>
    <value>true</value>
    <description>Whether to print the names of the columns in query output.</description>
  </property>
   <property>
    <name>hive.cli.print.current.db</name>
    <value>true</value>
    <description>Whether to include the current database in the Hive prompt.</description>
 </property>
~~~

**3.退出hive窗口：**

~~~
hive(default)>exit;
hive(default)>quit;
~~~


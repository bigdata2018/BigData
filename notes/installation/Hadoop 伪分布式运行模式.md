## Hadoop 伪分布式运行模式

<nav>
<a href="#一、前置条件">一、前置条件</a><br/>
<a href="#二、配置HDFS集群">二、配置HDFS集群</a><br/>
<a href="#三、配置文件总结">三、配置文件总结</a><br/>
</nav>




### 一、前置条件

Hadoop本地运行模式的运行依赖 JDK，Hadoop需要预先安装，安装步骤见：

+ [Centos7.6环境下 JDK8 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)
+ [Hadoop 安装](https://github.com/heibaiying/BigData-Notes/blob/master/notes/installation/Linux下JDK安装.md)



### 二、配置HDFS集群

**1.切换到配置环境的目录**

~~~shell
cd /opt/module/hadoop-3.1.3/etc/hadoop/
~~~

**2.配置：hadoop-env.sh**

~~~shell
#进入hadoop-env.sh
vim hadoop-env.sh
~~~

~~~shell
#在hadoop-env.sh里修改JAVA_HOME 路径：
export JAVA_HOME=/opt/module/jdk1.8.0_212
~~~

**3.配置：core-site.xml**

~~~shell
#以下内容入在<configuration>与</configuration>之间
<!-- 指定HDFS中NameNode的地址 -->
<property>
	<name>fs.defaultFS</name>
    <value>hdfs://hadoop101:9820</value>
</property>

<!-- 指定Hadoop运行时产生文件的存储目录 -->
<property>
	<name>hadoop.tmp.dir</name>
	<value>/opt/module/hadoop-3.1.3/data/tmp</value>
</property>

<!--  至少配置以上两项 -->
<!--  缓冲区大小，实际工作中根据服务器性能动态调整 -->
<property>
	<name>io.file.buffer.size</name>
	<value>4096</value>
</property>

<!--  开启hdfs的垃圾桶机制，删除掉的数据可以从垃圾桶中回收，单位分钟 -->
<property>
	<name>fs.trash.interval</name>
	<value>10080</value>
</property>
~~~

**4.配置：hdfs-site.xml**

~~~shell
<!-- 指定HDFS副本的数量 -->
<property>
	<name>dfs.replication</name>
	<value>1</value>
</property>
~~~

**5.启动集群**

1. 格式化NameNode（第一次启动时格式化，以后就不要总格式化）

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs namenode -format
   ~~~

2. 启动NameNode

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs –daemon start namenode
   ~~~

3. 启动DataNode

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs –daemon start datanode
   ~~~

**6.查看集群**

1. 查看是否成功启动

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ jps
   13786 Jps
   13866 NameNode
   13618 DataNode
   ~~~

2. web端查看HDFS文件系统

   http://hadoop101:9870

**7.操作集群**

1. 在HDFS文件系统上创建一个input文件夹

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs dfs -mkdir -p /user/nogc/input
   ~~~

2. 将测试文件内容上传到文件系统上

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs dfs -put wcinput/wc.input /user/nogc/input/
   ~~~

3. 查看上传的文件是否正确

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs dfs -ls  /user/nogc/input/
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs dfs -cat  /user/nogc/ input/wc.input
   ~~~

4. 运行MapReduce程序

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hadoop jar
   share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /user/nogc/input/ /user/nogc/output
   ~~~

5. 查看输出结果

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs dfs -cat /user/nogc/output/*
   ~~~

6. 将测试文件内容下载到本地

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ hdfs dfs -get /user/nogc/output/part-r-00000 ./wcoutput/
   ~~~

7. 删除输出结果

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ hdfs dfs -rm -r
   /user/nogc/output
   ~~~

**8.配置yarn-site.xml**

~~~shell
<!-- Reducer获取数据的方式 -->
<property>
 		<name>yarn.nodemanager.aux-services</name>
 		<value>mapreduce_shuffle</value>
</property>

<!-- 指定YARN的ResourceManager的地址 -->
<property>
	<name>yarn.resourcemanager.hostname</name>
	<value>hadoop101</value>
</property>
<property>
     <name>yarn.nodemanager.env-whitelist</name>        	  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
 </property>
~~~

**9.配置mapred-site.xml**

~~~shell
<!-- 指定MR运行在YARN上 -->
<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
</property>
~~~

**10.启动YARN集群**

1. 启动ResourceManager

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/yarn --daemon start resourcemanager
   ~~~

2. 启动NodeManager

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/yarn --daemon start nodemanager
   ~~~

**11.YARN集群操作**

1. YARN的浏览器页面查看

   http://hadoop101:8088

2. 执行MapReduce程序

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hadoop jar
    share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount /user/nogc/input  /user/nogc/output
   ~~~

3. 查看运行结果

   ~~~shell
   [nogc@hadoop101 hadoop-3.1.3]$ bin/hdfs dfs -cat /user/nogc/output/*
   ~~~

### 三、配置文件总结

1. Hadoop配置文件分两类：默认配置文件和自定义配置文件，只有用户想修改某一默认配置值时，才需要修改自定义配置文件，更改相应属性值。

2. 自定义配置文件：

   **core-site.xml**、**hdfs-site.xml**、**yarn-site.xml**、**mapred-site.xml**四个配置文件存放在$HADOOP_HOME/etc/hadoop这个路径上，用户可以根据项目需求重新进行修改配置。

3. 默认配置文件：

| 要获取的默认文件     | 文件存放在Hadoop的jar包中的位置                             |
| -------------------- | ----------------------------------------------------------- |
| [core-default.xml]   | hadoop-common-3.1.3.jar/  core-default.xml                  |
| [hdfs-default.xml]   | hadoop-hdfs-3.1.3.jar/  hdfs-default.xml                    |
| [yarn-default.xml]   | hadoop-yarn-common-3.1.3.jar/  yarn-default.xml             |
| [mapred-default.xml] | hadoop-mapreduce-client-core-3.1.3.jar/  mapred-default.xml |
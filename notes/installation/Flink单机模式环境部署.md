# Flink简介

[一、Flink集群安装模式种类](https://github.com/heibaiying/BigData-Notes/blob/master/notes/Spark简介.md#一简介)
[二、单机模式环境部署](https://github.com/heibaiying/BigData-Notes/blob/master/notes/Spark简介.md#二特点)

## 一、Flink集群安装模式种类

1. Flink支持多种安装模式

- local（本地）——单机模式，一般不使用
- standalone——独立模式，Flink自带集群，开发测试环境使用
- yarn——计算资源统一由Hadoop YARN管理，生产测试环境使用

## 二、单机模式环境部署 

![](D:\黑马大数据2020\就业课件\2\23--Flink基础\flink基础\原始笔记\assets\1559019398324.png)

- Flink程序需要提交给`Job Client`
- Job Client将作业提交给`Job Manager`
- Job Manager负责协调资源分配和作业执行。 资源分配完成后，任务将提交给相应的`Task Manager`
- Task Manager启动一个线程以开始执行。Task Manager会向Job Manager报告状态更改。例如开始执行，正在进行或已完成。 
- 作业执行完成后，结果将发送回客户端（Job Client）



**环境准备:**

- 下载安装包 https://archive.apache.org/dist/flink/flink-1.6.1/flink-1.6.1-bin-hadoop27-scala_2.11.tgz
- 服务器: hadoop101 (192.168.1117.101)



**安装步骤：**

1. 上传压缩包

2. 解压 

   ```shell
   tar -zxvf flink-1.6.1-bin-hadoop27-scala_2.11.tgz -C ../module/
   ```

   

3. 启动

   ```shell
   cd /opt/module/flink-1.6.1
   ./bin/start-cluster.sh 
   ```

   

   > **使用JPS可以查看到如下面两个进程（每个人端口号不一样）**
   >
   > - 1724 StandaloneSessionClusterEntrypoint
   >
   > - 2174 TaskManagerRunner



4. 访问web界面

   ```shell
   http://hadoop101:8081/
   ```

   ![image-20200414083701488](C:\Users\whj\AppData\Roaming\Typora\typora-user-images\image-20200414083701488.png)

   > `slot`在flink里面可以认为是资源组，Flink是通过将任务分成子任务并且将这些子任务分配到slot来并行执行程序。

5. 运行测试任务

   注意输出的文件名flink_out之前不存在 否则报IOException错误

   ```shell
   bin/flink run /opt/module/flink-1.6.1/examples/batch/WordCount.jar --input /opt/module/flinktest --output /opt/module/flink_out
   ```

   > **控制台输出:** 
   >
   > Starting execution of program
   > Program execution finished
   > Job with JobID 5c108b292ddb4f07ebf25ac5cee461cb has finished.
   > Job Runtime: 634 ms

   

   **观察WebUI**

![image-20200414084836211](C:\Users\whj\AppData\Roaming\Typora\typora-user-images\image-20200414084836211.png)
#### 安装环境：Windows 10家庭版+CentOS 7.6+VMware-workstation-full-15.5.1-15018445

整个安装过程分两大步，第一步装机器，第二步装系统.

##### 第一步：装机器

1) 检查物理机虚拟化支持是否开启，需要进入到BIOS中设置，因各种电脑型号进入BIOS

方式不同，同学们自行查找对应品牌电脑如何进入BIOS

建议: 先安装，如果安装中提示虚拟化未开启，再进入BIOS设置，如安装一切顺序，则不需要进行任何设置。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(1).jpg) 

2) 在VMware中新建虚拟机

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(2).jpg) 

3) 默认即可,不需要做任何修改，直接下一步

 ![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(3).jpg)

4) 选择稍后安装操作系统，然后下一步 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(4).jpg) 

5) 选择安装的操作系统为Linux,版本为CentOS7 64位 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(5).jpg) 

6) 虚拟机命名，可随意取， 安装位置最好选择固态硬盘(有固态的情况..)，快的飞起 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(6).jpg) 

7) 按照物理机CPU实际情况，选择处理器配置, 处理器数量*每个处理器内存数量要小于等于物理机CPU的数量，否则报错.

查看物理机CPU数量:

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(7).jpg) 

选择虚拟机CPU配置

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(8).jpg) 

8) 选择分配给虚拟机的内存，最少2G,后续Hadoop阶段建议适当调大, 土豪机可任性一把

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(9).jpg) 

9) 网络类型选择NAT 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(10).jpg) 

10) I/O控制器类型选择默认推荐即可，同学们无须纠结不同处，不重要。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(11).jpg) 

11) 磁盘类型选择SCSI， 同学们无须纠结不同处，不重要。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(12).jpg) 

12) 选择创建新虚拟磁盘

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(13).jpg) 

13) 磁盘容量指定50G，选择将虚拟磁盘拆分成多个文件. 不要勾选立即分配所有磁盘空间,否则会直接占用50G大小的磁盘空间。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(14).jpg) 

14) 选择Linux文件的存储位置，建议选择到Linux的安装位置，存储到第6步选择的目录下

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(15).jpg) 

15) 至此，装机器完成。 点击完成即可。如果想更改配置，可点击自定义硬件。对之前步骤的选择进行更改。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(16).jpg) 

##### 第二步：装系统

 

1) 选择系统盘位置

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(17).jpg) 

 

2) 加电，开启虚拟机

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(18).jpg) 

3) 进入倒计时,鼠标点进去, 键盘上下键可以选择，选择Install CentOS 7 ，然后回车即可.

不要选择Test this media & install CentOS 7, 然后就没有然后了……

  TIPS:  Ctrl+Alt可以实现Windows主机和VM之间窗口的切换

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(19).jpg) 

4) 选择安装语言为简体中文，虽然我知道大家英语都很好，但是不要浪…… 汉语是最NB的语言，不接受任何反驳。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(20).jpg) 

5) 设置日期和时间  选择亚洲/上海

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(21).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(22).jpg) 

6) 设置软件选择  GNOME桌面,

TIPS：第一次安装建议选择GNOME桌面，实际以后真实服务器中不会带桌面,都是最小化安装，进入系统就是Shell界面，全部通过命令操作。 等学习完Linux命令，能使用命令熟练操作Linux后，可选择最小安装.

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(23).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(24).jpg) 

7) 设置安装位置,即进行分区。（可选）

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(25).jpg) 

选择我要配置分区,然后点左上角完成进入分区界面

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(26).jpg) 

第一个分区: /boot  引导分区,建议给1G 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(27).jpg) 

修改设备类型为标准分区,文件系统为ext4

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(28).jpg) 

第二个分区 swap , 交换分区，建议设置与内存大小一致. 2G

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(29).jpg) 

修改设备类型为标准分区,文件系统为swap

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(30).jpg) 

第三个分区 / , 剩余的磁盘大小全部分配。 /为linux文件系统的根目录。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(31).jpg) 

修改设备类型为标准分区,文件系统为ext4

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(32).jpg) 

确认最终分区后的情况,点击左上角完成即可。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(33).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(34).jpg) 

8) 关闭KDUMP

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(35).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(36).jpg) 

9) 配置网络和主机名(可选,也可在安装好后进入到系统中配置).

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(37).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(38).jpg) 

10) 最后确认配置的各个选项无误,点击开启安装即可.

11) 配置ROOT密码。Linux会默认提供一个超级管理员用户，就是root. 类似于Windows

的admin用户

TIPS: 设置的root用户密码一定要记住，因此设置一个别人能帮你记住的密码，如123456.你们懂的!!!!!  

 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(39).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(40).jpg) 

12) 等等等……等待安装完成，预估10分钟左右……

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(41).jpg) 

 

13) 安装完成，重启虚拟机

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(42).jpg) 

14) 初始设置,接受许可证即可， 其他的不用配置。

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(43).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(44).jpg) 

15) 点击完成配置

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(45).jpg) 

 

16) 进入欢迎界面，选择汉语 ，点击右上角 前进

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(46).jpg) 

 

17) 选择键盘布局为汉语

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(47).jpg) 

18) 隐私设置 ，根据自己的喜好选择即可

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(48).jpg) 

19) 确定时区

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(49).jpg) 

 

20) 跳过关联账号

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(50).jpg) 

21) CentOS要求必须设置一个普通账户,可随意设置

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(51).jpg) 

 

22) 设置普通账户密码

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(52).jpg) 

23) 终于可以开始使用了

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(53).jpg) 

24) 关闭Getting Started

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(54).jpg) 

25) 注销当前普通用户，使用root用户登录

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(55).jpg) 

26) 选择未列出,使用root登录

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(56).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(57).jpg) 

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(58).jpg) 

27) 为root用户配置欢迎设置。参考上面设置的步骤

![img](https://github.com/bigdata2018/BigData/blob/master/picture/Centos_wps63%20(59).jpg) 

28) 安装总结:

整个Linux的安装分两大步，第一个大步装机器，也就是要虚拟一台机器出来,这里需要注意的是以后工作中不需要装虚拟机，全部都是真实的服务器，直接装系统即可.

第二大步就是装系统。

步骤比较多，大家安装时一定要仔细,如果安装过程出错，可选择删除重新安装.多试几次也好，毕竟慢慢就熟练了。

到此，可以开心的使用Linux了.

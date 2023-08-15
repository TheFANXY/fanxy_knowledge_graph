# **第** **1** **章** **Linux** **入门** 

## **1.1** **概述** 

![img](https://img-blog.csdnimg.cn/f51d66214ab441689b25f8a00f8452c6.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

## **1.2 Linux** **和** **Windows** **区别** 

![img](https://img-blog.csdnimg.cn/e1b299e3b8144d16967bc87eb4f9dccc.png)

##  **1.3 CentOS** **下载地址**

 ![img](https://img-blog.csdnimg.cn/95285e94ddc4499d901fc5f39dd8d17d.png)

# **第** **2** **章** **VM** **与** **Linux** **的安装** 

## **2.1** [VMWare 安装 CentOS 安装](https://blog.csdn.net/weixin_44981126/article/details/131284325)

# **第** **3** **章** **Linux** **文件与目录结构** 

## **3.1 Linux** **文件** 

Linux 系统中一切皆文件。 

## **3.2 Linux** **目录结构**

**如果不进行分区，那么磁盘都会在/根目录下。在boot下分区，挂载点就是boot，在boot里面的所有文件都会存储到磁盘分区1里，而不是根目录磁盘。从硬盘上看，boot与/是平行的，但从目录结构（逻辑）上看，boot是在/下面的。**

![img](https://img-blog.csdnimg.cn/0922d8d2e7b5489da2ab11a187e0d9e5.png)

**这里带箭头的意思是不是实际目录，是逻辑目录。比如下面的bin，发现实际目录是usr下的bin。不要被误导，不是user的缩写，而是 unix system resources。而sbin就是system binary 系统管理员，超级用户才能使用的二进制机器码文件。**

![img](https://img-blog.csdnimg.cn/decc9145bb06493cb73a5f8b82b19182.png)

![img](https://img-blog.csdnimg.cn/fd0ab8a2321240c19d426ccc1037e37e.png)

![img](https://img-blog.csdnimg.cn/0bba84b13f0341d6833bc5435c28aca7.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/0856e22748fa401d87bd47b63f2191ed.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/a02ecc52579a44db8521814119b5403f.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/ba147be68f324bf08c3bcd5a84422873.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/d05ce1cb72e14f97b69b1d76c90c0f23.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

# **第** **4** **章** **VI/VIM** **编辑器（重要）** 

## **4.1** **是什么** 

VI 是 Unix 操作系统和类 Unix 操作系统中最通用的文本编辑器。 

VIM 编辑器是从 VI 发展出来的一个性能更强大的文本编辑器。可以主动的以字体颜 

色辨别语法的正确性，方便程序设计。VIM 与 VI 编辑器完全兼容。 

## **4.2** **测试数据准备** 

### **1****）拷贝****/etc/profile** **数据到****/root** **目录下**

![img](https://img-blog.csdnimg.cn/0fc4167b5a5f4740a1df37f1d61c20a9.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

## **4.3** **一般模式** 

**以 vi 打开一个档案就直接进入一般模式了（这是默认的模式）。在这个模式中， 你可****以使用『上下左右』按键来移动光标，你可以使用『删除字符』或『删除整行』来处理档****案内容， 也可以使用『复制、粘贴』来处理你的文件数据。**

**这里的shifit + 6 （^） shifit + 4 ($) 相当于正则表达式里面的匹配开头和结尾**

![img](https://img-blog.csdnimg.cn/94ece8367b464352b619ca31998f7f96.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/49f5c28fa8ca4d5dad8e4418d72e53d6.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑 

## **4.4** **编辑模式** 

**在一般模式中可以进行删除、复制、粘贴等的动作，但是却无法编辑文件内容的！要等到你按下『i, I, o, O, a, A』等任何一个字母之后才会进入编辑模式。** 

**注意了！通常在Linux中，按下这些按键时，在画面的左下方会出现『INSERT或 REPLACE』的字样，此时才可以进行编辑。而如果要回到一般模式时， 则必须要按下 『Esc』这个按键即可退出编辑模式。** 

### **1）进入编辑模式**

***\*![img](https://img-blog.csdnimg.cn/5f17d8c3b61c4c3baa86f9486d07820a.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

## **4.5 指令模式** 

**在一般模式当中，输入『 : / ?』3个中的任何一个按钮，就可以将光标移动到最底下那一行。** 

**在这个模式当中， 可以提供你『搜寻资料』的动作，而读取、存盘、大量取代字符、 离开 vi 、显示行号等动作是在此模式中达成的！**

### **1****）基本语法**

![img](https://img-blog.csdnimg.cn/e16a8133946e4d31b6500af96b4d706a.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

> **只有s没有百分号：当前行第一个匹配到的替换**
>
> **只有s没有百分号结尾加/g：当前行全部替换**
>
> **:n1， n2s/word1/word2/g：n1与n2为数字，在n1行与n2行之间寻找word1这个字符串，并将该字符串替换为word2
>  :1， $s/word1/word2/g：将全文的word1替换为word2**
>
> **:1， $s/word1/word2/gc：将全文的word1替换为word2，且在替换前要求用户确认**
>
> **v：选中文本，按两下ESC取消选中状态**

### **2****）案例实操** 

（1）强制保存退出 :wq!

## **4.6** **模式间转换** 

如图 4-2 所示。

![img](https://img-blog.csdnimg.cn/8cbc1ff129144bca8ac07ed5ecda556e.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

# **第** **5** **章 网络配置（重点）** 

## **5.1** **查看网络** **IP** **和 网关** 

**当前windows下的网络适配器。多了几个网卡。（为什么多了？）**

![img](https://img-blog.csdnimg.cn/40d3ec16f4ee44a2b83cddc39b8beb50.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**桥接模式下，相当于通过搭建网桥和交换机，使得虚拟机对于当前局域网的别的设备是同等通信状态，位于同一个局域网。** 

### ![img](https://img-blog.csdnimg.cn/76992c8a638b4ad6922a9aff79031537.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**NAT模式，全称就是Natwork address Thraslation，网络地址转换，虚拟机和主机构建一个专用网络，并通过虚拟网络地址转换（NAT）设备对IP进行转换，虚拟机通过共享主机IP可以访问外部网络，但外部网络无法访问虚拟机。**

**按理说，根据虚拟路由（其实是NAT，起到类似功能，生成一个DHCP服务器动态分配地址，这也是为什么虚拟机网段和外面不同还能ping同我们pc的原因），我们外面的pc不应该能ping同内部的虚拟机。就像我们上网，到达外面的公网，但是访问到的ip只能获取我们最上级的公网ip，而不是我们私网的设备ip。这里VmWare的做法是创建一个新的网卡，在这个网卡下，就相当于pc和这些内部的虚拟机又同在一个网段了，这个网卡，就是我们之前看到的VmWare8。**

**所以这里就是为什么主机虚拟的网卡设置为1，而内部我们查看虚拟机的VmWare8的网关是2。而DHCP设置的分配范围也避开了这两个地址。所以此时他们能够彼此无冲突在一个网段通信。**

![img](https://img-blog.csdnimg.cn/c725a9c839d3475282605d333c247082.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**而仅主机模式，仍然单独虚拟一块网卡，也就是我们之前看到的VmWare1，但虚拟路由的功能消失，仅起到一个交换机的效果，让主机和虚拟机唯一通信。**

### **1****）查看虚拟网络编辑器，如图** **5-1** **所示**

### ![img](https://img-blog.csdnimg.cn/6c8f7ccd9c824bcfbc484291d3c9e0f8.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**2****）修改虚拟网卡** **Ip****，如图** **5-2** **所示** 

### ![img](https://img-blog.csdnimg.cn/1b8c86e1761946489da1d5993b316ccd.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**3****）查看网关，如图** **5-3** **所示** 

### ![img](https://img-blog.csdnimg.cn/eb21411eb124413ebcd5dfa5165e83c9.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**4****）查看** **windows** **环境的中** **VMnet8** **网络配置，如图** **5-4** **所示** 

![img](https://img-blog.csdnimg.cn/b1bc37649a5c441ba552a91347b18277.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

## **5.2** **配置网络** **ip** **地址** 

### **5.2.1 ifconfig** **配置网络接口** 

**ifconfig :network interfaces configuring 网络接口配置**

**1）基本语法** 

**ifconfig （功能描述：显示所有网络接口的配置信息）**

**2）案例实操** 

**（1）查看当前网络 ip**

***\*![img](https://img-blog.csdnimg.cn/94580253b8f04b22a988b2f277157bce.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **5.2.2 ping 测试主机之间网络连通性** 

### **1）基本语法** 

**ping 目的主机 （功能描述：测试当前服务器是否可以连接目的主机）**

### **2）案例实操** 

**（1）测试当前服务器是否可以连接百度**

***\*![img](https://img-blog.csdnimg.cn/3f2f6dddf0ce48da994bdf0578fe15e4.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **5.2.3 修改 IP 地址** 

### **1） 查看 IP 配置文件，如图 5-5 所示**

![img](https://img-blog.csdnimg.cn/fbf63f94b6ad4e7db8a62adcab4fea22.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/cb662bedb67849afb08339f7a1fec7db.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/97529b0f819e48289ecd549302ea1b7c.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**修改后，如图 5-6 所示** 

![img](https://img-blog.csdnimg.cn/43d3f5f34690492ca6edd4894597c970.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**编辑完后，按键盘 esc ，然后输入 :wq 回车即可。** 

![img](https://img-blog.csdnimg.cn/96673412478f455e9951f498da396324.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**这里对照默认的VMware的默认设置更改，把ip改成和主机一样即可。 其他保持一样。**

### **2****）执行** **service network restart** **重启网络****,****如图** **5-7** **所示** 

![img](https://img-blog.csdnimg.cn/6a3ed7a0c6844bee97dbff58d03dd762.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **5.2.4** **修改** **IP** **地址后可能会遇到的问题** 

**（1）物理机能 ping 通虚拟机，但是虚拟机 ping 不通物理机,一般都是因为物理机的防火墙问题,把防火墙关闭就行** 

**（2）虚拟机能 Ping 通物理机,但是虚拟机 Ping 不通外网,一般都是因为 DNS 的设置有问题** 

**（3）虚拟机 Ping www.baidu.com 显示域名未知等信息,一般查看 GATEWAY 和 DNS 设置是否正确** 

**（4）如果以上全部设置完还是不行，需要关闭 NetworkManager 服务** 

​    **systemctl stop NetworkManager 关闭** 

​    **systemctl disable NetworkManager 禁用** 

**（5）如果检查发现 systemctl status network 有问题 需要检查 ifcfg-ens33**

## **5.3** **配置主机名** 

### **5.3.1** **修改主机名称** 

**1****） 基本语法** 

h**ostname （功能描述：查看当前服务器的主机名称）**

**2） 案例实操** 

**（1）查看当前服务器主机名称**

***\*![img](https://img-blog.csdnimg.cn/9d919ed72f9d4301a949304571f5a827.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（2）如果感觉此主机名不合适，我们可以进行修改。通过编辑/etc/hostname 文件**

***\*![img](https://img-blog.csdnimg.cn/01c76365ca2242529fce68e574dc1a57.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**修改完成后重启生效。** 

**如果不想重启服务器，可以通过以下命令修改**

```
hostnamectl set- hostname |->这里写想改的名字，前面的符号是为了分界，不需要打
```

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

### **5.3.2 修改 hosts 映射文件** 

**1）修改 linux 的主机映射文件（hosts 文件）** 

**后续在 hadoop 阶段，虚拟机会比较多，配置时通常会采用主机名的方式配置，比较简单方便。 不用刻意记 ip 地址。** 

**（1）打开/etc/hosts**

***\*![img](https://img-blog.csdnimg.cn/dd004bb2fdd345619a1ae358380ed817.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**添加如下内容** 

***\*![img](https://img-blog.csdnimg.cn/accea43dca68402e86d189c55822d8dc.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（2）重启设备，重启后，查看主机名，已经修改成功** 

**2）修改 windows 的主机映射文件（hosts 文件）** 

**（1）进入 C:\Windows\System32\drivers\etc 路径** 

**（2）打开 hosts 文件并添加如下内容**

***\*![img](https://img-blog.csdnimg.cn/e9b674f139ea49ff876d7f2370091dc9.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**3）修改 window10 的主机映射文件（hosts 文件）** 

**（1）进入 C:\Windows\System32\drivers\etc 路径** 

**（2）拷贝 hosts 文件到桌面** 

**（3）打开桌面 hosts 文件并添加如下内容**

***\*![img](https://img-blog.csdnimg.cn/32375c29a312492fa3f611bf72956902.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（4）将桌面 hosts 文件覆盖 C:\Windows\System32\drivers\etc 路径 hosts 文件**

## **5.4 远程登录** 

**通常在工作过程中，公司中使用的真实服务器或者是云服务器，都不允许除运维人员之外的员工直接接触，因此就需要通过远程登录的方式来操作。所以，远程登录工具就是必不可缺的，目前，比较主流的有 Xshell, SSH Secure Shell, SecureCRT,FinalShell 等，同学们可以根据自己的习惯自行选择.**

***\*![img](https://img-blog.csdnimg.cn/4904b81eb17845e4a7b3ce0668e53724.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**ssh** 

# ![img](https://img-blog.csdnimg.cn/25fa996d86e54a42a9dcfe8b51063b86.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**xshell设置**

# **第** **6** **章 系统管理** 

## **6.1 Linux** **中的进程和服务** 

**计算机中，一个正在执行的程序或命令，被叫做“进程”（process）。** 

**启动之后一只存在、常驻内存的进程，一般被称作“服务”（service）。** 

## **6.2 service 服务管理（CentOS 6 版本-了解）**

**系统的服务是需要由一个后台进程，也就是守护进程（deamon）进行管理。\*守护进程\*(daemon)是一类在后台运行的特殊进程，用于执行特定的系统任务。很多\*守护进程\*在系统引导的时候启动，并且一直运行直到系统关闭。另一些只在需要的时候才启动，完成任务后就自动结束。我们能看到很多带有.d结尾的就是守护进程。也就代表它是一个系统服务了。**

### **1） 基本语法** 

**service 服务名 start | stop | restart | status** 

### **2） 经验技巧** 

**查看服务的方法：/etc/init.d/服务名 ,发现只有两个服务保留在 service**

![img](https://img-blog.csdnimg.cn/3b3680b628b74197b93766d04387acd9.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **3****） 案例实操** 

**（1）查看网络服务的状态**

***\*![img](https://img-blog.csdnimg.cn/c352cd44da9e4be29ed463ca95b08837.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（2）停止网络服务**

***\*![img](https://img-blog.csdnimg.cn/17bb8f6d2f1d414e9ad899cafd00eeec.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（3）启动网络服务**

***\*![img](https://img-blog.csdnimg.cn/d9d0ac95948649a1887fd51cc04126bf.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（4）重启网络服务**

***\*![img](https://img-blog.csdnimg.cn/ad6870267b5441908ddf0899ed6d6256.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

## **6.3 chkconfig 设置后台服务的自启配置（CentOS 6 版本）** 

### **1） 基本语法**

**chkconfig （功能描述：查看所有服务器自启配置）** 

**chkconfig 服务名 off （功能描述：关掉指定服务的自动启动）** 

**chkconfig 服务名 on （功能描述：开启指定服务的自动启动）** 

**chkconfig 服务名 --list （功能描述：查看服务开机启动状态）**

### **2） 案例实操** 

**（1）开启/关闭 network(网络)服务的自动启动**

***\*![img](https://img-blog.csdnimg.cn/de89700366c44a16805e6dc5992fa501.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（2）开启/关闭 network 服务指定级别的自动启动**

![img](https://img-blog.csdnimg.cn/f624bd6bc7fa4d4a82f5a2cca27b2c52.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

## **6.4 systemctl** **（****CentOS 7** **版本****-****重点掌握****）** 

### **1****） 基本语法**

systemctl start | stop | restart | status  服务名

### **2****） 经验技巧** 

查看服务的方法：/usr/lib/systemd/system

![img](https://img-blog.csdnimg.cn/bc9d621728e94d7389d355044ccc55b8.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/b7d51380862045be8b03ceb3cacc8de6.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **3****）案例实操** 

（**1）查看防火墙服务的状态        systemctl status firewalld** 

**（2）停止防火墙服务              systemctl stop firewalld** 

**（3）启动防火墙服务              systemctl start firewalld** 

**（4）重启防火墙服务              systemctl restart firewalld**

## **6.5 systemctl 设置后台服务的自启配置** 

### **1）基本语法**

**systemctl list-unit-files （功能描述：查看服务开机启动状态）** 

**systemctl disable service_name （功能描述：关掉指定服务的自动启动）** 

**systemctl enable service_name （功能描述：开启指定服务的自动启动）**

### **2）案例实操** 

**（1）开启/关闭 iptables(防火墙)服务的自动启动** 

![img](https://img-blog.csdnimg.cn/e9879fc0bc364b428fbf9cda0fa472e2.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

## **6.6** **系统运行级别** 

### **1****）****Linux** **运行级别****[CentOS 6]****，如图** **7-1** **所示**

![img](https://img-blog.csdnimg.cn/0506430e97714820930273c0db6d9f8b.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**补充：--------------------------------------------------------------------------------------------------------------------->**

**运行级别1：类似于windows的安全模式，可以不需要密码以root进入，重设密码，但是需要本地登录即物理机**

**运行级别2：没有NetWork FileSystem，即无网络文件系统，就无法使用网络**

### **2****）****CentOS7** **的运行级别简化为****:** 

**multi-user.target 等价于原运行级别 3（多用户有网，无图形界面）** 

**graphical.target 等价于原运行级别 5（多用户有网，有图形界面）** 

**图形化界面5下，可以通过命令 setup直接进入图形化管理系统服务，也可以终端通过init 3或者 init 5命令进行切换**

### **3****） 查看当前运行级别****:** 

**systemctl get-default** 

### **4）修改当前运行级别** 

**可以使用快捷键** **ctrl + Alt + F2** **切到** **原运行级别 3（多用户有网，无图形界面）**

**可以使用快捷键** **ctrl + Alt + F1** **切到** **原运行级别 5（多用户有网，有图形界面）**

**或者通过代码形式**

**systemctl set-default TARGET.target （这里 TARGET 取 multi-user 或者 graphical）** 

## **6.7 关闭防火墙** 

### **1） 临时关闭防火墙** 

**（1）查看防火墙状态**

***\*![img](https://img-blog.csdnimg.cn/5566e1a543fb4e9c9409460c1ec18bfb.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（2）临时关闭防火墙**

***\*![img](https://img-blog.csdnimg.cn/2953e9b676ec4b7aad2eba26deb54d01.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **2）开机启动时关闭防火墙** 

**（1）查看防火墙开机启动状态**

***\*![img](https://img-blog.csdnimg.cn/6889e206e0f3451aa57f5dfb89fc7ad1.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（2）设置开机时关闭防火墙**

***\*![img](https://img-blog.csdnimg.cn/dcbdf016d29440408c6c2ef6c658b393.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

## **6.8 关机重启命令** 

**在 linux 领域内大多用在服务器上，很少遇到关机的操作。毕竟服务器上跑一个服务是永无止境的，除非特殊情况下，不得已才会关机。**

### **1）基本语法** 

![img](https://img-blog.csdnimg.cn/66a6dd8002434d8d826a4cfb20f4a15a.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**也可以写指定的几点几分关机。如果是  -h （小写h） 就是关机，这里要区分一下大写。同时写-p就是断电关机，但是容易记混就没必要记，可以直接poweroff**

### **2****） 经验技巧** 

**Linux 系统中为了提高磁盘的读写效率，对磁盘采取了 “预读迟写”操作方式。当用户****保存文件时，Linux 核心并不一定立即将保存数据写入物理磁盘中，而是将数据保存在缓****冲区中，等缓冲区满时再写入磁盘，这种方式可以极大的提高磁盘写入数据的效率。但是，****也带来了安全隐患，如数据还未写入磁盘时，系统掉电或者其他严重问题出现，则将导****致数据丢失。****使用 sync 指令可以立即将缓冲区的数据写入磁盘。**

### **3****）案例实操** 

![img](https://img-blog.csdnimg.cn/61a58cb256f04a4d9f63a02a86de654f.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

# **第** **7** **章 常用基本命令（重要）** 

![img](https://img-blog.csdnimg.cn/35d8e1cde5db4f77aaf63500939663a2.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**Shell 可以看作是一个命令解释器，为我们提供了交互式的文本控制台界面。我们可以通过终端控制台来输入命令，由 shell 进行解释并最终交给内核执行。 本章就将分类介绍常用的基本 shell 命令。**

## **7.1** **帮助命令** 

### **7.1.1 man(****manual手册****)** **获得帮助信息**

### **1****）基本语法** 

**man [命令或配置文件] （功能描述：获得帮助信息）** 

### **2****）显示说明**

![img](https://img-blog.csdnimg.cn/eeb7c00a190a4100b5d7986606af263f.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **3****）案例实操** 

（1）查看 ls 命令的帮助信息

![img](https://img-blog.csdnimg.cn/b24ab0e0526c4b6b8a55f2cc20294c29.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.1.2 help** **获得** **shell** **内置命令的帮助信息** 

**一部分基础功能的系统命令是直接内嵌在 shell 中的，系统加载启动之后会随着 shell一起加载，常驻系统内存中。这部分命令被称为“内置（built-in）命令”，如cd，exit；相应的其它命令被称为“外部命令”。** 

**1****）基本语法** 

**help 命令（功能描述：获得 shell 内置命令的帮助信息）** 

**想要获得外部命令的参数说明版，man内嵌了一个方法，即通过： xxx（命令名称+空格）--help 查询**

**2）案例实操** 

**（1）查看 cd 命令的帮助信息** 

**[root@hadoop101 ~]# help cd**

### **7.1.3** **常用快捷键**

***Ctrl\*+\*S\*的作用是暂停终端的输出，如果您想恢复已暂停的输出，则需要按下Ctrl+Q**

![img](https://img-blog.csdnimg.cn/2484b5688c424fa2a8d91c4ae0d3b02b.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

![img](https://img-blog.csdnimg.cn/e5c36b6e2fd34f9da6141ff445b19a51.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**可以使用 type + 想要查看类型的命令，查看当前查看命令是内置命令还是外置命令。**

**reset命令将完全刷新终端屏幕，之前的终端输入操作信息将都会被清空。**

**这样虽然比较清爽，但整个命令过程速度有点慢，使用较少。**

**reset命令在终端控制错乱时非常有用。如果屏幕字符显示卡住了，此时就需要用reset命令了。**

## **7.2** **文件目录类** 

### **7.2.1 pwd** **显示当前工作目录的****绝对路径** 

**pwd:print working directory 打印工作目录** 

**1）基本语法** 

**pwd （功能描述：显示当前工作目录的绝对路径）** 

**pwd -P 显示软连接实际路径**

**2****）案例实操** 

（1）显示当前工作目录的绝对路径 

![img](https://img-blog.csdnimg.cn/e2117078013f4160a913d691a1619e1a.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.2.2 ls** **列出目录的内容** 

**ls:list 列出目录内容** 

**1）基本语法** 

**ls [选项] [目录或是文件]** 

**2）选项说明**

***\*![img](https://img-blog.csdnimg.cn/c7c39410588644f294189292258e613e.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**-h ：人性化输出**

**3）显示说明** 

**每行列出的信息依次是： 文件类型与权限 链接数 文件属主 文件属组 文件大小用byte****来表示 建立或最近修改的时间 名字**

**4）案例实操** 

**（1）查看当前目录的所有内容信息**

![img](https://img-blog.csdnimg.cn/098c90a1b8534fe8b0a69d9a909f8d2c.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.2.3 cd** **切换目录** 

**cd:Change Directory 切换路径** 

**1）基本语法** 

**cd [参数]** 

**2****）参数说明**

![img](https://img-blog.csdnimg.cn/c3da25ccac0b427493c15d494e5ca4c4.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**3****）案例实操**

![img](https://img-blog.csdnimg.cn/22bd4a69803042d7a3433e69250b5c41.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.2.4 mkdir** **创建一个新的目录** 

**mkdir:Make directory 建立目录** 

**1）基本语法** 

**mkdir [选项] 要创建的目录**

**2****）选项说明** 

![img](https://img-blog.csdnimg.cn/3272e3c6346441859034bf52956c1102.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**-p: parents（父目录）****可以把父目录一并创建**

**3****）案例实操** 

![img](https://img-blog.csdnimg.cn/c0c3f365e0ee4c92b149015305eb7c21.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.2.5 rmdir 删除一个空的目录** 

**rmdir:Remove directory 移除目录** 

**1）基本语法** 

**rmdir 要删除的空目录** 

**2）案例实操** 

**（1）删除一个空的文件夹**

***\*![img](https://img-blog.csdnimg.cn/a4c6de51a88c4c4182521510c23faacb.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **7.2.6 touch 创建空文件** 

**1）基本语法** 

**touch 文件名称 这里的文件如果不人为指定后缀，默认是文本文件，和vim文件不同的是，touch可以创建空文件。vim如果创建文件不书写内容，不会创建空文件，如果保存并退出就行。**

**2）案例实操**

### **7.2.7 cp 复制文件或目录** 

**1）基本语法** 

**cp [选项] source dest** 

**（功能描述：复制source文件到dest）**

**2）选项说明** 

***\*![img](https://img-blog.csdnimg.cn/4325c87b19854b0c9a9725ce87dd70b0.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\**3）参数说明** 

![img](https://img-blog.csdnimg.cn/7dcb775de4e94374a692bace2b0f019e.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**4****）经验技巧** 

**强制覆盖不提示的方法：\cp 加反斜线可以省略询问是否覆盖，加了反斜杠相当于使用linux原生命令，默认其实调用的是 cp -i （interactive）交互式命令，出现覆盖操作会进行询问
 alias 命令可以查看当前各种命令的别名**

**5）案例实操**

***\*![img](https://img-blog.csdnimg.cn/d49d42a90cd04306a433439e22e5cf07.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **7.2.8 rm 删除文件或目录** 

**1）基本语法** 

**rm [选项] deleteFile** 

**（功能描述：递归删除目录中所有内容）** 

**2）选项说明**

***\*![img](https://img-blog.csdnimg.cn/c16a91d62997498e96a8a87d1a35b000.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**3）案例实操** 

**（1）删除目录中的内容**

***\*![img](https://img-blog.csdnimg.cn/be1f38111a3740d08301954404a7521d.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

**（2）递归删除目录中所有内容**

***\*![img](https://img-blog.csdnimg.cn/070a5835dc9e4f7096a3eb261fd0807d.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **7.2.9 mv 移动文件与目录或重命名** 

**1）基本语法** 

**（1）mv oldNameFile newNameFile** 

**（功能描述：重命名）** 

**（2）mv /temp/movefile /targetFolder** 

**（功能描述：移动文件）**

**2）案例实操** 

***\*![img](https://img-blog.csdnimg.cn/323c03f354014b4bbf9d9c0e98912769.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **7.2.10 cat 查看文件内容 （concatenate and print files）**

**查看文件内容，从第一行开始显示。** 

**1）基本语法** 

**cat [选项] 要查看的文件** 

**2）选项说明**

***\*![img](https://img-blog.csdnimg.cn/2167b9ecee39468b92b5a26f5ef423df.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **7.2.11 more 文件内容分屏查看器** 

**more 指令是一个基于 VI 编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件****的内容。more 指令中内置了若干快捷键，详见操作说明。** 

**1）基本语法** 

**more 要查看的文件** 

**2）操作说明**

![img](https://img-blog.csdnimg.cn/7c83be1aae1d4ff3a2348534977ffe0c.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/ed4d703f8dfa4389bc1c8eab71a119cf.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.2.12 less** **分屏显示文件内容** 

**less 指令用来分屏查看文件内容，它的功能与 more 指令类似，但是比 more 指令更加强大，支持各种显示终端。less 指令在显示文件内容时，并不是一次将整个文件加载之后才显示，而是根据显示需要加载内容，对于显示大型文件具有较高的效率。** 

**1）基本语法** 

**less 要查看的文件** 

**2****）操作说明**

![img](https://img-blog.csdnimg.cn/7837bc890ba24e0494cad5b0595c1ef0.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/c3017f0ca785465cb5f13c623c508fdf.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.2.13 echo** 

**echo 输出内容到控制台** 

**1）基本语法** 

**echo [选项] [输出内容]** 

**选项：** 

**-e： (escape character)支持反斜线控制的字符转换**

***\*![img](https://img-blog.csdnimg.cn/119f1eca317542e199bb741f27b2867c.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

***\*![img](https://img-blog.csdnimg.cn/058ccd6497b44ac5a654480457484dd3.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

***\*![img](https://img-blog.csdnimg.cn/3ccbc9dd8300465b813a8513b7fe52b6.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑\****

### **7.2.14 head 显示文件头部内容**

### ![img](https://img-blog.csdnimg.cn/8e699792a6d540818716764910bf7c33.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**7.2.15 tail** **输出文件尾部内容**

![img](https://img-blog.csdnimg.cn/edc504a96d054c7c987bfc756e3d8080.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**若复写，可以复写，检测的控制台会报错。此时如果使用vim进行修改，追踪不会更新，因为文件在硬盘文件其实在底层是以一个带有index的结点进行记录的，就是inode，我们可以使用 ls -i 查看当前文件的索引号。而追踪的命令其实是根据index进行追踪，使用vim进行更改，会发现index变了。自然追踪也出现了问题。vim本质是先写到.swp文件里面，然后进行替换索引的，所以索引号发生了变化。**

**按 ctrl + s：可以暂停显示追踪（但其实还是后台记录了更新）**

**按 ctrl + q：可以继续显示追踪**

**按 ctrl + c：可以退出进程**

### **7.2.16 >** **输出重定向和** **>>** **追加** 

**1****）基本语法** 

**（1）ls -l > 文件** 

**（功能描述：列表的内容写入文件 a.txt 中（覆盖写））**

### ![img](https://img-blog.csdnimg.cn/8a3db6630a7d41cd9eeb780784b3bba8.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**7.2.17 ln** **软链接**

![img](https://img-blog.csdnimg.cn/8ee227f3a86b4d3d847e4d68c86a6c9b.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**可以看到使用链接，文件类型是l，正常文件是 -，而文件夹是d**

### ![img](https://img-blog.csdnimg.cn/fe285cff118d40e6b7cc15057a1473d3.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**不加 -s 相当于硬链接，删除链接文件对目标文件在磁盘的存储无影响。软连接就是一个文件指向，软链接类似于链表的一个节点，硬链接类似于一个指针。**

**加上 -s 相当于软链接，新建一个新的inode，指向目标结点，记录标记文件的地址。**

**一个文件当所有的硬链接数为0，此时没有能指向物理磁盘的文件，才会真正删除，在此之前哪怕删除源文件，还可以通过未删除的硬链接访问到这个文件。**

**新建一个目录后，目录本身是指向自身inode的一个硬链接，新建的目录内也有一个叫“.”的硬链接指向新建的目录，所以新建目录的硬链接数目是2**

### **7.2.18 history** **查看已经执行过历史命令**

## ![img](https://img-blog.csdnimg.cn/51b6bfbcbea540fb863c084a45dd08ec.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**history [数字] ：****显示指定数目的行数**

**![数字] ：重复执行指定行数****的命令**

**history -c：清除所有历史**

## **7.3** **时间日期类**

### ![img](https://img-blog.csdnimg.cn/b4e09d02b6ef4885ac35a4b25e98ece1.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑**7.3.1 date** **显示当前时间** 

**加号是需要打出来的，不是连接符号**

### ![img](https://img-blog.csdnimg.cn/d597c2379d8844948faa73aed8968137.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**date +%s : 把当前日期以时间戳形式显示**

### **7.3.2 date** **显示非当前时间** 

![img](https://img-blog.csdnimg.cn/5ac59083c97e4715911625853e87f578.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑![img](https://img-blog.csdnimg.cn/c5d71993316f4fa7aa2aaf00483c6f34.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑 

### **7.3.3 date** **设置系统时间** 

![img](https://img-blog.csdnimg.cn/2dc92708b8b048489842f92ece8b905c.png)

### **7.3.4 cal** **查看日历** 

![img](https://img-blog.csdnimg.cn/0dd330b659794eca89313bdffdc5b849.png)

## **7.4** **用户管理命令** 

### **7.4.1 useradd** **添加新用户**

![img](https://img-blog.csdnimg.cn/7959065467b6468396af72a320933d70.png)

**-M 不为用户设置家目录，一般用于创建系统用户** 

**-d [/home/xxx （用户名）] 新建用户但是给它的家目录改名，用户名不变。而如果使用有root权限的用户修改家目录的名称，本质上登录会显示找不到自己的家目录，然后跳转到home目录，并没有达到改变该用户的家目录的效果。** 

### **7.4.2 passwd** **设置用户密码** 

**1**）基本语法

**passwd 用户名** 

**（功能描述：设置用户密码)**

![img](https://img-blog.csdnimg.cn/8e284c990dd74f0a8502f7aaaeee92db.png)

### **7.4.3 id** **查看用户是否存在**

![img](https://img-blog.csdnimg.cn/6afa6b6bb2a740e18af71365fdde49c7.png)

### **7.4.4 cat /etc/passwd** **查看创建了哪些用户** 

![img](https://img-blog.csdnimg.cn/3612680832c6474eac95be1e86f2b790.png)

### **7.4.5 su** **切换用户**

**其实这里su切换用户是嵌套会话，我们可以通过exit退出，返回之前的用户，不需要再su切换回去。**

![img](https://img-blog.csdnimg.cn/ea772de004b249a08b9d9f371de6c728.png)

### **7.4.6 userdel** **删除用户**

![img](https://img-blog.csdnimg.cn/727bf40bec3944119f949d1330a38b3d.png)![img](https://img-blog.csdnimg.cn/d82a9dbff85e4c199044b89f441bab32.png)

### **7.4.7 who** **查看登录用户信息** 

![img](https://img-blog.csdnimg.cn/f741255ba96a4f5fb16c0cb0c9b2326d.png)

### **7.4.8 sudo** **设置普通用户具有** **root** **权限**

***sudo的英文全称是super user do\*,即以超级用户(root 用户)的方式执行命令。**

![img](https://img-blog.csdnimg.cn/e05ce7bf1c7648f8903823a125f979c0.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

### **7.4.9 usermod** **修改用户**

**1**）基本语法

**usermod -g 用户组 用户名**

![img](https://img-blog.csdnimg.cn/3edad2d9d2d146fcae5e2fa47626e1e2.png)

## **7.5 用户组管理命令** 

**每个用户都有一个用户组，系统可以对一个用户组中的所有用户进行集中管理。不同**Linux 系统对用户组的规定有所不同， 如Linux下的用户属于与它同名的用户组，这个用户组在创建用户时同时创建。

**用户组的管理涉及用户组的添加、删除和修改。组的增加、删除和修改实际上就是对**/etc/group文件的更新。

### **7.5.1 groupadd** **新增组** 

![img](https://img-blog.csdnimg.cn/253215abe180430ebb5a4cf3f2cf5b51.png)

### **7.5.2 groupdel** **删除组**

![img](https://img-blog.csdnimg.cn/84c9370145e545e5a2759126405559dd.png)

### **7.5.3 groupmod** **修改组** 

![img](https://img-blog.csdnimg.cn/1d0b9637d5374527826b4acde0cd6b91.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)![img](https://img-blog.csdnimg.cn/3d00b231f26c47e59369a6098ea2bb38.png)

### **7.5.4 cat /etc/group** **查看创建了哪些组**

![img](https://img-blog.csdnimg.cn/82b70a95084348eaaa80d42ad47e95f3.png)

## **7.6** **文件权限类**

### **7.6.1** **文件属性** 

**Linux系统是一种典型的多用户系统，不同的用户处于不同的地位，拥有不同的权限。** 

**为了保护系统的安全性，Linux系统对不同的用户访问同一文件（包括目录文件）的权限做了不同的规定。在Linux中我们可以使用ll或者ls -l命令来显示一个文件的属性以及文件所属的用户和组。**

**c字符文件，b块文件，l链接文件。**

![img](https://img-blog.csdnimg.cn/e59dc4ca32f648ccaf66824082e7f271.png)![img](https://img-blog.csdnimg.cn/75c48c5dc52847da88a615225decce6a.png)

新建一个目录后，目录本身是指向自身inode的一个硬链接，新建的目录内也有一个叫“.”的硬链接指向新建的目录，所以新建目录的硬链接数目是2

### **7.6.2 chmod** **改变权限**

![img](https://img-blog.csdnimg.cn/567209af50aa4015ab8d51cff1b9df22.png)![img](https://img-blog.csdnimg.cn/bc4149bcec8c494bb8a3947b9c52a0ca.png)

### **7.6.3 chown** **改变所有者**

![img](https://img-blog.csdnimg.cn/cf54f3ef75e14d9ab4665d331d642d0b.png)

### **7.6.4 chgrp** **改变所属组** 

![img](https://img-blog.csdnimg.cn/e8053d98c2b14156bd91dcbf0666a413.png)![img](https://img-blog.csdnimg.cn/171dfb5a8aa441288ecd0300a544d37c.png)

## **7.7** **搜索查找类** 

### **7.7.1 find** **查找文件或者目录**

![img](https://img-blog.csdnimg.cn/f22387306abd4809a98559d863a681ac.png)

### **7.7.2 locate** **快速定位文件路径**

**最好每次查询之前要更新一下！**

![img](https://img-blog.csdnimg.cn/8adf520db2904ccca22ddf3ef85bdbbd.png)![img](https://img-blog.csdnimg.cn/b2ee505c26b64147a46f9e871ddf7141.png)

**类似的也可以通过**

**which命令 + 命令查别名和位置**

**where is 命令 可以查位置** 

### **7.7.3 grep** **过滤查找及** “|” **管道符**

**grep  -i  ：忽略大小写**

**grep -m ： 最大匹配个数**

![img](https://img-blog.csdnimg.cn/96e7e59d4dbb47c5b84ca114e88cce03.png)

## **7.8** **压缩和解压类** 

### **7.8.1 gzip/gunzip** **压缩**

![img](https://img-blog.csdnimg.cn/05dcad731f074e4baeebb40e6b9417cd.png)

### **7.8.2 zip/unzip** **压缩** 

**1**）基本语法

**zip [选项] XXX.zip 将要压缩的内容 （功能描述：压缩文件和目录的命令）**

![img](https://img-blog.csdnimg.cn/21a18067ce464d1aba3917b28f708dc9.png)

### **7.8.3 tar** **打包**

**-z：调用gzip，-z打包同时压缩，-x解包**

**-c : create 创建打包文件**

![img](https://img-blog.csdnimg.cn/a5564b7901b943e8a143e993dccca9fe.png)![img](https://img-blog.csdnimg.cn/568eb27d22d94fa7871907db5da9d2d1.png)

## **7.9** **磁盘查看和分区类** 

### **7.9.1 du** **查看文件和目录占用的磁盘空间**

**disk(磁盘) 一般都不会打具体文件，我们使用ls的衍生命令，或者ll就可以了。**

**而直接使用，或者使用 -a(显示的更多)，显示的太冗余，我们只关心具体占用，就用-s（只显示总和）。但是这么操作显示又太少，就用最后的 ： du --max-depth=n（这里填1效果很好），可以选择显示层级**

![img](https://img-blog.csdnimg.cn/b5b280a5ad434e09a889a7614e5864c8.png)

### **7.9.2 df** **查看磁盘空间使用情况**

![img](https://img-blog.csdnimg.cn/5983454a4fad4355a576fae4f2dcaaf1.png)

![img](https://img-blog.csdnimg.cn/99a3c69c0bdc4cbbb939efbcc8ad4ada.png)

**free -h ：通过命令 free，可以查看当前内存占用情况**

### **7.9.3 lsblk** **查看设备挂载情况** 

![img](https://img-blog.csdnimg.cn/6d0e0d68053f48bfad46834c492cdd6d.png)

![img](https://img-blog.csdnimg.cn/1ddc6ebb74914efdb4c73c5a22a4e27e.png)

**MAJ:MIN : 显示设备的主要和次要设备号，MAJ(major number)表示不同的设备类型，MIN(minor number)表示同一个设备的的不同分区。**

**RM : 显示设备是否可移动。请注意，在此示例中，设备sr0的RM值等于1，表示它是可移动的。**

**SIZE : 提供有关设容量的信息。**

**RO : 显示设备是否为只读。在这种情况下，所有设备的RO均为RO = 0，表示它们不是只读的。**

**TYPE : 显示块设备是磁盘还是磁盘中的分区（部分）的信息。在此示例中，sda和sdb是磁盘，而sr0是只读存储器（rom）。**

**MOUNTPOINT : 显示设备的挂载点。**

**sr0其实指的是光驱。硬盘以前有IDE硬盘，但是现在已经不常用了，而常用的是SATA硬盘，还有SCSI小型计算机接口硬盘，服务器一般用的这个。像vda，就是虚拟硬盘。**

![img](https://img-blog.csdnimg.cn/45cc2111872042db80494914c0619c9a.png)

### **7.9.4** **mount/umount** **挂载**/卸载

![img](https://img-blog.csdnimg.cn/cb3e55d4e2734d07b5abcbf33f13f7eb.png)![img](https://img-blog.csdnimg.cn/81f16670fb0f46f6b577b93824f082bb.png)

![img](https://img-blog.csdnimg.cn/1fca1646cd124118be0d272ee00aa691.png)

![img](https://img-blog.csdnimg.cn/f0180f8819aa42a4b326cf08211961b8.png)![img](https://img-blog.csdnimg.cn/60adeece70d640bc97f71d22a2740f70.png)

**fstab: file system tab ：** **fstab是Linux系统中的一个配置文件，用于定义文件系统的挂载点和选项。**

**后面的两个0，第一个代表dump，之前创建虚拟机的时候有个选项就是kdump，勾选1会定期做备份。第二个0代表自检优先级。0代表不检查，1最高，2之后递减。**

**这里科普 fsck 命令： 文件系统检查，系统会自己调用。**

![img](https://img-blog.csdnimg.cn/06a0071e0e6c45628eae64926549f639.png)

**这里如果不想通过 lsblk -f 查询uuid，直接写路径地址就可以。这边用uuid好一点，实际使用时块设备文件名可能变化**

### **7.9.5 fdisk** **分区**

**1**）基本语法

**fdisk -l （功能描述：查看磁盘分区详情）** 

**fdisk 硬盘设备名 （功能描述：对新增硬盘进行分区操作）**

![img](https://img-blog.csdnimg.cn/80125336f9f845fc8173b62c50d78d63.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

![img](https://img-blog.csdnimg.cn/50cbb171c8af46fca9f9d9a20263ea1e.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**实战分区操作**

![img](https://img-blog.csdnimg.cn/7b83bb6eecfc40aaa61207c68e2bc769.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**添加后需要进行虚拟机的重启 ： reboot**

![img](https://img-blog.csdnimg.cn/bc87a864bd0745c38f699584703b7b69.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑

**Linux最多四个主分区，1 - 4，而拓展分区 5 - 16（12个）这是对MBR分区而言的，现在都是GPT分区了，主分区都无限了。**

**但这时我们分区的磁盘没有文件系统，可以使用 lsblk -f 查看。这个时候就需要通过命令给它增加文件系统。**

**mkfs -t xfs /dev/sdb1 : (Make File System) -t 指自定义类型，这里选择了xfs，然后路径选择了刚刚创建的分区。**

**别忘了给它配置挂载点，可以配置到某个用户的家目录（当然其实哪里都可以），挂载自然就有挂载点了，也给这个设备分配uuid了，哪怕卸载挂载点，uuid也在，但是挂载点不在了。**

**如果home目录下的这个文件夹挂在前有文件的话，挂载后这部分文件会被隐藏，这个文件夹就完全是新分区了，卸载分区后会显示出来。**

**还是得挂载到etc 不然重启下虚拟机挂载得就没了。**

## **7.10** **进程管理类** 

**进程是正在执行的一个程序或命令，每一个进程都是一个运行的实体，都有自己的地址空间，并占用一定的系统资源**。

### **7.10.1 ps** **查看当前系统进程状态**

**这种加 - 的选项，就是类unix格式，而不加的是 类bsd格式。**

**a的意思重在所有用户，x重在所有进程，二者结合用就是所有用户的所有进程**

***\*[P57结合实际案例，讲解了具体的细节信息，例如虚拟终端和图形化终端，还有权限分离](https://www.bilibili.com/video/BV1WY4y1H7d3/?p=57&spm_id_from=pageDriver&vd_source=da8c316450987e3173a62ba5ea9acd61)\****

**加了 a 或者是下面的 -e 会把和用户无关的也展示，如果仅仅是当前用户的，可以省略。什么都不加只会显示和当前终端相关的进程。**

![img](https://img-blog.csdnimg.cn/8dcba8d199d64bc2b972f78f2220ee63.png)![img](https://img-blog.csdnimg.cn/5169aa537ac44aa4b4dc1bf36e57f554.png)

### **7.10.2 kill** **终止进程**

**可以通过 kill -l 查看各种kill信号代表的含义**

![img](https://img-blog.csdnimg.cn/32d659440c6a49fa92c5148c7fc23976.png)

### **7.10.3 pstree** **查看进程树**

![img](https://img-blog.csdnimg.cn/10bc67ff675c450a8adf30e1561d341e.png)

### **7.10.4 top** **实时监控系统进程状态** 

**-i ：只要刷新时段内用过CPU的进程，都会显示，但这些进程可能在刷新时点又睡了**

![img](https://img-blog.csdnimg.cn/0ad61486595949d591069c8d5e5eb43b.png)![img](https://img-blog.csdnimg.cn/507ebef1717242eca51a3d64437f5d2d.png)![img](https://img-blog.csdnimg.cn/0bad504ae0cf4f0eba248883947afeee.png)

### **7.10.5 netstat** **显示网络状态和端口占用信息**

![img](https://img-blog.csdnimg.cn/b5066837680e429baf3ac99519959af1.png)![img](https://img-blog.csdnimg.cn/d52a5756be3a42feb68eb6ef3d796ef8.png)

## **7.11 crontab** **系统定时任务**

### **7.11.1 crontab** **服务管理**

**Ubuntu是cron，没有d**

![img](https://img-blog.csdnimg.cn/a5970da73afe45789058237420f752a6.png)

### **7.11.2 crontab** **定时任务设置**

![img](https://img-blog.csdnimg.cn/c8d66e5bec9b41ef8fcc978096590c1b.png)![img](https://img-blog.csdnimg.cn/a0fee95911e44de0ab62187808e10893.png)![img](https://img-blog.csdnimg.cn/7accf78e72494a10afefa00376d5eda9.png)



# **第** **8** **章 软件包管理** 

## **8.1 RPM** 

### **8.1.1 RPM** **概述**

![img](https://img-blog.csdnimg.cn/cb4b4136550f489e9d2ab6ada67a7b1e.png)

### **8.1.2 RPM** **查询命令**（rpm -qa）

**rpm -qi (查询详细信息)**

![img](https://img-blog.csdnimg.cn/d171142cec9e4efd96cff4f11076c947.png)

### **8.1.3 RPM** **卸载命令**（rpm -e）

![img](https://img-blog.csdnimg.cn/93ee7420cca6435aab218451ad29a7ca.png)![img](https://img-blog.csdnimg.cn/c6b7b1141f334155906a543e7e0ded26.png)

### **8.1.4 RPM** **安装命令**（rpm -ivh）

**这里卸载的火狐浏览器在光驱里面，我们找到它对应的位置，然后可以直接进行安装。**

![img](https://img-blog.csdnimg.cn/42a038b6f0804f649eb4cc08bed32115.png)

## **8.2 YUM** **仓库配置** 

### **8.2.1 YUM** **概述**

![img](https://img-blog.csdnimg.cn/7297b10c91c247c4a5d8b1128684e63b.png)

![img](https://img-blog.csdnimg.cn/9436e9f5b2854063853347547793ea8f.png)

### **8.2.2 YUM** **的常用命令** 

![img](https://img-blog.csdnimg.cn/0287422144db49f6bff2998f1a6da308.png)

### **8.2.3** **修改网络** **YUM** **源**

![img](https://img-blog.csdnimg.cn/a334005d4b9c48b9b5d24a41f6def83c.png)

![img](https://img-blog.csdnimg.cn/47de225223de4967b9b9f266e1704eaf.png)

![img](https://img-blog.csdnimg.cn/ddb745f803904060b3a04e20bcdac54a.png)

# **第** **9** **章 克隆虚拟机** 

## **9.1** **克隆** 

**1**）从现有虚拟机(关机状态)克隆出新虚拟机，右键选择管理=>克隆，如图 9-1

![img](https://img-blog.csdnimg.cn/c66e2d40fcd94e2e87777b144a56e139.png)

**2**) 点击下一步,如图 9-2 

![img](https://img-blog.csdnimg.cn/531d9313ce7e4181b8d6e02f231e4b84.png)

![img](https://img-blog.csdnimg.cn/7ab73f1d3bd14e3c877f955b7f1719f4.png)

![img](https://img-blog.csdnimg.cn/38c828697b4d4083ab0656f96660eb91.png)

## 9.2 **开机修改系统相关配置**

![img](https://img-blog.csdnimg.cn/1ec0448302c44da4a6f686ced4176444.png)

![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)![img](https://img-blog.csdnimg.cn/cc995535033447379afa1271d7c3743e.png)

# **第** **10** **章 常见错误及解决方案** 

**1**）虚拟化支持异常情况如下几种情况

![img](https://img-blog.csdnimg.cn/a85c2d5cdb1d4e7986cc545e198e3ec8.png)



![img](https://img-blog.csdnimg.cn/4c8ea36a6c3b40789092994e4178187a.png)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)

![img](https://img-blog.csdnimg.cn/79b988199f1a4b42ae0c03c26269f8d5.png)

# **第** **11** **章 企业真实面试题** 

## **11.1** 百度&考满分

问题：Linux 常用命令

> 参考答案：find、df、tar、ps、top、netstat 等。（尽量说一些高级命令） 

## **11.2** **瓜子二手车** 

 问题：Linux 查看内存、磁盘存储、io **读写**、端口占用、进程等命令

```sh
答案:
1、查看内存: top
2、查看磁盘存储情况:df -h
3、查看磁盘ro读写情况: iotop（需要安装一下: yum install iotop)、iotop -o(直接查看输出比较高的磁盘读写程序）
4、查看端口占用情况:netstat -tunlp | grep 端口号
5、查看进程:ps -aux
```
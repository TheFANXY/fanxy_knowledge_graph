# 第01章 Linux下MySQL的安装与使用

这里关于最基本的 `Mysql` 内容如增删改，视图，`ddl`，`dml`， `dql`， 这部分就不加赘述了，相信这部分大多数人应该学过了。没学过的可以看我的 `CSDN` 部分有基础 `mysql` 笔记，当然，有价值的章节日后也会移植到我的博客。比如关于相关查询，外连接，内连接等。

可能有些人会认为，`docker` 会了安装，这部分就不学了，这部分 `mysql` 的安装其实也是作为一个补充，如果 `docker` 会安装，不代表出现了问题就寻求的是 `docker` 的笔记，配置文件还是得靠学 `mysql` 才会配置，同时 `docker` 安装，比如怎么进入设置一些， `mysql` 连接用户的 `host` ，字符集等，是需要先学 `mysql` 懂了这些操作，才能用 `docker` 玩的轻松。   



# **1.** **安装前说明**

## **1.1** **查看是否安装过MySQL**

- 如果你是用rpm安装, 检查一下RPM PACKAGE：

```shell
rpm -qa | grep -i mysql # -i 忽略大小写
```

- 检查mysql service：

```shell
systemctl status mysqld.service
```



## 1.2 MySQL的卸载

**1.** **关闭** **mysql** **服务**

```shell
systemctl stop mysqld.service
```

**2.** **查看当前** **mysql** **安装状况**

```shell
rpm -qa | grep -i mysql
# 或
yum list installed | grep mysql
```

**3.** **卸载上述命令查询出的已安装程序**

```shell
yum remove mysql-xxx mysql-xxx mysql-xxx mysqk-xxxx
```

务必卸载干净，反复执行 `rpm -qa | grep -i mysql` 确认是否有卸载残留

**4.** **删除** **mysql** **相关文件**

- 查找相关文件

```shell
find / -name mysql
```

- 删除上述命令查找出的相关文件

```shell
rm -rf xxx
```

**5.删除 my.cnf**

```shell
rm -rf /etc/my.cnf
```



# 2. MySQL的Linux版安装

## 2.1 CentOS7下检查MySQL依赖 

**1.** **检查/tmp临时目录权限（必不可少）**

由于mysql安装过程中，会通过mysql用户在/tmp目录下新建tmp_db文件，所以请给/tmp较大的权限。执行 ：

```shell
chmod -R 777 /tmp
```

**2.** **安装前，检查依赖**

```shell
rpm -qa|grep libaio
rpm -qa|grep net-tools
```



## 2.2 CentOS7下MySQL安装过程 

**1.** **将安装程序拷贝到/opt目录下**

在mysql的安装文件目录下执行：（必须按照顺序执行）

```shell
rpm -ivh mysql-community-common-8.0.25-1.el7.x86_64.rpm 

rpm -ivh mysql-community-client-plugins-8.0.25-1.el7.x86_64.rpm 

rpm -ivh mysql-community-libs-8.0.25-1.el7.x86_64.rpm 

rpm -ivh mysql-community-client-8.0.25-1.el7.x86_64.rpm 

rpm -ivh mysql-community-server-8.0.25-1.el7.x86_64.rpm
```

- `rpm` 是Redhat Package Manage缩写，通过RPM的管理，用户可以把源代码包装成以rpm为扩展名的文件形式，易于安装。
- `-i`, --install 安装软件包
- `-v`, --verbose 提供更多的详细信息输出
- `-h`, --hash 软件包安装的时候列出哈希标记 (和 -v 一起使用效果更好)，展示进度条

> 若存在 `mariadb-libs` 问题，则执行 **`yum remove mysql-libs`** 即可



## 2.3 查看MySQL版本

```shell
mysql --version 
#或
mysqladmin --version
```



## 2.4 服务的初始化

为了保证数据库目录与文件的所有者为 mysql 登录用户，如果你是以 root 身份运行 mysql 服务，需要执行下面的命令初始化：

```shell
mysqld --initialize --user=mysql
```

说明： --initialize 选项默认以“安全”模式来初始化，则会为 root 用户生成一个密码并将`该密码标记为过期`，登录后你需要设置一个新的密码。生成的`临时密码`会往日志中记录一份。

查看密码：

```shell
cat /var/log/mysqld.log
```

root@localhost: 后面就是初始化的密码



## 2.5 启动MySQL，查看状态 

```shell
#加不加.service后缀都可以 
启动：systemctl start mysqld.service    
关闭：systemctl stop mysqld.service 
重启：systemctl restart mysqld.service 
查看状态：systemctl status mysqld.service
```



## 2.6 查看MySQL服务是否自启动

```shell
systemctl list-unit-files | grep mysqld.service
```

- 如不是enabled可以运行如下命令设置自启动

```shell
systemctl enable mysqld.service
```

- 如果希望不进行自启动，运行如下命令设置

```shell
systemctl disable mysqld.service
```



# 3. MySQL登录

## 3.1 首次登录

通过`mysql -hlocalhost -P3306 -uroot -p`进行登录，在Enter password：录入初始化密码



## 3.2 修改密码

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';
```



## 3.3 设置远程登录

**1.** **确认网络** 

1.在远程机器上使用ping ip地址`保证网络畅通`

2.在远程机器上使用telnet命令`保证端口号开放`访问

**2.** **关闭防火墙或开放端口**

**方式一：关闭防火墙**

- CentOS6 ：

```shell
service iptables stop
```

- CentOS7：

```shell
#开启防火墙
systemctl start firewalld.service
#查看防火墙状态
systemctl status firewalld.service
#关闭防火墙
systemctl stop firewalld.service
#设置开机启用防火墙 
systemctl enable firewalld.service 
#设置开机禁用防火墙 
systemctl disable firewalld.service
```

**方式二：开放端口**

- 查看开放的端口号

```shell
firewall-cmd --list-all
```

- 设置开放的端口号

```shell
firewall-cmd --add-service=http --permanent
firewall-cmd --add-port=3306/tcp --permanent
```

- 重启防火墙

```shell
firewall-cmd --reload
```



# 4. Linux下修改配置

- 修改允许远程登陆

```mysql
use mysql;
select Host,User from user;
update user set host = '%' where user ='root';
flush privileges;
```

> `%`是个 通配符 ，如果Host=192.168.1.%，那么就表示只要是IP地址前缀为“192.168.1.”的客户端都可以连接。如果`Host=%`，表示所有IP都有连接权限。
>
> 注意：在生产环境下不能为了省事将host设置为%，这样做会存在安全问题，具体的设置可以根据生产环境的IP进行设置。

配置新连接报错：错误号码 2058，分析是 mysql 密码加密方法变了。

**解决方法一：**升级远程连接工具版本

**解决方法二：**

```mysql
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'abc123';
```



# 5. 字符集的相关操作

## 5.1 各级别的字符集

```mysql
show variables like 'character%';
```

- character_set_server：服务器级别的字符集
- character_set_database：当前数据库的字符集
- character_set_client：服务器解码请求时使用的字符集
- character_set_connection：服务器处理请求时会把请求字符串从character_set_client转为character_set_connection 
- character_set_results：服务器向客户端返回数据时使用的字符集



## 5.2 mysql5.7改字符集

```sh
vim /etc/my.cnf
```

在mysql5.7或之前的版本中，在文件最后加上中文字符集的配置：

```sh
character_set_server=utf8
```



## 5.3 已有库或表字符集的变更

```sql
alter database 【库名】 character set 'utf8';
```

```sql
alter table 【表名】 convert to character set 'utf8';
```

**小结**

- 如果`创建或修改列`时没有显式的指定字符集和比较规则，则该列`默认用表的`字符集和比较规则
- 如果`创建表时`没有显式的指定字符集和比较规则，则该表`默认用数据库的`字符集和比较规则
- 如果`创建数据库时`没有显式的指定字符集和比较规则，则该数据库`默认用服务器的`字符集和比较规则



## 5.4 请求到响应过程中字符集的变化

```mermaid
graph TB
A(客户端) --> |"使用操作系统的字符集编码请求字符串"| B(从character_set_client转换为character_set_connection)
B --> C(从character_set_connection转换为具体的列使用的字符集)
C --> D(将查询结果从具体的列上使用的字符集转换为character_set_results)
D --> |"使用操作系统的字符集解码响应的字符串"| A
```



# 6. Mysql大小写设置

## 6.1. window

Mysql在Windows环境下全部不区分大小写。



## 6.2. Linux大小写规则设置

Linux如表名，数据库名，列的别名，严格区分大小写，想设置为大小写不敏感的时候，要在`my.cnf`这个配置文件`[mysqld]`中加入`lower_case_table_names=1`，然后重启服务器。

- 但是要在重启数据库实例前就需要将原来的数据库和表转换为小写，否则找不到数据库名。
- 但是Mysql8禁止在重新启动的时候把这个值更改的操作。如果非要这么操作，可以：

> 1 停止mysql服务
>
> 2 删除数据目录 即删除 /var/lib/mysql 目录
>
> 3 在Mysql配置文件 ( /etc/my.cnf ) 中添加 `llower_case_table_names=1`
>
> 4 启动Mysql服务

在进行数据库设置之前，需要掌握这个参数带来的影响，切不可盲目设置



## 6.3. SQL编写建议

>1. 关键字和函数名称全部大写
>2. 数据库名，表名，表别名，字段名，字段别名等全部小写
>3. SQL语句必须以分号结尾

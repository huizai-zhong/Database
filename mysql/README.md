# Mysql Study Notes

## Origin and development(起源与发展)

-   **应用最广泛的开源数据库软件**
    -   最早隶属于瑞典的MySQL AB公司
    -   2008年1月，MySQL AB被sun收购
    -   2009年4月，SUN被Oracle收购
-   **崭新的开源分支MariaDB**
    -   为应付MySQL可能会闭源的风险而诞生
    -   由MySQL原作者Widenius主导开发
    -   与MySQL保持最大程度兼容

## Features and applications(特点及应用)

-   **主要特点**
    -   适用于中小规模，关系型数据库系统
    -   支持Linux，Unix，Windows等多种操作系统
    -   支持Python、Java、Perl、PHP等编程语言
-   典型应用环境
    -   LAMP平台，与Apache HTTP Server组合
    -   LNMP平台，与Nginx组合

## 利用Docker搭建MySQL服务器

### 1. 从官网获取mysql镜像

```shell
$ docker pull mysql:5.7 #获取5.7版本的mysql镜像
$ dokcer pull mysql  #获取最新版本的mysql镜像
```

### 2. Create MySQL container

```shell
$ docker run -p 3306:3306 --name mysql_zlh -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

-   --name : container name
-   -e : Configuration information, configuration the login password for the root of mysql
-   -p : Port mapping, where port 3306 of the host is mapped to port 3306 of the container
-   -d : Source image name

### 3. Enter the container

```shell
$ docker exec -it mysql_zlh /bin/bash
```

### 4. Enter the MySQL

```shell
$ mysql -uroot -p12345
```

## 利用官网的rpm包搭建MySQL服务器

### 1. 下载解压(Download decompression)

```shell
[root@host50 ~]# tar -xvf mysql-5.7.17.tar               //解压mysql整合包
./mysql-community-client-5.7.17-1.el7.x86_64.rpm #客户端应用程序
./mysql-community-common-5.7.17-1.el7.x86_64.rpm #数据库和客户端库共享文件
./mysql-community-devel-5.7.17-1.el7.x86_64.rpm  #客户端应用程序的库和头文件
./mysql-community-embedded-5.7.17-1.el7.x86_64.rpm #嵌入式函数库
./mysql-community-embedded-compat-5.7.17-1.el7.x86_64.rpm #嵌入式兼容函数库
./mysql-community-embedded-devel-5.7.17-1.el7.x86_64.rpm #头文件和库文件作为MySQL的嵌入式文件
./mysql-community-libs-5.7.17-1.el7.x86_64.rpm #MySQL 数据库客户端应用程序的共享库
./mysql-community-libs-compat-5.7.17-1.el7.x86_64.rpm  #客户端应用库的共享兼容库
./mysql-community-minimal-debuginfo-5.7.17-1.el7.x86_64.rpm
./mysql-community-server-5.7.17-1.el7.x86_64.rpm
./mysql-community-test-5.7.17-1.el7.x86_64.rpm
```

### 2. 安装(Installation)

-   yum 安装，自动解决软件依赖

```shell
$ yum -y install mysql-community-*.rpm
$ rpm -qa | grep -i mysql #查看
```

### 3. 启动服务(Start the service)

```shell
$ systemctl start mysqld #Start
$ systemctl enable mysqld #设置开机自启
$ systemctl status mysqld #查看状态 Check the status
```

### 4. 相关参数(Related parameters)

|  文件(File)  |    说明(explain)    |
| :----------: | :-----------------: |
|  主配置文件  |     /etc/my.cnf     |
|  数据库目录  |   /var/lib/mysql    |
|  默认端口号  |        3306         |
|    进程名    |       mysqld        |
|   传输协议   |         TCP         |
|  进程所有者  |        mysql        |
|  进程所属组  |        mysql        |
| 错误日志文件 | /var/log/mysqld.log |

## The initial configuration(初始配置)

-   **修改root密码**

```mysql
> alter user root@"localhost" indentified by "密码"
```

-   **修改密码策略**

|       策略名称       |                验证方式                |
| :------------------: | :------------------------------------: |
|       0 or LOW       |                  长度                  |
| 1 or MEDIUM(DEFAULT) |      长度；数字，大小写，特殊字符      |
|     2 or STRONG      | 长度；数字，大小写，特殊字符；字典文件 |

```mysql
> show variables like "%password%"; #查看变量
> set global validate_password_policy=0; #修改密码策略
> set global validate_password_length=6; #修改密码长度
# 永久配置
$ vim /etc/my.cnf
[mysqld]
validate_password_policy=0
validate_password_length=6
```

## 基本操作(Basic operation)

### 1. 连接MySQL服务

-   **连接方式**

    -   客户端连接MySQL服务的方法

        -   命令行

        -   web界面

        -   安装图形软件

        -   编写脚本

    -   使用MySQL命令

```shell
$ mysql -h服务器IP -u用户名 -p密码 [数据库名]
mysql> quit or exit #退出
```

### 2. SQL命令

-   **使用规则**
    -   SQL命令不区分字母大小写（密码，变量值除外）
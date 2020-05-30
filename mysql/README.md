### Mysql Study Notes

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
    -   每条SQL命令以；结束
    -   默认命令不支持tab补全
    -   \c 终止sql命令
-   常用的SQL命令分类
    -   管理数据库使用SQL（结构化查询语言）
        -   DDL 数据定义语言 如：create alter drop
        -   DML 数据操作语言 如：insert update delete
        -   DCL 数据控制语言 如：grant revoke
        -   DTL 数据事务语言 如：commit rollback savepoint

### 3. 库管理命令

-   **类似于文件夹，用来存储表**
-   **可以创建多个库，通过库名区分**
-   **库名的命名规则**
    -   仅可以使用数字、字母、下划线，不能纯数字
    -   区分字母大小写，具有唯一性
    -   不可以使用关键字，特殊字符
-   **常用命令**

```mysql
> show databases;	#显示已有的库
> select user();	#显示连接用户
> use 库名;			#切换库
> select database();	#显示当前所在库
> create database 库名;	#创建新库
> show tables;			#显示已有的表
> drop database 库名;		#删除库
```

### 4. 表管理命令

-   **存储数据的文件**
-   **基本命令**

```mysql
# 建表
> create table 库名.表名(
>		字段名1 类型（宽度）,
>		字段名2 类型（宽度）,
>		...
>)DEFAULT CHARSET=utf8; #指定中文字符集，可以给字段赋值中文

# 查看表结构
> desc 库名.表名;

# 删除表
> drop table 库名.表名;
```

### 5. 记录管理命令

-   **类似文件里的行**
-   **常用命令**

```mysql
# 查看表记录
> select * from 库名.表名;
# 插入记录
> insert into 库名.表名 values(值列表);
# 修改记录
> update 库名.表名 set 字段=值;
# 删除表记录
> delete from 库名.表名;
```

## MySQL数据类型（data type）

-   **常见的信息种类**
    -   数值型：体重、升高、成绩、工资
    -   字符型：姓名、工作单位、通信住址
    -   枚举型：兴趣爱好、性别、专业
    -   日期时间型：出生日期、注册时间

### 1. 字符型

-   **定长char**
    -   最大字符个数255
    -   不能够指定字符串个数时左右两边用空格补全
    -   字符个数超出时，无法写入数据

-   **变长varchar**
    -   按数据实际大小分配存储空间
    -   字符个数超出时，无法写入数据

-   **大文本类型**
    -   text/blob
    -   字符数大于65535存储时使用

### 2. 数值型

-   **整数型**

|   类型    |        名称        |      有符号范围       |  无符号范围  |
| :-------: | :----------------: | :-------------------: | :----------: |
|  tinyint  |      微小整数      |       -128~127        |    0~255     |
| samllint  |       小整数       |     -32768~32767      |   0~65535    |
| mediumint |       中整数       | $-2^{23}$~$2^{23}-1$  | 0~$2^{24}-1$ |
|    int    |       大整数       | $-2^{31}$~$2^{31}-1$  | 0~$2^{32}-1$ |
|  bigint   |      极大整数      | $-2^{63}$~$-2^{63}-1$ | 0~$2^{64}-1$ |
| Unsigned  | 使用无符号存储范围 |                       |              |

-   **浮点型**
    -   格式1：字段名 类型;
    -   格式2：字段名 类型（总宽度，小数位数）;

|  类型  |  名称  |                       有符号范围                        |                      无符号范围                       |
| :----: | :----: | :-----------------------------------------------------: | :---------------------------------------------------: |
| float  | 单精度 |            -3.402823466E+38~-1.175494351E-38            |            1.175494351E-38~3.402823466E+38            |
| double | 双精度 | -1.7976931348623157E+308~<br />-2.2250738585072014E-308 | 2.2250738585072014E-308~<br />1.7976931348623157E+308 |

### 3. 日期时间类型

|   类型    |                   范围                    |      格式      |
| :-------: | :---------------------------------------: | :------------: |
| datetime  | 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59 | yyyymmddhhmmss |
| timestamp | 1970-01-01 00:00:00 ~ 2038-01-19 00:00:00 | yyyymmddhhmmss |
|   date    |          0001-01-01 ~ 9999-12-31          |    yyyymmdd    |
|   year    |                1901 ~ 2155                |      yyyy      |
|   time    |                                           |    HH:MM:SS    |

-   当未给timestamp字段赋值时，自动以当前系统时间赋值，而datetime值为NULL
-   year类型，要求使用4位数赋值，当使用两位数赋值时
    -   01~69 视为 2001~2069
    -   70~99 视为 1970~1999

-   **时间函数**

|   函数    |           用途           |
| :-------: | :----------------------: |
| curtime() |    获取当前的系统时间    |
| curdate() |    获取当前的系统日期    |
|   now()   | 获取当前的系统时间和日期 |
|  year()   |          获取年          |
|  month()  |          获取月          |
|   day()   |          获取日          |
|  date()   |         获取日期         |
|  time()   |         获取时间         |

### 4. 枚举类型

-   **enum 单选**
    -   格式：字段名	enum(值1，值2，值N)
    -   仅能选择一个值
    -   字段值必须在列表里选择

```mysql
> create table db1.test_enum(
    name char(5),
    sex	enum("boy", "girl", "no")
);

> insert into db1.test_enum values("bob","boy");
```

-   **set 多选**
    -   格式：字段名	enum(值1，值2，值N)
    -   选择一个或多个值
    -   字段值必须在列表里选择

```mysql
> create table db1.test_set(
    name char(5),
    likes set("eat", "game", "music", "money")
);

> insert into db1.test_set values("bob", "eat,music");
```

## 约束条件

### 1. 查看约束条件

```shell
> desc 库名.表名;
字段名|类型|空|键值|默认值|额外设置|
```

### 2. 设置约束条件

-   null	允许为空（默认设置）
-   not null    不允许为空
-   key   键值类型
-   default   设置默认值，缺省为NULL
-   extra    额外设置

## 修改表结构

### 1.语法结构

```shell
mysql>alter table 库名.表名 执行动作;
执行动作：
	add 添加字段
	modify 修改字段类型
	change 修改字段名
	drop 删除字段
	rename 修改表名
```

### 2.添加新字段

-   用法
    -   新字段默认添加到字段末尾

```shell
mysql> alter table db1.t2
	-> add
	-> name char(15) first;
```

### 3.修改字段类型

-   用法
    -   修改字段类型不能与已存储的数据冲突

```shell
mysql> alter table 库名.表名
	 > modify 字段类型 类型(宽度) 约束条件
	 > [after 字段名 | first]
```

### 4.修改字段名称

-   基本用法
    -   也可以用来修改字段类型

```shell
mysql> alter table 库名.表名
	   change 源字段名	新字段名	类型(宽度)
	   约束条件;   --当更新类型和约束条件时，可修改字段类型
```

### 5.删除字段

-   基本用法
    -   表中有多条记录时，所有列的此字段值都会被删除

```shell
mysql> alter table db1.t2 drop user_num, drop pay;
```

### 6.修改表名

-   基本用法
    -   表对应的文件名，也被改变
    -   表记录不受影响

```sql
mysql> alter table t2 rename school;
```

## 键值概述




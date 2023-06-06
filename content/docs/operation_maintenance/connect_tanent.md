---
title: 连接租户
weight: 6
---


OceanBase 开源版的租户只兼容 MySQL，连接协议兼容 MySQL 5.6 。因此 MySQL 命令行客户端或者图形化工具理论上也能连接 OceanBase 数据库的租户。此外，OceanBase 数据库也提供专属的命令行客户端工具 OBClient 和图形化客户端工具 ODC。
## MySQL 客户端连接
OceanBase MySQL 租户支持传统 MySQL 客户端连接，连接方式基本不变，跟传统 MySQL 不一样的地方是用户名的格式。
示例：
```bash
mysql -hxxx -uroot@sys#obdemo -P2883 -p4S****Sr -c -A oceanbase
```
说明：

- -h：提供 OceanBase 数据库连接 IP，通常是一个 OBProxy 地址。
- -u：提供租户的连接账户，格式有两种：用户名@租户名#集群名 或者 集群名:租户名:用户名 。MySQL 租户的管理员用户名默认是 root。
- -P：提供 OceanBase 数据库连接端口，也就是 OBProxy 的监听端口，默认是 2883，可以自定义。如果使用 2881 也就是 OBServer 的连接端口，那么连接的用户不能带集群名，即去掉 #集群名。
- -p：提供账户密码，为了安全可以不提供，改为在后面提示符下输入，密码文本不可见。
- -c：表示在 MySQL 运行环境中不要忽略注释。
- -A：表示在 MySQL 连接数据库时不自动获取统计信息。
- oceanbase：访问的数据库名，可以改为业务数据库。

新创建的业务租户的管理员（root）密码默认为空，需要修改密码。
```bash
$ strings /dev/urandom |tr -dc A-Za-z0-9 | head -c8; echo
b******t

mysql -hxxx -uroot@obmysql#obdemo -P2883 -p -c -A oceanbase

MySQL [oceanbase]> alter user root identified by 'b******t' ;
Query OK, 0 rows affected (0.118 sec)
```
如果没有安装 MySQL 客户端，可以安装 mariadb-server。MySQL 官方 8.0 的客户端连接协议在密码处调整了逻辑，导致无法通过早期的 OBProxy 连接到 OceanBase 的 MySQL 租户，会报密码错误。可以通过选项 --default-auth=mysql_native_password 解决这个问题。
```bash
# 安装 mariadb
sudo yum -y install mariadb-server.x86_64

# 或者
# 安装官方 mysql 客户端
sudo yum -y install mysql.x86_64

mysql -hxxx -uroot@obmysql#obdemo -P2883 -pbJ****Vt -c -A --default-auth=mysql_native_password  oceanbase
```
#### 说明
OBProxy 2.0 版本已经修复了这个问题。
## OBClient 客户端连接
OceanBase 数据库提供了专用的命令行客户端工具 obclient。使用方法和使用 MySQL 客户端一样。
示例：
```bash
obclient -hxxx -uroot@obmysql#obdemo -P2883 -pbJ****Vt -c -A oceanbase
```
## OceanBase 连接驱动（JDBC）
OceanBase 数据库目前支持的应用主要是 Java 和 C/C++ 。

- Java 语言MySQL 官方 JDBC 驱动下载地址：[MySQL Connector/J 5.1.46](https://downloads.mysql.com/archives/c-j/)OceanBase 官方 JDBC 驱动下载地址：[OceanBase-Client](https://help.aliyun.com/document_detail/212815.html)
- C 语言具体驱动说明请参考官网文档：[OceanBase Connector/C 简介](https://www.oceanbase.com/docs/community-connector-c-cn-10000000000017244)下载地址：[OceanBase Connector/C 下载](https://github.com/oceanbase/obconnector-c)
## DBEAVER 客户端连接
DBeaver 是一款通用的数据库客户端工具，其原理是使用各个数据库提供的 JDBC 驱动连接数据库，支持常见的关系型数据库、非关系型数据库、分布式数据库等等。使用 OceanBase 提供的 JDBC 驱动或者 MySQL 官方驱动，DBeaver 也可以连接 OceanBase 数据库的 MySQL 租户。
官方下载地址：[https://dbeaver.io/download/](https://dbeaver.io/download/)
DBeaver 连接 OceanBase 数据库的时候选择 MySQL 数据库类型。第一次使用会自动下载官方 MySQL 驱动。与 MySQL 不同， DBeaver 连接端口默认是 2883，用户名格式是：用户名@租户名#集群名 或 集群名:租户名:用户名。
## ODC 客户端连接
OceanBase 提供官方图形化客户端工具 OceanBase Developer Center，简称 ODC。该工具的详细信息请参考官网文档 [OceanBase 开发者中心](https://www.oceanbase.com/docs/enterprise-odc-doc-cn-10000000000833893) 。
ODC 下载地址：[下载客户端版 ODC](https://help.aliyun.com/document_detail/212816.html?spm=a2c4g.11186623.6.848.2cb5535fzdJK9X) 。
目前 ODC 是对 OceanBase 数据库适配性最好的客户端工具。


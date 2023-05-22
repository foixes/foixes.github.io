---
title: 白屏部署
weight: 2
---
# OCP白屏部署

## 推荐使用OCP白屏部署

# 创建表

本文主要介绍了创建表相关的一些要求及示例。

## 表简介

数据库表是一系列二维数组的集合，用来代表和储存数据对象之间的关系。从数据的分布上，表可以分为非分区表和分区表：

* 分区表有多个分区。分区表在创建时，至少要指定一级分区列和相关分区信息。

* 非分区表只有一个分区。

更多表的介绍及说明，请参见 [表概述](../../../7.reference/1.oceanbase-database-concepts/4.database-objects/1.database-objects-of-oracle-mode/2.table-of-oracle-mode/1.table-overview-of-oracle-mode.md)。

## 表创建准备

在创建表前，您需要确认以下事项：

* 请确认您已连接到数据库的 Oracle 租户，连接数据库的操作请参见 [连接数据库](../1.database-connection-of-oracle-mode/1.connection-methods-overview-of-oracle-mode.md)。

  <main id="notice" type='explain'>
   <h4>说明</h4>
   <p>当前登录租户所属的租户模式可以由 <code>sys</code> 租户通过查询 <code>oceanbase.DBA_OB_TENANTS</code> 视图进行确认。 </p>
  </main>

* 请确认您已拥有 `CREATE TABLE` 权限，查看当前用户权限的相关操作请参见 [查看用户权限](../../../7.reference/2.administrator-guide/2.basic-database-management/4.manage-tenants/5.manage-users-and-permissions/2.oracle-mode/4.view-user-permissions-of-oracle-mode.md)。如果不具备该权限，请联系管理员为您授权，用户授权的相关操作请>参见 [修改用户权限](../../../7.reference/2.administrator-guide/2.basic-database-management/4.manage-tenants/5.manage-users-and-permissions/2.oracle-mode/5.modify-user-permissions-of-oracle-mode.md)。

## 创建表

请使用 [CREATE TABLE](../../../7.reference/4.development-reference/1.sql-syntax/3.common-tenant-of-oracle-mode/9.sql-statement-of-oracle-mode/1.ddl-of-oracle-mode/24.create-table-of-oracle-mode.md) 语句创建表，并参考以下内容完成表的创建。

### 定义表名


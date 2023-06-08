---
title: 常用操作
weight: 1
---

# RootService 切主

```sql
-- 操作
ALTER SYSTEM SWITCH REPLICA leader LS=1 SERVER='目标ip:rpc_port' TENANT ='sys';
-- 实例
ALTER SYSTEM SWITCH REPLICA leader LS=1 SERVER ='x.x.x.x:2882' TENANT ='sys'
```

# 清理租户并释放空间
先删除租户drop tenant xxx force，再删除对应的资源池drop resource pool xxx，才能释放
注意：该操作会清理租户下的所有数据。

# 开启慢查询
租户参数 trace_log_slow_query_watermark，默认超过1s就会记录，记录在observer.log日志，或ocp白屏监控有记录

# 普通用户租户查看自己租户下所有的表

```sql
-- 表类型
select distinct(OBJECT_TYPE) from DBA_OBJECTS;  
-- 根据类型查表名
select OBJECT_NAME from DBA_OBJECTS where OBJECT_TYPE='table' ;
```

# obd cluster list 显示destroyed销毁信息怎么清理
cd  ~/.obd/cluster，删除对应的Name目录

# 日志打印相关

**调整日志文件以及记录数量**

```sql
-- 开启日志回收
alter system set enable_syslog_recycle=true
-- 关闭wf日志打印
alter system set enable_syslog_wf=false
-- 限制日志个数，按需调整(单个日志256M)
alter system set max_syslog_file_count=10
-- 设置系统日志级别
syslog_level (DEBUG/TRACE/INFO/WARN/USER_ERR/ERROR)
```

# 磁盘预占用相关
数据磁盘：datafile_size / datafile_disk_percentage
日志磁盘：log_disk_size / log_disk_percentage

# SQL 并行执行

**1）增加并行加快索引创建**

```sql
-- 增加SQL执行内存百分比
SET GLOBAL OB_SQL_WORK_AREA_PERCENTAGE = 30; 
-- 设置DDL并行度
SET SESSION _FORCE_PARALLEL_DDL_DOP = 32;
-- 并行度增加需要设置该参数，比所有并行度和大即可
SET GLOBAL PARALLEL_SERVERS_TARGET = 64;
```

# OBProxy日志打印相关

**系统租户或者root@proxysys连接集群修改**

```sql
show proxyconfig like '%log_file_percentage%';
alter proxyconfig set log_file_percentage=75;
```

以下是obproxy日志相关参数

| 参数 | 默认值 | 取值范围 | 解释 |
| --- | --- | --- | --- |
| **log_file_percentage** | 80 | [0, 100] | ODP 日志百分比阈值。超过阈值即进行日志清理。 |
| **log_dir_size_threshold** | 64GB | [256MB, 1T] | ODP 日志大小阈值。超过阈值即进行日志清理。 |
| **max_log_file_size** | 256MB | [1MB, 1G] | 单个日志文件的最大尺寸。 |
| **syslog_level** | INFO | DEBUG, TRACE, INFO, WARN, USER_ERR, ERROR | 日志级别。 |


# 持续更新中，敬请期待...


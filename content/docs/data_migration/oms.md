---
title: OMS
weight: 1
---

# 适用版本
**数据迁移和数据同步功能中，OceanBase 社区版和其它数据终端的适用版本如下**

| 分类 | 数据迁移 | 数据同步 |
| --- | --- | --- |
| OceanBase 社区版 | V3.1.0-CE、V3.1.1-CE、V3.1.2-CE、V3.1.3-CE、V3.1.4-CE、V4.0.0-CE、V4.1.0-CE | V3.1.0-CE、V3.1.1-CE、V3.1.2-CE、V3.1.3-CE、V3.1.4-CE |
| 其它数据终端 | - MySQL：V5.5、V5.6、V5.7、V8.0<br> - MariaDB：V10.2<br> - TiDB：4.x、5.x<br> - PostgreSQL：10.x |- Kafka：V0.9、V1.0、V2.x<br> - RocketMQ：V4.7.1 |


# **目前支持的数据源和目标端**
其中，OceanBase 4.x 版本只能作为 MySQL 的目标端，其他的暂不支持。

| 数据源 | 目标端 | 数据迁移 | 数据同步 |
| --- | --- | --- | --- |
| MySQL | OceanBase | 支持 | 支持 |
| TiDB | OceanBase | 支持 | 支持 |
| PostgreSQL | OceanBase | 支持 | 支持 |
| OceanBase | OceanBase | 支持 | 支持 |
| OceanBase | MySQL | 支持 | 支持 |
| OceanBase | Kafka | \\ | 支持 |
| OceanBase | RocketMQ | \\ | 支持 |

# TiDB 迁移到 OceanBase 需要注意的点
OMS 要做 TiDB 增量数据迁移的话，需要先为 TiDB 集群部署 TiCDC，并且把数据写到 kafka，之后在 OMS 创建数据源，先创建 kafka 数据源，再创建 TiDB 数据源。
其中 TiDB 数据源里需要包含刚创建的那个 kafka 数据源，这样在创建迁移项目的时候才能选择增量同步

# 详情
详细操作步骤可以参照官网: https://www.oceanbase.com/docs/community-oms-cn-10000000002131097

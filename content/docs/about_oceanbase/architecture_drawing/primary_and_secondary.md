---
title: 主副分区和节点关系
weight: 1
author: LiMei
---
# 主副分区和节点关系

OceanBase 数据库支持部署在 x86_64 以及 ARM_64 架构的物理服务器和主流的虚拟机。 OceanBase 数据库支持单副本部署，并且单副本 OceanBase 集群还可以扩容（增加节点），所
以也称为集群。本节主要介绍在普通的物理服务器上通过使用 RPM 包安装单副本 OceanBase 集群。

有关 OceanBase 集群部署的详细信息，请参见 [部署简介](../../4.deploy/1.deploy-overview.md)。

## 服务器和介质准备

1. 服务器架构及 Linux 操作系统版本要求。

   支持在下表所示的 Linux 操作系统中安装 OceanBase 数据库。

   |             Linux操作系统         |    版本     | 服务器架构|
   |-----------------------------------|-----------| ----- |
   | AliOS                             | 7.2 及以上   | x86_64（包括海光），ARM_64（仅支持鲲鹏、飞腾）|
   | CentOS / Red Hat Enterprise Linux | 7.2 及以上   | x86_64（包括海光），ARM_64（仅支持鲲鹏、飞腾）|
   | SUSE Enterprise Linux             | 12SP3 及以上 | x86_64（包括海光），ARM_64（仅支持鲲鹏、飞腾）|
   | Debian / Ubuntu                   | 8.3 及以上   | x86_64（包括海光），ARM_64（仅支持鲲鹏、飞腾）|
   | KylinOS                           | V10         | x86_64（包括海光），ARM_64（仅支持鲲鹏、飞腾）|

2. 服务器配置。

   服务器配置至少是 CPU 4 Core，内存 16 GB。

3. 安装包准备。

   准备 OceanBase 数据库独立版（Antman）RPM 包、OceanBase 4.0 RPM 包和 OBClient RPM 包。

   |文件名|版本|说明|
   |---|---|---|
   |t-oceanbase-*.rpm|最新版本|为 OBServer 节点提供标准化操作系统配置的能力，请联系技术支持获取最新 Antman 数据库安装包。|
   |oceanbase-*.rpm|4.0.0.0|OceanBase 软件 RPM 文件，部署 OceanBase 集群时用。|
   |obclient-*.rpm|1.2.6|命令行客户端 OBClient 的 RPM 文件，连接 ORACLE 实例必需有。|

   <main id="notice" type='explain'>
   <h4>说明</h4>
   <p>请联系技术支持获取对应安装包。</p>
   </main>


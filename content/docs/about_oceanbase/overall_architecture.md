---
title: 整体架构
weight: 2
---

OceanBase 集群默认是 n-n-n 的三副本架构，代表三个可用区（Zone），每个 Zone 内都有 n 个节点。

像下面的架构图，就是 2-2-2 的集群架构，每个 Zone 内有两个节点，默认数据分片是三副本。应用访问默认通过 OBProxy 进行连接，OBProxy 会解析 SQL 内容，自动将请求下发到对应的 OBServer 并返回结果。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35913569/1685937588603-3c0e8694-ca32-4a3c-a8e3-373834e30105.png?x-oss-process=image%2Fresize%2Cw_1135%2Climit_0)

# 名词定义

**Zone**

集群的可用区，不同的可用区可以在同机房，也可以在不同机房；每个 Zone 内的服务器也同样，可以在同机房也可以不同机房，不过建议同机房，并且建议 Zone 之间的网络延迟不要太高。

**Server**

通常代表 OBServer，OB 的数据库服务，每个 Zone 内有 1-n 个 Server。

**Unit**

资源单元，OceanBase 按照 Unit 来管理物理资源，是 CPU、内存、存储空间、IOPS 等物理资源的集合。

**Resource Pool**

资源池，每个 Unit 都归属于一个资源池，每个资源池由若干个 Unit 组成，资源池是资源分配的基本单位，同一个资源池内的各个 Unit 具有相同的资源规格，即该资源池内 Unit 的物理资源大小都相同。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/35913569/1685937607302-6c7acf7c-ceed-4ab9-9273-d5262461435a.png)
如上图，展示了一个由 6 个 Unit 组成的资源池 a_pool，该资源池具有如下重要属性：

- ZONE_LIST：描述了该资源池中的 Unit 分布在哪些 Zone，本例为 ZONE_LIST='zone1,zone2,zone3'。
- Unit_NUM：描述了 ZONE_LIST 中每个 Zone 中的 Unit 个数，本例为 Unit_NUM=2。
- Unit_CONFIG_ID：描述了该资源池关联的资源规格，从而决定该资源池中每个 Unit 的物理资源大小，包括 CPU、内存、日志盘空间、IOPS 等。

**Tenant**

租户，类似于传统数据库的数据库实例，租户通过资源池与资源关联，从而独占一定的资源配额，可以动态调整资源配额。在租户下可以创建 Database、表、用户等数据库对象。默认创建 sys 租户，业务使用或者压测建议创建自己的业务租户，因为 sys 租户会做系统管理等工作，业务使用会对性能产生影响。

- 创建租户必须先创建 Unit 和 Resource Pool，或者使用已有的。创建顺序 Unit -> Resource Pool -> Tenant。

**Tablet**

数据分片，存储层以一张表或者一个分区为粒度提供数据存储与访问，每个分区对应一个用于存储数据的Tablet，用户定义的非分区表也会对应一个 Tablet，每个分片的副本数量有 Locality 定义。

**Locality:**

描述的对象是承载数据的容器，定义副本类型以及副本数量。

**副本**

指的是实际的分片数据，单分片最大的副本数量不会超过 Zone 的数量。

![image.png](https://cdn.nlark.com/yuque/0/2023/png/35913569/1685937623886-e84d5965-529a-461a-8f14-478b266debbe.png?x-oss-process=image%2Fresize%2Cw_1500%2Climit_0)

更多的系统架构介绍可以参考官网：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001687855](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001687855)


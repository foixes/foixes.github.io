---
title: 常用参数配置
weight: 1
---
<!-- 这个看着和配置文件介绍那一篇文档内容重合了，可以合为一篇的吧 -->

# OBServer 关键配置
除了快速安装流程，一般都需要手动修改.yaml文件进行配置。ob可配置的参数很多，本文重点关注性能相关的参数，详细信息参考前面的部署文档。暂时只考虑observer的参数（也叫配置项）。

查询配置项：show parameters where name = 'xxx';

查询server关键参数：select * from GV$OB_SERVERS;

- **memory_limit**

参数含义：observer内存上限，优先于memory_limit_percentage配置项

默认值：0

推荐配置：系统总内存的80%~90%

动态修改：alter system set memory_limit='32G';

查询：show parameters where name = 'memory_limit';

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001700950](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001700950)

- **memory_limit_percentage**

参数含义：observer内存占系统内存的百分比（memory_limit为0时该配置项才生效）

默认值：80

推荐配置：80~90

动态修改：alter system set memory_limit_percentage=80;

查询：show parameters where name = 'memory_limit_percentage';

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699328](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699328)

- **system_memory**

参数含义：系统租户占用的内存，包含在 memory_limit 里面，未配置的情况下会自适应设置。

默认值：企业版：30G 社区版：0M

推荐配置：memory_limit的10%

动态修改：alter system set system_memory='2G';

查询：show parameters where name = 'system_memory';

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702104](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702104)

- **cpu_count**

参数含义：observer可用cpu总数

默认值：0（系统将会自动检测cpu数量）

推荐配置：系统cpu总数（通过lscpu查看）

动态修改：alter system set cpu_count=24;

查询：show parameters where name = 'cpu_count';

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702089](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702089)

- **net_thread_count**

参数含义：网络线程数量

默认值：0（RPC 和 MySQL 的 Libeasy 的 I/O 线程各为 max(6, CPU_NUM/8) ）

推荐配置：CPU 总数的 1/8，如果事务耗时较少，相对来说网络开销较大，可以设置为CPU 总数的20%

该配置项只能在部署observer时静态设置，不能动态修改。

查询：show parameters where name = 'net_thread_count';

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699335](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699335)

- **sql_net_thread_count**

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702138](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702138)

- **datafile_size**

参数含义：数据文件的总大小，优先于datafile_disk_percentage配置项

默认值：0

推荐配置：与日志共用磁盘时设为磁盘空间的60%，独占磁盘时设为磁盘空间的90%

动态修改：alter system set datafile_size='80G';

查询：show parameters where name = 'datafile_size';

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702095](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702095)

- **datafile_disk_percentage**

参数含义：数据文件占用其所在磁盘总空间的百分比

默认值：0

推荐配置：与日志共用磁盘时设为60，独占磁盘时设为90

动态修改：alter system set datafile_disk_percentage=60;

查询：show parameters where name = 'datafile_disk_percentage';

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702094](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702094)

- **log_disk_size**

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702124](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702124)

- **log_disk_percentage**

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702125](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001702125)


# 租户关键配置

- **min_cpu**

参数说明：租户最小可用cpu数量（所有租户min_cpu之和 <= cpu_count）

推荐值：cpu_count/2

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699430](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699430)

- **max_cpu**

参数说明：租户最大可用cpu数量（所有租户max_cpu之和 <= cpu_count * resource_hard_limit / 100）

推荐值：cpu_count/2 ~ cpu_count（或者跟min_cpu设成一样也行）

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699430](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699430)

- **cpu_quota_concurrency**

参数说明：租户每个cpu可以并发的线程数（max_cpu * cpu_quota_concurrency = 最大业务线程数）

推荐值：4（一般建议默认值不改，cpu密集的压测可以改为2）

社区文档：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699378](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001699378)

- **memory_size**

参数说明：资源单元能够提供的 Memory 的大小

推荐值：总内存的50%~80%（如果要创建多个资源单元，则需要灵活分配）

- **log_disk_size**

参数说明：日志盘大小

推荐值：够用就行，比如设置为40~80GB，如果数据量很大则可以设置更大的数值。

- **primary_zone**

参数说明：主副本分配到 zone 内的优先级，逗号两侧优先级相同，分号左侧优先级高于右侧。比如 zone1,zone2;zone3

推荐值：

1. 三台机器规格相同且网络延迟相近，可以让leader均匀分布，以提高性能：RANDOM
2. 如果其中一台性能更好，或者是为了方便统计和观察数据，也可以指定该zone比如：'zone1'
 



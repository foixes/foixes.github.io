---
title: 生态组件FAQ大全
weight: 1
---

**格式要求:**
```bash
问：（不涉及具体方案和流程性问题）
答：（判断、对错、简要知识点）

问题现象：（文字/配图）
关键报错信息：（日志/错误码/抛出异常）
现场信息：（客户名称+版本）
解决方案：（修复方式或临时规避手段）
原因分析：（偶现？必现？修复计划或优化方式）

```

## 问答

### 知识点

```bash
问：社区OB支持IPv6吗？
答：当前版本暂不支持，预计OB4.2版本支持。

```
```bash
问：OB4.x支持哪几种备份介质？
答：已支持NFS、阿里云OSS、腾讯云COS，其他的暂不确定。

```
```bash
问：4.0版本触发器、存储过程、自定义函数最大支持长度是多少？
答：触发器最大支持65536字节，存储和自定义函数clob类型存储，基本理解无上限

```
```bash
问：OceanBase的主键是否是聚簇索引?
答：是，分区表的主键的话是全局唯一的，是全局索引

```
```bash
问：4.x版本如何rs切主?
答：ALTER SYSTEM SWITCH REPLICA leader LS=1 SERVER='目标ip:rpc_port' TENANT ='sys';
例如：ALTER SYSTEM SWITCH REPLICA leader LS=1 SERVER ='xx.xx.xx.xx:2882' TENANT ='sys'

```
```bash
问：OB是否能只进行数据备份，不做日志备份呢？
答：进行数据备份前提是开启日志备份，不支持只做数据备份。

```
```bash
问：为什么刚部署的ob，数据和日志磁盘占用90%，或者占用很大？
答：OB是数据和日志目录采用的是预占用机制，只能动态增加占用大小，暂不支持缩小。（4.1版本支持log_disk_size动态扩缩）
OB4.x磁盘预占用相关参数为：
数据磁盘：datafile_size / datafile_disk_percentage
日志磁盘：log_disk_size / log_disk_percentage

```
```bash
问：下划线开头格式的参数为隐藏参数，无法通过 SHOW PARAMETERS 语句来查询?
答：查看方式：SELECT * FROM oceanbase.__all_virtual_sys_parameter_stat WHERE name='_ob_enable_prepared_statement';

```
```bash
问：如何清理租户并释放空间？
答：先删除租户drop tenant xxx force，再删除对应的资源池drop resource pool xxx，才能释放，注意：该操作会清理租户下的所有数据。

```
```bash
问：mysql迁移到oceanbase还需要用shardingsphere做分库分表吗？
答：不需要，OB本身支持分区表，可以通过分区表设计业务。

```
```bash
问：主机资源够的情况下，多个server可以部署在同一台主机吗？
答：手动部署是可以的，如果obd工具部署使用deploy，不能使用autodeploy命令，修改端口和安装目录，ocp部署ob不支持此场景。但生产环境不建议如此部署，单服务器单observer进程最佳。

```
```bash
问：obd部署社区版三节点，用root还是必须是admin用户 ？
答：建议使用admin用户部署，后期ocp接管的集群需要是admin用户部署的OB。

```
```bash
问：OB社区3.x部署一个读写分离的集群，用4个Zone，3个zone是全能型副本，一个zone是只读的副本？
答：我们建议zone的数量最好是奇数，以3zone或5zone为主。

```
```bash
问：手动部署的OB可以用OBD管理吗？
答：不支持。

```
```bash
问：OMS可以搭建集群吗？
答：目前社区版OMS 3.3.1版本支持集群化部署，低于此版本不支持。

```
```bash
问：社区版mysql模式的jdbc驱动在哪下载？
答：如果使用ob的mysql 模式, 推荐mysql5.1.47版本，如果使用企业版ob的oracle 模式, 使用github 上开源的ob 的jdbc 驱动。

```
```bash
问：使用springboot连接oceanbase正确写法是什么？
答：参考mysql方式，区别在于直连2881 时 username是用户名@租户名，obproxy 2883 时 username是用户名@租户名#集群名

```
```bash
问：普通租户可使用的最大内存是怎么计算的？
答：普通租户能配置的最大内存是 memory_limit - system_memory - 系统500租户内存（与其他租户系统用户共享）

```
```bash
问：obd部署失败，怎么清理配置和目录？
答：
1）命令方式：obd cluster destory 部署名称 ，销毁会清理相关目录
2）后台方式：杀掉所部署组件相关进程（ps -ef|grep ob），按部署配置文件里配置路径删除安装、数据、日志目录，并检查目录所属用户权限是否正确即可重新部署

```
```bash
问：ocp的docker容器是否为无状态？
答：无状态，数据保留在部署在宿主机上的metadb(单机/集群OB)中。

```
```bash
问：内部表和内部虚拟表是什么？
答：内部表一般指系统表，虚拟表只是系统表中的一部分，ID号介入10000-20000之间。

```
```bash
问：备份数据时候，需要每个observer节点都挂载NFS么？
答：是的。

```
```bash
问：备份可以备份单个租户的么？
答：3.x只支持集群备份，4.0支持租户级别备份，且不再支持集群级别备份。

```
```bash
问：副本保持同步是通过同步clog吗？
答：是的，以分区为单位做clog副本同步，只有分区的主副本才能进行更新，如果有多个分区的主副本同时在更新，仍然以分区为单位做Paxos同步。

```
```bash
问：社区OB怎么查看哪些表是复制表？
答：3.x版本：select table_name from  oceanbase.__all_table_v2 where duplicate_scope=1 
4.x版本：预计4.2版本才支持。

```
```bash
问：4.0版本，CDB开头的视图、DBA开头的视图、GV开头的视图，这些是什么规则哪？
答：CBD 是sys租户的，能看到全局的信息。DBA只能看到租户自己的信息。gv$一般和v$一起，直连数据库节点的时候，v$只能看到本机器，gv$是所有机器节点的信息。

```
```bash
问：4.0版本，普通用户租户查看自己租户下所有的表，该查哪个视图？
答：表类型：select distinct(OBJECT_TYPE) from DBA_OBJECTS;  
根据类型查表名：select OBJECT_NAME from DBA_OBJECTS where OBJECT_TYPE='table' ;

```
```bash
问：obd cluster list 显示destroyed销毁状态怎么清理删除？
答：cd  ~/.obd/cluster，删除对应的Name目录

```
```bash
问：社区版OCP开源的吗？
答：目前工具开放供下载使用，不开源，后续轻量版OCPExpress会考虑开源。

```
```bash
问：hint中的NO_USE_PX是什么？
答：NO_USE_PX 指的是关闭并行查询。4.x版本暂不支持关闭并行查询功能。

```
```bash
问：X86版本OCP是否支持接管ARM版本的OB集群
答：不支持混合接管和部署。

```
```bash
问：OB支持openeuler(欧拉)部署么？
答：暂未进行兼容适配，能部署但不保证运行后功能可靠性。

```
```bash
问：OceanBase一个表的分区大小是否有推荐大小？
答：OB对分区大小未做限制，一般建议一个分区大小不要超过100G，分区大小也可以按照磁盘空间和分区数预估。

```
```bash
问：proxy_sessid和server_sessid之间的关系是什么？
答：proxy_sessid唯一标识客户端和OBProxy之间的连接，用server_sessid唯一标识OBProxy和OBServer之间的连接。

```
```bash
问：OBClient是不是没有ubuntu/debian系统的版本？
答：暂不支持，使用mysql客户端即可。

```
```bash
问：社区版OMS是否支持将MySQL数据库数据迁移到企业版OB的MySQL租户？
答：不支持。

```
```bash
问：4.0怎么查看锁和事务的信息？
答：DBA_OB_DEADLOCK_EVENT_HISTORY

```
```bash
问：怎么查租户表占用磁盘空间大小？
答：仅查询基线数据的，转储合并后能看到最新的占用大小
3.x：select tenant_id, svr_ip, unit_id, table_id, sum(data_size)/1024/1024/1024 size_G from __all_virtual_meta_table group by 1, 2, 3, 4;
4.x：select sum(size)/1024/1024/1024 from   (select DATABASE_NAME,TABLE_NAME,TABLE_ID,PARTITION_NAME,TABLET_ID,ROLE from DBA_OB_TABLE_LOCATIONS ) AA full join   (select distinct(TABLET_ID) ,size from  oceanbase.GV$OB_SSTABLES ) BB on AA.TABLET_ID=BB.TABLET_ID  where  AA.role='leader' and AA.table_name='table_name';

```
```bash
问：4.0版本TRUNCATE TABLE 支持指定分区清除数据吗？
答：支持，具体清理分区类型参考文档[https://www.oceanbase.com/docs/community-observer-cn-100000000009015982](https://www.oceanbase.com/docs/community-observer-cn-10000000000901598)

```
```bash
问：obd和obproxy能和ob集群部署在一起么？有什么性能影响？
答：可以，obd和obproxy占用资源很少，业务量或者节点数不大情况下，几乎没什么性能影响。如果是大规模生产环境建议单独节点部署obproxy。

```
```bash
问：PHP怎么连接OB？
答：[https://github.com/oceanbase/oceanbase/issues/841](https://github.com/oceanbase/oceanbase/issues/841)

```
```bash
问：obd cluster destory后能否再恢复？
答：不能，该操作直接删除物理数据文件，如果存在备份文件，可以新建集群备份恢复。

```
```bash
问：mysql-connector-java的8.x版本怎么连接OB？
答：MySQL Connector/J 8.x 版本，Class.forName("com.mysql.jdbc.Driver") 中的 com.mysql.jdbc.Driver 需要替换成 com.mysql.cj.jdbc.Driver 

```
```bash
问：jdbc连接ob配置示例？
答：conn=jdbc:oceanbase://x.x.x.x(ip):xx(port)/xxxx(dbname)?rewriteBatchedStatements=TRUE&allowMultiQueries=TRUE&useLocalSessionState=TRUE&useUnicode=TRUE&characterEncoding=utf-8&socketTimeout=3000000&connectTimeout=60000

```
```bash
问：社区版OCP支持RHEL8吗？
答：支持RHEL7.2及以上版本

```
```bash
问：oceanbase是否支持主键修改？
答：3.x不支持，4.x支持

```
```bash
问：datax 的ob writer 里obWriteMode 支持insert ignore吗？
答：不支持，采用 insert into 或者 replace into 或者 ON DUPLICATE KEY UPDATE 语句。

```
```bash
问：如何查看所有的sql记录？
答：
3.x：sql审计视图gv$sql_audit。 
4.x：sql审计视图gv$ob_sql_audit。

```
```bash
 问：4.0版本合并表信息是哪些？
答：major freeze相关的信息放到了__all_merge_info(用于存放租户合并的整体merge信息)、__all_zone_merge_info(用于存放租户下每个zone的merge信息)。
__all_merge_info中有合并版本号、合并状态、是否发生了error等信息。
除了sys租户外，每个用户租户对应的meta租户下，都有这两张表，用于保存该用户租户和meta租户的merge信息。也可以在sys租户下直接查看CDB_OB_MAJOR_COMPACTION来查看所有租户的__all_merge_info信息、CDB_OB_ZONE_MAJOR_COMPACTION来查看所有租户的__all_zone_merge_info信息。
OB 4.0支持对每个租户单独设置合并时间点，相关配置项为major_freeze_duty_time，系统租户下可执行以下设置语句：alter system set major_freeze_duty_time = 'xxx' tenant sys;

```
```bash
问：OceanBase 4.0为什么取消了轮转合并？
答：4.0版本将合并拆分成了租户级，粒度更小，执行时间更短，因此取消了轮转合并。

```
```bash
问：如何手动杀掉正在执行sql？
答：show processlist; kill $ID;

```

### 原理


```bash
问：oceanbase的索引采用的是哪种方式？
答：MemTable 使用的是 B+ 树结构，而 SSTable 使用的是宏块结构
```
```bash
问：ob内存是怎么规划的？
```
答：
![image.png](/img/FAQ/all_faq/1668247000430-b1ee6f69-76c1-47de-9490-81703b27afd5.png)
```bash
问：plan cache分配资源过少或者大并发会造成plan cache命中率较低的问题吗？
答：
资源少：需要看流量, 如果流量一直只是一个query, 那么这个plan将一直存在plan cache中, 而且一直命中;但是, 如果流量是多种多样的query, 那么由于资源分配有限, plan 将会发生频繁汰换, 最终分配资源过少会导致plan cache命中过低的。
并发大：
并发量大导致内存快速写满会造成plan的频繁汰换, 这将导致计划命中率降低. 但是, 需要注意的是并发量大不一定会导致plan cache命中率降低, 只要汰换率没有上去, 命中率也不会降低。
```
```bash
问：租户memstore使用达到阈值后，选择合并或者转储的依据是什么？
答：有两种转储方式：
自动转储和手动转储，第一种是当memstore使用达到预设的限定是，例如memstore内存使用率大于 memstore_limit_percentage * freeze_trigger_percentage的值，自动触发冻结+转储，转储为mini sstable后根据minor_compact_trigger来触发mini minor或minor 。
第二种是执行alter system minor freeze 手动转储。
有三种合并方式：自动合并、定时合并与手动合并。如果自动合并过程中失败了，除了个别错误外，会一直重试，合并在默认情况下是以每日合并的方式定时触发的。

```
```bash
问：parallel_servers_target并发参数怎么理解？
答：parallel_servers_target这个指标都是针对px类型的SQL进行约束的，当超过这个阈值，阻塞的是后续的px类型sql的执行，不阻塞本地SQL的执行。也就是并发sql不会占用所有线程，会给普通sql预留一些线程资源。所以parallel数量的设置应该根据需求和资源来确定，如果全都设成并发sql，就会互相阻塞。
```
```bash
问：用户的SQL请求和RPC之间有什么关系？
答：observer之间沟通使用的是RPC 2882端口，最简单的以广播查询SQL，是建立一张分区表，但是SQL语句不带分区键，用户执行SQL是发到了一台OB上，但是分区表的各个分区的主副本是在多台OB机器上的，需要将OB接收SQL的请求拆分，调用各个分区主副本所在的OB获取数据，然后再聚合后返回给用户
```
```bash
问：global的变量会持久化到什么地方?
答：仅 GLOBAL 级别的变量会持久化，SESSION 级别的变量不会进行持久化。持久化到内部表与配置文件，可以在strings /home/admin/oceanbase/etc/observer.config.bin 与 strings /home/admin/oceanbase/etc/observer.config.bin.history 文件中查询配置项

```
```bash
问：4.0版本，GV$OB_SERVER_SCHEMA_INFO视图中的SCHEMA是什么？
答：Oceanbase的schema泛指一切需要在集群范围内同步的数据库对象元信息，包括但不限于table、database、user等元信息。此外Oceanbase的schema是多版本且各租户独立，在集群范围同步是最终一致的。
GV$OB_SERVER_SCHEMA_INFO可以理解为每台ObServer每个租户已经刷新的最新版本的schema的信息，这个视图用户比较关注的schema信息是REFRESHED_SCHEMA_VERSION、SCHEMA_COUNT、SCHEMA_SIZE，其含义如下：

- REFRESHED_SCHEMA_VERSION：对应租户在对应机器已刷新到的schema版本。
- SCHEMA_COUNT：对应schema版本下，各schema对象数目的总和（table数目+database数目+…）。
- SCHEMA_SIZE：对应schema版本下，各schema对象总共所占的内存大小(B)。


```
```bash
问：OB的sql执行流程
```
答：![image.png](/img/FAQ/all_faq/1679833693317-53646c42-6030-4025-9a9e-ef1ae0cdc6a4.png)
![image.png](/img/FAQ/all_faq/1679833729438-ff8ed706-d886-4cce-a024-e5677c46fe67.png)


```bash
问：执行计划中的px partition iterator，px block iterator是什么意思？
答：partition iterator 是以分区粒度迭代数据，block iterator是以block粒度迭代数据，Iterator 是对数据的抽象，它把数据抽象成一块一块的，以便多个线程并发地处理这些数据。一块数据如果对应的是一个分区，则是 partition iterator。一块数据如果对应的是一个或数个宏块，则是 block iterator。

```
```bash
问：OceanBase 是如何创建索引，不影响更新操作的？
答：OB在创建索引的时候，是一个渐进的过程，首先不影响写入，因为OB是追加写。对于索引列有更新的情况，会进行记录。然后会去扫描现有数据去创建索引，扫描完成之后将更新的和新增的数据做一次合并，最后再将索引标记为可用状态，就完成了索引的创建。

```
```bash
问：OB4.0生产环境最小磁盘使用空间限制为什么是54G？
答：54G是目前计算出来ob启动需要的空间，下面是计算公式：
clog统计规则：
1. 理论最小值：新建一个租户预期需要创建4个日志流，每个日志流目前空间需求最低512M，这里算出需要的最小磁盘是2G；   
2. 用户设置值：系统租户：取max（2G，用户设置）普通租户：取max（2G，所在盘全部）   
3. OBD计算：根据ob最小规则（4c8g）计算，即8 * 3 = 24，其中3为4.x中对于clog内存和磁盘占用的大概估算 slog统计规则：slog统计10G，目前是硬编码，有计划优化到2、4G左右； 
sstable统计规则：空集群启动后转储合并实测大概会有1G磁盘开销，代码没有针对sstable做限制，但是按照长稳运行需要20G； 
所以目前，选了 24G+10G+20G = 54G作为启动最小依赖的内存，不过后续有优化的计划；

```
### 报错

```bash
问：oblogproxy的config.setTableWhiteList(“sys.abc.*”);如何设置指定表的信息？
答：使用“|”分隔符，sys.abc.tableA|sys.abc.tableB
```
```bash
问：observer3.1.0升级3.1.4版本失败？
答：3.1.0不支持本地升级，只能做数据迁移逻辑升级，且3.x也不支持直接升级至4.x版本。
```
```bash
问：报错：OBD-1006: Failed to connect to oceanbase-ce的原因是？
答：
1）OBD 和目标机器之间网络不连通。
2）对应的组件进程已经退出或者不提供服务。
3）账号密码不匹配。
4）OB日志磁盘不足。

```
```bash
问：oceanbase-ce’s servers list is empty ？
答：检查部署配置文件 oceanbase-ce 模块下的 servers 中 ip 格式是否填写正确。

```
```bash
问：使用obloader导入数据失败，未返回报错怎么办？
答：OBLOADER 默认的行为是：运行错误日志记录在 logs/ob-loader-dumper.error，具体错误的记录会记录在 logs/ob-loader-dumper.bad。如果要求遇到错误快速失败，可以指定 --max-errors 选项。

```
```bash
问：OCP界面无操作退出登陆，怎么设置超时时间？
答：默认是30m无操作退出，OCP系统参数中设置server.servlet.session.timeout。

```
```bash
问：ocp上集群状态显示运维中？
答：“任务”中可能有任务在执行或执行失败。

```
```bash
问：用benchmarksql导入数据时，出现如下3类错误：
Worker 089: ERROR: Failed to init SQL parser
Worker 044: ERROR: No memory or reach tenant memory limit
Worker 083: ERROR: Internal error
答：首先看下相关日志文件，benchmarksql.log. 
（1）先确认是用的哪个租户，如果是sys租户不建议使用，因为sys租户内存太小，很可能不够用。 select a.tenant_id,max(tenant_name), round(sum(used)/1024/1024/1024,2) “mem_quota_used(G)” from gv$memory a, __all_tenant b where a.tenant_id=b.tenant_id; 如果查询结果tenant_id > 1000 则是普通租户，这样可以判断租户的情况。 
（2）如果是普通租户，则看下memory_limit的值是不是太小，建议调大些。 
（3）看下props.ocenbase 这个配置文件，里面配置了很多测试的配置信息，比如数据库，仓库数等等。 
（4）props.oceanbase文件中参数warehouses和loadWorkers的值需要修改成较小的值，然后登录（用 user参数值登录），再测试一下，如果没问题，说明测试资源不足，可能是租户资源，也可能是机 器资源有问题，此时可以参数下租户资源和机器配置分析原因。

```

### 故障恢复

```bash
问：服务器硬件故障或者需要长时间停止某个节点或某个zone，怎么操作？
答：需要调整 server_permanent_offline_time 参数，防止被永久下线。默认是1小时
该配置项的适用场景及建议值如下：

- OB版本升级场景：建议将该配置项的值设置为 72h（OCP升级OB可忽略，默认会调整）
- OB硬件更换场景：建议手动将该配置项的值设置为 4h
- OB清空上线场景：建议将该配置项的值设置为 10m，使集群快速上线

```
```bash
问：创建分区表报错：Too many partitions (including subpartitions) were defined？
答：
1）超出单表最大分区数限制（8192个）
2）租户内存不足。
```
```bash
问：系统租户root@sys用户密码忘记了？
答：
1）OCP对应集群总览界面-修改密码。![image.png](/img/FAQ/all_faq/71456382/1669531503193-3103f39a-b2c1-44e9-8a01-b71323c6f235.png)
2）obd cluster edit-config 部署名称 查看配置文件中的密码信息。

```
```bash
问：ocp怎么关联oms的告警信息？
答：1）OMS上添加关联OCP
![image.png](/img/FAQ/all_faq/1683343829395-a9f15b52-9680-46d7-8471-d70e0d48b7bd.png)
2）OMS上新建告警通道
![image.png](/img/FAQ/all_faq/1683343840763-a278804a-401f-4658-a0d0-8ecbedc4f42c.png)
3）OMS告警信息如下
![image.png](/img/FAQ/all_faq/1683343850716-fb9c077a-2501-4a08-9804-9e4cd536da27.png)
4）ocp同样能收到告警
![image.png](/img/FAQ/all_faq/1683343869507-e28028c5-c9ec-4180-8762-20b596d1a425.png)

```
### 性能调优


```bash
问：explain预估耗时不准，有具体过程执行耗时吗?
答：视图表gv$sql_plan_monitor

```
```bash
问：亿级别数据创建索引耗时超长怎么优化？
答：
1）增加并行加快索引创建
设置并发
SET GLOBAL OB_SQL_WORK_AREA_PERCENTAGE = 30;   ---  增加SQL执行内存百分比
SET SESSION _FORCE_PARALLEL_DDL_DOP = 32;  --- 设置DDL并行度 
SET GLOBAL PARALLEL_SERVERS_TARGET = 64;   --- 并行度增加需要设置该参数，比所有并行度和大即可
怎么查所有并行度之和呢？
1）所有DDL的并行度加起来不超过租户max_cpu的上限
2）ALTER SYSTEM SET _TEMPORARY_FILE_IO_AREA_SIZE = '5';  --- 调大临时文件内存缓存，建议小于10
3）如果有限流可以关闭
关闭IO限流：alter resource unit xxxx max_iops=10000000, min_iops=10000000;  --- 1 个 Core 对应 1 万 IOPS 的值
关闭网络限流：alter system set sys_bkgd_net_percentage = 100;   --- 默认60

```
```bash
问：大数据量插入如何提高效率？
答：
应用层：
jdbc url上开启批量参数，重写批量，ps缓存相关参数打开。
cacheServerConfiguration=true
rewriteBatchedStatements=true
useServerPrepStmts=true
cachePrepStmts=true
当然代码也是要addBatch，executeBatch
数据库层：
1）4.1版本支持旁路导入
[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001687926](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001687926)
2）4.x版本支持并行导入
[https://www.oceanbase.com/docs/community-observer-cn-10000000000900958](https://www.oceanbase.com/docs/community-observer-cn-10000000000900958?_gl=1*1ob9hre*_ga*ODQzNDgzMjU4LjE2NjM1NzU0MjE.*_ga_T35KTM57DZ*MTY4NTg3MTM2MS44ODAuMS4xNjg1ODcyODMyLjYwLjAuMA..)
工具层：
使用OBloader导数工具，并按需进行调优
[https://www.oceanbase.com/docs/community-obloaderdumper-cn-10000000002035960](https://www.oceanbase.com/docs/community-obloaderdumper-cn-10000000002035960)
```
```bash
问：OB系统日志打印太大太多怎么办？
答：#进入ob的日志目录
cd ~/observer/log
#可以删除wf和日期结尾的日志
rm -f observer*.wf observer.log.2023*
#登录系统租户，设置系统日志参数
alter system set enable_syslog_recycle=true  -- 开启日志回收
alter system set enable_syslog_wf=false  --关闭wf日志打印
alter system set max_syslog_file_count=10  --限制日志个数，按需调整(单个日志256M)
alter system set syslog_level ='ERROR'; (DEBUG/TRACE/INFO/WARN/USER_ERR/ERROR)  设置系统日志级别，生产环境建议保持INFO级别，方便问题排查定位。
```
```bash
问：OBProxy日志打印太大太多怎么办？
答：系统租户或者root@proxysys连接集群修改
show proxyconfig like '%log_file_percentage%';
alter proxyconfig set log_file_percentage=75;
以下是obproxy日志相关参数

| 参数 | 默认值 | 取值范围 | 解释 |
| --- | --- | --- | --- |
| **log_file_percentage** | 80 | [0, 100] | ODP 日志百分比阈值。超过阈值即进行日志清理。 |
| **log_dir_size_threshold** | 64GB | [256MB, 1T] | ODP 日志大小阈值。超过阈值即进行日志清理。 |
| **max_log_file_size** | 256MB | [1MB, 1G] | 单个日志文件的最大尺寸。 |
| **syslog_level** | INFO | DEBUG, TRACE, INFO, WARN, USER_ERR, ERROR | 日志级别。 |


```
```bash
问：ob怎么开启慢查询记录？
答：租户参数 trace_log_slow_query_watermark，默认超过1s就会记录，记录在observer.log日志，或ocp白屏监控有记录。

```
```bash
问：如何查看某个实例（租户）下库表分区主副本的位置和大小？
答：
3.1.x版本：
SELECT t.tenant_id, a.tenant_name, t.table_name, d.database_name, tg.tablegroup_name , t.part_num , t2.partition_id, t2.ZONE, t2.svr_ip , round(t2.data_size/1024/1024/1024) data_size_gb
, a.primary_zone , IF(t.locality = '' OR t.locality IS NULL, a.locality, t.locality) AS locality FROM oceanbase.__all_tenant AS a
JOIN oceanbase.__all_virtual_database AS d ON ( a.tenant_id = d.tenant_id )
JOIN oceanbase.__all_virtual_table AS t ON (t.tenant_id = d.tenant_id AND t.database_id = d.database_id) 
JOIN oceanbase.__all_virtual_meta_table t2 ON (t.tenant_id = t2.tenant_id AND (t.table_id=t2.table_id OR t.tablegroup_id=t2.table_id) AND t2.ROLE IN (1) )
LEFT JOIN oceanbase.__all_virtual_tablegroup AS tg ON (t.tenant_id = tg.tenant_id and t.tablegroup_id = tg.tablegroup_id)
WHERE a.tenant_id IN (1006 ) AND t.table_type IN (3)
AND d.database_name = 'T_FUND60PUB'  -- 库名
and table_name in ('BMSQL_HISTORY')  -- 表名
ORDER BY t.tenant_id, tg.tablegroup_name, d.database_name, t.table_name, t2.partition_id;
4.x版本：
select svr_ip,count(1) from __all_virtual_ls_meta_table where tenant_id=1002 groupby svr_ip;

# OBD部署问题
```
```bash
现象：部署卡在Remote oceanbase-ce* repository install 阶段
![image.png](/img/FAQ/all_faq/1682389370244-dedd852a-3c9d-487f-96ef-0d69dce7b26f.png)
报错：检查obd日志报错
信息：obd2.0
解决方案：具体根据obd日志分析，目前已知有如下可能：
1）本机缺少rsync命令  -- yum install -y  rsync  安装rsync命令即可
2）sftp-server不一致  -- /etc/ssh/sshd_config配置和本机sftp-server路径保持一致，并重启sshd服务
3）非公网环境，使用了在线安装方式  -- obd mirror disable remote 关闭远程仓库拉取安装包，采用离线安装
4）本地磁盘满  -- 下载包较大，磁盘满下载将失败
原因：此处会涉及是拉包和传包过程，涉及基础命令，如果非公网是无法使用远程仓库的，如果磁盘可使用空间不足，也无法下载成功。

```
```bash
报错：[WARN] (x.x.x.x) clog and data use the same disk (/)
现象：安装提示warn级别的报错信息，安装流程可正常进行，程序正常
信息：3.1.4
解决方案：obd cluster edit-config xxx，将data_dir和redo_dir设置为不同磁盘目录
原因：小规模且短期的测试环境可以忽略，生产环境必须保证安装目录、数据目录、日志目录均是分盘目录，否则后期使用会出现非业务数据占满磁盘而出现磁盘使用率满问题。

```
```bash
报错：[ERROR] Cluster NTP is out of sync
现象：部署报错NTP服务未同步
![image.png](/img/FAQ/all_faq/1668859623282-6964f341-7192-4d06-84ab-37da69464f1b.png)
信息：3.1.3
解决方案：
原因：服务器之间时差超过100ms，会出现rootserver无主情况，部署时做了时差校验

```
```bash
报错：ERROR 2013 (HY000): Lost connection to MySQL server at ‘reading authorization packet’
现象：新部署环境，使用obproxy登录数据库报错
信息：3.1.4
解决方案：obd编辑配置文件proxyro_password和observer_sys_password密码保持一致，obd重载即可
原因：配置文件中要求，这2个参数密码需要保持一致。

```
```bash
报错：[ERROR] Cluster init failed
![image.png](/img/FAQ/all_faq/1670835604707-b526c34a-d51f-482c-8cdb-af4c578cd5f9.png)
现象：安装初始化失败。observer.log日志：fail to send rpc(tmp_ret=-4122
信息：4.0.0
解决方案：关闭防火墙
原因：现场防火墙做了限制，导致rpc无法互相通信

```
```bash
报错：Cluster bootstrap x
现象：部署失败，无明显提示
![image.png](/img/FAQ/all_faq/1670656607163-a8cc5851-fedd-402c-ba24-5c27462718c3.png)
信息：3.1.4
解决方案：现场双网卡环境导致，改成静态固定IP，将网卡文件BOOTPROTO=dhcp 改成 static 重启网络
原因：IP检测可能失败

```
```bash
报错：[ERROR] import connect failed
现象：使用OBD命令安装OceanBase集群的时候提示缺少pymysql模块，但是这个模块已经安装
ModuleNotFoundError：No module name 'pymysql'
![image.png](/img/FAQ/all_faq/1670665435483-e597b7b5-1ade-485a-8b32-bf683933f950.png)
![image.png](/img/FAQ/all_faq/1670665860175-1f4b8886-409a-41d6-9ecf-c47b579adf8d.png)
信息：obd1.6.0，ob4.0.0
解决方案：安装时不要使用sudo方式，安装使用的是当前用户进行的，创建缺少的/usr/obd/lib/site-packages目录，把python模块拷贝进去
原因：现场启动用户不对，直接使用root即可，不要切换用户再sudo，并且看报错缺少/usr/obd/lib/site-packages目录应该是obd安装有问题，可以重新安装obd，现场通过创建目录拷贝方式最终也能解决。

```
```bash
报错：Open ssh connection x
现象：obd部署在ssh连接处报错 
信息：obd：1.6.0
解决方案：config.yaml内关于user部分设置是否正确。若使用的password的方式，可以尝试下手动使用对应的用户ssh是否能登录到对应机器
原因：机器之间跳转使用配置文件中的user模块下的ssh连接信息，无法连接可能是未配置或配置用户信息不正确等。


```
```bash
报错：type object 'ConfigUtil' has no attribute 'get_random_pwd_by_rule'
现象：all-in-one4.1高版本换4.0低版本安装后，obd web部署报错
![image.png](/img/FAQ/all_faq/1686118020920-bd707347-d28f-45e6-bf38-d9b86f61d521.png)
信息：obd2.0.1
解决方案：
方法1：
1）cd  ~/.obd/    2）rm -f versiom (隐藏文件)  3）执行obd --version  会重新生成新的plugins
4）obd web部署即可
方法2：
1）rpm -e  卸载obd包  2）rm -rf ~/.obd/  删除.obd目录  3）重新install.sh 安装obd 初始化环境
4）obd web部署即可
原因：obd2.1版本支持随机密码功能，但重新安装的2.0.1版本不支持，导致此类无法找到。obd不支持降级，如果需要降级，需要清理环境再安装obd，生产环境可以使用方法1，方法2会清理部署环境信息。

```
```bash
现象：obd web 无法访问到白屏部署界面
报错：白屏界面无法访问
信息：OBD2.0
解决方案：
1）防火墙问题
2）其他安全程序禁止IP或端口访问
3）内外网环境，部署使用内网IP，访问需要使用外网IP
4）obd web服务不能手动杀掉
原因：建议优先检查现场安全环境相关问题。

```
## OBD使用问题


```bash
报错：ERROR Another app is currently holding the obd lock
现象：执行obd cluster display xx 报错
信息：1.5
解决方案：确认是否有未结束的obd其他操作，或者有人也在调用obd
原因：obd不支持多人同时操作。

```
```bash
现象：obproxy产生大量core.XXX文件
报错：core文件占满磁盘
信息：1.6.0
解决方案：方法一：调大proxy_mem_limited（最少500M）
	       方法二：更新obd版本到1.6.1或者最新版本，重新部署集群
原因：obd已知问题，1.6.1解决


```
```bash
报错：ERROR 2013 (HY000): Lost connection to MySQL server at 'reading authorization packet'
现象：后台修改完密码，使用obproxy登录数据库报错
信息：3.1.4
解决方案：后台登录数据库还原成obd配置中的密码，再通过obd cluster edit-config方式修改密码，reload重载集群
原因：这个报错基本是密码不一致导致，后台修改密码不会同步到obd配置文件中，导致无法使用obd配置中的密码登录。

```
## OB部署问题

```bash
现象：obd web白屏部署，报错ping不通
![image.png](/img/FAQ/all_faq/1686104635581-64717c92-f7ef-404c-be4b-84b27c0c80ab.png)
报错：OBD-2007：xx.xx.xx.xx lo fail to ping xx.xx.xx.xx. Please check configuration `devname`
信息：OBD2.1
解决方案：返回上一步`集群配置`下的`更多配置`中`devname`设置为自定义，填写IP对应的网卡名称即可
原因：默认使用lo网卡，对应IP是本地127.0.0.1。后续版本会考虑优化。

```
```bash
报错：ERROR [SHARE] operator() (ob_common_config.cpp:128) [20728][][T0][Y0-0000000000000000-0-0] [lt=5] Invalid config, value out of [1073741824,) (for reference only). name=min_full_resource_pool_memory, value=268435456, ret=-4147 
现象：源码编译ob再部署失败[ERROR] oceanbase-ce start failed
![image.png](/img/FAQ/all_faq/1671363516865-030ecc03-2a08-4d4e-b16a-9b7af65a2c1f.png)
信息：4.0.0
解决方案：配置文件中调大__min_full_resource_pool_memory参数的值2147483648，alter system __min_full_resource_pool_memory=2147483648，隐藏参数查看方式SELECT * FROM oceanbase.__all_virtual_sys_parameter_stat WHERE name='__min_full_resource_pool_memory';
原因：该参数为允许以最小多少内存的规格创建租户。


```
```bash
报错：ERROR 4015 (HY000): System error
现象：手动部署OB，初始化alter system bootstrap报错
信息：3.1.3
解决方案：调大虚拟机cpu核数
原因：资源不足，官方要求最小2.5个核心。

``` 
## OB使用问题

```bash
现象：新导入的数据时，做count查询特别慢？
报错：无报错，或者超时Timeout
信息：OB4.x
解决方案：
1）手动收集统计信息
2）触发转储合并
3）增加ob_query_timeout超时参数
原因：
1）OB4.x版本支持自动收集统计信息，如果还未触发自动收集统计，需要手动收集统计。
2）数据量大和数据场景复杂的时候，需要手动触发合并会重整数据，加快查询效率。
3）查询如果非单纯count表，且租户资源不足扩展时，count性能就是很慢，需要增大ob_query_timeout参数防止查询超时中断。


```
```bash
报错：[1235] [0A000]: (conn=3221487627) unit MEMORY_SIZE less than __min_full_resource_pool_memory not supported
现象：创建租户阶段，创建资源时报错
信息：OB4.1
解决方案：1）如果MEMORY_SIZE设置太小，建议是增大资源单元unit的MEMORY_SIZE参数大小
alter resource unit your_unit_name MEMORY_SIZE='2G';
2) 如果MEMORY_SIZE无法再增大了，可以降低最小资源池参数限制__min_full_resource_pool_memory
该隐藏参数查看方式SELECT * FROM oceanbase.__all_virtual_sys_parameter_stat WHERE name='__min_full_resource_pool_memory';
修改方式alter system __min_full_resource_pool_memory=2147483648; （2G)
原因：该报错主要是设置的memory_limit比__min_full_resource_pool_memory小导致，__min_full_resource_pool_memory在4.x版本默认是2G，3.x版本默认是5G。


```
```bash
报错：obclient [jydb]> select * into outfile ‘/tmp/a.csv’ from a;
ERROR 1227 (42501): Access denied
现象：使用select outfile语法报错权限问题
信息：OB4.0
解决方案：直连observer，不能通过obproxy连接
原因：导出是到本地位置，obproxy代理不是本地。



```
```bash
报错：1203 (42000): Too many sessions
现象：插入1W条数据，报错Too many sessions
信息：OB4.0
解决方案：
应用层使用未配置连接池回收。
ODP线程满：[https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001579566](https://www.oceanbase.com/docs/common-oceanbase-database-cn-10000000001579566) 增加proxy服务线程数 client_max_connections参数 
ODP端故障：[https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000001579572](https://www.oceanbase.com/docs/enterprise-oceanbase-database-cn-10000000001579572)
原因： 连接池需要程序设置回收。obproxy 自身的故障或者因流量上升，obproxy 线程用满（报错信息类似 too many sessions）。此时的处理方法参见 ODP 端故障 以及 ODP 线程满。


```
```bash
报错：WARN [COMMON] try_flush_washable_mb (ob_kvcache_store.cpp:630) [52886][T1001_ReplaySrv][T1001][Y0-0000000000000000-0-0] [lt=4] can not find enough memory block to wash(ret=-4273, size_washed=0, size_need_washed=2097152)
现象：测试环境，内存有限，日志报1001租户内存不足，是否可以从普通租户那匀一点给meta租户
信息：OB4.0
解决方案：可以通过调整1001租户的规格来自动调整对应meta租户的规格
原因：4.0引入了用户的meta租户概念，其规格配置不支持用户独立设置，具体的规则详见官网文档[https://www.oceanbase.com/docs/community-observer-cn-10000000000901425](https://www.oceanbase.com/docs/community-observer-cn-10000000000901425)

```
```bash
现象：用obd管理集群报错无法连接到集群，实际集群状态正常，obd日志报错密码不正确Access denied for user
![image.png](/img/FAQ/all_faq/1668842141080-38f78b3b-ce4d-4359-b57d-b6ef1efbb0de.png)
报错：OBD-1006: Failed to connect to oceanbase-ce
信息：3.1.4
解决方案：登录数据库设置系统租户密码和obd配置root_password参数保持一致
原因：用户未通过obd方式修改密码，直接登录数据库修改，但obd配置文件不会同步修改，导致密码配置不一致，且当前情况下不能再通过obd修改密码，因为密码已经不一致，edit-config编辑仍然是原密码信息

```
```bash

现象：日常使用抛出报错ERROR 4654 (HY000) at line 1: location leader not exist
报错：leader revoke，please attention!（revoke reason="clog sliding_window_timeout"）
![image.png](/img/FAQ/all_faq/1668242958923-72e5cb92-1219-4946-a2e0-5058d2966d55.png)
![image.png](/img/FAQ/all_faq/1668242992148-84c46d49-81ea-4f73-aba2-26614aa058b4.png)
信息：3.1.4
解决方案：检查节点间的时差是否超出50ms，或者调大系统参数
alter system set _ob_clog_timeout_to_force_switch_leader ='10s';
原因：看第一个日志是leader被revoke，observer.log是超时了，有可能是与其他节点ntp时钟同步有较大差值

```
```bash

现象：停止zone操作失败
报错：ERROR 4660 (HY000): cannot stop server or stop zone in multiple zones
信息：3.3.0
解决方案：环境是3个zone，停止一个zone后，再停止一个是不允许的
原因：停止 Zone 时需要确保多数派副本均在线，否则操作会失败。


```
```bash
现象：ODC上执行sql报错
![image.png](/img/FAQ/all_faq/1669539589485-040e0a5f-16f6-4bff-85e8-2b5d3589924a.png)
报错：Unkown thread id
信息：3.1.4
解决方案：增大超时参数ob_query_timeout，事务参数也建议增大ob_trx_timeout、ob_trx_idle_timeout
原因：unknow thread id是因为在jdbc代码执行SQL时，设置了ob_query_timeout，超时了，驱动就会执行kill query connectionId命令将超时执行的SQL取消掉。但是这个命令在多个obproxy时，可能会发给其他的，就会报错unknow thread id


```
```bash
现象：新建租户，使用租户信息无法登录集群？
报错：ERROR 1227 (42501): Access denied
信息：3.1.4
解决方案：sys租户设置xxx租户白名单，ALTER TENANT xxx SET VARIABLES ob_tcp_invited_nodes='%';或者使用-h127.0.0.1登录业务租户，SET GLOBAL ob_tcp_invited_nodes='%';
原因：创建租户时默认密码为空，如果不能登录，一般是未设置白名单，导致无访问权限。


```
```bash
现象：字段为外键时插入数据报错
报错：1235 - Not supported feature or function
信息：3.1.1
解决方案：新建普通租户进行业务使用
原因：sys租户下有此限制，普通租户下可以正常插入，同时建议，sys租户仅做集群管理使用，不可当做业务租户

```
```bash
现象：集群连接不上，observer进程挂掉
报错：worker cnt larger than max cnt
信息：3.1.4
解决方案：调大sys_cpu_limit_trigger大于16，或者更大
原因：基本为租户cpu资源不足够导致

```
```bash
现象：load data导入数据报错
报错：ERROR 1227 (42501): Access denied
信息：3.1.4
解决方案：set global secure_file_priv = '/tmp’，或者导入的数据文件放到启动ob的用户权限下执行
原因：访问数据文件无权限导致


```
```bash
现象：eth0网卡ping服务器正常，但部署报错ping失败
报错：eth0 fail to ping xxx.xxx.xxx.xxx. Please check configuration `devname`
信息：3.1.4，Anolis8.6
解决方案：执行chmod u+s /usr/sbin/ping
原因：兼容性问题，后续版本修复


```
```bash
现象：执行obd cluster deploy obce-single -c obce-single.yaml不成功，Cluster状态是configured
报错：[ERROR] Failed to download [mirrors.aliyun.com/oceanbase/community/stable/el/None/aarch64///repodata/repomd.xml](http://mirrors.aliyun.com/oceanbase/community/stable/el/None/aarch64///repodata/repomd.xml) to /home/admin/.obd/mirror/remote/OceanBase-community-stable-elNone/repomd.xml
信息：3.1.4
解决方案：禁用远程镜像仓库获取obd mirror disable remote，将安装包复制本地仓库obd mirror clone /tmp/obd/*.rpm
原因：obd默认开启远程镜像下载，无法联网环境将下载失败，需要关闭远程镜像获取，将安装包导入本地仓库，本地安装即可 


```
```bash
现象：手动修改observer.config.bin文件后启动失败
![image.png](/img/FAQ/all_faq/1670657367060-af9a5525-b9d6-448f-bdb8-a94e93f3ae9d.png)
报错：check data checksum failed(ret=-4103)
信息：3.1.4
解决方案：etc2和etc3下有备份文件，find /home/admin/oceanbase  |grep  "observer.conf"
/home/admin/oceanbase/etc3/observer.conf.bin
/home/admin/oceanbase/etc3/observer.conf.bin.history
/home/admin/oceanbase/etc2/observer.conf.bin
/home/admin/oceanbase/etc2/observer.conf.bin.history
/home/admin/oceanbase/etc/observer.config.bin
/home/admin/oceanbase/etc/observer.config.bin.history
原因：首先不支持直接手动修改该二进制文件，该文件配置参数是通过alter system方式持久化此处的，可以通过./bin/observer -o 参数=参数值的方式启动成功后也会持久化到该配置，当然该配置文件加上history一共有6份，可以cp etc2/observer.conf.bin etc/observer.config.bin也能恢复配置


```
```bash
现象：obd升级ob失败
报错：fail to get upgrade graph: ‘NoneType’ object has no attribute ‘version’
信息：3.1.0升级3.1.4
解决方案：重新搭建一套3.1.4ob环境，通过oms进行数据迁移
原因：3.1.0版本不支持本地升级，只能做数据迁移这种逻辑升级


```
```bash
现象：租户修改lower_case_table_names参数报错
报错：Variable 'lower_case_table_names'is a read only variable
信息：3.1.4
解决方案：使用系统租户root@sys操作
原因：mysql的只读变量在mysql里即使是root也不能修改，需要有主机访问权限的人在外部修改配置文件然后重启实例生效。ob的mysql租户是逻辑实例，没有独立进程，不存在重启操作。所以用有sys租户权限的超级管理员去修改租户的全局参数变量


```
```bash
现象：一次性导入大批数据报错租户内存不足
报错：ERROR: No memory or reach tenant memory limit
信息：3.1.4
解决方案：1）不能使用系统租户root@sys当业务租户使用；2）datafile_size不能太小，防止租户的磁盘被写满；3）租户内存阈值memstore_limit_percentage 调大，触发版本冻结的阈值freeze_trigger_percentage 调小，memstore写入限速阈值writing_throttling_trigger_percentage 设置70
原因：1）系统租户本身作为系统管理者，资源有限，不适合业务使用；2）磁盘写满，无法触发转储合并，租户内存会写满；3）通过参数调优，可以降低内存写满风险，转储合并过程也会顺利进行


```
```bash
现象：使用obclient通过obproxy连接ob报错obclient -h127.1 -uroot@proxysys -P2883，mysql客户端正常
报错：ERROR 2027 (HY000): received malformed packet
信息：ob：4.0.0，obclient：2.x
解决方案：暂时使用mysql客户端，下个迭代修复
原因：当前obclient 2.x使用root@proxysys用户连接到proxy之后，会发送SQL语句查询version，proxy会直接回复EOF包，这个包再obclient 1.x的时候是会被忽略，在obclient 2.x的时候会解析异常


```
```bash
现象：导入表的时候总是报Minor freeze not allowed now，且无法truncate表数据
报错：ERROR 4263 (HY000): Minor freeze not allowed now
WARN [STORAGE.BLKMGR] alloc_block (ob_block_manager.cpp:305) [9021][T1_MINI_MERGE][T1][YB42C0A81FCA-0005EC7D0FFFD8D6-0-0] [lt=22] Failed to alloc block from io device(ret=-4184)
WARN [STORAGE] alloc_block (ob_macro_block_writer.cpp:1132) [9021][T1_MINI_MERGE][T1][YB42C0A81FCA-0005EC7D0FFFD8D6-0-0] [lt=4] Fail to pre-alloc block for new macro block(ret=-4184, current_index=0, current_macro_seq=0)
信息：4.0.0
解决方案：增大对应租户的磁盘资源，alter resource unit xxx datafile_size='xxG'
原因：Minor freeze not allowed now指的是无法进行转储，原因一般有：
1）租户分配磁盘空间占满，内存的数据无法转储。
2）使用了系统租户当业务租户使用，系统租户资源较少，转储性能跟不上。
3）使用了业务租户，但该租户的内存资源较少，转储性能跟不上。

```
```bash
现象：部署报错[ERROR] OBD-1006: Failed to connect to oceanbase-ce
报错：observer.log日志：ERROR [CLOG] resize (ob_server_log_block_mgr.cpp:183) [2790][][T0][Y0-0000000000000000-0-0] [lt=5] The size of reserved disp space need greater than 1GB!!!(ret=-4007
信息：ob4.0.0
解决方案：配置文件中调大log_disk_size参数
原因：log_disk_size用于设置 Redo 日志磁盘的大小，不能小于1G的限制，默认为分配内存的3-4倍


```
```bash
报错：ERROR 2013 (HY000): Lost connection to MySQL server during query
ERROR 2006 (HY000): MySQL server has gone away
No connection. Trying to reconnect…
现象：单机demo部署，通过proxy连接数据库，经常出现断连现象，直连没问题
信息：4.0.0
解决方案：调大proxy_mem_limited参数500M或1G，alter proxyconfig set proxy_mem_limited ='1G';
原因：现在demo模式proxy给的内存（200M）比较少，可能有触发内存超限重启的问题，后续版本会优化。


```
```bash
报错：ERROR 2013 (HY000): Lost connection to MySQL server at 'reading authorization packet', system error: 0
现象：单机部署，启动失败，后通过连接obproxy连接报错
ob日志报错：ERROR [SERVER.OMT] alloc (ob_worker_pool.cpp:93) [24864][454][Y0-0000000000000000] [lt=22] [dc=0] worker cnt larger than max cnt(worker_cnt_=256, max_cnt_=256)
信息：3.1.4
解决方案：调大cpu_count这个配置项
原因：最低以cpu_count 16启动，如果修改过改参数可能导致无法启动


```
```bash
报错：ERROR [ELECT] leader_revoke (ob_election.cpp:2151) [42767][1849][Y0-0000000000000000] [lt=13] [dc=0] leader_revoke, please attention!(revoke reason=“clog sliding_window timeout”
现象：导数据到OB，期间出现切主报错信息
信息：4.0.0
解决方案：适当调大隐藏参数_ob_clog_timeout_to_force_switch_leader，oceanbase.__all_sys_parameter表查看隐藏参数信息
原因：Leader同步Clog超时导致切主了，可以尝试调小客户端压力，或者调大 _ob_clog_timeout_to_force_switch_leader，这是日志同步超时配置项，默认10秒，可适当调大，影响是leader异常时，相应的RTO也会变长


```
```bash
报错：java.lang.ClassNotFoundException: com.mysql.jdbc.Driver
现象：使用mysql驱动，连接报错找不到驱动信息![image.png](/img/FAQ/all_faq/1673165722440-5addaf82-c034-4e37-82cd-8c5f3842c84c.png)
信息：4.0.0
解决方案：修改驱动连接为jdbc:mysql
原因：mysql驱动写mysql的信息jdbc:mysql，如果是oceanbase驱动需要写oceanbase信息jdbc:oceanbase

```
```bash
现象：springboot项目使用ob数据库，数据库中字段是datetime格式，用bean接收sql查询的结果，bean中使用string来接收，出现2022-12-08 12:23:56.0，预期结果是2022-12-08 12:23:56
报错：结果不符合预期
信息：4.0.0
解决方案：对于OceanBase的datatime格式，建议使用Date的数据类型接收。如果想要用String接收OB的时间类型，建议尝试将数据库改为timestamp格式
原因：业务侧使用姿势问题，可以在应用层和数据库侧可进行调整。


```
```bash
报错：ERROR 1564:This partition function is not allowed
现象：给日期类型datetime的字段按年分区报错![image.png](/img/FAQ/all_faq/1685863518267-caae7175-4423-4415-a508-53517fac19e3.png)
信息：4.0.0
解决方案：可以按如下函数语法获取年/月
PARTITION BY RANGE( YEAR(dt) )
SUBPARTITION BY HASH( MONTH(dt) )
原因：原生mysql也如此表现，报错符合预期，Hash 分区键的表达式必须返回 INT 类型，left截取方式返回的是字符串类型。

```
```bash
现象：write only场景时，磁盘读在一个较高的频率，纯写场景为什么会有这个高的读操作？
![image.png](/img/FAQ/all_faq/1685871560930-10712934-79b6-4810-abc0-546f39cf2899.png)
报错：无报错
信息：4.0.0
解决方案：符合预期
原因：ob的存储整体是一个LSM架构，从上到下是memtable，minor sstable，major sstable，数据从memtable到sstable会写盘，从minor到minor/major会读盘 + 写盘，minor sstable累积一定数量后也会触发合并，合并需要重排列，需要读+写磁盘。


```
```bash
报错：observer日志报错：Fail to alloc data block, (ret=-9202)
现象：申请不到内存，合并超时，转储卡住
信息：ob3.1.0
解决方案：扩容或增大datafile_size或datafile_disk_percentage大小
原因：申请不到数据库，不一定是磁盘（df -h）被占满，可能是设置的datafile使用完了，可以通过查询__all_virtual_disk_stat查看ob整体磁盘占用信息。

```
```bash
现象：执行合并报错：ERROR 4179 (HY000): Operation not allowed now
报错：rootserver日志报错：enable_major_freeze is off, refuse to to major_freeze
信息：测试环境（3.1.4）
解决方案：打开合并参数 alter system set enable_major_freeze='true';
原因：默认是开启的，测试环境修改了参数，导致无法手动触发合并


```
```bash
报错：ERROR 1235 (0A000): Not supported feature or function
现象：sys租户设置数据备份路径报错Not supported feature or function
ALTER SYSTEM SET data_backup_dest=‘file:///data/bak’;
OB日志报错 ：WARN log_user_error_inner (ob_table_modify_op.cpp:1042) [7800][EvtHisUpdTask][T1][YB42ACAC1F54-0005F645ABA8855E-0-0] [lt=18] Data too long for column ‘value2’ at row 1
信息：OB4.1
解决方案：ALTER SYSTEM SET data_backup_dest=‘file:///data/bak/’ TEANT = $your_tenant_name;
原因：OB4.x版本为租户级别备份，且sys租户不能为其自身备份，sys租户登录操作的话，需要指定备份租户，其中log_archieve_dest/data_backup_dest也只能为业务租户备份设置。
```
```bash
报错：ERROR 9081 (HY000): the content of the format file at the destination does notmatch
现象：后台设置日志归档备份路径报错。
ALTERSYSTEMSET LOG_ARCHIVE_DEST='LOCATION=file:///data/bak' TENANT = fund; 
信息：OB4.1
解决方案：ALTER SYSTEM SET data_backup_dest='file:///data/bak/logdir';
原因：OB4.x版本，日志备份目录log_archive_dest和数据目录data_backup_dest不能在同级目录下，因为每一个路径下都有一个format校验文件。该文件记录该路径的一些相关属性，设计上不允许备份目录相同。


```
```bash
报错：ERROR 1235 (0A000): start log archive backup when not STOP is not supported
现象：ocp备份和手动发起ALTER SYSTEM ARCHIVELOG命令报错
信息：3.1.x
解决方案：
--关闭日志备份
ALTER SYSTEM NOARCHIVELOG;
--强制取消所有备份任务（备份目录会重置）
ALTER SYSTEM CANCEL ALL BACKUP FORCE;
--查看备份路径
SHOW PARAMETERS LIKE 'backup_dest'
--修改备份目录
ALTER SYSTEM SET backup_dest='file:///data/obbackup';
--启动日志备份
ALTER SYSTEM ARCHIVELOG
原因：日志备份进程仍在，不能重复执行备份，如果是初次全量备份，可以关停备份并清理备份任务和进程，重新发起

```
```bash
现象：启动日志备份后，很快就显示断流interrupted
报错：log archive status is interrupted, need manual process(sys_info={status:{tenant_id:1, copy_id:0, start_ts:1671070878595931, checkpoint_ts:1671070878595931, status:5, incarnation:1, round:3, status_str:“INTERRUPTED”, is_mark_deleted:false, is_mount_file_created:true, compatible:1, backup_piece_id:0, start_piece_id:0}, backup_dest:“file:///home/nfs_server/backup”}) 
信息：3.1.4
解决方案：1）nfs客户端配置错误；2）nfs服务器目录权限不够，
原因：1）ob最为nfs客户端，需要所有ob节点本地创建备份目录挂载到服务端的nfs的地址；2）将nfs服务器上backup目录修改属组nfsnobody:nfsnobody。或者777权限；

```
## OCP部署问题


```bash
现象：用ocp部署集群bootstrap ob的时候失败
报错：ERROR [CLOG] update_free_quota (ob_log_file_pool.cpp:465) [77084][0][Y0-0000000000000000] [lt=3] [dc=0] clog disk is almost full(type_=0, total_size=1888745000960, free_quota=-198549053440, warn_percent(%)=80, limit_percent=95, used_percent(%)=90)
信息：3.1.4
解决方案：ocp下发部署时安装路径参数home_path 、data、redo 采用分盘部署，不要混部到一块盘上，如果是简单测试，可以把datafile_size 或者 datafile_disk_percentage给调小
原因：数据文件采用预占用方式，同盘会出现磁盘空间不足问题，生产环境必须分盘，测试环境可降低占用比例

```
```bash
现象：OCP部署集群时卡在Bootstrap ob阶段
报错：日志提示try adjust config ‘memory_limit’ or ‘system_memory’
信息：4.0.0
解决方案：调小memory_limit 和 system_memory 参数
原因：system_memory 默认是30G，memory_limit_percentage默认是占用80%物理内存，如果不指定这2个参数配置信息，小规格服务器配置可能出现申请不到内存问题。

```
```bash
报错：ocp precheck failed
现象：部署OCP报错预检测失败
![image.png](/img/FAQ/all_faq/1677054083006-45acdf0a-de75-4077-962c-cb64ed224fe1.png)
信息：OCP4.0
解决方案：关闭预检参数precheck_ignore: true
原因：预检测会检查服务器硬件资源是否符合生产环境标准，如果不通过会报此错误，测试环境建议关闭预检测功能。

```
```bash
报错：1146(42S02)：Table‘meta_database.compute_vpc’ doesn’t exist
现象：OCP部署过程中遇到了meta data的一个表不存在的问题
信息： v3.3.0 
解决方案：可以先去ocp meta 租户的库中确认一下是否已经存在 compute_vpc 这张表，如果存在，把其他的表删除，仅保留compute_vpc，然后重新初始化ocp。
原因：这个是 OCP 3.3.0 一个已知bug问题，已发布 bp 版本中已经修复。

```
```bash
报错：failed to load docker image
现象：ocp部署时加载docker镜像流程失败
![image.png](/img/FAQ/all_faq/1668845077153-b9ff9a57-0209-4bcd-be4d-8a8c2e1b61a4.png)
信息：3.3.0
解决方案：检查ssh互信是否正常
原因：安装的时候是当成远程主机，需要涉及节点之间ssh互信

```
```bash
现象：ocp部署obproxy时，check if process not exit阶段失败
![image.png](/img/FAQ/all_faq/1670131241278-78cdfae8-ae76-4960-8b72-c508421880d9.png)
报错：status=500 INTERNAL_SERVER_ERROR, errorCode=COMMON_UNEXPECTED, args=process obproxy should not exists on host
信息：4.0
解决方案：更换默认的2883端口，或者卸载该节点已经部署的obproxy服务
原因：该节点已经存在了obproxy服务和进程了


```
```bash
报错：2003：Can't connect to MySQL on 'xx.xx.xx.xx' (111  Connection refused)
现象：ocp部署报错无法连接到metadb
![image.png](/img/FAQ/all_faq/1670161481022-cbfd877d-fc4c-4d16-8ce4-028e43bbabb7.png)
信息：3.3.0
解决方案：配置文件create_metadb_cluster设置为true，默认安装个metadb数据库
原因：create_metadb_cluster参数默认为false，会使用配置中ob_cluster模块信息当metadb，实际现场不存在。


```
```bash
报错：create monitor tenant failed
现象：ocp安装时报错resource not enough:memory(Avail:1.6G,Need:8.0G)
信息：3.3.0
解决方案：custom_config模块中memory_limit设置为服务器free -g可用内存范围以内
原因：memory_limit默认使用物理总内存80%，但如果可用内存不足，会出现创建租户时申请不到资源

```
```bash
报错：PermissionError: [Errno 13] Permission denied: '/root/installer/config.yaml'
现象：ocp部署报错/root/installer/config.yaml没有权限？
![image.png](/img/FAQ/all_faq/1670572772711-54b3f21a-e127-4cd9-9e35-e977d8d429be.png)
信息：4.0.0
解决方案：关闭selinux
原因：selinux会影响程序的访问文件，影响程序的服务程序功能，影响服务所使用的资源，部署前需要关闭。

```
```bash
报错：NameError: name ‘traceback’ is not defined
现象：ocp部署到create meta user阶段报错
![image.png](/img/FAQ/all_faq/1670661197103-eb4f28ed-0eb6-4277-b719-417938c4419f.png)
信息：3.3.0
解决方案：检查/tmp目录下是否有precheck-*.sh文件，如果有，则删除
原因：-*是当时生成的uuid，可能找不到对应的文件导致，删除重新部署会产生新的文件

```
```bash
报错：Access denied for user 'meta_user'@'xxx.xxx.xxx.xxx
现象：metadb初始化阶段报错连接不上ob
![image.png](/img/FAQ/all_faq/1670846524338-9faab5d8-3ce6-4b85-bf50-7e128b4b28c4.png)
信息：3.3.0
解决方案：内存不足导致，增加内存。
原因：仅仅只有5G内存，此处报错不一定是连接密码问题，启动容器失败，还未到连接抛出异常

```
```bash
现象：ocp安装过程中报错sudo命令找不到，最终导致failed to load docker image
![image.png](/img/FAQ/all_faq/1671331216078-9b93aeea-70a6-43dc-8997-8ae5ee4453d7.png)
报错：/bin/sh: sudo: command not found
信息：ocp：4.0.0
解决方案：config.yaml的ssh模块配置上密码信息
原因：暂未复现，用户配置环境问题影响较大

```
## OCP使用问题


```bash
现象：OCP添加主机，无法安装ocp agent服务
![image.png](/img/FAQ/all_faq/1685439897417-af7f8be4-67bc-4a68-af42-3ddb52ee120f.png)
报错：args:/tmp/8c76f061414e4d6/pos.py uninstall_package ^t-oceanbase-ocp-agent, return code:2, output:failed to call pos: func=uninstall_package, args=['^t-oceanbase-ocp-agent'], code=2, output=/tmp/a463f6de-fde4-11ed-8e6e-fefcfeb8fb: line 1: unexpected EOF while looking for matching `''
信息：OCP4.0.3
解决方案：卸载dpkg命令，rpm -qa|grep dpkg , rpm -e --nodeps $dpkg
原因：现场是centos7系统，安装ocp-agent前会做操作系统检查，dpkg命令会错误的判断现场操作系统类型。


```
```bash
现象：使用ocp部署obproxy后，使用obproxy连接报错密码错误
![image.png](/img/FAQ/all_faq/1683272882395-d8607cdb-3ef2-4e33-a783-2ef2d69cd1ee.png)
报错：查看obproxy日志显示，fail to get cluster name(ret=-4018)
信息：obproxy4.1
解决方案：-u参数写完整，带上集群名称，-uroot@sys#集群名称
原因：ocp部署的obproxy虽然关联了ob集群，但是

```
```bash
现象：添加主机报错，没有找到指定OCP Agent类型的记录
![image.png](/img/FAQ/all_faq/1679294604936-21502072-f996-49b8-a69c-2ea5a9583539.png)
报错：No route to host (Host unreachable)
![image.png](/img/FAQ/all_faq/1679294670666-987ea895-1277-4cb1-bd5d-1761b065e343.png)
信息：OCP4.0
解决方案：关闭防火墙即可
原因：添加主机防火墙开启，安装程序路由不到该节点。

```
```bash
现象：ocp接管报错
报错：observer进程属于root，需要是admin账号
信息：4.0
解决方案：可参看接管案例
[https://github.com/oceanbase/obdeploy/blob/master/docs/zh-CN/3.user-guide/4.OCP-takeover-OBD-deployment-cluster.md](https://github.com/oceanbase/obdeploy/blob/master/docs/zh-CN/3.user-guide/4.OCP-takeover-OBD-deployment-cluster.md)
[https://ask.oceanbase.com/t/topic/30100012](https://ask.oceanbase.com/t/topic/30100012)
原因：ocp接管限制中，要求observer进程启动用户是admin


```
```bash
报错：ob install pre check failed，maybe from another observer
现象：ocp上部署ob时，Pre check for install ob阶段异常
信息：3.3.0
解决方案：卸载ob节点上的observer，或者使用未部署过ob的资源环境
原因：ocp不支持在已有observer集群环境再部署一套ob集群

```
```bash
现象：ocp部署的ob，使用root用户后台启动observer后，重新ocp或者后台admin用户启动失败
![image.png](/img/FAQ/all_faq/1670653105470-80824e4b-b9d5-4b23-8c53-fbcd928294c4.png)
报错： ERROR [COMMON] inner_open_fd (ob_log_disk_manager.cpp:1043) [5889][0][Y0-0000000000000000] [lt=5] [dc=0] open file fail(ret=-4009, fname="/home/admin/oceanbase/store/lzq/slog/4", flag=1069122, errno=13, errmsg="Permission denied")
ERROR [SERVER] init (ob_server.cpp:172) [4195][0][Y0-0000000000000000] [lt=2] init config fail(ret=-4009)
信息：3.1.2
解决方案：修改所有 observer 目录的权限
chown -R admin.admin /home/admin/oceanbase/
chown -R admin.admin /data/1
chown -R admin.admin /data/log1
原因：ocp部署或接管的ob，均是admin用户权限，使用root启用后会导致observer.conf.bin文件和redo日志目录下的文件权限变更，无法再使用admin启动成功

```
```bash
现象：部署OB时安装路径检测失败
![image.png](/img/FAQ/all_faq/1670662138500-5195c3f6-80ae-4640-b1ba-267a878f3bf3.png)
报错：权限不足
![image.png](/img/FAQ/all_faq/1670662200520-accb94fc-e87a-4ea8-9dde-c0dd62b7c363.png)
信息：3.3.0
解决方案：chown -R admin:admin /data &&  chown -R admin:admin /redo && chown -R admin:admin /home/admin 
原因：安装和数据目录需要递归admin用户权限


```
```bash
报错：OBProxy proxyro 用户密码与 OCP 设置不相同
现象：OCP接管集群时，使用proxy连接ocp报错
信息：ocp:3.3.0，ob:3.1.4，不适用于OCP4.0.3版本
解决方案：curl --user admin:aaAA11__ -X POST "http://xx.xx.xx.xx:8080/api/v2/obproxy/password" -H "Content-Type:application/json" -d '{"username":"proxyro","password":"*****"}'
说明：--user  ：OCP白屏登录用户:密码
  xx.xx.xx.xx:8080：OCP地址:端口
*****：obd部署配置文件中observer_sys_password参数的密码
原因：OCP初始的proxyro密码是空，如果ob设置了proxyro密码，需要ocp也保持一致


```
```bash
报错：OBProxy proxyro 用户密码与 OCP 设置不相同
现象：接管obd部署的集群时，使用proxy连接ocp报错
信息：OCP4.0.3，OB4.x
解决方式：obd cluster edit-config 部署名称
修改proxyro_password和observer_sys_password一致为 3u^0kCdpE   再重载 obd cluster reload 部署名称
原因：ocp4.0.3废弃了/api/v2/obproxy/password接口，无法通过接口方式保持两端密码一致。后续版本会优化。


```
```bash
现象：接管obd部署ob集群失败
![image.png](/img/FAQ/all_faq/1671164550736-33458eb8-886e-4ad5-b0c2-900d895e514f.png)
报错：操作OB失败，错误信息: (conn=10) Table 'oceanbase.v$ob_cluster' doesn't exist
信息：ocp:3.3.0，ob:4.0.0
解决方案：升级ocp版本到4.0.0
原因：ob4.0.0.0版本架构变动较大，部分系统表进行调整，使用不配套的ocp版本无法接管

```
```bash
现象：OCP怎么把管理的OB移除
可以通过调用后端restful api的方式来迁出OB集群。
命令如下：
curl -X POST --user {user}:{password} -H "Content-Type:application/json" -d '{}' "http://{ocp-url}：{port}/api/v2/ob/clusters/{cluster_id}/moveOut"
报错：无
信息：OCP3.x-4.x
解决方案：curl -X POST --user {user}:{password} -H "Content-Type:application/json" -d '{}' "http://{ocp-url}：{port}/api/v2/ob/clusters/{cluster_id}/moveOut"  示例如下：
curl -X POST --user admin:aaAA11__ -H "Content-Type:application/json" -d '{}' "http://11.11.11.11:8080/api/v2/ob/clusters/2/moveOut"
原因：ocp暂不支持迁出部署和接管的集群，但可以使用接口实现，并且迁出的集群不管是obd部署的还是ocp部署的均能再次接管。后续版本会增加迁出集群功能。
注意，上面的cluster_id指的是ocp页面浏览器地址行中的那个ob集群资源id
![image.png](/img/FAQ/all_faq/1669197720558-6ef785ab-5fa6-44ac-be09-b479f0a6fe7a.png)


```
```bash
报错：所在idc与目标observer的idc不匹配
现象：接管集群报错信息不匹配
信息：4.0.0
解决方案：主机列表删掉对应的节点信息，重新接管
原因：因为接管前已经人为将节点添加到主机列表中了，列表中的idc机房和region地区信息和接管的集群默认信息不一致，当然也可以登录ob通过sql修改idc和region信息alter system alter zone 'zone1' set idc = 'xx;alter system alter zone 'zone1' set region = 'xx';

```
```bash
报错：Connect to xx.xx.xx.xx:62888 [/xx.xx.xx.xx] failed: Connection refused (Connection refused）
现象：ocp上重启集群卡住，obd重启ob是正常的
![image.png](/img/FAQ/all_faq/1671330730151-a86d1780-969f-469c-b778-0603e730219e.png)
信息：ocp3.3.0，ob3.1.4
解决方案：重启主机节点的ocp-agent服务
原因：ocp-agent服务的端口62888，报错连接被拒绝，基本为ocp-agent服务异常无法通过agent服务下发指令导致

```
```bash
现象：OCP关闭集群页面报错403
报错：集群obcluster不允许进行操作
![image.png](/img/FAQ/all_faq/1676864800701-308adf5b-2473-4c80-8219-2e5aa7b50029.png)
信息：OCP4.0
解决方案：不能使用OCP停止接管的metadb
原因：metadb被接管，如果使用OCP管理该集群会导致OCP服务不可用


```
```bash
报错：plan cache memory used reach limit
现象：在业务租户有批量或者手工导数期间，偶然性会有上述告警
信息：OB3.1.4
解决方案：调整ob_plan_cache_percentage阈值，show variables like'%ob_plan_cache_percentage%'; set global  ob_plan_cache_percentage =10;
原因：如果租户内存设置太小，或者批量导数条数不一致可能出现此类报错，扩容租户内存或调整ob_plan_cache_percentage阀值。


```
```bash
报错：OCP操作页面报错403
现象：通过OCP删除租户下的test1库，提示不允许进行该操作？
![image.png](/img/FAQ/all_faq/1684810148859-3d4ebd0c-5c6f-4efb-9c09-e8be5d825fca.png)
信息：ocp4.x
解决方案：ocp meta租户下操作 update config_properties set value='' where `key`='ocp.ob.cluster.ops.blacklist';
原因：此集群和metadb共用，ocp metadb默认是不允许通过ocp做运维操作的，如果希望支持，需要将黑名单对应的value置空。
```
```bash
现象：ocp升级失败，报错yaml格式不正确
![image.png](/img/FAQ/all_faq/1665307305238-ceb2f464-3711-42c4-8ac1-698ef8c880fe.png)
报错：yaml line column
信息：OCP3.1.1升级3.3.0
解决方案：按3.3.0版本配置模版文件改写
原因：3.1.1和3.3.0的配置文件格式差异较大，升级需要使用3.3.0的配置格式

```
```bash
报错：KeyError：'metadb'
现象：ocp升级失败，报错metadb的key值找不到
![image.png](/img/FAQ/all_faq/1665307499897-36b98b84-243b-40df-8401-63c72484e1eb.png?x-oss-process=image/format,png)
信息：OCP3.1.1升级3.3.0
解决方案：保留config.yaml配置文件中的metadb模块信息，不得注释或删除，并改写正确
原因：文档描述config.yaml中的metadb模块是部署时候使用的参数，实际升级也是需要的通过此模块信息进行连接验证的，不能删掉或注释改模块的内容

```
```bash
报错：Can't connect to MySQL server on xx.xx.xx.xx:2883(-2 Name or server not know)
现象：ocp升级失败，使用obproxy配置测试可以连接，但升级报错连接不上metadb
![image.png](/img/FAQ/all_faq/1665308205549-d0ee5e92-5853-4024-b003-8fd9b28ff3bb.png)
信息：OCP3.1.1升级3.3.0
解决方案：该版本未采用proxy方式连接metadb，配置改为使用直连方式
原因：107是obproxy的地址，非metadb地址，程序的这一步是要ssh到ocp节点IP，运行ocp的docker命令指向的地址是该IP，但107非ob元数据库也非ocp地址，所以报错连不上，因此不能使用非metadb本机的proxy地址。当然这里的版本升级也不建议使用proxy连接的方式

```
```bash
现象：ocp升级失败，报错连接不上metadb
报错：Can't connect to MySQL server on xx.xx.xx.xx:2881(-2 Name or server not know)
![image.png](/img/FAQ/all_faq/1665308040282-69fbfbc7-38cc-4b1b-9ab8-7542921f6f0f.png)
信息：3.1.1升级3.3.0
解决方案：yaml配置文件metadb模块使用直连方式，去掉租户的#集群信息
原因：3.1.1版本配置是直连方案，3.3.0版本配置是proxy连接方案，升级需要保持原方案，直连不能带集群名称，否则会被误把 “租户#集群名称” 解析成租户。
```
```bash

```
```bash
现象：ocp上创建的unit规格配置不显示？
报错：无报错
信息：OCP3.x-4.x
解决方案：ocp 的 meta 租户，ocp 库中通过如下参数控制：select * from config_properties where key like ‘%small%’ \G，在特殊情况下，比如demo演示等非生产环境，可以将其改为 true，就会取消这个限制，
UPDATE config_properties SET value='true' WHERE `key` = 'ocp.operation.ob.tenant.allow-small-unit';
原因：小于 5G 的 Unit 规格在前端展示的时候会被过滤掉

```
```bash
报错：KeyError: 'buildVersion'
现象：ocp升级4.0报错
![image.png](/img/FAQ/all_faq/1671178876425-4f89a6d7-14e7-4c5f-81a7-7b45d832204b.png)
信息：3.3.0升级4.0.0
解决方案：cinfig.yaml文件auth模块的信息改为ocp白屏登录用户和密码
原因：升级需要调用接口连接ocp，使用的账户为配置文件中的auth模块信息，auth模块配置只有升级过程会用到。如果做过ocp白屏登录密码修改，该配置中也修改即可。

```
```bash
现象：replace方式修改ocp的obproxy地址或端口失败或者监控信息不显示
报错：ocp.log报错：connection refused
![image.png](/img/FAQ/all_faq/1686110140303-58e9a553-23c2-4479-a548-cfc17e432d9a.png)
信息：OCP3.x-4.x
解决方案：meta租户连接metadb，修改monitordb相关连接信息。
select * from config_properties where `key` like '%ocp.monitordb.port%';
select * from config_properties where `key` like '%ocp.monitordb.host%';
原因：设计原因，replace未修改monitor监控相关连接参数，后续版本考虑优化。

```
## OMS使用问题


```bash
报错：NNER_ERROR[CM-RESONF000003]: no enough host resource for a CHECKER, reason [host: IP unavailable cause: current memory usage 0.8573829 exceed limited 0.85]
现象：OMS在迁移多张大表到OB后，checker组件报错
信息：oms：3.3.1
解决方案：迁移项目详情页点击查看组件监控，更新checker的配置中的 task.checker_jvm_param 调整 jvm 参数
原因：迁移大表较多，使用默认的checker组件资源过小，可以调整组件的jvm参数


```
```bash
报错：INVALID_STATUS_ERROR [INVALID_STATUS_ERROR] {“message”:“Not found ocp name from connectInfo: jdbc:oceanbase://xx.xx.xx.xx:2883?allowLoadLocalInfile=false&autoDeserialize=false&allowLocalInfile=false&allowUrlInLocalInfile=false&useSSL=false”}
现象：OMS启动增量同步报错
信息：3.3.0
解决方案：在数据源的高级选项里配置 configUrl
原因：启动增量任务没找到ocp或者configUrl，检查一下数据源里有没有配置 OCP 或者 configUrl

```
## OBProxy问题

```bash
报错： obproxy's memroy is out of limit's 80% !!!
现象：使用obproxy做大批量插入时候，应用报错Connection reset by peer
信息：OBP4.x
解决方案：使用 root@proxysys 账号2883端口连接到 OBProxy，修改ALTER proxyconfig SET proxy_mem_limited = 6G; 
原因：通过报错信息看是obproxy的内存超出了，但最终问题是现场插入是未做连接回收引发多种问题，可以借鉴。

```
## ODC问题

```bash
报错：BadRequest exception, type=IllegalArgumentException, message=Not a valid secret key
现象：ODC新建连接报错
![image.png](/img/FAQ/all_faq/1670485766785-ba8e1d24-8779-4102-9ade-ba1a04cdf1e9.png)
信息：ODC3.x
解决方案：下载最新ODC版本，推荐使用内置 jre 的 ODC 版本
原因：JRE 版本过低，出现加密失败导致，JRE选择了1.8.121以上版本


```
```bash
报错：[com.alipay.odc.config.BeanCreateFailedAnalyzer][21]: bean create failed
现象：mac安装odc报错
信息：ODC3.3.2
解决方案：推荐使用自带 JDK 的版本，最新版下载地址
原因：有可能是 JAVA 版本的问题

```
```bash
现象：ODC 客户端报 ： (conn=33406) Query timed out 错误
报错：ErrorCode = 1317, SQLState = 70100, Details = (conn=405741) Query timed out
信息：ODC3.2.3
解决方案：需要在连接详情界面更改“SQL 查询超时时间”，使其大于SQL的实际执行时间：
![image.png](/img/FAQ/all_faq/1670659134978-31a3f79b-c8f8-446f-b5b4-6ee80a5683a5.png)
原因：SQL的执行时间超过了 ODC 在驱动层设定的超时时间导致，ODC工具自身有个 “SQL 查询超时时间” 设置

```
```bash
现象：刚部署的ODC打开保存Java进程异常退出
![image.png](/img/FAQ/all_faq/1685864534556-2172dfa7-d407-4d33-b8cc-c409a3b2c97d.png)
报错：Error creating bean with name 'dataSource' defined in class path resource [org/springframework/boot/autoconfigure/jdbc/DataSourceConfiguration$Hikari.class]: Initialization of bean failed; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.boot.autoconfigure.jdbc.DataSourceInitializerInvoker': Invocation of init method failed; nested exception is org.springframework.jdbc.datasource.init.UncategorizedScriptException: Failed to execute database script; nested exception is org.springframework.jdbc.CannotGetJdbcConnectionException: Failed to obtain JDBC Connection; nested exception is org.h2.jdbc.JdbcSQLNonTransientException: General error: "java.lang.IllegalStateException: Chunk 3233 not found [1.4.200/9]" [50000-200] 
信息：3.2.3
解决方案：H2修复方式参考如下
![image.png](/img/FAQ/all_faq/1685864853944-65066b28-0894-4253-aa31-6b1c5d2aa699.png)
原因：桌面版的 H2 database 元数据库损坏导致。

```
## OBDUMP/LOADER使用问题

```bash
Q：如何解决使用 OBLOADER 导入时遇到 OOM 错误？
A：首先修改 bin/obloader 脚本中的 JAVA 虚拟机内存参数。其次排除 OpenJDK GC Bug。
```
```bash
Q：如何在调试模式下运行 OBLOADER 排查问题？
A：直接运行 bin/obloader-debug 进行导入。
```
```bash
Q：如何使用 OBLOADER 导入与表不同名的数据文件？
A：命令行中的 -f 选项指定为数据文件的绝对路径。例如：--table 'test' -f '/output/hello.csv'。
```
```bash
Q：为表配置了控制文件，导入的数据为什么没有生效？
A：要求控制文件的名称与表名相同且大小写一致。MySQL 默认表名为小写，Oracle 默认表名为大写。
```
```bash
Q：运行 OBLOADER 脚本时，命令行选项未被正常解析的原因是什么？
A：可能是命令行参数值中存在特殊符号。例如：Linux 平台上运行导数工具，密码中存在大于号（注：> 是重定向符），导致运行的日志都会出现丢失。因此在不同的运行平台使用正确的引号进行处理（参见下个问题）。
```
```bash
Q：运行 OBLOADER 脚本时，何时需要对参数加单引号或者双引号？
A：建议用户在一些字符串的参数值左右加上相应的引号。

- Windows 平台参数使用双引号。例如：--table "*"。
- 类 Linux 平台参数使用单引号。例如：--table '*'。
```
```bash
Q：外部文件格式不符合要求导致导入失败，应该如何解决？
A：导入外部文件时对格式有以下要求：

- 外部文件如果为 SQL 文件，要求 SQL 文件中不能有注释和 SET 开关语句等，并且文件中只能有 INSERT 语句，每条语句不可以换行。除此以外，文件中存在 DDL 或者 DML 语句，建议使用 MySQL source 命令导入。
- 外部文件如果为 CSV 文件，CSV 文件需符合标准定义。要求有转义符、定界符、列分隔符和行分隔符。数据中存在定界符需要指定转义符。
```
```bash
Q：OBLOADER 启动报错：Access denied for user 'root'@'xxx.xxx.xxx.xxx'。
A：导数工具默认依赖 root@sys 用户和密码。如果集群中已为 root@sys 用户设置的密码，请在命令行中指定 --sys-password 选项的参数值为 root@sys 用户设置的密码。
```
```bash
Q：OBLOADER 运行报错：Over tenant memory limits 或者 No memory or reach tenant memory limit。
A：调大全局 SQL 工作区的内存比例或者减少 --thread 并发数。
set global ob_sql_work_area_percentage=30; -- Default 5 
```
```bash
Q：OBLOADER 运行报错：No tables are exists in the schema: "xxx"。
A：--table 't1,t2' 选项指定的表名一定是数据库中已经定义的表名，且大小写需要保持一致。MySQL 模式下默认表名为小写，Oracle 模式下默认表名为大写。如果 Oracle 中定义的表名为小写，表名左右需要使用中括号。例如：--table '[t1]' 表示小写的表名。
```
```bash
Q：OBLOADER 运行报错：The xxx files are not found in the path: "xxx"。
A：要求 -f 指定的目录中的数据文件的名称与表名相同且大小写一致。MySQL 模式下默认表名为小写，Oracle 模式下默认表名为大写。例如：--table 't1'目录中的数据文件须为 t1.csv 或者 t1.sql，不能是 T1.csv 或者其它的文件名。
```
```bash
Q：OBLOADER 运行报错：The manifest file: "xxx" is missing。
A：元数据文件 MANIFEST.bin 是 OBDUMPER 导出时产生的。使用其它工具导出时没有元数据文件。用户指定 --external-data 选项可跳过检查元数据文件。
```
```bash
Q：OBLOADER 导入 Delimited Text 格式时报错：Index：0，Size：0。
A：出现这种错误的原因是数据中存在回车符/换行符，请先使用脚本删除数据中的回车符/换行符后再导入数据。
```
```bash
Q：OceanBase MySQL 模式下，连接 ODP (Sharding) 逻辑库导入 KEY 分区表数据时，OceanBase Database Proxy (ODP) 显示内存不足且 OBLOADER 运行报错：socket was closed by server。
A：设置 proxy_mem_limited 参数的权限，确认是否有外部依赖，ODP 默认内存限制为 2GB。连接 ODP (Sharding) 逻辑库且通过 OBLOADER 导入数据时需要使用 root@proxysys 账号权限，修改逻辑库内存限制语句如下：
ALTER proxyconfig SET proxy_mem_limited = xxg
```
```bash
Q：如何解决使用 OBDUMPER 导出时遇到 OOM 错误？
A：首先修改 bin/obdumper 脚本中的 JAVA 虚拟机内存参数。其次排除 OpenJDK GC Bug。
```
```bash
Q：如何在调试模式下运行 OBDUMPER 排查问题？
A：直接运行 bin 目录下的调试脚本。
例如：obdumper-debug。
```
```bash
Q：为表配置了控制文件，导入或者导出的数据为什么没有生效？
A：要求控制文件的名称与表名相同且大小写一致。MySQL 默认表名为小写，Oracle 默认表名为大写。
```
```bash
Q：OBDUMPER 导出数据时，为什么空表未产生空数据文件？
A：默认空表不会产生对应的空文件。--retain-empty-files 选项可保留空表所对应的空文件。
```
```bash
Q：OBDUMPER 运行脚本时，命令行参数未被正常解析的原因是什么？
A：可能是命令行参数中存在特殊符号。Linux 平台上运行 OBDUMPER，密码中存在大于号（> 是重定向符），导致运行的日志出现丢失。因此在不同的运行平台请使用正确的引号。
```
```bash
Q：OBDUMPER 指定 --query-sql '大查询语句' 导出数据过程中报错：Connection reset。
A：登入 sys 租户，将 ODP 配置参数 client_tcp_user_timeout 和 server_tcp_user_timeout 设置为 0。
```
```bash
Q：OBDUMPER 启动报错：Access denied for user 'root'@'xxx.xxx.xxx.xxx'。
A：OBDUMPER 默认依赖 root@sys 用户和密码。如果集群中已为 root@sys 用户设置密码，请在命令行中输入 --sys-password 选项并指定正确的 root@sys 用户的密码。
```
```bash
Q：OBDUMPER 运行报错：The target directory: "xxx" is not empty。
A：为防止数据覆盖，导出数据前，OBDUMPER 会检查输出目录是否为空（--skip-check-dir 选项可跳过此检查）。
```
```bash
Q：OBDUMPER 运行报错：Request to read too old versioned data。
A：当前查询所依赖的数据版本已经被回收，用户需要根据查询设置 UNDO 的保留时间。
例如：set global undo_retention=xxx。默认单位：秒。
```
```bash
Q：OBDUMPER 运行报错：ChunkServer out of disk space。
A：由于 _temporary_file_io_area_size 参数值过小引起存储块溢出错误，可修改该系统配置参数。
例如：使用 _temporary_file_io_area_size 参数 SELECT * FROM oceanbase.__all_virtual_sys_parameter_stat WHERE name='_temporary_file_io_area_size'; 查询该参数值，修改该参数值 ALTER SYSTEM SET _temporary_file_io_area_size = 20;。
```
```bash
Q：OBDUMPER 查询视图报错：SELECT command denied to user 'xxx'@'%' for table SYS.XXX。
A：由于用户无访问内部表或者视图的权限，需要运行语句 GRANT SELECT SYS.XXX TO xxx; 为用户进行授权。
```
```bash
Q：obloader怎么一次性加载多个文件导入？
A：数据文件放置一个文件夹内，-f指定文件夹即可

```
```bash
报错：Invalid usage long options max width 60. Value must not exceed width
现象：执行obdumper --version报错
信息：ob-loader-dumper-3.0.0-RELEASE-ce
解决方案：编辑obdumper脚本，增加参数-Dpicocli.usage.width=180
![image.png](/img/FAQ/all_faq/1673161412003-95726b07-dd81-4714-a19d-b91d42b29408.png)
原因：已知的命令行框架bug，ob-loader-dumper-4.0.0版本修复

```
```bash
报错：Invalid usage long options max width 60. Value must not exceed width(55) - 20
现象：在执行obloader导入时遇到如下错误
![image.png](/img/FAQ/all_faq/1671345885973-1fddcabc-cd28-4140-a522-d821a9d30843.png)
信息：3.0.0
解决方案：运行脚本中添加jvm启动参数 -Dpicocli.usage.width=180
原因：由命令行框架导致的

```
## OBLOGPROXY部署问题


```bash
报错：Failed to create oblogreader
现象：使用canal同步ob数据，oblogproxy部署后，oblogreader进程未启动
![image.png](/img/FAQ/all_faq/1685861368736-f1250a43-f7ff-4c25-9b58-5eec663599ea.png)
信息：OB4.0 ，oblogproxy1.0.0
解决办法：使用最新oblogproxy1.1.0版本即可
原因：1.0.0版本是3.x版本适配，4.0使用1.1.0版本，注意：建议使用oblogproxy最新版本。

```
## 其他


```bash
报错：mysqltest: At line 85: Version format 4 has not yet been implemented
现象：mytest执行失败
信息：OBD(1.5.0)
解决方案：更新最新的obclient
原因：获取的mysqltest的二进制版本不是最新的，可以更新obclient，获取最新的mysqltest可执行文件

```
```bash
报错：The table charset: ''latin1" is unsupported in OBMYSQL_2.2.50(2.2.50). Object: test.t2
现象：dbcat迁移数据到ob经常报错The table charset: ''latin1" is unsupported
信息：3.1.x
解决方案：使用最新的4.x社区版本，或者保证字符集保持一致
原因：3.x 社区版本支持的字符集非常有限, 社区版4.x 后, 常见的字符集基本上都支持了, 就不会有该问题，当从MySQL中导出数据时，要确保数据库里的字符都能正确输出到文件中。通过vim 命令下的 :set fileencoding 命令可以查看文件的编码。一般建议都是utf-8。 这样通过文件迁移MySQL数据时就不会出现乱码现象。解决方法1. 修改/etc/my.cnf 文件 参考文档：[centos 修改 mysql 字符集 - 一像素 - 博客园](https://www.cnblogs.com/onepixel/p/9154884.html) 2. 如果还不行，则因为上面只修改了数据库编码，而表的默认编码没有修改，再执行alter table t1 convert to charset uft8 就可以了。3. 如果还不行就重新创建数据库，一开始就指定好编码： create database test character set utf8 collate utf8_general_ci;

```




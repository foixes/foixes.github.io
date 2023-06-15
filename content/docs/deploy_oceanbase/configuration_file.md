---
title: 配置文件
weight: 3
---
# 配置文件
<!-- 这篇文档需要重新写吧 -->
## 基础配置说明

| 配置 | 默认值 | 推荐值 | 详细解释 |
| --- | --- | --- | --- |
| memory_limit | 0 | 系统总内存的80%~90%。 | 能从OS 获取的最大内存。也可以通过 memory_limit_percentage 设置占用百分比。 |
| system_memory | 企业版：30G 社区版：0M，未配置的情况下会自适应设置。 | memory_limit的10%。 | 表示从memory_limit划出指定大小的内存做保留系统内存。 |
| datafile_size | 0 | 与日志共用磁盘时设为磁盘空间的60%，独占磁盘时设为磁盘空间的90%。 | 表示数据文件的大小，observer 启动后会立即从磁盘上将指定的大小占用。优先于datafile_disk_percentage配置项。 |
| log_disk_size | 0 | 参照 datafile_size。 | 表示 Redo 日志磁盘的大小，observer 启动后会立即从磁盘上将指定的大小占用。也可以通过 log_disk_percentage 设置占用百分比。 |
| appname | obcluster | 必须跟obproxy部分的obproxy->global->cluster_name保持一致。 | 指定的是OceanBase集群的名字。 |
| devname | eth0 | 根据实际情况填写。 | 这个是跟 servers 里指定的 IP 对应的网卡。如果前面 IP 是 127.0.0.1 ，那么这里就填  lo 。通过 ip addr 命令可以查看 IP 和网卡对应关系。 |
| cluster_id | 1 | 不同集群不要重复即可。 | OceanBase 集群ID 标识。 |
| syslog_level | INFO | WARN 或 ERROR。 | 运行日志的日志级别，有 INFO 、WARN、 ERROR 等几个级别。级别越低，日志量越大。进程 observer的日志量非常大， |
| enable_syslog_recycle | TRUE |  | 指定运行日志是否以滚动方式输出，最多保留指定数量的运行日志。 |
| max_syslog_file_count | 4 |  | 根据磁盘空间大小定，这里默认保留最多 10 个历史运行日志文件。 |

## 基础配置文件

最新配置可以参照: xxx/.oceanbase-all-in-one/conf/distributed-with-obproxy-example.yaml，其中如果在 admin 用户下，xxx 默认为 /home/admin。

```shell
#如果中控机（obd机器）是远程访问observer，需要提供用户和密码或者用户和对应的公钥。（如果部署单节点，只有在ip为127.0.0.1，用户为当前用户时才不需要身份校验，其他时候需要填写正确的密码或者密钥。）
user:
   username: admin
#   password: your password if need
   key_file: /home/admin/.ssh/id_rsa.pub
#   port: your ssh port, default 22
#   timeout: ssh connection timeout (second), default 30
oceanbase-ce:
  servers: # 表示observer的节点，需要调整IP为自己的规划的observer节点IP
    - name: server1
      ip: x.x.x.x
    - name: server2
      ip: x.x.x.x
    - name: server3
      ip: x.x.x.x
  global:
    devname: eth0 # 表示observer节点对应的主机IP对应的网卡名
    memory_limit: 64G # 表示observer进程最大能使用的内存上限
    system_memory: 30G # 表示从memory_limit划出指定大小的内存做保留系统内存，在OceanBase中这部分内存不属于任何一个租户，在内部称之为500租户
    datafile_size: 200G # 表示OceanBase预分配的数据文件大小，会立即从磁盘上将指定的大小占用，目前改参数生效后不支持调小，只能调大，建议初始设置不宜太大，可分配磁盘空间50%的大小
    log_disk_size: 200G # 表示OceanBase预分配的redo日志文件大小，会立即从磁盘上将指定的大小占用。
    syslog_level: WARN # 默认是info级别，会生成大量的observer日志，可以根据需要调整WARN，或者ERROR级别
    enable_syslog_wf: false # Print system logs whose levels are higher than WARNING to a separate log file. The default value is true.
    enable_syslog_recycle: true # Enable auto system log recycling or not. The default value is false.
    max_syslog_file_count: 10 # 默认最多保留4个observer日志，每个observer日志文件256M，建议结合磁盘大小保留尽可能多的日志，避免有问题排查的时候发现日志已经被循环复用了
    appname: obcluster #指定的是OceanBase集群的名字，必须跟obproxy部分的obproxy->global->cluster_name保持一致。后续可以通过show parameters like 'cluster';查看，注意查看的时候并不是appname
    root_password: ****** # 将对应的注释打开，填写的是sys租户下的root用户的密码
    proxyro_password: ******  # 将对应的注释打开，填写的是sys租户下proxyro用户的密码
  server1: # 首先需要说明一下这个层级的server1/server2/server3 分别对应的是 oceanbase-ce -> servers -> name 名字可以修改，做到一一对应即可
    mysql_port: 2881 # 作为外部连接OceanBase的端口，默认是2881，可以自定义，在集群启动后不允许修改
    rpc_port: 2882 # 作为OceanBase的内部的rpc通信端口，默认是2882，可以自定义，在集群启动后不允许修改
    home_path: /home/admin/observer #OceanBase的工作目录，软件/lib/etc等都在这个路径下
    data_dir: /data # OceanBase的data目录,默认在$home_path/store，$home_path/store/$appname部署，建议软链接到独立盘部署
    redo_dir: /redo # OceanBase的redo目录，建议软链接到独立盘部署
    zone: zone1 # zone1 指定的observer对应的zone名称
  server2:
    mysql_port: 2881
    rpc_port: 2882
    home_path: /home/admin/observer
    data_dir: /data
    redo_dir: /redo
    zone: zone2
  server3:
    mysql_port: 2881
    rpc_port: 2882
    home_path: /home/admin/observer
    data_dir: /data
    redo_dir: /redo
    zone: zone3
obproxy-ce:
  depends:
    - oceanbase-ce
  servers: #表示obproxy的节点，需要调整IP为我们自己的规划的obproxy节点IP，如果有多个obproxy节点，可以参照oceanbase-ce -> servers的格式书写
    - name: server1
      ip: x.x.x.x
    - name: server2
      ip: x.x.x.x
    - name: server3
      ip: x.x.x.x
  global:
    listen_port: 2883 # 表示obproxy对外提供的访问端口，默认2883，可以自定义
    prometheus_listen_port: 2884 # 表示对外提供对接prometheus port的端口，默认2884，可以自定义
    home_path: /home/admin/obproxy # 表示obproxy的工作目录,包括bin/lib/etc/log等子目录
    # format: ip:mysql_port;ip:mysql_port. When a depends exists, OBD gets this value from the oceanbase-ce of the depends.
    # rs_list: x.x.x.x:2881;x.x.x.x:2881;x.x.x.x:2881
    enable_cluster_checkout: false
    cluster_name: obcluster # 表示obproxy可以对接的OceanBase集群的名字，目前依赖已经自动跟 oceanbase-ce -> global -> appname 保持一致，了解即可
    skip_proxy_sys_private_check: true
    obproxy_sys_password: ****** # 表示管理obproxy的root@proxysys用户的密码
    observer_sys_password: ****** # 对应OceanBase集群proxyro@sys的密码，目前的依赖已经自动跟 oceanbase-ce -> global -> proxyro_password 密码保持一致，了解即可
```

如果需要prometheus监控，可以添加obagent以及prometheus的配置

## 其他组件

- OBAgent：监控采集框架。OBAgent 默认支持的插件包括主机数据采集、OceanBase 数据库指标的采集、监控数据标签处理和 Prometheus 协议的 HTTP 服务。
- OCP Express：支持数据库集群关键性能指标查看及基本数据库管理功能的管理平台。
- Prometheus：开源监控解决方案，用于收集和聚合指标作为时间序列数据。若想监控到 OBServer 及服务器相关的数据，必须安装 OBAgent。
- Grafana：可视化工具。

**说明**

如果需要部署其中的任意一个或多个组件，可以参照配置，添加对应组件的配置信息。
配置文件：
xxx/.oceanbase-all-in-one/conf/grafana/all-components-with-prometheus-and-grafana.yaml，其中如果在 admin 用户下，xxx 默认为 /home/admin。

各个组件的独立配置，可以到 xxx/.oceanbase-all-in-one/conf/ 下对应名字的文件夹内查看。

**也可以到 Github 上面查看**

[https://github.com/oceanbase/obdeploy/blob/ae0f3bcac764d9365a6f1f7db68049edee7074ff/example/all-components.yaml](https://github.com/oceanbase/obdeploy/blob/ae0f3bcac764d9365a6f1f7db68049edee7074ff/example/all-components.yaml)

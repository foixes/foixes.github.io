---
title: 分区相关SQL
weight: 2
---

- 查看test库下t1表leader分布（用户租户下）
```sql
select * from oceanbase.DBA_OB_TABLE_LOCATIONS 
where DATABASE_NAME='test' and TABLE_NAME='t1' 
and ROLE='LEADER';
```
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2023/png/65656351/1684809903948-ab4c42c3-1e5d-44f3-a1f1-2a4763c27c23.png#clientId=u631f7bf7-f9e0-4&from=paste&height=133&id=u8b69a94a&originHeight=266&originWidth=2824&originalType=binary&ratio=2&rotation=0&showTitle=false&size=67670&status=done&style=none&taskId=uda55b282-23ee-46e5-ac37-dd29b162f00&title=&width=1412)

- 计算test库下分区个数（用户租户下）、（包含索引分区）
```sql
select count(*) from oceanbase.DBA_OB_TABLE_LOCATIONS
where DATABASE_NAME='test' and ROLE='LEADER';
```
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2023/png/65656351/1684812764890-32e85b5b-a198-4183-9689-80a98c65f999.png#clientId=u631f7bf7-f9e0-4&from=paste&height=102&id=uf3c17869&originHeight=204&originWidth=434&originalType=binary&ratio=2&rotation=0&showTitle=false&size=14333&status=done&style=none&taskId=u7e83d887-098d-472b-937a-d458ee6de36&title=&width=217)

- 计算test库在某台server上分区个数（用户租户下）
```sql
select count(*) from oceanbase.DBA_OB_TABLE_LOCATIONS 
where SVR_IP='172.17.104.187' 
and database_name='test' 
and ROLE='LEADER'
```
![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2023/png/65656351/1684812772708-64eafce2-1949-4942-abef-2821c75b070a.png#clientId=u631f7bf7-f9e0-4&from=paste&height=104&id=u1ae4bdb7&originHeight=208&originWidth=416&originalType=binary&ratio=2&rotation=0&showTitle=false&size=14735&status=done&style=none&taskId=u8e4be3ae-e998-43e8-b9f1-310b8443a8c&title=&width=208)

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

- 计算test库下分区个数（用户租户下）、（包含索引分区）
```sql
select count(*) from oceanbase.DBA_OB_TABLE_LOCATIONS
where DATABASE_NAME='test' and ROLE='LEADER';
```

- 计算test库在某台server上分区个数（用户租户下）
```sql
select count(*) from oceanbase.DBA_OB_TABLE_LOCATIONS 
where SVR_IP='x.x.x.x' 
and database_name='test' 
and ROLE='LEADER'
```

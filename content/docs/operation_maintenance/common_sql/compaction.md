---
title: 合并转储相关SQL
weight: 1
---
# 合并转储相关 SQL

- 查看全局合并是否开启

  ```sql
  SHOW PARAMETERS LIKE 'enable_major_freeze';
  ```

  ![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2023/png/65656351/1684822222424-ee93e8da-c493-44f9-8663-1ec4be9fcff3.png#clientId=u0f7d1ad4-4356-4&from=paste&height=133&id=ueb7389a0&originHeight=266&originWidth=3422&originalType=binary&ratio=2&rotation=0&showTitle=false&size=90189&status=done&style=none&taskId=u9fbd4923-9016-48b3-8a58-2b0e4b1c409&title=&width=1711)

- 手动发起所有租户合并

  ```sql
  ALTER SYSTEM MAJOR FREEZE TENANT=ALL;
  ```

- 手动发起单个租户合并，以租户名为 obtest 为例

  ```sql
  ALTER SYSTEM MAJOR FREEZE TENANT=obtest;
  ```

- 查看所有租户合并情况

  ```sql
  SELECT * FROM oceanbase.CDB_OB_ZONE_MAJOR_COMPACTION;
  ```

  ![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2023/png/65656351/1684814629058-117ce5cb-441f-4d9b-9fc8-1dc089113d79.png#clientId=u0f7d1ad4-4356-4&from=paste&height=441&id=uab8c9c6f&originHeight=882&originWidth=1918&originalType=binary&ratio=2&rotation=0&showTitle=false&size=245583&status=done&style=none&taskId=u651af399-c2f5-4af0-a7f4-9eaf99b57f7&title=&width=959)

- 查看合并剩余表数量

  ```sql
  select * from GV$OB_COMPACTION_PROGRESS 
  where UNFINISHED_TABLET_COUNT != 0;
  ```

  ![image.png](https://intranetproxy.alipay.com/skylark/lark/0/2023/png/65656351/1684821965718-61173715-732d-4b78-bb43-d8f6328d1799.png#clientId=u0f7d1ad4-4356-4&from=paste&height=141&id=u1acaddbb&originHeight=282&originWidth=4780&originalType=binary&ratio=2&rotation=0&showTitle=false&size=104417&status=done&style=none&taskId=uad9c99d8-17f4-40fb-8a19-8b256883041&title=&width=2390)

---
title: 使用 Outline 绑定执行计划
weight: 9
---
# 使用 Outline 绑定执行计划

## 语法

```sql
CREATE [OR REPLACE] OUTLINE outline_name ON stmt [ TO target_stmt ];

# 或 

CREATE [OR REPLACE] OUTLINE outline_name ON 'SQLID' using 'HINTS';
```

通过语句文本（用户执行的带参数的原始语句）或者 SQLID 都可以创建 Outline。不过由于文本中空格和换行如果有差异就会导致匹配不上，所以不建议通过文本创建 Outline（除非文本非常简单）。这里通过 SQLID 来创建 Outline，只要 SQL 文本不变，SQLID 就不会变。

Outline 的用法也是通过 SQL Hint 固定 SQL 的执行计划，可以调整表连接算法、使用的索引等等。

## 示例

下面示例还是针对前面测试 SQL，尝试调整一下执行计划里表连接顺序。

- 执行原 SQL，通过视图 gv$ob_sql_audit 或 gv$ob_plan_cache_plan_stat 找到该 SQL 的 SQLID。

- 根据 SQLID 创建 Outline，指定 HINTS。
<!-- 需要再看看这里的命令是在干啥 -->
```sql
create outline otl_test_1 on "9D276020142C5B8259B85C3E8966C579" using hint /*+ leading(i) */ ;

MySQL [tpccdb]> select * from oceanbase.__all_outline \G
*************************** 1. row ***************************
     gmt_create: 2021-10-05 12:18:26.032586
   gmt_modified: 2021-10-05 12:18:26.032586
      tenant_id: 0
     outline_id: 1001
    database_id: 1052
 schema_version: 1633407506030264
           name: otl_test_1
      signature:
outline_content: /*+leading(i) */
       sql_text:
          owner: root
           used: 0
        version: 3-b20901e8c84d3ea774beeaca963c67d7802e4b4e
     compatible: 1
        enabled: 1
         format: 0
 outline_params: ����
 outline_target:
         sql_id: 9D276020142C5B8259B85C3E8966C579
       owner_id: NULL
1 row in set (0.006 sec)
```

- 删除 Outline

  ```sql
  drop outline otl_test_1 ;
  ```

  删除 Outline 后，该 SQL ID 的执行计划重新生成，又回归到原始的执行计划了。

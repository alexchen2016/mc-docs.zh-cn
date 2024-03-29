---
title: 如何对 Azure Database for MariaDB 中的查询性能问题进行故障排除
description: 本文介绍了如何使用 EXPLAIN 对 Azure Database for MariaDB 中的查询性能问题进行故障排除。
author: WenJason
ms.author: v-jay
ms.service: mariadb
ms.topic: conceptual
origin.date: 11/09/2018
ms.date: 05/27/2019
ms.openlocfilehash: 600dc6e8f092026e9211c4736db131b8bb9b8fc8
ms.sourcegitcommit: 60169f39663ae62016f918bdfa223c411e249883
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66173320"
---
# <a name="how-to-use-explain-to-profile-query-performance-in-azure-database-for-mariadb"></a>如何使用 EXPLAIN 分析 Azure Database for MariaDB 中的查询性能
**EXPLAIN** 是一个可用来优化查询的易用工具。 可以使用 EXPLAIN 语句来获取有关 SQL 语句执行情况的信息。 下面的输出显示了 EXPLAIN 语句的一个执行示例。

```sql
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 10.00
        Extra: Using where
```

如此示例中所示，*key* 的值为 NULL。 此输出表明，MariaDB 找不到针对查询优化的任何索引，并且它执行全表扫描。 让我们通过添加一个基于 **ID** 列的索引来优化此查询。

```sql
mysql> ALTER TABLE tb1 ADD KEY (id);
mysql> EXPLAIN SELECT * FROM tb1 WHERE id=100\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ref
possible_keys: id
          key: id
      key_len: 4
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
```

新的 EXPLAIN 表明，MariaDB 现在使用索引将行数限制为 1，并因此显著缩短了搜索时间。
 
## <a name="covering-index"></a>涵盖索引
涵盖索引在索引中包含了查询中的所有列以减少从数据表进行的值检索。 下面的 **GROUP BY** 语句中进行了展示。
 
```sql
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

如输出所示，MariaDB 未使用任何索引，因为没有正确的索引可用。 它还显示了 *Using temporary; Using file sort*，这意味着 MariaDB 创建一个临时表来满足 **GROUP BY** 子句。
 
单独基于 **c2** 列创建索引没有任何区别，并且 MariaDB 仍然需要创建一个临时表：

```sql 
mysql> ALTER TABLE tb1 ADD KEY (c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using temporary; Using filesort
```

在本例中，可以同时基于 **c1** 和 **c2** 创建一个**涵盖索引**，从而将 **c2** 的值直接添加到索引中以避免进一步的数据查找。

```sql 
mysql> ALTER TABLE tb1 ADD KEY covered(c1,c2);
mysql> EXPLAIN SELECT MAX(c1), c2 FROM tb1 WHERE c2 LIKE '%100' GROUP BY c1\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: covered
          key: covered
      key_len: 108
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using index
```

如上面的 EXPLAIN 所表明，MariaDB 现在使用涵盖索引并避免了创建临时表。 

## <a name="combined-index"></a>组合索引
组合索引由来自多个列的值组成，并且可以包含行数组，其中的行按已编制索引的列的串联值排序。 可以在 **GROUP BY** 语句中使用此方法。

```sql
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 995789
     filtered: 11.11
        Extra: Using where; Using filesort
```

MariaDB 执行“文件排序”操作时非常缓慢，尤其是必须对大量行进行排序时。 若要优化此查询，可以基于要排序的两个列创建一个组合索引。

```sql 
mysql> ALTER TABLE tb1 ADD KEY my_sort2 (c1, c2);
mysql> EXPLAIN SELECT c1, c2 from tb1 WHERE c2 LIKE '%100' ORDER BY c1 DESC LIMIT 10\G
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: tb1
   partitions: NULL
         type: index
possible_keys: NULL
          key: my_sort2
      key_len: 108
          ref: NULL
         rows: 10
     filtered: 11.11
        Extra: Using where; Using index
```

EXPLAIN 现在表明，MariaDB 能够使用组合索引避免额外的排序，因为该索引已排序。
 
## <a name="conclusion"></a>结论
 
使用 EXPLAIN 和各种类型的索引可以显著提高性能。 表上有索引并不一定意味着 MariaDB 能够将其用于查询。 请始终使用 EXPLAIN 来验证假设并使用索引优化查询。


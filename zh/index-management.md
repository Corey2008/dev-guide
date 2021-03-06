---
title: 创建和管理索引
summary: 创建和管理索引的基本 SQL 操作和规范
category: management
---

# 创建和管理索引

本文介绍创建和管理索引的基本 SQL 操作和规范。

## 创建和管理索引

SQL 语句参阅：

- [CREATE INDEX](https://docs.pingcap.com/zh/tidb/stable/sql-statement-create-index#create-index)
- [ADD INDEX](https://docs.pingcap.com/zh/tidb/stable/sql-statement-add-index#add-index)
- [DROP INDEX](https://docs.pingcap.com/zh/tidb/stable/sql-statement-drop-index#drop-index)
- [RENAME INDEX](https://docs.pingcap.com/zh/tidb/stable/sql-statement-rename-index#rename-index)
- [SHOW INDEXES [FROM|IN]](https://docs.pingcap.com/zh/tidb/stable/sql-statement-show-indexes#show-indexes-fromin)

## 索引命名规范

- 唯一索引：`uk_[表名称简写]_[字段名简写]`
- 普通索引：`idx_[表名称简写]_[字段名简写]`
- 多单词组成的 column_name，取尽可能代表意义的缩写。

## 索引设计

- 选择区分度大的列建立索引，不在低基数列上建立索引，例如：“性别”，“是否是 XXX”；
- 单张表的索引数量控制在 5 个以内，避免冗余索引；
- 索引中的字段数建议不超过 5 个；
- 唯一索引建议由 3 个以下字段组成；
- 尽量不要在频繁更新的列上创建索引；
- 对于确定需要组成组合索引的多个字段，建议将选择性高的字段靠前放；
- 最左前缀原则，使用联合索引时，从左向右匹配，比如索引 `idx_c1_c2_c3 (c1,c2,c3)`，相当于创建了 (c1)、(c1,c2)、(c1,c2,c3) 三个索引，`WHERE` 条件包含上面三种情况的字段比较则可以用到索引，但像 `WHERE c1=a and c3=c` 只能用到 c1 列的索引，像 `c2=b and c3=c` 等情况就完全用不到这个索引；
- 不对过长的 `VARCHAR` 字段建立索引；
- 定期删除一些长时间未使用过的索引；
- `ORDER BY`，`GROUP BY`，`DISTINCT` 的字段需要添加在索引的后面，形成覆盖索引；
- 新的 `SELECT`，`UPDATE`，`DELETE` 上线，都要先 `EXPLAIN`，确保索引的正确性；
- 不建议在 `WHERE` 条件索引列上使用函数，会导致索引失效，如 `lower(email)`；
- 使用 `LIKE` 模糊匹配，`%` 不要放首位，会导致索引失效。

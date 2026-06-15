# 数据模型与 DDL 质量标准

## 字段规范

1. 主键统一 `BIGINT UNSIGNED NOT NULL AUTO_INCREMENT`。
2. 金额统一 `BIGINT`，单位分，人民币。
3. 当前阶段不设计 `currency / cur / currency_code`。
4. 不设计 `tenant_id`，除非全局决策统一引入。
5. 主业务表必须有 `version`、`deleted`、`create_time`、`update_time`。
6. 跨平台写入必须有 `caller_system / biz_domain / biz_no / idempotent_key / request_hash`。

## 索引规范

每个索引必须对应查询场景，不能为了“可能用到”随意加索引。

## 一致性规范

DDL、Entity、Mapper、DTO 中的字段命名和枚举含义必须一致。

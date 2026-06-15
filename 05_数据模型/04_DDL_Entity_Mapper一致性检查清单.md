# DDL / Entity / Mapper 一致性检查清单

| 检查项 | 要求 |
|---|---|
| 表名一致 | Entity `@TableName` 与 DDL 表名完全一致。 |
| 字段一致 | Entity 字段、Mapper ResultMap 与 DDL 字段一一对应。 |
| 金额类型 | Java 使用 `Long`，数据库使用 `BIGINT`。 |
| 状态枚举 | Java enum 与状态机文档完全一致。 |
| 主键 | `id` 使用 `Long`，数据库 `BIGINT UNSIGNED AUTO_INCREMENT`。 |
| 逻辑删除 | `deleted` 字段统一使用 `TINYINT`。 |
| 乐观锁 | `version` 字段必须用于状态更新。 |
| 幂等键 | 写入表必须有 `idempotent_key` 与 `request_hash`。 |
| 时间精度 | Java `LocalDateTime` 对应数据库 `DATETIME(3)`。 |
| 旧字段 | 不允许 Entity 出现旧 `finance_detail` 映射字段。 |
| 退款字段 | P0 不允许 Entity 出现退款字段。 |
| 币种字段 | 不允许出现 `currency / cur / currency_code`。 |
| 租户字段 | 不允许出现 `tenant_id`。 |

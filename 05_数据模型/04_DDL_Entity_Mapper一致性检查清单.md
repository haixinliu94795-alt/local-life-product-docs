# DDL Entity Mapper 一致性检查清单

Codex 开发完成后必须逐项检查：

| 检查项 | 要求 | 状态 |
|---|---|---|
| 表名一致 | Entity、Mapper XML、DDL 表名完全一致 | 待检查 |
| 字段一致 | Entity 字段、Mapper 字段、DDL 字段完全一致 | 待检查 |
| 金额类型 | Java 使用 `Long`，数据库使用 `BIGINT`，单位分 | 待检查 |
| 时间类型 | Java 使用 `LocalDateTime` 或项目统一时间类型，数据库 `DATETIME(3)` | 待检查 |
| 枚举字段 | Java 枚举值与文档状态值一致 | 待检查 |
| 逻辑删除 | 所有查询默认过滤 `deleted = 0` | 待检查 |
| 乐观锁 | 需要状态更新的表使用 `version` 条件更新 | 待检查 |
| 唯一索引 | 幂等和业务防重索引与 DDL 一致 | 待检查 |
| Mapper 更新 | 状态流转不得无条件更新，必须带当前状态和 version | 待检查 |
| 禁止字段 | 不得新增 `currency / cur / currency_code / tenant_id` | 待检查 |

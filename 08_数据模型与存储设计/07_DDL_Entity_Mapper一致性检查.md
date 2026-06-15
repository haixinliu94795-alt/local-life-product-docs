# DDL-Entity-Mapper 一致性检查

| 检查项 | 要求 |
|---|---|
| 表字段 | Entity 字段必须与 DDL 一致。 |
| 枚举 | Java 枚举与状态机文档一致。 |
| 索引字段 | Mapper 查询必须命中已设计索引。 |
| 金额字段 | 统一 long/BIGINT 分，不使用 BigDecimal 表示数据库金额。 |
| JSON 字段 | 需要明确序列化对象和兼容策略。 |
| 逻辑删除 | 查询默认过滤 `deleted=0`。 |

# 幂等与 request_hash

## 本章结论

幂等键解决重复请求，request_hash 解决同一幂等键下参数不一致。

| 层 | 幂等键 |
|---|---|
| 来源事件 | `caller_system + biz_domain + biz_no + event_type` 或 `idempotent_key` |
| 清分 | `event_no` |
| 头寸 | `clearing_item_no` |
| 结算确认 | `confirm_idempotent_key` |
| 账务入账 | `accounting_idempotent_key` |

## 冲突规则

1. 幂等键相同、request_hash 相同：返回原结果。
2. 幂等键相同、request_hash 不同：拒绝并返回 `CCS_IDEMPOTENT_CONFLICT`。

## V009 策略写入幂等补充

策略创建、策略版本创建、策略绑定创建都必须遵循相同幂等规则：

```text
同一 idempotent_key + 相同 request_hash -> 返回原结果
同一 idempotent_key + 不同 request_hash -> 返回 IDEMPOTENT_CONFLICT
```

策略绑定不能使用 MySQL 部分唯一索引表达“active 唯一”。P0 通过应用层在写入前校验同一对象、生效区间、策略优先级冲突，数据库层使用普通索引支持查询。

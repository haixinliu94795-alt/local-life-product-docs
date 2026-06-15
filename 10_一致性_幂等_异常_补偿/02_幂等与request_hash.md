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

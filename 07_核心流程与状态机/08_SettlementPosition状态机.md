# SettlementPosition 状态机

## 本章结论

本状态机是开发和测试的准入依据。任何代码实现不得出现未定义状态和未定义转移。

| 当前状态 | 触发事件 | 前置条件 | 目标状态 | 说明 |
|---|---|---|---|---|
| IN_TRANSIT | mature | 当前时间 >= eligible_time | ELIGIBLE | 可结算 |
| ELIGIBLE | lock | 确认结算 | LOCKED | 防重结 |
| LOCKED | startAccounting | 结算单入账 | ACCOUNTING_PROCESSING | 入账中 |
| ACCOUNTING_PROCESSING | success | 账务成功 | ACCOUNTED | 成功终态 |
| ACCOUNTING_PROCESSING | failed | 账务失败 | ACCOUNTING_FAILED | 可重试 |
| ACCOUNTING_PROCESSING | unknown | 账务结果未知 | UNKNOWN | 需查询 |
| ACCOUNTING_FAILED | retry | 复用幂等键 | ACCOUNTING_PROCESSING | 重试 |
| UNKNOWN | querySuccess | 账务查询成功 | ACCOUNTED | 修复 |
| UNKNOWN | queryFailed | 账务查询失败 | ACCOUNTING_FAILED | 修复 |

## 禁止规则

1. 终态不得回退。
2. UNKNOWN 不得直接重复执行外部副作用。
3. 重试必须复用原幂等键。
4. 状态变更必须记录操作日志或系统日志。

# AccountingPostingOrder 状态机

## 本章结论

本状态机是开发和测试的准入依据。任何代码实现不得出现未定义状态和未定义转移。

| 当前状态 | 触发事件 | 前置条件 | 目标状态 | 说明 |
|---|---|---|---|---|
| INIT | request | 准备调用账务 | REQUESTING | 请求中 |
| REQUESTING | success | 账务返回成功 | SUCCESS | 成功终态 |
| REQUESTING | failed | 账务明确失败 | FAILED | 可重试 |
| REQUESTING | unknown | 超时/异常 | UNKNOWN | 需查询 |
| FAILED | retry | 复用幂等键 | REQUESTING | 重试 |
| UNKNOWN | querySuccess | 查账成功 | SUCCESS | 修复 |
| UNKNOWN | queryFailed | 查账失败 | FAILED | 修复 |

## 禁止规则

1. 终态不得回退。
2. UNKNOWN 不得直接重复执行外部副作用。
3. 重试必须复用原幂等键。
4. 状态变更必须记录操作日志或系统日志。

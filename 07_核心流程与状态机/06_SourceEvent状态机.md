# SourceEvent 状态机

## 本章结论

本状态机是开发和测试的准入依据。任何代码实现不得出现未定义状态和未定义转移。

| 当前状态 | 触发事件 | 前置条件 | 目标状态 | 说明 |
|---|---|---|---|---|
| RECEIVED | startProcess | 事件校验通过 | PROCESSING | 开始清分 |
| PROCESSING | processSuccess | 清分成功 | PROCESSED | 完成 |
| PROCESSING | processFailed | 清分失败 | FAILED | 可重试 |
| RECEIVED | ignore | 非 P0 事件或重复无效 | IGNORED | 不处理 |

## 禁止规则

1. 终态不得回退。
2. UNKNOWN 不得直接重复执行外部副作用。
3. 重试必须复用原幂等键。
4. 状态变更必须记录操作日志或系统日志。

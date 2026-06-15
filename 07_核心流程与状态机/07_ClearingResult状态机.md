# ClearingResult 状态机

## 本章结论

本状态机是开发和测试的准入依据。任何代码实现不得出现未定义状态和未定义转移。

| 当前状态 | 触发事件 | 前置条件 | 目标状态 | 说明 |
|---|---|---|---|---|
| INIT | startClearing | 策略快照已确定 | CLEARING | 清分中 |
| CLEARING | clearingSuccess | 金额校验通过 | CLEARED | 清分成功 |
| CLEARING | clearingFailed | 策略缺失或金额异常 | CLEARING_FAILED | 可重试 |
| CLEARED | cancel | 源事件作废，P0不启用 | CANCELED | 预留 |

## 禁止规则

1. 终态不得回退。
2. UNKNOWN 不得直接重复执行外部副作用。
3. 重试必须复用原幂等键。
4. 状态变更必须记录操作日志或系统日志。

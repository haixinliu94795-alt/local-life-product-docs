# 账务失败与 UNKNOWN 补偿

## 1. 明确失败

账务平台返回明确失败：

- 入账单：`FAILED`
- 结算单：`ACCOUNTING_FAILED`
- 待结算项：`ACCOUNTING_FAILED`
- 记录 `fail_code`、`fail_reason`
- 后台允许人工重试

## 2. 结果未知

网络超时、连接中断、响应解析失败等结果未知：

- 入账单：`UNKNOWN`
- 结算单：`UNKNOWN`
- 待结算项保持 `ACCOUNTING_PROCESSING`
- 不允许直接再次入账
- 必须先查询账务平台幂等结果

## 3. UNKNOWN 回正

| 查询结果 | 处理 |
|---|---|
| 账务成功 | 回写 `accounting_request_no`、`fund_account_flow_no`，推进成功。 |
| 账务失败 | 推进失败，允许重试。 |
| 查不到 | 按失败处理或进入人工核查，具体由账务平台能力确认。 |

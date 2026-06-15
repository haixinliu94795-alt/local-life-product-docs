# 账务 UNKNOWN 补偿

## 本章结论

账务调用超时或结果未知时，系统必须进入 UNKNOWN，禁止直接重复入账。

## 补偿流程

```text
postSettlement 超时
  -> PostingOrder.UNKNOWN
  -> Bill.UNKNOWN
  -> Position.UNKNOWN
  -> querySettlementPosting(accountingIdempotentKey + bizKey)
  -> 查到成功：SUCCESS / ACCOUNTED
  -> 查到失败：FAILED / ACCOUNTING_FAILED
  -> 查不到：保持 UNKNOWN，进入人工诊断
```

## 开发要求

1. 重试入账必须复用原 `accounting_idempotent_key`。
2. 查询补偿必须优先按账务请求号，其次按幂等键和业务键。
3. UNKNOWN 状态必须有监控和操作日志。

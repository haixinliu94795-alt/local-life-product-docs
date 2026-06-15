# 账务失败与 UNKNOWN 补偿

## 1. 明确失败

账户账务平台返回明确失败时：

```text
AccountingPostingOrder -> FAILED
SettlementBill -> ACCOUNTING_FAILED
SettlementPosition -> ACCOUNTING_FAILED
```

允许运营或任务重试。重试必须复用同一 `accounting_idempotent_key`。

## 2. UNKNOWN

以下场景进入 UNKNOWN：

- 调用账务平台超时；
- 网络中断；
- 账户账务平台返回处理中或结果不明；
- 本地收到响应但回写状态失败。

UNKNOWN 处理规则：

1. 不允许直接再次入账。
2. 必须调用 `querySettlementPosting(accountingRequestNo)` 或等价查询接口。
3. 查询成功则回正 `ACCOUNTED/SUCCESS`。
4. 查询失败则回正 `ACCOUNTING_FAILED/FAILED`。
5. 查询仍未知则保留 UNKNOWN 并告警。

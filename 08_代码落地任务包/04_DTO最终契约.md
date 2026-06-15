# DTO 最终契约

核心 DTO：

```text
SettlementSourceEventCommand
SourceEventResult
ConfirmMerchantSettlementCommand
SettlementBillResult
SettlementAccountingPostCommand
SettlementAccountingPostResult
SettlementPositionQuery
SettlementPositionVO
SettlementBillVO
AccountingPostingOrderVO
```

所有 DTO 金额字段使用 `Long`，命名后缀统一为 `Cent`。

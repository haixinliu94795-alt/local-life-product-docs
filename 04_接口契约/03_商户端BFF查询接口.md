# 商户端 BFF 查询接口

## 1. 数据来源

| 展示项 | 数据来源 |
|---|---|
| 可提现余额 | 账户账务平台。 |
| 待结算金额 | 新清结算宽口径 + 双轨期旧 finance_detail。 |
| 已结算金额 | 新 `ccs_settlement_bill.ACCOUNTED` + 双轨期旧 `local_settlement_record`。 |
| 账户明细 | 账户账务平台 `fund_account_flow`。 |
| 提现中/已提现 | 出款/提现平台。 |

## 2. 宽口径待结算

商户端待结算可聚合：

```text
IN_TRANSIT + ELIGIBLE + LOCKED + ACCOUNTING_PROCESSING
```

失败态 `ACCOUNTING_FAILED` 默认不对商户端展示，进入后台运营处理。

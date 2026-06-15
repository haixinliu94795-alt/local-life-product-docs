# Command 接口契约

## 统一结算确认命令

```json
{
  "merchantNo": "M10001",
  "businessScene": "LOCAL_LIFE_NORMAL",
  "positionNos": ["POS202606150001"],
  "operatorNo": "OP1001",
  "idempotentKey": "CCS:SETTLE:202606150001",
  "requestHash": "sha256(...)"
}
```

## 行为

1. 同一幂等键、同一 request_hash 返回原结算单。
2. 同一幂等键、不同 request_hash 拒绝。
3. 任一头寸不可结算，整批失败。
4. 成功后生成批次、结算单、结算明细和入账单。

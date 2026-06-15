# Event 事件契约

## 标准来源事件

```json
{
  "callerSystem": "local-life-order",
  "bizDomain": "LOCAL_LIFE",
  "bizNo": "WO202606150001",
  "eventType": "WRITE_OFF_COMPLETED",
  "sourceOrderNo": "O202606150001",
  "sourceFulfillmentNo": "WO202606150001",
  "merchantNo": "M10001",
  "businessScene": "LOCAL_LIFE_NORMAL",
  "occurredTime": "2026-06-15T10:00:00.000+08:00",
  "orderAmountCent": 10000,
  "paidAmountCent": 8800,
  "payload": {},
  "idempotentKey": "CCS:EVENT:WO202606150001",
  "requestHash": "sha256(...)"
}
```

## P0 支持事件

P0 只支持履约/核销完成类事件。退款、撤销、冲正不进入 P0。

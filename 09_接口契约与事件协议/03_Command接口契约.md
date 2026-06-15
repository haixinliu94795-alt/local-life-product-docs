# Command 接口契约

## 1. 本章结论

Command 接口是改变清结算状态的写接口，必须具备幂等键、request_hash、明确错误码和重复请求行为。V010 重点收口结算确认接口的批量上限和 request_hash 字段集合。

## 2. `confirmMerchantSettlement`

### 2.1 语义

统一处理单条和批量结算。单条结算是 `positionNos.size() = 1`，批量结算是 `positionNos.size() > 1`。

### 2.2 请求字段

| 字段 | 必填 | 说明 |
|---|---:|---|
| `callerSystem` | 是 | 调用方系统。 |
| `bizDomain` | 是 | 业务域。 |
| `bizNo` | 是 | 业务编号。 |
| `merchantNo` | 是 | 商户编号。 |
| `businessScene` | 是 | 业务场景。 |
| `positionNos` | 是 | 结算头寸编号列表，1-500 条。 |
| `operatorNo` | 是 | 操作人编号。 |
| `idempotentKey` | 是 | 幂等键。 |
| `requestHash` | 是 | 请求核心参数指纹。 |

### 2.3 处理规则

| 场景 | 结果 |
|---|---|
| `positionNos` 为空 | `CCS_PARAM_INVALID` |
| `positionNos` 超过 500 | `CCS_BATCH_LIMIT_EXCEEDED` |
| 包含非 `ELIGIBLE` 头寸 | 整批失败 |
| 包含不同商户头寸 | 整批失败 |
| 同一幂等键同一 hash 重试 | 返回原结算单 |
| 同一幂等键不同 hash | `CCS_IDEMPOTENT_CONFLICT` |

### 2.4 成功响应

```json
{
  "code": "SUCCESS",
  "message": "success",
  "data": {
    "billNo": "SB202606150001",
    "batchNo": "BT202606150001",
    "billStatus": "CREATED"
  }
}
```

## 3. 非 P0 Command

以下写接口不进入 P0：

```text
/eligible/export
/refund/*
/freeze/*
/payout/*
```

# DTO 最终契约

## ConfirmMerchantSettlementCommand

| 字段 | 类型 | 必填 | 说明 |
|---|---|---:|---|
| `callerSystem` | String | 是 | 技术调用方系统。 |
| `bizDomain` | String | 是 | 业务归属域。 |
| `bizNo` | String | 是 | 业务单号。 |
| `businessScene` | String | 是 | 业务场景。 |
| `merchantId` | String | 是 | 商户ID。 |
| `pendingNos` | List<String> | 是 | 待结算项号。 |
| `voucherUrls` | List<String> | 否 | 结算凭证。 |
| `idempotentKey` | String | 是 | 幂等键。 |
| `requestHash` | String | 是 | 请求指纹。 |
| `operatorId` | String | 是 | 操作人。 |
| `operatorName` | String | 否 | 操作人名称。 |
| `remark` | String | 否 | 备注。 |

## ConfirmSettlementResult

| 字段 | 类型 | 说明 |
|---|---|---|
| `billNo` | String | 结算单号。 |
| `batchNo` | String | 批次号。 |
| `billStatus` | String | 结算单状态。 |
| `accountingStatus` | String | 入账状态。 |
| `duplicate` | Boolean | 是否幂等重复返回。 |

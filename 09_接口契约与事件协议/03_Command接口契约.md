# Command 接口契约

## 1. 本章结论

Command 接口用于产生状态变更，必须携带幂等键和请求指纹。V009 在策略命令中补充结算模式、发起方式、最小/最大金额和留存策略，在策略绑定命令中补充业务对象绑定。

## 2. 创建策略版本 Command

`PolicyCommand` 关键字段：

| 字段 | 说明 | P0 默认 |
|---|---|---|
| `settlementMode` | 结算模式 | `INTERNAL_ACCOUNT` |
| `initiationMode` | 发起方式 | `OPERATOR_MANUAL` |
| `autoSettlementEnabled` | 是否自动结算 | `false` |
| `minSettlementAmountCent` | 最小结算金额 | `0` |
| `maxSettlementAmountCent` | 最大结算金额 | `0` |
| `reservePolicyType` | 留存策略 | `NONE` |
| `reserveAmountCent` | 固定留存金额 | `0` |
| `reserveRatioBp` | 留存比例 | `0` |

P0 校验：

```text
settlementMode 必须为 INTERNAL_ACCOUNT
initiationMode 必须为 OPERATOR_MANUAL
autoSettlementEnabled 必须为 false
reservePolicyType 必须为 NONE 或 reserveAmountCent/reserveRatioBp 为 0
```

## 3. 策略绑定 Command

`PolicyBindingCommand` 用于将策略绑定到业务对象。

| 字段 | 说明 |
|---|---|
| `bindingSubjectType` | GLOBAL/BUSINESS_SCENE/CATEGORY/MERCHANT/CHANNEL/CAMPAIGN |
| `bindingSubjectNo` | 对象编号 |
| `businessScene` | 业务场景 |
| `policyNo` | 策略编号 |
| `bindingPriority` | 匹配优先级 |
| `effectiveStartTime` / `effectiveEndTime` | 生效区间 |
| `idempotentKey` / `requestHash` | 幂等与冲突治理 |

## 4. 统一结算确认 Command

单条和批量结算仍使用同一个接口：

```text
ConfirmSettlementCommand.positionNos
```

- 单条：`positionNos.size == 1`
- 批量：`positionNos.size > 1`

P0 批量策略：全成功 / 全失败。

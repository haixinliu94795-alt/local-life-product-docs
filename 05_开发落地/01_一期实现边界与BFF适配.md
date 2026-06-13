# 一期实现边界与 BFF 适配

## 1. 一期目标

一期只实现本地生活普通商品从核销完成到商户账户入账的清结算闭环：

```text
核销完成
  -> 标准事件
  -> 清分结果/规则快照
  -> 在途待结算项
  -> 账期成熟后进入可结算
  -> 后台商家应付
  -> 运营单条/批量确认结算
  -> 结算批次/结算单/明细
  -> 账户账务入账
  -> 商户端展示已结算与账户流水
```

## 2. 一期不做但必须设计预留

| 能力 | 一期处理 |
|---|---|
| 风控止付/冻结规则 | 只预留 `hold_status`、`hold_reason`、`hold_no`，不做复杂规则引擎。 |
| 自动结算 | 手动结算为主，自动任务接口预留。 |
| 自动出款 | 不做，入账后由商户提现平台处理。 |
| 团餐/邀新/渠道权益券 | DDD 模型支持，具体接入 P1/P2。 |
| 对账差错工作台 | P1；一期至少保留结算单与账务流水可反查。 |
| 历史数据全量迁移 | 不做；新老双轨，旧链路查询兼容。 |

## 3. 当前产品“待结算”的 BFF 适配

底层标准状态：

```text
IN_TRANSIT_UNMATURED    在途未到期
SETTLEMENT_ELIGIBLE    到期可结算
ON_HOLD                止付/冻结
LOCKED_FOR_SETTLEMENT  已锁定进结算批次
ACCOUNTING_PROCESSING  入账处理中
ACCOUNTED              入账成功
```

当前产品若要求“核销完、未结算都叫待结算”，BFF 查询可以这样处理：

| 产品展示项 | 底层查询范围 | 说明 |
|---|---|---|
| 商户端待结算金额 | `IN_TRANSIT_UNMATURED + SETTLEMENT_ELIGIBLE` | 默认不含 `ON_HOLD`，如产品要展示冻结金额则单列。 |
| 后台商家应付 | `SETTLEMENT_ELIGIBLE` | 后台只给可结算项操作，不能混入未到期。 |
| 结算中 | `LOCKED_FOR_SETTLEMENT + ACCOUNTING_PROCESSING` | 可在商户端合并到待结算或单列，取决于页面。 |
| 已结算 | `ACCOUNTED` | 以账务入账成功为准。 |
| 冻结/止付金额 | `ON_HOLD` | 一期可不展示，P1 再做。 |

## 4. 统一结算确认方法

单条和批量结算共用一个应用服务方法：

```java
SettlementBillNo confirmMerchantSettlement(ConfirmMerchantSettlementCommand command);
```

命令对象：

```java
class ConfirmMerchantSettlementCommand {
    String merchantId;
    String businessScene;
    List<String> pendingItemIds;
    String voucherUrl;
    String operatorId;
    String operatorName;
    String remark;
    String requestId;
}
```

单条结算：`pendingItemIds.size() == 1`。  
批量结算：`pendingItemIds.size() > 1`。

批量校验：

1. 全部属于同一商户。
2. 全部属于同一结算主体。
3. 全部已到账期且未止付。
4. 全部未锁定、未结算。
5. 全部币种一致。
6. 金额汇总等于明细金额之和。
7. 一期采用全成功/全失败，不做部分成功。

## 5. 推荐开发改动

| 层 | 建议 |
|---|---|
| Controller | 可保留 `settle-one` / `settle-batch` 两个入口，但都转换成统一 Command。 |
| Application Service | 只保留一套 `confirmMerchantSettlement` 流程。 |
| Domain Service | 做结算资格校验、锁定、金额校验。 |
| SettlementBill Aggregate | 生成结算单、明细、状态机。 |
| Accounting Adapter | 复用现有账户账务平台，幂等入账。 |
| Merchant Query BFF | 按产品口径聚合标准状态。 |

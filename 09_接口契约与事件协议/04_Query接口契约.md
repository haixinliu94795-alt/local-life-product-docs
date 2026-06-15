# Query 接口契约

## 查询原则

1. 查询接口不修改状态。
2. 查询返回标准领域状态，不做产品展示口径转换。
3. 分页查询必须限制时间范围或状态范围。

## 查询对象

| 查询 | 用途 |
|---|---|
| `querySettlementPositions` | 查询结算头寸。 |
| `querySettlementBills` | 查询结算单。 |
| `querySettlementBillDetail` | 查询结算单和明细。 |
| `queryAccountingPostingOrder` | 查询账务入账单。 |

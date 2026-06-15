# 账务平台 Port 契约

## 本章结论

清结算平台通过标准端口对接账户账务平台，不绑定当前平台某个历史方法名。

## 入账命令

```java
SettlementAccountingPostResult postSettlement(SettlementAccountingPostCommand command);
```

## 查询命令

```java
SettlementAccountingPostResult querySettlementPosting(AccountingPostingQuery query);
```

## 查询条件

`AccountingPostingQuery` 至少包含：

1. `accountingRequestNo`，可选。
2. `accountingIdempotentKey`，必须支持。
3. `callerSystem`。
4. `bizDomain`。
5. `bizNo`。
6. `accountingScene`。

## 返回要求

账务平台应返回 `accounting_request_no` 和 `fund_account_flow_no`，用于清结算平台追溯。

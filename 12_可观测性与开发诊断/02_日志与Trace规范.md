# 日志与 Trace 规范

## 关键链路 Trace 字段

`event_no`、`clearing_no`、`clearing_item_no`、`position_no`、`batch_no`、`bill_no`、`posting_no`、`accounting_request_no`。

每个应用服务入口必须打印业务键、幂等键和状态变更结果。

# Codex 任务拆分建议

## Step 1：只读现状复核

目标：确认当前订单、finance_detail、local_settlement_record、账户账务接口、商户端查询的真实代码路径。

## Step 2：建清结算模块骨架

目标：新增 clearsettlement 领域包、DTO、领域对象、Repository 接口，不接业务流量。

## Step 3：建核心表和实体

目标：落地 source_event、rule_snapshot、clearing_result、pending_item、settlement_bill、accounting_order、operation_log 等表。

## Step 4：普通商品 Adapter 与清分

目标：本地生活普通商品核销完成后生成标准事件和清分结果。

## Step 5：待结算池和后台商家应付

目标：后台商家应付列表读取新待结算池，支持筛选和状态。

## Step 6：结算确认与状态机

目标：实现单条/批量结算、凭证、批次、结算单、明细、状态流转。

## Step 7：账务入账编排

目标：调用现有 AccountingFacade，处理成功、失败、未知和重试。

## Step 8：商户端查询切换

目标：待结算、结算记录、结算详情切新清结算读模型。

## Step 9：最小对账和灰度

目标：实现结算单 vs 账务流水校验，新旧链路对比，灰度切换。

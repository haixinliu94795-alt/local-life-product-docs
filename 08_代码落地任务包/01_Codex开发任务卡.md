# Codex 开发任务卡

## Task 01：新增清结算模块骨架

| 项 | 内容 |
|---|---|
| 方案依据 | V004 全部文档 |
| 主关联对象 | `SettlementBill` / `bill_no` |
| 修改区域 | 新增 `org.jeecg.modules.ccs` 包或新模块 |
| 禁止项 | 不修改旧结算业务逻辑 |
| 输出 | 包结构、空类骨架、枚举、基础 DTO |

## Task 02：落地 DDL、Entity、Mapper、Repository

| 项 | 内容 |
|---|---|
| 方案依据 | `05_数据模型/02_DDL_V004_P0.sql` |
| 修改区域 | db 脚本、Entity、Mapper、Repository |
| 禁止项 | 不得新增 currency/tenant_id；金额必须 Long/Bigint 分 |
| 测试 | DDL-Entity-Mapper 一致性检查 |

## Task 03：实现来源事件接入

| 项 | 内容 |
|---|---|
| 输入 | `SourceEventAcceptCommand` |
| 输出 | `ccs_source_event` |
| 必须 | 幂等键、requestHash、重复请求处理 |

## Task 04：普通商品清分映射

| 项 | 内容 |
|---|---|
| 输入 | `WRITE_OFF_COMPLETED` + `local_order_finance_detail` |
| 输出 | `ccs_clearing_result`、`ccs_clearing_result_item` |
| 必须 | 金额字段单位分、公式映射、金额校验 |

## Task 05：待结算项生命周期

| 项 | 内容 |
|---|---|
| 输出 | `ccs_pending_settlement_item` |
| 必须 | `IN_TRANSIT -> ELIGIBLE`、退款前置规则、状态矩阵测试 |

## Task 06：商家应付和统一确认结算

| 项 | 内容 |
|---|---|
| 接口 | `/admin/ccs/merchant-payable/page`、`/admin/ccs/merchant-payable/settle` |
| 必须 | 单条/批量同方法，全成功/全失败，批量校验 |

## Task 07：账务入账编排

| 项 | 内容 |
|---|---|
| 接口 | `AccountingFacade.recordProductSettlement` |
| 必须 | SUCCESS/FAILED/UNKNOWN、重试、查询补偿、回写流水号 |

## Task 08：商户端 BFF 新老双轨

| 项 | 内容 |
|---|---|
| 输出 | 资金汇总、待结算明细、结算详情 |
| 必须 | 新老数据合并，后台和商户端口径分离 |

## Task 09：退款最小闭环

| 项 | 内容 |
|---|---|
| 场景 | 结算前、结算中、结算后 |
| 必须 | 负向清分和负向待结算项 |

## Task 10：测试、准出和灰度

| 项 | 内容 |
|---|---|
| 必须 | 状态矩阵、幂等并发、账务异常、退款、双轨回归 |
| 输出 | 测试报告、准出清单、灰度开关说明 |

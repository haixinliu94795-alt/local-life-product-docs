# Codex 开发任务卡

## 1. 总体要求

本任务包只允许实现 V010 P0 范围：正向清结算闭环、账期成熟、结算确认、账务入账、UNKNOWN 补偿、幂等 request_hash、基础查询和测试。

禁止实现：退款、冻结/止付、出款、BFF 展示聚合、百万级导出、容量规划、分库分表、复杂任务调度。

## Task 01：新增清结算模块骨架

| 项 | 内容 |
|---|---|
| 目标 | 新增 `local-live-module-clearing-settlement` 或等价模块。 |
| 依赖 | 无 |
| 交付 | 包结构、基础配置、领域包、应用包、基础 Repository 接口。 |
| 禁止 | 不接入旧结算 Service 逻辑。 |
| 验收 | 模块可编译，包结构符合 DDD 分层。 |

## Task 02：落地 V010 DDL / Entity / Mapper

| 项 | 内容 |
|---|---|
| 目标 | 执行 `03_DDL_V010_P0.sql`，生成 Entity/Mapper/Repository。 |
| 依赖 | Task 01 |
| 必须 | 金额 BIGINT 分；无 currency；无 tenant_id；状态枚举一致。 |
| 禁止 | 临时新增旧 `finance_detail` 映射字段。 |
| 验收 | DDL-Entity-Mapper 一致性检查通过。 |

## Task 03：实现 request_hash 公共组件

| 项 | 内容 |
|---|---|
| 目标 | 实现 `RequestHashCalculator`。 |
| 依赖 | Task 01 |
| 规则 | SHA-256、Canonical JSON、字段名升序、数组排序、空值不参与。 |
| 验收 | SourceEvent、ConfirmSettlement、AccountingPosting 三类 hash 单测通过。 |

## Task 04：实现来源事件接入和清分

| 项 | 内容 |
|---|---|
| 目标 | 接入 `WRITE_OFF_COMPLETED/FULFILLMENT_COMPLETED` 事件，生成清分结果和头寸。 |
| 依赖 | Task 02、Task 03 |
| 必须 | 事件幂等、清分状态机、清分明细金额校验。 |
| 验收 | 重复事件返回原结果，冲突事件拒绝。 |

## Task 05：实现账期成熟任务

| 项 | 内容 |
|---|---|
| 目标 | 实现 `SettlementPositionMaturityJob`。 |
| 依赖 | Task 04 |
| 规则 | 每 5 分钟一次；单轮 200 条；只推进 `IN_TRANSIT -> ELIGIBLE`。 |
| 禁止 | 分片、容量规划、复杂任务平台。 |
| 验收 | 成熟任务状态机测试通过。 |

## Task 06：实现后台可结算头寸查询

| 项 | 内容 |
|---|---|
| 目标 | 查询 `ELIGIBLE` 头寸分页。 |
| 依赖 | Task 05 |
| 必须 | pageSize 最大 100。 |
| 禁止 | `/eligible/export`。 |
| 验收 | 分页查询正确，导出接口不存在或不属于 P0。 |

## Task 07：实现统一结算确认

| 项 | 内容 |
|---|---|
| 目标 | 实现 `confirmMerchantSettlement(command)`。 |
| 依赖 | Task 06、Task 03 |
| 规则 | `positionNos` 1-500；全成功/全失败；同一商户/场景/主体校验；操作日志 afterCommit。 |
| 验收 | 单条、500 条、501 条、混入不可结算项测试通过。 |

## Task 08：实现账务入账 Port 和状态联动

| 项 | 内容 |
|---|---|
| 目标 | 对接账户账务平台标准端口。 |
| 依赖 | Task 07 |
| 必须 | 成功回写 requestNo/flowNo；失败进入 ACCOUNTING_FAILED；未知进入 UNKNOWN。 |
| 禁止 | UNKNOWN 直接重入账。 |
| 验收 | 成功/失败/UNKNOWN 三类用例通过。 |

## Task 09：实现 UNKNOWN 补偿任务

| 项 | 内容 |
|---|---|
| 目标 | 实现 `AccountingUnknownCompensationJob`。 |
| 依赖 | Task 08 |
| 规则 | 每 2 分钟查一次；5 次自动查询；10 分钟 overdue；30 分钟人工介入。 |
| 验收 | UNKNOWN 回正成功、回正失败、人工介入测试通过。 |

## Task 10：补齐测试与准出

| 项 | 内容 |
|---|---|
| 目标 | 补齐状态机、幂等、事务、账务补偿、批量边界测试。 |
| 依赖 | Task 01-09 |
| 验收 | `13_测试验收` 中 P0 用例全部通过。 |

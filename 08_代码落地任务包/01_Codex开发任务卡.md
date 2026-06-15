# Codex 开发任务卡

## 总要求

1. 严格依据 V005 SDD，不参考旧系统表来反向设计清结算平台。
2. 不允许在核心模型中出现旧 `finance_detail` 字段映射。
3. 不允许实现退款、提现、冻结、止付、BFF 展示口径。
4. 不允许绑定 `AccountingFacade.recordProductSettlement` 方法名；必须通过 `SettlementAccountingPort` 端口隔离。
5. 所有状态变更必须经过状态机服务，不允许直接 update 状态字段。
6. 所有写接口必须校验 `idempotent_key + request_hash`。
7. 所有金额使用 Long 分。

## Task 01：模块骨架

- 新增清结算模块和 DDD 分层包。
- 输出 Entity、Repository、Application Service、Domain Service、Adapter 包结构。

## Task 02：DDL / Entity / Mapper

- 执行 `05_数据模型/02_DDL_V005_P0.sql`。
- 按 `05_数据模型/04_DDL_Entity_Mapper一致性检查清单.md` 创建 Entity 和 Mapper。

## Task 03：来源事件接入

- 实现 `SettlementSourceEventCommand`。
- 支持幂等和 request_hash 冲突校验。
- 生成清分结果和结算头寸。

## Task 04：账期成熟与商家应付

- 实现 `IN_TRANSIT -> ELIGIBLE` 推进。
- 实现后台查询 `ELIGIBLE` 结算头寸。

## Task 05：统一结算确认

- 实现 `confirmMerchantSettlement(command)`。
- 单条和批量共用同一核心方法。
- 批量策略全成功/全失败。

## Task 06：账务入账编排

- 实现 `SettlementAccountingPort`。
- 实现账务入账状态机。
- 支持成功、失败、UNKNOWN。

## Task 07：补偿查询

- 实现 UNKNOWN 查询补偿。
- UNKNOWN 不允许直接重复入账。

## Task 08：标准查询接口

- 实现结算头寸、结算单、账务入账单标准查询。
- 不实现页面展示聚合。

## Task 09：测试

- 状态机测试。
- 幂等并发测试。
- 账务异常补偿测试。
- 金额一致性测试。

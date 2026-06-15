# 幂等与 request_hash

## 1. 本章结论

`idempotent_key` 用于识别同一次业务请求，`request_hash` 用于识别同一个幂等键下请求核心参数是否一致。二者必须配套使用，否则重复请求和参数冲突无法可靠区分。

## 2. 标准算法

| 项 | 标准 |
|---|---|
| Hash 算法 | SHA-256 |
| 字符编码 | UTF-8 |
| 输出格式 | 小写十六进制字符串 |
| 序列化格式 | Canonical JSON |
| 字段排序 | 按字段名 ASCII 升序 |
| 空值字段 | 不参与 hash |
| 数组字段 | 业务无序数组必须排序后参与 hash |
| 金额字段 | 统一使用分单位整数，不允许小数 |
| 时间字段 | 使用接口规范固定格式，推荐 ISO-8601 或 `yyyy-MM-dd HH:mm:ss.SSS` |

## 3. 判断规则

| 场景 | 处理 |
|---|---|
| 同一 `idempotent_key` + 同一 `request_hash` | 返回原处理结果。 |
| 同一 `idempotent_key` + 不同 `request_hash` | 拒绝，返回 `CCS_IDEMPOTENT_CONFLICT`。 |
| 不同 `idempotent_key` 但业务唯一键冲突 | 按业务唯一键返回原对象或拒绝，具体由接口语义定义。 |
| 缺失 `request_hash` | 写接口拒绝请求。 |

## 4. SourceEvent request_hash 字段集合

参与字段：

```text
caller_system
biz_domain
biz_no
event_type
source_order_no
source_fulfillment_no
merchant_no
business_scene
occurred_time
order_amount_cent
paid_amount_cent
payload_json  // canonical 化后参与
```

不参与字段：

```text
trace_id
request_time
operator_ip
client_version
```

## 5. ConfirmSettlement request_hash 字段集合

参与字段：

```text
caller_system
biz_domain
biz_no
merchant_no
business_scene
position_nos        // 升序排序
settlement_scene
settlement_mode
initiation_mode
operator_no         // P0 作为资金操作主体，参与 hash
```

不参与字段：

```text
remark
trace_id
request_time
operator_name
operator_ip
```

## 6. AccountingPosting request_hash 字段集合

参与字段：

```text
posting_no
bill_no
batch_no
caller_system
biz_domain
biz_no
accounting_scene
participant_role
participant_no
posting_amount_cent
accounting_idempotent_key
```

不参与字段：

```text
last_request_time
retry_count
trace_id
```

## 7. Canonical JSON 示例

原始请求：

```json
{
  "bizNo": "BIZ-001",
  "callerSystem": "LOCAL_LIFE",
  "positionNos": ["P3", "P1", "P2"],
  "businessScene": "LOCAL_LIFE_NORMAL"
}
```

参与 hash 前必须规范化为：

```json
{"bizNo":"BIZ-001","businessScene":"LOCAL_LIFE_NORMAL","callerSystem":"LOCAL_LIFE","positionNos":["P1","P2","P3"]}
```

然后对该字符串做 SHA-256。

## 8. 开发要求

1. 服务端必须自行重新计算 `request_hash`，不能完全信任调用方传入值。
2. 写接口必须先做幂等键查询，再判断 `request_hash`。
3. 所有幂等冲突必须打审计日志和错误指标。
4. Codex 实现时必须提供 `RequestHashCalculator` 公共组件。

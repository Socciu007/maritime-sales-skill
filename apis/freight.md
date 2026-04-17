# 运价 API

## 接口信息
- **方法**：POST
- **地址**：`https://www.dadaex.cn/api/interface/freight/getOceanfreightToChatNolink`

## 入参

| 参数 | 说明 | 是否必填 | 格式 |
|------|------|---------|------|
| start_port | 起运港 | 必填 | 港口英文名小写，如 `shanghai` |
| end_port | 目的港 | 必填 | 港口英文名小写，如 `singapore` |
| hhtime | 货好时间 | 选填 | `YYYY-MM-DD`，未提及传 `""` |
| etd | 预计开船时间 | 选填 | `YYYY-MM-DD`，未提及传 `""` |
| carrier | 船公司 | 选填 | 船公司英文名大写，如 `MAERSK`，未提及传 `""` |

> QQ 和 requireSailingSchedule 字段忽略不传

## 出参关键字段

| 字段 | 说明 | 后续用途 |
|------|------|---------|
| 航线代码 | 如 `KMSK/S` | 可复用给舱位API.routeCode / 船期API.routeCode |
| 航线名称 | 如 `东南亚线` | 可复用给舱位API.route |
| 船名 | 如 `ZHONG GU YING KOU` | 可复用给船期API.shipName |
| 航次 | 如 `2603S` | 可复用给船期API.voyage |
| 中转类型 | `直达(DIRECT)` 或中转 | 可参考用于舱位API.type |
| 晚开日期 | 截关时间 | 重要销售信息 |
| 现舱 | 空或有值 | 舱位紧张信号 |
| 重要提示 | 如 `特价/最便宜最低报价` | 销售话术素材 |
| 最低报价 | `true` / `false` | 标注最优价 |
| 免箱期 | 如 `14天混用COMBINE` | 销售加分项 |
| extendInfo.surcharge | 附加费列表 | 本地费用展示 |

## surcharge 费用结构

- 单位为"票"：含 `单票价格` 字段
- 单位为"箱"：含 `20gpSell` / `40gpSell` / `40hqSell` 字段
- 有 `备注: 可做SEAWAY` 时单独说明

## 调用示例

```json
{
  "start_port": "shanghai",
  "end_port": "singapore",
  "hhtime": "",
  "etd": "2026-04-20",
  "carrier": "MAERSK"
}
```

## 错误处理
任何情况下不能卡住，统一处理：
- status=0 或无数据 → "暂时没有查到相关运价，请稍后重试或换个条件查询。"
- 网络超时 → "查询超时，请稍后重试。"
- 返回异常 → "运价查询出现问题，请稍后重试。"

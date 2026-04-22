# 船期 API

## 接口信息
- **方法**：GET
- **地址**：`https://www.dadaex.cn/api/interface/freight/getSchedule`
- **超时设置**：
  - 连接超时：120秒
  - 总超时：120秒（或你希望的秒数）

## 入参

| 参数 | 说明 | 是否必填 | 格式/说明 |
|------|------|---------|---------|
| startPort | 起运港 | 必填 | 港口英文名，如 `SHANGHAI` |
| endPort | 目的港 | 必填 | 港口英文名，如 `SINGAPORE` |
| carrier | 船公司 | **必填** | 船公司英文名大写，如 `KMTC`。缺少此参数必须先追问客户 |
| etd | 开船时间 | 选填 | `YYYY-MM-DD`，未提及传 `""` |
| routeCode | 航线代码 | 选填 | 如 `KCM3`，未知传 `""` |
| shipName | 船名 | 选填 | 如 `AS PATRIA`，未知传 `""` |
| voyage | 航次 | 选填 | 如 `2603S`，未知传 `""` |

> ⚠️ carrier 是必填项，缺少时不可调用，需先追问客户

## 出参字段

| 字段 | 说明 |
|------|------|
| etd预计开航日 | 预计开船日期，如 `2026-04-07` |
| 晚开日期 | 晚开日期，如 `2026-04-10` |
| 船名 | 如 `AS PATRIA` |
| 航次 | 如 `2603S` |
| 航线代码 | 如 `KCM3` |
| 船期 | 星期几开船，如 `TUE` |

> data 数组可能包含多条记录，需全部展示，建议按开航日期从近到远排序

## 调用示例

```
GET https://www.dadaex.cn/api/interface/freight/getSchedule
  ?startPort=SHANGHAI
  &endPort=SINGAPORE
  &carrier=KMTC
  &etd=
  &routeCode=
  &shipName=
  &voyage=
```

## 错误处理
任何情况下不能卡住，统一处理：
- status=0 或无数据 → "暂时没有查到船期信息，请确认船公司和港口是否正确。"
- 网络超时 → "查询超时，请稍后重试。"
- 返回异常 → "船期查询出现问题，请稍后重试。"

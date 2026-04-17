# Freetime API

## 接口信息
- **方法**：GET
- **地址**：`https://www.dadaex.cn/api/interface/freight/getFreeTime`

## 入参

| 参数 | 说明 | 是否必填 | 格式/示例 |
|------|------|---------|---------|
| endPort | 目的港 | 必填 | 港口英文名大写，如 `SINGAPORE` |
| carrier | 船公司 | 必填 | 船公司英文名大写，如 `MAERSK` |

> ⚠️ 两个参数都是必填，缺少任意一个必须先追问客户

## 出参

### 查询成功
```json
{
  "message": "success",
  "data": {
    "port": "新加坡",
    "port_en": "SINGAPORE",
    "carrier": "海洋网联",
    "carrier_en": "ONE",
    "noticeContent": "3DEM+5DET"
  }
}
```

| 字段 | 说明 |
|------|------|
| port | 目的港中文名 |
| port_en | 目的港英文名 |
| carrier | 船公司中文名 |
| carrier_en | 船公司英文名 |
| noticeContent | freetime 内容，如 `3DEM+5DET`、`14天混用COMBINE` |

### 查询失败
```json
{ "code": 0, "data": {}, "message": "港口数据不完整请重新提供" }
{ "code": 0, "data": {}, "message": "船司数据不完整请重新提供" }
```

## 错误处理
- `code=0` 且 message 含"港口数据不完整" → 回复："没有找到该港口的 freetime 信息，请确认港口名称是否正确。"
- `code=0` 且 message 含"船司数据不完整" → 回复："没有找到该船司的 freetime 信息，请确认船公司名称是否正确。"
- `data` 为空对象 → 回复："暂时没有查到相关 freetime 信息。"
- 网络超时 → 回复："查询超时，请稍后重试。"
- **任何错误都不能卡住，直接回复友好提示**

## freetime 内容解读

| 格式 | 含义 |
|------|------|
| `3DEM+5DET` | 免装期3天 + 免卸期5天，分开计算 |
| `14天混用COMBINE` | 14天混用，装卸合并计算 |
| `21 DET COMBINE` | 21天混用免卸期 |
| `14DEM+7DET` | 免装14天 + 免卸7天 |

回复时要把 freetime 格式翻译成客户能看懂的中文说明。

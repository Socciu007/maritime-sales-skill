# 停航查询 API

## 接口信息
- **方法**：GET
- **地址**：`https://www.dadaex.cn/api/suspension/getSuspension`

## 入参

| 参数 | 说明 | 是否必填 | 格式/示例 |
|------|------|---------|---------|
| shipCompany | 船公司 | 选填 | 船公司英文名大写，如 `MAERSK`、`ONE` |
| routeCode | 航线代码 | 选填 | 大写，如 `GS2`、`AG3` |

> ✅ **两个参数都是选填的**，不传任何参数也可以调用，返回所有船司的近期停航数据（最多10条）
> 只返回今天及以后的停航，按开航日倒序排列

## 出参

### 有停航数据
```json
{
  "status": 1,
  "message": "成功",
  "data": {
    "停航的船期": [
      {
        "船公司": "ONE",
        "航线代码": "GS2",
        "开航日": "2026-04-30",
        "晚开时间": null
      }
    ]
  }
}
```

### 无停航数据
```json
{
  "status": 1,
  "message": "成功",
  "data": {
    "停航的船期": "未查询到停航信息"
  }
}
```

> ⚠️ **重要**：无数据时 `停航的船期` 是**字符串**而不是数组，解析前必须判断类型

## 字段说明

| 字段 | 说明 |
|------|------|
| 船公司 | 船司英文名，如 `ONE`、`MAERSK` |
| 航线代码 | 停航的航线代码，如 `GS2` |
| 开航日 | 原计划开航日期 |
| 晚开时间 | 停航后重新安排的开航时间，null 表示暂无安排 |

## 错误处理
- `停航的船期` 是字符串（"未查询到停航信息"）→ 回复："近期没有查到相关停航信息。"
- 网络超时 → 回复："查询超时，请稍后重试。"
- 任何错误都不能卡住，直接给友好提示

## 调用示例

```
# 查某船司的停航
GET .../getSuspension?shipCompany=ONE&routeCode=

# 查某航线的停航
GET .../getSuspension?shipCompany=&routeCode=GS2

# 查所有停航
GET .../getSuspension?shipCompany=&routeCode=

# 同时指定船司和航线
GET .../getSuspension?shipCompany=ONE&routeCode=GS2
```

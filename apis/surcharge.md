# 附加费 API（本地费用）

## 接口信息
- **方法**：POST
- **地址**：`https://www.dadaex.cn/api/interface/freight/getSurchargesToChat`

## 入参

| 参数 | 说明 | 是否必填 | 格式/示例 |
|------|------|---------|---------|
| start_port | 起运港 | 选填 | 默认 `SHANGHAI`，港口英文名大写 |
| end_port | 目的港 | 二选一 | 港口英文名大写，如 `SINGAPORE` |
| carrier | 船公司 | **必填** | 船公司英文名大写，如 `MAERSK` |
| routeLine | 航线名称 | 二选一 | 航线中文名，如 `东南亚线` |
| containerType | 箱型 | 选填 | 箱型代码：`GP`、`HQ`、`FR`、`OT`、`RE` 等，未提及传 `""` |

> ⚠️ **必填规则：**
> - `carrier` 必填，缺少时必须追问
> - `end_port` 和 `routeLine` 至少提供一个，都没有时必须追问
> - 两个都有时优先用 `routeLine`

### containerType 箱型枚举值

| 代码 | 箱型说明 |
|------|----------|
| `GP` | 干货箱 (General Purpose) - 20GP/40GP |
| `HQ` | 高箱 (High Cube) - 40HQ |
| `FR` | 框架箱 (Flat Rack) |
| `OT` | 开顶箱 (Open Top) |
| `RE` | 冷藏箱 (Reefer) |
| `OH` | 超高箱 (Over Height) |
| `TK` | 罐式集装箱 (Tank) |
| `HT` | 挂衣箱 (Hanger) |
| `DG` | 危险品 (Dangerous Goods) |
| `FQ` | 其他特种箱 |

> 注：当查询特种箱（FR/OT/RE等）附加费时，必须传入 `containerType` 参数

## 出参

### 查询成功
```json
{
  "data": [
    {
      "费用名称": "电放费",
      "费用英文名称": "Telex Release Fee",
      "币种": "CNY",
      "单位": "票",
      "单票价格": 300
    },
    {
      "费用名称": "码头操作费",
      "费用英文名称": "Dock Handling Fee",
      "币种": "CNY",
      "单位": "箱",
      "20gpSell": 670,
      "40gpSell": 1020,
      "40hqSell": 1020
    },
    {
      "费用名称": "SEAWAY",
      "费用英文名称": "SEAWAY",
      "备注": "可做SEAWAY"
    }
  ]
}
```

### 查询失败
```json
{ "code": 0, "data": [], "message": "起运港有误请重新提供起运港" }
{ "code": 0, "data": [], "message": "船司有误请重新提供船司" }
{ "code": 0, "data": [], "message": "小恒提示如果要查询本地费,航线或者目的港二者至少提供一个才能帮你查询本地费了" }
{ "code": 0, "data": [], "message": "抱歉系统暂未收录该航线附加费" }
{ "code": 0, "data": [], "message": "暂无附加费" }
```

## 出参字段说明

### 按票费用（单位=票）
| 字段 | 说明 |
|------|------|
| 费用名称 | 中文名称 |
| 费用英文名称 | 英文名称 |
| 币种 | 如 `CNY`、`USD` |
| 单位 | `票` |
| 单票价格 | 每票价格 |

### 按箱费用（单位=箱）
| 字段 | 说明 |
|------|------|
| 费用名称 | 中文名称 |
| 费用英文名称 | 英文名称 |
| 币种 | 如 `CNY`、`USD` |
| 单位 | `箱` |
| 20gpSell | 20GP 价格 |
| 40gpSell | 40GP 价格 |
| 40hqSell | 40HQ 价格 |

### 特种箱费用字段
当传入 `containerType` 为特种箱（FR/OT/RE等）时，可能返回以下字段：

| 字段 | 说明 |
|------|------|
| `specialContainerSell` | 特种箱附加费（币种和单位视具体费用而定） |
| `overweightFee` | 超重附加费 |
| `oversizeFee` | 超尺附加费 |

> 注：特种箱附加费结构可能与普通箱不同，以API实际返回为准

### SEAWAY 特殊字段
只有 `费用名称`、`费用英文名称`、`备注` 三个字段，单独一行展示。

## 错误处理
- `code=0` + 任何 message → 直接把 message 内容告知客户，不重试
- `data` 为空数组 → 回复："暂无该航线的附加费信息。"
- 网络超时 → 回复："查询超时，请稍后重试。"
- **任何错误都不能卡住**

## 追问规则

| 缺少参数 | 追问话术 |
|---------|---------|
| carrier | "请问您要查哪家船司的附加费？" |
| end_port 和 routeLine 都没有 | "请问您要查哪个目的港或哪条航线的附加费？" |
| 查特种箱缺containerType | "请问您要查什么箱型的附加费？（如FR框架箱、OT开顶箱等）" |

## 调用示例

### 普通箱附加费查询
```json
{
  "carrier": "MAERSK",
  "end_port": "SINGAPORE",
  "start_port": "SHANGHAI",
  "routeLine": "",
  "containerType": ""
}
```

### 特种箱附加费查询（以40FR为例）
```json
{
  "carrier": "ONE",
  "end_port": "LAEM CHABANG",
  "start_port": "SHANGHAI",
  "routeLine": "",
  "containerType": "FR"
}
```

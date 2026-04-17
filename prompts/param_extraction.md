# 参数提取

## 任务
从客户输入和上下文对话中提取调用 API 所需的全部参数。
无法提取的字段一律返回空字符串 `""`，不得猜测或捏造。

## 输出格式

```json
{
  "start_port": "",
  "end_port": "",
  "carrier": "",
  "etd": "",
  "hhtime": "",
  "routeCode": "",
  "route": "",
  "shipName": "",
  "voyage": "",
  "type": "",
  "portType": "",
  "order_blNo": [],
  "order_portname": "",
  "order_carrier": "",
  "order_vessel": "",
  "order_voyage": "",
  "order_line_code": "",
  "order_start_time": "",
  "order_end_time": "",
  "order_etd_time": ""
}
```

---

## 各字段提取规则

### start_port / end_port（港口）
- 将中文港口名转换为对应英文名（**小写**）
- 客户说什么港口，就用该港口对应的英文名，不做归并
- 映射表见 → `knowledge/port_mapping.md`

示例：
- "上海" → `shanghai`
- "盐田" → `yantian`
- "南沙" → `nansha`
- "巴生" → `port klang`
- "新加坡" → `singapore`

---

### carrier（船公司）
- 将中文名或简称转换为英文全称（**大写**）
- 映射表见 → `knowledge/carrier_mapping.md`

示例：
- "马士基" / "Maersk" → `MAERSK`
- "中远" → `COSCO`
- "长荣" → `EVERGREEN`
- "太荣" / "KMTC" → `KMTC`

---

### etd（预计开船时间）/ hhtime（货好时间）
- 统一转换为 `YYYY-MM-DD` 格式
- 未提及时传 `""`
- 当前日期由系统提供，根据当前日期计算相对日期

| 客户说 | 转换规则 |
|--------|---------|
| 今天 | 当前日期 |
| 明天 | 当前日期 + 1天 |
| 后天 | 当前日期 + 2天 |
| 下周三 | 计算下一个周三的具体日期 |
| 下周 | 取下周一日期 |
| 5.6 / 5-6 / 5/6 | 当年5月6日（**中国习惯：月在前**） |
| 4.27 / 4-27 | 当年4月27日 |
| 具体日期如 2026-05-01 | 直接使用 |

---

### routeCode（航线代码）/ route（航线名称）
- 客户直接提及时提取（如"TP1"、"AEX"、"东南亚线"）
- 未提及时传 `""`
- 在复合查询中，可从运价API返回结果中补充这两个字段

---

### shipName（船名）/ voyage（航次）
- 客户有时会说"XX轮"、"V.123航次"、"某某船 0291S航次"
- 提取船名和航次，分别填入对应字段
- 未提及时传 `""`

---

### type（直达/中转）
- 客户说"直达" → `N`
- 客户说"中转" → `Y`
- 未提及 → `""`

---

### portType（巴生港类型）
- **仅当目的港为巴生港时生效**
- 客户说"北港" → `NORTH`
- 客户说"西港" → `WEST`
- 未指定或非巴生港 → `""`

---

### 订单 API 专用参数

#### order_blNo（单号数组）
统一用数组格式传入，可混传 EB号 和 提单号：
- EB号格式：`FYEB` 开头，如 `FYEB088797-120160`
- 提单号格式：船公司代码+港口+数字，如 `ONEYSHAG72304600`、`177IAHAHS2005852`、`KMTCSHAP503825`
- 提取后放入数组：`["FYEB088797-120160"]` 或 `["ONEYSHAG72304600", "KMTCSHAP503825"]`
- 未提及时传空数组 `[]`

#### order_portname（目的港）
- 客户说"去新加坡的订单" → `SINGAPORE`（大写英文）
- 与运价/舱位的 end_port 同一港口，但订单API传**大写**

#### order_carrier（船公司）
- 同运价API的 carrier 提取规则，大写英文

#### order_vessel / order_voyage（船名/航次）
- 客户说"CALLAO BRIDGE 0291S 的订单" → vessel=`CALLAO BRIDGE`，voyage=`0291S`

#### order_line_code（航线代码）
- 客户说"东南亚线的订单" → 提取航线代码

#### order_start_time / order_end_time / order_etd_time（时间筛选）
- 格式：`YYYY-MM-DD`，未提及传空字符串 `""`
- 客户说"本周的订单" → start_time=本周一，end_time=本周日
- 客户说"今天的订单" → start_time=今天，end_time=今天

---

### userId（舱位API专用）
- 从系统 session 字符串中提取数字部分
- 示例：session = `agent%3Amain%3A001` → userId = `001`
- 示例：session = `agent%3Amain%3A123` → userId = `123`

---

## 上下文继承规则
- 如果客户在上一条消息已提供起运港/目的港，下一条消息只说"有没有位"，应继承上文的港口信息
- 参数提取要结合完整对话上下文，不能只看当前一句话

---

## 超短/极简输入处理规则（重要）

销售人员习惯极简输入，必须能正确处理以下场景：

| 客户输入 | 理解为 | 处理方式 |
|---------|--------|---------|
| `新加坡价格` | 查上海→新加坡运价 | start_port默认shanghai，查运价 |
| `KMTC新加坡` | 查KMTC上海→新加坡运价 | carrier=KMTC，查运价 |
| `马士基巴生` | 查MAERSK上海→巴生运价 | carrier=MAERSK，end_port=port klang |
| `新加坡舱位` | 查上海→新加坡舱位 | 查舱位API |
| `巴生有没有位` | 查上海→巴生舱位 | 查舱位API |
| `中远新加坡船期` | 查COSCO上海→新加坡船期 | 查船期API |
| `FYEB088797` | 查该EB号订单 | blNo=["FYEB088797..."] 查订单 |
| `ONEYSHAG72304600` | 查该提单订单 | blNo=["ONEYSHAG72304600"] 查订单 |
| `新加坡` 单独一个字 | 询问新加坡相关，追问意图 | 问"请问您要查运价、舱位还是船期？" |
| `长荣` 单独船司名 | 询问该船司，追问 | 问"请问您要查长荣哪个港口的信息？" |

**起运港默认规则：**
销售输入不带起运港时，**默认起运港为上海（shanghai）**，不需要追问。

**意图默认规则：**
只有港口+船司，没有其他词时，默认查**运价**。

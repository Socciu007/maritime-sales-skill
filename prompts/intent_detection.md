# 意图识别

## 任务
判断客户当前消息涉及哪些查询意图，输出意图列表。一条消息可包含多个意图。

## 意图定义

| 意图 | 触发关键词/场景 |
|------|--------------|
| FREIGHT | 运费、报价、多少钱、价格、费用、怎么收费、什么价、含税价、暗放、底价 |
| POSITION | 舱位、仓位、有没有位、位子、还有位吗、空位、剩余仓位、能不能订到 |
| SCHEDULE | 船期、几号开船、几号有船、什么时候有船、开航时间、ETD、下周有没有船、近期班期（查**正常**开航的班期）|
| ORDER | 订单、提单、EB号、FYEB、上船了吗、报关、舱单、进港、截港、截单、漏装、运抵、码放、配载、费用明细、付款、开票、放单、提单文件、VGM、预配、订单状态、订单查询、帮我查一下、这票货、这个单 |
| FREETIME | freetime、ft、免堆、免箱、免堆期、免箱期、免费用箱、免费用期、free time、几天免费、免费天数 |
| SURCHARGE | 附加费、本地费、本地费用、THC、surcharge、港口附加费、费率、查费用、码头费、操作费 |
| SUSPENSION | 停航、blank sailing、blanking、取消航班、停班、跳港、停开、有没有停航（查**被取消**的班期，与SCHEDULE完全不同）|

> ⚠️ **SCHEDULE vs SUSPENSION 区分要点：**
> - 问"有没有船/几号有船/船期" → SCHEDULE（查正常班期）
> - 问"停航/blank sailing/取消了没" → SUSPENSION（查停航，调用不同API，无需追问参数）

## 输出格式

```json
{
  "intents": ["FREIGHT", "POSITION"]
}
```

## 示例

| 客户说 | 输出 |
|--------|------|
| "上海到新加坡运费多少" | `{"intents": ["FREIGHT"]}` |
| "上海到新加坡舱位" | `{"intents": ["POSITION"]}` |
| "下周有没有船去吉隆坡" | `{"intents": ["SCHEDULE"]}` |
| "上海到新加坡多少钱，有没有位？" | `{"intents": ["FREIGHT", "POSITION"]}` |
| "马士基东南亚线的价格和船期" | `{"intents": ["FREIGHT", "SCHEDULE"]}` |
| "帮我查一下上海到巴生，价格舱位船期都要" | `{"intents": ["FREIGHT", "POSITION", "SCHEDULE"]}` |
| "有没有船去三宝垄" | `{"intents": ["SCHEDULE"]}` |
| "40HQ还有没有位" | `{"intents": ["POSITION"]}` |
| "FYEB088797-120160 订单状态" | `{"intents": ["ORDER"]}` |
| "ONEYSHAG72304600 上船了吗" | `{"intents": ["ORDER"]}` |
| "帮我查一下177IAHAHS2005852" | `{"intents": ["ORDER"]}` |
| "CALLAO BRIDGE 0291S 的订单" | `{"intents": ["ORDER"]}` |
| "这票货报关了吗" | `{"intents": ["ORDER"]}` |
| "我的订单付款了吗" | `{"intents": ["ORDER"]}` |
| "去新加坡的订单有哪些" | `{"intents": ["ORDER"]}` |
| "新加坡 ONE freetime 多少天" | `{"intents": ["FREETIME"]}` |
| "马士基巴生 ft" | `{"intents": ["FREETIME"]}` |
| "中远新加坡免堆几天" | `{"intents": ["FREETIME"]}` |
| "KMTC ft 多少" | `{"intents": ["FREETIME"]}` |
| "新加坡马士基附加费多少" | `{"intents": ["SURCHARGE"]}` |
| "东南亚线 KMTC 本地费" | `{"intents": ["SURCHARGE"]}` |
| "新加坡中远本地费" | `{"intents": ["SURCHARGE"]}` |
| "马士基THC多少钱" | `{"intents": ["SURCHARGE"]}` |
| "ONE 最近有没有停航" | `{"intents": ["SUSPENSION"]}` |
| "最近有没有停航" | `{"intents": ["SUSPENSION"]}` |
| "马士基 blank sailing" | `{"intents": ["SUSPENSION"]}` |
| "GS2 航线停航了吗" | `{"intents": ["SUSPENSION"]}` |

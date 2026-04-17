# 示例：只问舱位

## 场景
客户只询问舱位，提供了起运港和目的港，未提船公司。

## 对话

**客户：** 上海到新加坡舱位

**意图识别：** `POSITION`

**参数提取：**
```json
{
  "end_port": "singapore",
  "carrier": "",
  "routeCode": "",
  "route": "",
  "delayDate": "",
  "portType": "",
  "type": ""
}
```

**userId：** 从 session 提取，如 `001`

**调用 API：** 舱位API（carrier 为空也可调用）

**期望回复示例：**

---

上海 → 新加坡 舱位信息如下：

**【COSCO · CME】**
船期 ETD：2026-04-17  晚开日：2026-04-17
船名/航次：COSCO COLOMBO 141S
剩余仓位：1箱 ⚠️
起运港码头：明东（外5）
现舱港：PORT KELANG, SINGAPORE
价格（正常价）：20GP $750 | 40GP $1,500 | 40HQ $1,500
中转港：SINGAPORE

---

⚠️ 目前仓位非常紧张，仅剩 1 箱，建议尽快确认！

需要帮您安排订舱吗？

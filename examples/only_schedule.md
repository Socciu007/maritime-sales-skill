# 示例：只问船期（含缺参追问）

## 场景A：提供了船公司

**客户：** 马士基下周上海到新加坡的船期

**意图识别：** `SCHEDULE`

**参数提取：**
```json
{
  "startPort": "SHANGHAI",
  "endPort": "SINGAPORE",
  "carrier": "MAERSK",
  "etd": "2026-04-13",
  "routeCode": "",
  "shipName": "",
  "voyage": ""
}
```

**调用 API：** 船期API（carrier 已有，可直接调用）

**期望回复示例：**

---

上海 → 新加坡（MAERSK）下周船期如下：

**【AS PATRIA · 2603S · KCM3】**
预计开航：2026-04-07（周二）  截关日：2026-04-10

**【KMTC KEELUNG · 2603S · KMSK/S】**
预计开航：2026-04-04（周日）  截关日：2026-04-11

---

## 场景B：未提供船公司（需追问）

**客户：** 上海到新加坡下周有没有船

**意图识别：** `SCHEDULE`

**参数提取：**
```json
{
  "startPort": "SHANGHAI",
  "endPort": "SINGAPORE",
  "carrier": "",
  "etd": "2026-04-13"
}
```

**缺参检查：** carrier 为空，船期API必填，**需追问**

**期望回复：**

---

请问您要查哪家船公司的船期呢？

---

**客户：** 中远的

**继续提取：** carrier = `COSCO`

**调用 API：** 船期API

（后续按正常船期格式回复）

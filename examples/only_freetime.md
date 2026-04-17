# 示例：Freetime 查询

---

## 场景A：标准问法

**客户：** 新加坡 ONE freetime 多少天

**意图识别：** `FREETIME`

**参数提取：**
```json
{
  "endPort": "SINGAPORE",
  "carrier": "ONE"
}
```

**期望回复：**

---

ONE 到新加坡的 freetime：**3天免装期 + 5天免卸期（分开计算）**

即提箱后3天内须装货完毕，卸货后5天内须还箱。

---

## 场景B：懒人输入

**客户：** 新加坡马士基 ft

**意图识别：** `FREETIME`

**参数提取：**
```json
{
  "endPort": "SINGAPORE",
  "carrier": "MAERSK"
}
```

---

## 场景C：只说港口没说船司

**客户：** 新加坡 freetime

**意图识别：** `FREETIME`

**缺参检查：** carrier 为空，必须追问

**期望回复：** 请问您要查哪家船司到新加坡的 freetime？

---

## 场景D：只说船司没说港口

**客户：** 中远 ft 多少天

**意图识别：** `FREETIME`

**缺参检查：** endPort 为空，必须追问

**期望回复：** 请问您要查中远哪个港口的 freetime？

---

## 场景E：查不到数据

**API 返回：**
```json
{ "code": 0, "data": {}, "message": "港口数据不完整请重新提供" }
```

**期望回复：** 没有找到该港口的 freetime 信息，请确认港口名称是否正确。

---

## 场景F：混用 freetime

**API 返回 noticeContent：** `14天混用COMBINE`

**期望回复：**

KMTC 到巴生的 freetime：**14天混用**

即提箱到还箱合计14天，装卸时间合并计算。

# 总览

**cancel**(`id`): `void`

根据通知id取消通知。此函数只能取消本应用发出的通知，也即通过[notify](https://pro.autojs.org/docs/zh/v9/generated/modules/notification.html#notify)函数显示的通知。要获取和取消其他应用的通知，请通过[requestListeningNotifications](https://pro.autojs.org/docs/zh/v9/generated/modules/notification.html#requestlisteningnotifications)

# 参数

| 名称 | 类型   | 默认值 | 描述 |
| ---- | ------ | ------ | ---- |
| id   | number | tbd    | notify通知时传入的id，参见[notify](https://pro.autojs.org/docs/zh/v9/generated/modules/notification.html#notify)     |


# 返回值

void

---
2023-02-15
# 总览

**requestListeningNotifications**(`options?`): `Promise`<[`NotificationListenerService`](https://pro.autojs.org/docs/zh/v9/generated/interfaces/notification.NotificationListenerService.html)>

请求监听通知，使本应用获取通知监听服务的权限。返回通知监听服务[NotificationListenerService](https://pro.autojs.org/docs/zh/v9/generated/modules/%5BNotificationListenerService%5D(../interfaces/notification.NotificationListenerService.md))的Promise对象。

**示例**

```javascript
"nodejs";
const notification = require('notification');

async function main() {
    const notificationListenerService = await notification.requestListeningNotifications({
        toast: true,
    });
    console.log('listening notifications...');
    notificationListenerService.on("notification", n => {
        console.log("收到通知");
        console.log(`标题: ${n.getTitle()}, 文本: ${n.getText()}, 包名: ${n.getPackageName()}`);
    });

    $autojs.keepRunning();
}
main();
```
# 参数

| 名称     | 类型                                                                                                                                                    | 默认值 | 描述 |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ | ---- |
| options? | [`RequestListeningNotificationsOptions`](https://pro.autojs.org/docs/zh/v9/generated/interfaces/notification.RequestListeningNotificationsOptions.html) | tbd    | 请求监听通知的选项，比如显示给用户的Toast信息     |


# 返回值

`Promise`<[`NotificationListenerService`](https://pro.autojs.org/docs/zh/v9/generated/interfaces/notification.NotificationListenerService.html)>

---
2023-02-15
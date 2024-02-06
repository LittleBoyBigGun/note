# 总览

**notify**(`id`, `n`): `void`

根据通知选项创建通知，并显示通知。参数`id`是通知的唯一标识，若之前已经有相同id的通知，再次调用`notify`则会更新该通知。`id`也可用于取消通知

**示例**

```javascript
"nodejs";
const notification = require('notification');

const notificationId = 10001;
notification.notify(notificationId, {
    contentTitle: "点击触发一条新通知",
    contentText: "这是一条无法被用户清理的通知",
    ticker: "收到一条新通知",
    onContentClick: () => {
        showCounterNotification(0);
    },
    ongoing: true,
    autoCancel: true,
});
```

# 参数

| 名称 | 类型                     | 默认值 | 描述                                                           |
| ---- | ------------------------ | ------ | -------------------------------------------------------------- |
| id   | number                   | tbd    | 通知的唯一id，为了避免和Auto.js本身的通知冲突，建议从10000开始 |
| n    | BuildNotificationOptions | tbd    | 通知选项，包括标题、内容等，参见[BuildNotificationOptions](https://pro.autojs.org/docs/zh/v9/generated/interfaces/notification.BuildNotificationOptions.html)                                                               |

# 返回值

void

---
2023-02-15
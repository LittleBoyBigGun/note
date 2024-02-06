# 总览

**requestScreenCapture**(`options?`, `context?`): `Promise`<[`ScreenCapturer`](https://pro.autojs.org/docs/zh/v9/generated/classes/media_projection.ScreenCapturer.html)>

请求截图权限，并返回[ScreenCapturer](https://pro.autojs.org/docs/zh/v9/generated/classes/media_projection.ScreenCapturer.html)对象的Promise。如果用户拒绝或遇到错误，则会抛出一个[ScreenCaptureRequestError](https://pro.autojs.org/docs/zh/v9/generated/classes/media_projection.ScreenCaptureRequestError.html)。

请求截图权限需要启动新的Activity，因此在Android 10及以上，只有应用在前台时才能申请，并且截图期间需要保持前台服务运行，否则会无法收到新截图。

**示例**

```javascript
"nodejs";
const { requestScreenCapture } = require("media_projection");

async function main() {
  const capturer = await requestScreenCapture();
  const img = await capturer.nextImage();
  console.log(img);
}
main();
```

# 参数

| 名称     | 类型                 | 默认值 | 描述                                                    |
| -------- | -------------------- | ------ | ------------------------------------------------------- |
| options? | ScreenCaptureOptions | tbd    | 截图选项                                                |
| context? | any                  | tdb    | 用于启动请求截图权限的Activity的Context，一般无需此参数 | 

# 返回值

`Promise`<[`ScreenCapturer`](https://web.archive.org/web/20221207125225/https://pro.autojs.org/docs/zh/v9/generated/classes/media_projection.ScreenCapturer.html)>

---
2023-02-15
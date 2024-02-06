# 总览

**createOCR**(`options?`): `Promise`<[`OCR`](https://web.archive.org/web/20221207141242/https://pro.autojs.org/docs/zh/v9/generated/interfaces/ocr.OCR.html)>

根据给定选项，创建OCR对象，可用于文字识别。一般而已不必自定义参数，使用`createOCR`即可创建有效的OCR对象。

**示例**

```javascript
"nodejs";
const { createOCR } = require('ocr');
const { requestScreenCapture } = require('media_projection');
const { showToast } = require('toast');
const { delay } = require('lang');

async function main() {
    // 创建OCR对象，需要先在Auto.js Pro的插件商店中下载官方OCR插件。
    const ocr = await createOCR({
        models: 'default', // 指定精度相对高但速度较慢的模型
    });
    const capturer = await requestScreenCapture();
    for (let i = 0; i < 5; i++) {
        const capture = await capturer.nextImage();

        // 检测截图文字并计算检测时间，首次检测的耗时比较长
        // 检测时间取决于图片大小、内容、文字数量
        // 可通过调整createOCR的线程、CPU模式等参数调整检测效率
        const start = Date.now();
        const result = await ocr.detect(capture);
        const end = Date.now();
        console.log(result);

        showToast(`第${i + 1}次检测: ${end - start}ms`);
        await delay(3000);
    }
    ocr.release();
}

main().catch(console.error);
```

# 参数

| 名称     | 类型             | 默认值 | 描述              |
| -------- | ---------------- | ------ | ----------------- |
| options? | CreateOCROptions | tbd    | 创建OCR对象的选项 | 


# 返回值

`Promise`<[`OCR`](https://web.archive.org/web/20221207141242/https://pro.autojs.org/docs/zh/v9/generated/interfaces/ocr.OCR.html)>

---
2023-02-15
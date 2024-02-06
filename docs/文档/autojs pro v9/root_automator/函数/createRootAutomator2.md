# 总览

**createRootAutomator2**(`options?`): `Promise`<[`RootAutomator2`](https://web.archive.org/web/20221207121617/https://pro.autojs.org/docs/zh/v9/generated/interfaces/root_automator.RootAutomator2.html)>

根据选项创建一个新的RootAutomator2实例。RootAutomator2相比RootAutomator有更好的兼容性。

可以指定是否使用root权限、adb权限等，参见ShellOptions。如果不指定root或adb权限，则默认用getDefaultShellOptions获取的默认值。

示例：

```javascript
"nodejs";

const { createRootAutomator2 } = require('root_automator');
const { delay } = require('lang');
const { device } = require('device');

const { screenWidth, screenHeight } = device;

async function main() {
    const ra = await createRootAutomator2({ root: true });

    await ra.tap(200, 200);
    await delay(1000);

    await ra.press(screenWidth / 2, screenHeight / 2, 500);
    await delay(1000);

    await ra.swipe(500, 800, 500, 1000, 300);
    await delay(1000);

    let p0 = {
        x: screenWidth / 6,
        y: screenHeight / 6,
    };
    let p1 = {
        x: screenWidth - p0.x,
        y: screenHeight - p0.y,
    }
    ra.touchDown([
        { x: p0.x, y: p0.y, id: 0 },
        { x: p1.x, y: p1.y, id: 1 },
    ]);

    const steps = 20;
    const stepX = Math.round((p1.x - p0.x) / steps) / 2;
    const stepY = Math.round((p1.y - p0.y) / steps) / 2;
    for (let i = 0; i < steps; i++) {
        ra.touchMove([
            { x: p0.x + stepX * i, y: p0.y + stepY * i, id: 0 },
            { x: p1.x - stepX * i, y: p1.y - stepY * i, id: 1 }
        ]);
    }
    await ra.touchUp();
    await ra.flush();

    await ra.exit();
}

main().catch(console.error);
```

# 参数

| 名称     | 类型        | 默认值 | 描述                     |
| -------- | ----------- | ------ | ------------------------ |
| options? | ShellOptios | tbd    | 创建RootAutomator2的选项 | 


# 返回值

`Promise`<[`RootAutomator2`](https://web.archive.org/web/20221207121617/https://pro.autojs.org/docs/zh/v9/generated/interfaces/root_automator.RootAutomator2.html)>

---
2023-02-15

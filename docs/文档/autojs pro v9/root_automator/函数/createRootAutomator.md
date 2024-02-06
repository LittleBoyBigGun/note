# 总览

**createRootAutomator**(`options?`): `Promise`<[`RootAutomator`](https://web.archive.org/web/20221207121617/https://pro.autojs.org/docs/zh/v9/generated/interfaces/root_automator.RootAutomator.html)>

根据选项创建一个新的RootAutomator实例。

可以指定是否使用root权限、adb权限、输入设备路径等，参见[RootAutomatorOptions](https://web.archive.org/web/20221207121617/https://pro.autojs.org/docs/zh/v9/generated/interfaces/root_automator.RootAutomatorOptions.html)。如果不指定root或adb权限，则默认用getDefaultShellOptions获取的默认值。

对于输入设备路径`inputDevice`，如果不指定，则会自动检测，但检测失败时会抛出异常；你也可以手动在终端运行`getevent -t`，然后在屏幕上操作，看输入事件的设备路径是什么，比如`/dev/input/event5`。

>从Pro 9.3开始，推荐使用[createRootAutomator2](https://web.archive.org/web/20221207121617/https://pro.autojs.org/docs/zh/v9/generated/modules/root_automator.html#createrootautomator2)来代替RootAutomator，相比RootAutomator，它有更好的设备兼容性。

示例：

```javascript
"nodejs";
const { createRootAutomator } = require("root_automator");
async function main() {
    const ra = await createRootAutomator({root: true});
    await ra.tap(100, 100);
    await ra.exit();
}
main();
```

# 参数

| 名称     | 类型                 | 默认值 | 描述                    |
| -------- | -------------------- | ------ | ----------------------- |
| options? | RootAutomatorOptions | tdb    | 创建RootAutomator的选项 | 


# 返回值

`Promise`<[`RootAutomator`](https://web.archive.org/web/20221207121617/https://pro.autojs.org/docs/zh/v9/generated/interfaces/root_automator.RootAutomator.html)>

---
2023-02-15
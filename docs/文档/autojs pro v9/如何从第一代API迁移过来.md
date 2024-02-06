如果你对第一代API已比较熟悉，本文将引导你如何从第一代API迁移到第二代API。如果你想了解两代API的优缺点。参考[[阅读须知#第二代API和第一代API的区别|第一代api和第二代api的区别]]

# 启用第二代API与Node.js引擎

为了向前兼容，Pro 9中的代码仍然默认为旧的Rhino引擎/第一代API运行。要使用第二代API需要特别标识，参考[[阅读须知#用 node.js 引擎执行代码|用 node.js 引擎执行代码]]

# 从全局变量函数改为导入模块

在第二代API中，所有模块都需要导入才能使用。在第一代API中的全局变量，比如app, images模块，在第二代API中都需要导入后使用，比如：

```javascript
// 第一代API
app.viewFile(...)

// 第二代API
const app = require('app');
app.viewFile(...)

// 也可以这样只导入需要的函数
const { viewFile } = require('app');
viewFile(...)
```

第一代API中的全局变量、函数大多数也不能直接使用，比如`sleep`, `log`, `toast`：

```javascript
// 第一代API
sleep(1000)
log(context.getPackageName())
toast('')

// 第二代API
const { delay } = require('lang');
const { showToast } = require('toast')

async function main() {
    await delay(1000);
    const context = $autojs.androidContext;
    console.log(context.getPackageName());
    showToast('Hello');
}
main();
```

# 模块与函数对照表

在第二代API中，一部分模块的名称和第一代相似，比如app, color；一部分模块的功能则迁移到其他模块；一部分模块则由Node.js自带模块代替，比如files模块由Node.js的fs和path模块代替；一部分模块则由第三方npm模块代替，这些模块往往更加完善，比如WebSocket由ws模块代替。

以下是各个第一代API模块在第二代API中的对照或代替。需要注意即使模块名字一样，API设计也可能有所不同。比如在第一代API中获取屏幕宽度是`device.width`，在第二代API中则是`device.screenWdith`。

- **app模块**
在第二代API中使用[[文档/autojs pro v9/app/总览|app]]模块。
- **base64模块**
在第二代API中使用Buffer代替，比如字符串转换base64：`Buffer.from('autojs', 'utf8').toString('base64')`，base64转换为字符串：`Buffer.from('YXV0b2pz', 'base64').toString('utf8')`。
- **colors模块**
在第二代API中使用[[文档/autojs pro v9/color/总览|color]]模块。
- **canvas**
UI界面中的canvas与旧版canvas类似。暂不支持无Ui界面下使用Canvas。
- **console模块**
对于`console.log`等函数直接使用即可，无需迁移。设置日志路径等额外参数参考[[文档/autojs pro v9/globals/接口/Console|Console]]。

另外，`print`，`log`等函数需要使用`console.log`代替，不能简写。比如`log('hello')`需要替换为`console.log('hello')`。
- **crypto模块**
在第二代API中使用Node.js模块[crypto](http://nodejs.cn/api-v12/crypto.html)。
- **debug模块**
在第二代API中暂无代替
- **device模块**
在第二代API中使用[[文档/autojs pro v9/device/总览|device]]模块
- **dialogs模块**
在第二代API中使用[[文档/autojs pro v9/dialogs/总览|dialogs]]模块。
- **engines模块**
在第二代API中使用[[文档/autojs pro v9/engines/总览|engines]]模块。
- **events模块**
第一代API中events模块的事件在第二代API对应如下：

1.  `exit`: 用`process.on('exit', () => {})`代替
- **floaty模块**
在第二代API中使用[[文档/autojs pro v9/floating_window/总览|floating_window]]模块。
- **files模块**
在第二代API中使用Node.js模块[fs](http://nodejs.cn/api-v12/fs.html)和[path](http://nodejs.cn/api-v12/path.html)
- **globals全局函数与变量**
	- `sleep` 使用[[文档/autojs pro v9/lang/总览|lang]]模块中的`delay`函数代替，比如`await delay(1000)`
	- `toast`, `toastLog` 在[[文档/autojs pro v9/toast/总览|toast模块]]中，比如`showToast('hello', {log: true})`, `showToast('hello', {log: true})`
	- `exit` 使用Node.js函数`process.exit()`代替
	- `random` 使用`Math.random`代替
	- `requiresApi` 使用`device模块`的[[文档/autojs pro v9/device/函数/requiresAndroidVersion|requiresAndroidVersion]]代替
	- `requiresAutojsVersion` 使用`process.versions.autojspro`获取Auto.js版本后判断
	- `runtime.requesetPermissions` 在ui.Activity中使用`this.requesetPermissions`代替
	- `runtime.loadJar`, `runtime.loadDex` 使用`$java.loadJar`/`$java.loadDex`代替，参加[[文档/autojs pro v9/globals/接口/java|java]]
	- `context` 使用`$autojs.androidContext`代替
 - **http模块**
 推荐使用内置的[[文档/autojs pro v9/axios/总览|axios]]模块代替，功能比http模块强大得多
 - **media**
 在第二代API中使用[[文档/autojs pro v9/media/总览|media]]模块。
 - **plugins**
 在第二代API中使用[[文档/autojs pro v9/plugins/总览|plugins]]模块。
 - **power_manager**
 在第二代API中使用[[文档/autojs pro v9/power_manager/总览|power_manager]]模块。
 - **sensors**
 在第二代API中使用[[文档/autojs pro v9/sensors/总览|sensors]]模块。
 - **shell**
 在第二代API中使用[[文档/autojs pro v9/shell/总览|shell]]模块。
 - **storages**
 在第二代API中使用[[文档/autojs pro v9/datastore/总览|datastore]]模块。
 - **settings**
 在第二代API中使用[[文档/autojs pro v9/settings/总览|settings]]模块。
 - **threads**
 参考[[#多线程与异步]]。
 - **timers**
 在第二代API中使用[timers](http://nodejs.cn/api-v12/timers.html)模块。实际上`setTimeout`, `setInterval`等全局函数在第二代API中也可直接使用。
 - **work_manager**
 在第二代API中使用[[文档/autojs pro v9/work_manager/总览|work_manager]]模块。
 - **ui**
 在第二代API中使用[[文档/autojs pro v9/ui/总览|ui]]模块。UI模块由于有较大的API差别。参考[[#UI界面代码迁移]]
 - **util**
 在第二代API中使用Node.js模块[util](http://nodejs.cn/api-v12/util.html)
 - **WebSocket**
 使用npm模块[ws](https://www.npmjs.com/package/ws)代替。ws模块比第一代API模块中的WebSocket功能更完整、更强大
 - **zip**
 在第二代API中使用[[文档/autojs pro v9/zip/总览|zip]]模块
 - 
**CPUPowerMode**: `"LITE_POWER_HIGH"` | `"LITE_POWER_LOW"` | `"LITE_POWER_FULL"` | `"LITE_POWER_NO_BIND"` | `"LITE_POWER_RAND_HIGH"` | `"LITE_POWER_RAND_LOW"`

CPU模式。

- `LITE_POWER_HIGH` 绑定大核运行模式。如果 ARM CPU 支持 big.LITTLE，则优先使用并绑定 Big cluster，如果设置的线程数大于大核数量，则会将线程数自动缩放到大核数量。如果系统不存在大核或者在一些手机的低电量情况下会出现绑核失败，如果失败则进入不绑核模式。

- `LITE_POWER_LOW` 绑定小核运行模式。如果 ARM CPU 支持 big.LITTLE，则优先使用并绑定 Little cluster，如果设置的线程数大于小核数量，则会将线程数自动缩放到小核数量。如果找不到小核，则自动进入不绑核模式。

- `LITE_POWER_FULL` 大小核混用模式。线程数可以大于大核数量，当线程数大于核心数量时，则会自动将线程数缩放到核心数量。

- `LITE_POWER_NO_BIND` 不绑核运行模式（推荐）。系统根据负载自动调度任务到空闲的 CPU 核心上。

- `LITE_POWER_RAND_HIGH` 轮流绑定大核模式。如果 Big cluster 有多个核心，则每预测10次后切换绑定到下一个核心。

- `LITE_POWER_RAND_LOW` 轮流绑定小核模式。如果 Little cluster 有多个核心，则每预测10次后切换绑定到下一个核心。

**`参见`**

[createOCR](https://pro.autojs.org/docs/zh/v9/generated/modules/ocr.html#createocr)
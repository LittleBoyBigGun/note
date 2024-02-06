# 总览

**performGesture**(`points`, `duration`, `delay?`): `Promise`<`boolean`>

模拟手势。依次滑动多个点的折线路径，可通过大量点来模拟曲线。

# 参数

| 名称     | 类型    | 默认值    | 描述                   |
| -------- | ------- | --------- | ---------------------- |
| points   | Point[] | undefined | 路径，由点的数组构成。 |
| duration | number  | undefined | 滑动时长，单位毫秒     |
| delay    | number  | 0         | 滑动开始延迟，单位毫秒 | 

# 返回值

`Promise`<`boolean`>

返回是否运行成功的Promise

---
2023-02-15
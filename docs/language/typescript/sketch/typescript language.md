#typescript

# 面向对象

## class

```ts
class Point{
	x:number
	y:number
}

const pt = new Point()

pt.x = 10
pt.y = 11

console.log(pt)
//Point{x:10, y:11}
```

### 成员带默认值 

```ts
class Point {
	x=0
	y=0
}
```

# index signatures

索引签名
[TypeScript 中的 Index Signatures - 掘金 (juejin.cn)](https://juejin.cn/post/7017410361221447687)

`[name: type]: type2`

可以注解对象， 限制对象的结构


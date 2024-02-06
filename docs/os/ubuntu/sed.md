# 插入

## 某行前(i)后（a)插入

```shell
sed -i "line i content"
sed -i "line a content"
sed -i "i content" #每一行插入
sed -i "$a a content" #最后一行
sed -i "start,end i content"
```


# 使用script 文件

```shell
sed -f test.sed filename
```

# 动作

```shell
sed -e "scope action" filename
```

scope action 可以写到.sed 文件中， 使用 -f 使用改配置。

## scope

- n
- n,m

## action

- a    新增
- d    删除
- p    打印
- c     取代(范围)
- i      插入
- s     取代（正则)


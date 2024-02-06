
[官方文档](https://learn.microsoft.com/zh-cn/powershell/scripting/lang-spec/chapter-02?view=powershell-7.2)

# 标识符

powershell 支持 unicode 作为标识符， 同时大小写不敏感

```powershell
$你好=1111
echo $你好

$Ad="bbb"
echo $ad
```

# 注释

- 单行注释

```powershell
# 这是单行注释
```

- 多行注释

```powershell
<#
comments one
comments two
#>
```

- requires-comment

指定脚本需要满足的条件， 位于脚本起始位置， 不能出现在函数， cmdlet

```powershll
#requires whitespace command-arguments
```

共有四种 

```powershell
#requires --Assembly AssemblyId
#requires --Module ModuleName
#requires --PsSnapIn PsSnapIn [ -Version *N* [.n] ]
#requires --ShellId ShellId
```

# 续行符

```powershell
$number = 10 `
+ 20 `
- 50
$number # writes $number's value to the pipeline
-20
```

# 关键字

keyword: one of
    begin          break          catch       class
    continue       data           define      do
    dynamicparam   else           elseif      end
    exit           filter         finally     for
    foreach        from           function    if
    in             inlinescript   parallel    param
    process        return         switch      throw
    trap           try            until       using
    var            while          workflow

# 变量

[参看](https://learn.microsoft.com/zh-cn/powershell/scripting/lang-spec/chapter-02?view=powershell-7.2#232-variables)

powershell 变量
#makefile

# 为什么需要构建系统

项目规模比较小， 我们完全可以通过如下方式完成项目的编译：

```shell
gcc -o main main.c lib.c -Iinclude 
```

随着项目复杂性提高， 比如分模块等， 这种方式就显得力不从心。构建系统可以帮助解决此类问题。

# 一个简单例子

```
#makefile 

main:main.o lib.o
	cc -o main main.o lib.o
main.o:main.c
	cc -c -o main.o main.c
lib.o:lib.c
	cc -c -o lib.o lib.c

test:main
	./main
 
install:main
	mv main /usr/local/bin

.PHONY: clean
clean:
	rm main.o lib.o main
```

只需要命令行输入 `make` 就可以完成构建， 使用 `make clean ` 清理编译的中间文件。当修改了某个文件， makefile 会自动分析那些需要重新编译， 无需编译整个项目。makefile 的名字可以是

- makefile
- Makefile (推荐)
- GNUmakefile

# 命令行

## debug

如果我们只是想看看最终那些命令被执行了, 可以 `make -n`, 这对 debug 大有裨益。

## 指定 makefile 文件

make 自动识别 `makefile Makefile GNUmakefile` ， 其他形式的命名需要显式地指定 `-f filename`

## 指定 include 搜索目录

引入其他 makefile， 该从哪里搜索呢？

- `-I` 指定目录
- usr/local/include 和 usr/include

# 规则

```makefile
target1:deps 
	command
``` 

规则组成

- 目标 target1
- 依赖 deps
- 命令 command

makefile 格式要求：

- 目标名前面不能有空格
- 命令和左边必须之间有空格

一个 makefile 可能有多个规则，  `make` 默认执行第一个规则， 也可以 `make rulename` 显示指定执行的规则。依赖 `deps` 可以是

- 源文件
- 其他规则

他们之间用空格隔开。command 支持通配符 `[..] / * / ?`

## 规则执行时机

makefile 比较规则目标文件和依赖文件的时间上的先后， 来决定是否需要执行该规则。例如：main.c 修改后， main.o 的修改时间比 main.c 更旧， 所以需要编译 main.c 重新生成 main.o。此特性使得修改代码后无需重新编译整个项目， 即所谓增量编译， 加快编译速度。

如果依赖是规则， 情况稍微有一点不同， 执行该规则之前， 必须执行它的依赖。

## 隐式规则

在 [[#一个简单例子]]里边展示的是显示规则， main.o, lib.o 的编译都有对应的规则，  makefile 支持依赖推导， main 规则依赖 main.o、lib.o,  makefile 会自动推导出 main.o 规则和 lib.o 规则， 这就叫做隐式规则。

隐式规则降低了 makefile 的编写难度， 显得非常智能， 但会导致某些情况下文件修改后不会被 makefile 感知而重新编译, 笔者目前不知道何时触发该问题， 发现后显示添加相应规则即可解决。 

```makefile
OBJS=main.o lib.o

vpath %.cpp src
vpath %.hpp hdrs


locale_test:${OBJS}
  g++ -o  locale_test ${OBJS}

test:locale_test
  @echo "================"
  @./locale_test

.PHONY:clean

clean:
  rm ${OBJS} locale_test
 
```

`make -n` 输出

```shell
g++    -c -o main.o src/main.cpp
g++    -c -o lib.o src/lib.cpp
g++ -o  locale_test main.o lib.o
```

前面两条为自动推导的。

## 静态模式

```make
<targets ...>:<target-pattern..>:<deps-pattern>
	command
```

- targets 为目标集合， 支持变量， 通配符
- target-pattern 从 targets 摘取符合的目标， 例如： `%.o` 只处理 .o 结尾的目标
- deps-pattern 自动推导可以确定依赖的名字， 和目标同名。%.c 只依赖 c 文件。

```makefile
objects = foo.o bar.o func.pp
all: $(objects)

$(objects): %.o: %.c
	$(CC) -c $(CFLAGS) $< -o $@

等价于

foo.o: foo.c
	cc -c foo.c -o foo.o
bar.o: bar.c
	cc -c bar.c -o bar.o
```

### $@

自动化变量， 表示目标名(带后缀)

### $<

自动化变量， 表示依赖名

## 目标

### 伪目标

```makefile
.PHONY:target1 target2...
target1:
 command
target2:
 command
```

伪目标并不生成文件， 可以把其他规则当做依赖， 执行时必须显示指定规则名字`make clean` 。应该避免和普通规则同名， 用了 .PHONY 显式声明伪目标， 此时是可以和普通目标同名。

### 多目标

```makefile
big little: text.g
	generate text.g $@ > $@

等价于

big: text.g
	generate text.g big > big
little:text.g
	generate text.g little > little
```

多个目标依赖同一个文件， 则可以写成多目标， 配合 $@ (目标名) 和 $< （依赖名), 精简 makefile。

# 变量

makefile 支持变量， 变量一般都是字符串， 像 C 语言中的宏，当 Makefile 被执行时，其中的变量都会被扩展到相应的引用位置上。有两个作用

- 避免硬编码

```makefile
CC=gcc
CFLAGS=-o
main:main.o lib.o
	${CC} ${CFLAGS} main main.o lib.o
 .....
```

这里编译器和编译选项都用变量来表示。变量代替硬编码， 提供移植性、灵活性， 在编译大型项目时， 一旦编译器或者编译选项发生变化， 无需逐个修改。

- 提高便捷性

```makefile
OBJS=main.o lib1.o lib2.o lib3.o lib4.o lib5.o
main:${OBJS}
	gcc -o main ${OBJS}
```

这里使用变量大大减少了手动输入字符的数量

## 通配符

- * 多个字符
- ? 匹配单个字符
- `[...]` 未知

例如 `objs:=${wildcard *.o}`, 不能是 `objs=*.o`, 此时 * 被当做一个字符非通配符。

## 作用域

makefile 的变量是全局作用域。

## VPATH 和 vpath

看一个例子

```shell
.
├── Makefile
├── hdrs
│   └── lib.hpp
├── lib.o
├── locale_test
├── main.o
└── src
    ├── lib.cpp
    └── main.cpp
```

```makefile
OBJS=main.o lib.o

locale_test:${OBJS}
  g++ -o  locale_test ${OBJS}
  
test:locale_test
  @echo "================"
  @./locale_test

.PHONY:clean

clean:
  rm ${OBJS} locale_test
```

此时 `make` 会发生错误， 提示没有 main.o 这条规则， 因为 makefile 并不知道到哪里获取 main.cpp 和 lib.cpp。

可以使用 VPATH 和 vpath 告诉 makefile cpp 文件所在文件夹.

### VPATH

```makefile
VPATH = src:other_dir
```

多个文件夹用 `:` 分开， 无法精确到某一类文件所在文件夹。

### vpath

```makefile
vpath %.cpp src
vpath %.cpp other_dir
```

% 为通配符， 表示字符串。`vpath %.cpp src` 表示到 src 中寻找 cpp 文件。

>注意：对于 c/c++ 无法通过 vpath %.h headers 的方式指定头文件所在目录。有两种方式解决：
>1. 源文件 Include 的时候指定路径
>2. 编译的时候使用 -I 指定头文件所在目录

# 引用

`include <filename...>`

引入多个， 用空格隔开。filename 也支持通配符 `*`， 可以方便引入特定后缀的文件， 还可以用使用变量来引入。

```makefile
include foo.make *.mk $(bar) 

等价于

include foo.make 3.mk 4.mk 1.mk 2.mk

其中
bar=1.mk 2.mk
文件夹下有 3.mk 4.mk

```

可以使用 -I 指定引入目录， 未指定则从 usr/local/include 和 usr/include 引入

## 引入错误处理

发生错误， makefile 默认停止运行。`-inlucde xxxx` 会在引入失败后自动忽略。

## 最佳实践

- 在一个工程文件中,每一个模块都有一个独立的 Makefile 来描述它的重建规则 ，它们需要定义一组通用的变量定义或者是模式规则 ，通用的做法是将这些共同使用的变量或者模式规则定义在一个文件中,需要的时候用 include 包含这个文件
- 当根据源文件自动产生依赖文件时,我们可以将自动产生的依赖关系保存在另一个文件中 然后在 Makefile 中包含这个文件
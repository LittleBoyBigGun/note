`tlmgr` 全称 `tex live manager` ， 是 `texlive` 自带的包管理器。使用 tlmgr 可以方便的进行包的更新和安装。安装 texlive 后， 打开 cmd 输入 `tlmgr -version` , 成功显示版本信息， 则说明环境正常。

其命令格式为

```cmd
tlmgr [global options] <action> [action-specific options] [operand]
```

action  包括

- update  更新包
- info 输出包基本信息
- restore 回滚
- option 更新一些信息

# 更新包

## 查看 ctan 上可更新的包

```cmd
tlmgr update -list
```

```cmd
tlmgr.pl: package repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet (not verified: gpg unavailable)
tlmgr.pl: would save backups to C:/texlive/2022/tlpkg/backups
tlmgr.pl: skipping forcibly removed package: collection-texworks
update:   adjmulticol        [316k]: local:    62935, source:    63073
update:   hitex             [2565k]: local:    62529, source:    63073
update:   texlive-fr        [1394k]: local:    62853, source:    63071
update:   texlive-msg-translations [144k]: local:    63010, source:    63072
update:   texlive-scripts.win32 [36k]: local:    62199, source:    63068
update:   texlive-scripts    [504k]: local:    63049, source:    63068
update:   utfsym            [4766k]: local:    56729, source:    63076
update:   xduts              [871k]: local:    63013, source:    63075
update:   zwpagelayout       [641k]: local:    53965, source:    63074
```

## 更新全部的包

```cmd
tlmgr update -self -all
// 包括tlmgr
```

## 更新 pkg1, pkg2

```cmd
tlmgr update pkg1 pkg2
```

## 排除更新 pkg1

```cmd
tlmgr update -self -all -exclude pkg1
```

## 更新被意外中断

```cmd
tlmgr update -self -all -reinstall-forcibly-removed

```

# 查看包信息

## 查看 pkg1 , pkg2 基本信息

```cmd
tlmgr info pkg1 pkg2

```

## 列出更详细信息

```cmd
tlmgr info -list pkg1
```

# 回滚

## 回滚到上一个版本

```cmd
tlmgr restore pkg

```

## 回滚到指定版本

```cmd
tlmgr restore pkg revision
```

# 修改 ctan 源

```cmd
tlmgr option repository <mirror>
```

# 大陆可以用的源

|       source | url                                                        |
| ------------:|:---------------------------------------------------------- |
|       阿里云 | https://mirrors.aliyun.com/CTAN/systems/texlive/           |
| 北京交通大学 | https://mirror.bjtu.edu.cn/ctan/systems/texlive/           |
|     清华大学 | https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/ |

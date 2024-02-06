#powershell

PowerShell 是一种跨平台的任务自动化解决方案，由命令行 shell、脚本语言和配置管理框架组成。 PowerShell 在 Windows、Linux 和 macOS 上运行。

不同于其他 shell 处理文本， powershell 处理对象。且内置大量命令， 这个是其他 shell 所匮乏的。powershell 支持循环， 条件， 控制流， 变量等语言特性。

PowerShell 有四种类型的命令：脚本、函数和方法、cmdlet 和本机命令。

- 命令文件称为脚本。 根据约定，脚本的文件名扩展名为 .ps1。 PowerShell 程序的最顶层是脚本，而脚本又可以调用其他命令。
    
- PowerShell 支持通过命名过程进行模块化编程。 用 PowerShell 编写的过程称为函数，而由执行环境提供的外部过程（通常用其他语言编写）称为方法 。
    
- cmdlet（读作“command-let”）是一种简单的单任务命令行工具。 尽管 cmdlet 可以独自使用，但 cmdlet 的完整功能是在对其进行组合使用来执行复杂任务时实现的。
    
- 本机命令是内置于主机环境的命令。
    
每次 PowerShell 运行时环境开始执行时，它都会建立会话。 然后，命令在该会话的上下文中执行。


# 跨平台

powershell 基于 dotnet 平台构建， 所有 powershell 也是跨平台的。

- PowerShell 7.3 - 基于 .NET 7.0 构建
- PowerShell 7.2 (LTS-current) - 基于 .NET 6.0 (LTS-current) 构建
- PowerShell 7.1 - 基于 .NET 5.0 构建
- PowerShell 7.0 (LTS) - 基于 .NET Core 3.1 (LTS) 构建
- PowerShell 6.2 - 基于 .NET Core 2.1 构建
- PowerShell 6.1 - 基于 .NET Core 2.1 构建
- PowerShell 6.0 - 基于 .NET Core 2.0 构建

windows 平台同 linux、macos 基于的 dotnet 差异， 导致脚本存在兼容性挑战， 可以参考微软官方[迁移说明](https://learn.microsoft.com/zh-cn/dotnet/core/compatibility/fx-core)。另外可以参看 windows 和 linux、macos [差异](https://learn.microsoft.com/zh-cn/powershell/scripting/whats-new/unix-support?view=powershell-7.2)。

# 安装

## 查看版本

```powershell
echo $psversiontable
echo $host
```

## 使用 winget 安装

#winget

```powershell
#搜索版本
winget search Microsoft.PowerShell

#安装
winget install --id Microsoft.Powershell --source winget

winget install powershell
```

## 使用 msi 安装包安装

https://github.com/PowerShell/PowerShell/releases/download/v7.3.4/PowerShell-7.3.4-win-x64.msi

# 卸载

- 依然可以通过 winget 卸载
- windows 系统卸载工具

# 开发工具

- ISE 

ISE 为 windows powershell 集成开发环境， 提供图形界面， 编写， 测试， 上下文提示， 帮助等。按照如下方式可以启动 ISE。[更多](https://learn.microsoft.com/zh-cn/powershell/scripting/windows-powershell/ise/introducing-the-windows-powershell-ise?view=powershell-7.2)

```
运行--> powershell_ise.exe
```

- vscode + powershell 插件

借助插件， 可以提供提示， 执行， 语法高亮等功能， 笔者建议使用 vscode 编写 powershell 脚本。不过插件仅支持：[更多](https://learn.microsoft.com/zh-cn/powershell/scripting/dev-cross-plat/vscode/using-vscode?view=powershell-7.2)

- PowerShell 7.2 及更高版本（Windows、macOS 和 Linux）
- 具有 .NET Framework 4.8 的 Windows PowerShell 5.1（仅限 Windows）

# 启动 powershell

自 powershell 6.0 开始， powershell 二进制文件重命名为 pwsh.exe（windows) 和 pwsh（linux, macos)。6.0 以下名称为 powershell。

# DSC

PowerShell Desired State Configuration, 为系统和软件管理部署和管理配置数据。[ref1](https://learn.microsoft.com/zh-cn/powershell/dsc/overview?view=dsc-2.0)[ref2](https://blog.csdn.net/chancein007/article/details/54295796#:~:text=WindowPowerShell,DSC%E8%83%BD%E5%A4%9F%E5%B8%AE%E5%8A%A9%E6%88%91%E4%BB%AC%E7%94%A8%E6%88%B7%E7%9A%84%E8%B5%84%E6%BA%90%E5%9C%A8%E6%95%B0%E6%8D%AE%E4%B8%AD%E5%BF%83%E8%A2%AB%E6%AD%A3%E7%A1%AE%E7%9A%84%E9%85%8D%E7%BD%AE%EF%BC%9BDSC%E6%98%AFPowerShell%E8%AF%AD%E8%A8%80%E7%9A%84%E6%89%A9%E5%B1%95%EF%BC%8C%E4%B8%BA%E6%95%B0%E6%8D%AE%E4%B8%AD%E5%BF%83%E7%9A%84%E8%B5%84%E6%BA%90%E6%8F%90%E4%BE%9B%E4%BA%86%E5%8F%AF%E7%94%B3%E6%98%8E%EF%BC%8C%E5%8F%AF%E8%87%AA%E5%8A%A8%E5%8C%96%EF%BC%8C%E6%BB%A1%E8%B6%B3%E5%B9%82%E7%AD%89%EF%BC%88%E5%8F%AF%E9%87%8D%E5%A4%8D%E6%89%A7%E8%A1%8C%EF%BC%89%E6%80%A7%E5%92%8C%E4%B8%80%E8%87%B4%E6%80%A7%E7%9A%84%E9%85%8D%E7%BD%AE%E8%83%BD%E5%8A%9B%E3%80%82%20DSC%E8%83%BD%E5%A4%9F%E5%B8%AE%E5%8A%A9%E4%B8%93%E4%B8%9A%E8%BF%90%E7%BB%B4%E4%BA%BA%E5%91%98%EF%BC%8C%E5%BC%80%E5%8F%91%EF%BC%8CIT%E5%9F%BA%E7%A1%80%E8%AE%BE%E6%96%BD%E7%AE%A1%E7%90%86%E5%91%98%E7%AD%89%E5%AE%9A%E4%B9%89%E7%9B%AE%E6%A0%87%E8%8A%82%E7%82%B9%E7%9A%84%E9%85%8D%E7%BD%AE%EF%BC%88%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%88%96%E8%80%85%E8%AE%BE%E5%A4%87%EF%BC%89%E5%90%8C%E6%97%B6%E9%98%BB%E6%AD%A2%E9%85%8D%E7%BD%AE%E7%9A%84%E4%B8%8D%E4%B8%80%E8%87%B4%E6%80%A7%E5%92%8C%E9%85%8D%E7%BD%AE%E7%8A%B6%E6%80%81%E7%9A%84%E6%BC%82%E7%A7%BB%E3%80%82)

# WMF

#todo


# 获取帮助

- get-help/help/man

查看格式， 参数， 例子等

```powershell
man commandname -online
man commandname
get-help commandname
help commandname
```

- get-command/gcm

查看命令类型， 名字别名， 版本， 源码等信息， 可以使用多种方式过滤

- -verb 动词过滤
- -name 名字过滤(可省略)
- -module 模块过滤
- -noun 名词过滤
- -ListImported 导入的命令

也可以查看额外信息

- -ParameterName 参数
- -Syntax 语法
- -ParameterType 参数类型
- -ShowCommandInfo

详情请参看在线文档 `man get-command -online` 

```powershell
gcm #查看所有命令
gcm commandname #指定命令
gcm -verb get #指定动词的所有命令
```

# get-member

获取对象的属性和方法

```powershell
Get-Member
   [-InputObject <PSObject>]
   [[-Name] <String[]>]
   [-MemberType <PSMemberTypes>]
   [-View <PSMemberViewTypes>]
   [-Static]
   [-Force]
   [<CommonParameters>]
```

```powershell
get-help | get-member
```

# 基础

## 扩展名

.ps1

## 便捷别名

在 Windows 上，为了方便用户，PowerShell 提供一组映射到 Linux 命令名的别名。`ls`、`cp`、`mv`、`rm`、`cat`、`man`、`mount`、`ps`....

## 执行策略

执行策略可以防止 powershell 脚本被无意执行， 有两种执行策略

- Restricted               不能执行脚本
- RemoteSigned     可以执行脚本

### 查看

```powershell
Get-ExecutionPolicy
```

### 设置

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned
```

## 管道

符号 `|` 连接两个命令， 左边命令的输出重定向作为右边命令的输入。常规意义的输入输出均为文本， 但 powershell 的输入输出是对象， 可以使用 `man command -full` 查看帮助， 重点关注  `INPUTS` `OUTPUTS`， 这里写了接受什么类型的管道输入，以及哪些参数可以使用管道。

### 按值(类型)绑定

默认按值绑定管道参数。假设 command 有两个参数 -name  namestring, -inputobject object， 如果管道输入的是 string 则会把值绑定到 -name 参数， 否则绑定到 -inputobject。

### 按属性名绑定

如果传入的参数是个对象， 里边包含 name 属性， 则会把该对象的 name 属性绑定到 -name 参数。


# NuGet 

NuGet 为 powershell 模块、项目仓库， 等同于 java 世界的 maven 仓库， 可以使用 `PowerShellGet` 模块进行管理。

- 发现 `find-module -name name`
- 安装 `Find-Module -Name MrToolkit | Install-Module`

# 单、双引号

一般单双引号都可使用， 均表示字符串， 但存在些许差异

- 单引号中无法使用 $variable, 会原样展示


# cmdlet

PowerShell 中的编译命令称为 cmdlet。 Cmdlet 的发音为“command-let”（而不是 CMD-let）。 Cmdlet 名称采用单数形式的“动词-名词”命令形式，这样更易于被发现。 例如，用于确定正在运行哪些进程的 cmdlet 是 Get-Process，而用于检索服务及其状态的列表的 cmdlet 是 Get-Service。 PowerShell 中还有其他类型的命令（例如别名和函数），本书后面部分将对其进行介绍。 术语 PowerShell 命令是一个通用术语，通常用于指代 PowerShell 中任何类型的命令，不管是 cmdlet、函数还是别名。

实际使用中， cmdlet 命令可以首字母大写，也可以全小写， 即 `get-process` 和 `Get-Process` 都有效。

# 别名


# 函数


# 筛选器

# 脚本

# 应用程序


# CIM
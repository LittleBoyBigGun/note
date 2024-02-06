# 新建项目

```powershell
dotnet new template -lang|--language {C#|F#|VB} -n|--name <OUTPUT_NAME> -f|--framework <FRAMEWORK> -o projectName
```

可用的模板， [参考](https://learn.microsoft.com/zh-cn/dotnet/core/tools/dotnet-new)

- console
- classlib
- wpf
- wpflib
- wpfcustomcontrollib
- wpfusercontrollib
- winforms
- winformslib
- nunit
- xunit
- mstest
....

# 添加 sln

```powershell
#文件夹名同名
dotnet new sln

#使用指定文件名
dotnet new sln --name MySolution

#文件夹同名且创建文件夹
dotnet new sln --output MySolution
```

# 添加项目到解决方案

```powershell
dotnet sln [slnName] add projectPath
```

# 运行

```powershell
dotnet run
dotnet run --project ./projects/proj1/proj1.csproj
```

# 编译

[build](https://learn.microsoft.com/zh-cn/dotnet/core/tools/dotnet-build)

```powershell
dotnet build
```

# package

去 [nuget](https://www.nuget.org/)上找包

```powershell
#list
dotnet list package

#add
dotnet projectName add package

#remove
dotnet remove package
```
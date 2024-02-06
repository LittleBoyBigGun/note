# 原理

继承 `System.Management.Automation.Cmdlet` 类， 实现 `ProcessRecord()` 方法. [ref](https://learn.microsoft.com/zh-cn/powershell/scripting/developer/windows-powershell?view=powershell-7.2)

# 具体步骤

## 新建 classlib 工程

```powershell
dotnet new classlib -o removeTest
```

## nuget 下载依赖

依赖 `System.Management.Automation`, 可以通过如下方式来导入

```powershell
dotnet add package system.management.automation --version 7.3.4
```

或者找到`xxx.csproj` 文件添加

```xml
<ItemGroup>
    <PackageReference Include="system.Management.Automation" Version="7.3.4" />
  </ItemGroup>
```

编译的时候会自动下载依赖的包

## 编写一个 cmdlet 类

```c#
  

namespace yuxia.Cmdlet

{   using System.Management.Automation;

  

    [Cmdlet(VerbsCommon.Remove,"test")]

    public class RemoveTest : Cmdlet

    {

        [Parameter( Mandatory = true )]

        public string? Filename

        {

            get { return filename; }

            set { filename = value; }

        }

        private string? filename;

  

        protected override void BeginProcessing(){

          Console.WriteLine("=== cmdlet test start =======");

        }

  

        protected override void EndProcessing() {

          Console.WriteLine("=== cmdlet test end =========");

        }

        protected override void ProcessRecord()

        {

            if (ShouldProcess(

                string.Format("Deleting file {0}",filename),

                string.Format("Are you sure you want to delete file {0}?", filename),

                "Delete file"))

            {

               Console.WriteLine("{0} is removed", filename);

            }

        }

    }

}
```

## 编译

```powershell
dotnet build
```

## 使用

定位到 `xxx.dll` 目录， 

- `import-module .\xxx.dll`

接着就可以直接使用该命令了

当然了也可以通过
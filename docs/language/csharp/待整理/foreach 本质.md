
[foreach 本质及 yield ](https://learn.microsoft.com/zh-cn/archive/msdn-magazine/2017/april/essential-net-understanding-csharp-foreach-internals-and-custom-iterators-with-yield)

[Iterators](https://learn.microsoft.com/en-us/dotnet/csharp/iterators)

# IEnumerator/IEnumerator<T>

IEnumerator 为一个接口， 提供一套模式来遍历集合的元素而无须使用者提前知道集合内部结构，譬如元素个数等。

## Current 

为当前元素的引用

## MoveNext

移动到下个元素， 即更新 Current, 返回 bool 值， 到达最后元素则返回 false。

## Reset 

恢复初始状态， Current 指向第一个元素。

## 典型用法

```csharp
Enumerator et = new myCollection<int>();
int num;

while(et.MoveNext()) {
	Console.WriteLine(et.Current);
}
```

## 缺陷

考虑如下代码

```csharp

Enumerator et = new myCollection<int>();
int num;

while(et.MoveNext()) {
	num = et.Currnt;

	while(et.MoveNext()) {
		Console.WriteLine(et.Current);
	}
}
```

在嵌套中， 由于共用了同一个 Enumerator 导致运行结果不达预期， 所以 csharp 提供的几个 List , stack 等都是使用 IEnumerable。他们提供内部结构 Enumerator 实现 IEnumerator 接口, GetEnumerator 返回该结构体， 来进行遍历。

```csharp

var list = new List<int>() {1,2,3,4};
List<int>.Enumerator  et = list.GetEnumerator();

while(et.MoveNext()) {
	Console.WriteLine(et.Current);
}
```

# IEnumerable/ IEnumerable<T>

## GetEnumerator

返回 IEnumerator, 每次都返回不同的 IEnumerator 实例， 避免共用带来的问题。

# foreach

[[逆向#MSIL|MSIL]] 并没有 foreach 指令， foreach 是一种语法糖， 编译器将代码转换成对 IEnumerator/ IEnumerable 接口的使用。

# yield 

yield 也叫 iterator methods。枚举模式存在的问题是，手动实现起来不方便，因为必须始终指示描述集合中的当前位置所需的全部状态。对于列表集合类型类，指示这种内部状态可能比较简单；当前位置的索引就足够了。相比之下，对于需要递归遍历的数据结构（如二叉树），指示状态可能就会变得相当复杂。为了减少实现此模式所带来的挑战，C# 2.0 新增了 yield 上下文关键字，这样类就可以更轻松地决定 foreach 循环如何迭代其内容。

MSIL 同样没有 yield 对应的指令， 实际上也会转换成对 IEnumerator/ IEnumerable 接口的使用。

```csharp
using System;
using System.Collections.Generic;
public class CSharpBuiltInTypes: IEnumerable<string>
{
  public IEnumerator<string> GetEnumerator()
  {
    yield return "object";
    yield return "byte";
    yield return "uint";
    yield return "ulong";
    yield return "float";
    yield return "char";
    yield return "bool";
    yield return "ushort";
    yield return "decimal";
    yield return "int";
    yield return "sbyte";
    yield return "short";
    yield return "long";
    yield return "void";
    yield return "double";
    yield return "string";
  }
    // The IEnumerable.GetEnumerator method is also required
    // because IEnumerable<T> derives from IEnumerable.
  System.Collections.IEnumerator
    System.Collections.IEnumerable.GetEnumerator()
  {
    // Invoke IEnumerator<string> GetEnumerator() above.
    return GetEnumerator();
  }
}
public class Program
{
  static void Main()
  {
    var keywords = new CSharpBuiltInTypes();
    foreach (string keyword in keywords)
    {
      Console.WriteLine(keyword);
    }
  }
}
```
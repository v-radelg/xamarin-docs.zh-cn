---
title: .NET 嵌入 Objective c 的最佳实践
description: 本文档介绍了各种与 Objective-c。 使用.NET 嵌入的最佳做法 它讨论公开的托管代码的子集、 公开 chunkier API、 命名和的详细信息。
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 33138b7858b8bc04a5be30f9fad1709e916f5575
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364072"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>.NET 嵌入 Objective c 的最佳实践

这是草稿，可能不同步的功能目前支持该工具。 我们希望本文档将分别发展并最终根据最终的工具，即我们会建议从长远来看最佳方法-无法立即解决方法。

本文档的很大程度也适用于其他受支持的语言。 但是所有提供的示例是在C#和 Objective-c。

## <a name="exposing-a-subset-of-the-managed-code"></a>公开的托管代码的子集

生成本机库/框架包含用于调用每个公开的托管 Api 的 Objective C 代码。 你面上的多个 API （公开） 然后较大本机_粘附_库将成为。

可能会创建一个不同的、 较小程序集，以公开 Api 向本机开发人员只需一个好办法。 该机制还允许您更好地控制可见性，命名，生成的代码的...检查时出错。

## <a name="exposing-a-chunkier-api"></a>公开 chunkier API

没有从本机转换支付的价格为托管 （和后退）。 在这种情况下，最好公开_而不是琐碎块式_Api，用于本机开发人员，例如

**聊天式**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**块式**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

由于转换次数较小性能是更好的。 它还需要更少的代码生成，因此这会生成较小的本机库也。

## <a name="naming"></a>命名

命名内容是两种最困难问题之一在计算机科学中，其他人正在缓存失效并关闭-通过-1 的错误。 希望.NET 嵌入可以防止您以外的所有命名。

### <a name="types"></a>类型

Objective C 不支持命名空间。 一般情况下，其类型都带有前缀 2 （适用于 Apple) 或 3 （适用于第三方） 字符前缀，如`UIView`UIKit 的视图，这表示框架。

.NET 类型的跳过命名空间不可能因为它可能会带来重复或令人困惑，名称。 这使现有的.NET 类型很长例如

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

用法如下：

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

但是，您可以重新公开为类型：

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

使其更多的 Objective C 友好若要使用，例如：

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>方法

更好的.NET 名称可能不是适用于 Objective C API。

Objective C 中的命名约定是不同于.NET （而不是 pascal 大小写，更详细的混合大小写）。
请阅读[编码指南 Cocoa](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF)。

Objective C 开发人员的角度来看，方法与从`Get`前缀表示您已经不拥有该实例，即[获取规则](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1)。

此命名规则具有.NET GC 世界中; 中没有匹配项具有的.NET 方法`Create`前缀将具有相同行为在.NET 中。 但是，对于 Objective C 开发人员，这通常意味着你拥有返回的实例，即[创建规则](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

## <a name="exceptions"></a>Exceptions

它是在.NET 中使用广泛错误报告的例外情况很常见。 但是，它们是速度慢且不在 OBJECTIVE-C 完全相同 只要有可能则应从 OBJECTIVE-C 开发人员隐藏它们。

例如，.NET`Try`模式是更易于从 OBJECTIVE-C 代码使用：

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

与

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>内部异常 `init*`

在.NET 中构造函数必须成功并返回 (_希望_) 有效实例或引发异常。

与此相反，OBJECTIVE-C 允许`init*`以返回`nil`时，不能创建一个实例。 这是许多 Apple 的框架使用的常见，但不是常规模式。 在某些其他情况下`assert`可以发生这种情况 （并且终止当前进程）。

生成器应遵循相同`return nil`模式生成`init*`方法。 如果引发托管的异常，则它将打印 (使用`NSLog`) 和`nil`将返回到调用方。

## <a name="operators"></a>运算符

Objective C 不允许运算符重载为C#值有效，因此这些转换为类选择器。

["友好"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads)命名的方法生成优先运算符重载时找到，并且可以生成易于使用 API。

重写了运算符的类`==`和/或`!=`应重写标准的 Equals (Object) 方法。

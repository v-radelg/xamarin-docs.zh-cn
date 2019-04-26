---
title: 在 Xamarin.iOS 和 Xamarin.Mac NSString
description: 本文档介绍了 Xamarin.iOS 如何以透明方式将转换到 NSString 对象C#字符串对象，这不会发生时。
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: cc9e3f992642f3cdc3d16fe6f829b6a6c06b50fc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277809"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>在 Xamarin.iOS 和 Xamarin.Mac NSString

Xamarin.iOS 和 Xamarin.Mac 的设计要求使用 API 公开的本机.NET 字符串类型， `string`，用于字符串操作中C#和其他.NET 编程语言，并将字符串公开为 API 而不是公开的数据类型 `NSString` 数据类型。

这意味着，开发人员不应该有要保留旨在用于调入 Xamarin.iOS 和 Xamarin.Mac API （统一） 的字符串中一种特殊类型 (`Foundation.NSString`)，它们可以继续使用 Mono 的`System.String`所有操作，和每当在 Xamarin.iOS 或 Xamarin.Mac 的 API 需要一个字符串，我们的 API 绑定负责封送处理信息。

例如，OBJECTIVE-C 的"text"上的属性`UILabel`类型的`NSString`，声明如下：

```objc
@property(nonatomic, copy) NSString *text
```

这公开了作为 Xamarin.iOS:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

在后台，此属性的实现将封送C#字符串`NSString`并调用`objc_msgSend`方式 Objective C 将完全相同的方法。

第三方 Objective C api，也不会占用几`NSString`，但改为使用 C 字符串 ("*char*")。 在这些情况下，仍可以使用C#字符串数据类型，但必须使用[[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md)属性以通知不此字符串作为封送的绑定生成器`NSString`，而是作为 C 字符串。

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>规则的例外情况

在 Xamarin.iOS 和 Xamarin.Mac，我们做了此规则的例外。 当我们公开之间做出决定 `string`s，和我们进行时除外，并公开 `NSString`s，由如果 `NSString` 方法无法执行操作而不是内容比较指针比较。

发生此情况时 Objective C Api 使用公共 `NSString` 常量用作表示某一操作，而不是比较字符串的实际内容的标记。

在这些情况下， `NSString` 公开的 Api，并且有极少的具有此 Api。 您还会注意到 NSString 属性都在部分类中。 那些`NSString`属性公开的通知等项。 这些都是属性通常如下：

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```
通知是为使用的密钥`NSNotification`类时要广播的运行时为特定事件注册。

密钥通常如下所示：

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

另一个位置其中`NSString`s 中公开的 API 是用作在 iOS 中的某些 Api 或 OS X 获取的参数的令牌作为`NSDictionary`对象作为参数。 字典通常包含`NSString`密钥。 Xamarin.iOS，按照约定，名称与静态`NSString`通过将"密钥"名称添加属性。

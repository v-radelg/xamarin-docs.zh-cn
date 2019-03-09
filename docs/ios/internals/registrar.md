---
title: 适用于 Xamarin.iOS 的类型注册机构
description: 本文档介绍了 Xamarin.iOS 类型注册机构，这使得C#到 OBJECTIVE-C 运行时可用的类。
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 08/29/2018
ms.openlocfilehash: 83340ce2d5db145c29166d90d3a5180b1767d7ca
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2019
ms.locfileid: "57672490"
---
# <a name="type-registrar-for-xamarinios"></a>适用于 Xamarin.iOS 的类型注册机构

本文档介绍使用 Xamarin.iOS 的类型注册系统。

## <a name="registration-of-managed-classes-and-methods"></a>注册的托管的类和方法

在启动期间，将注册 Xamarin.iOS:

- 类与[[注册]](xref:Foundation.RegisterAttribute)为 Objective C 类的属性。
- 类与[[类别]](xref:ObjCRuntime.CategoryAttribute)为 Objective C 分类的属性。
- 与接口[[协议]](xref:Foundation.ProtocolAttribute)作为 Objective C 协议的属性。
- 具有成员[[导出]](xref:Foundation.ExportAttribute)，使得 Objective C 来对其进行访问。

例如，考虑托管`Main`在 Xamarin.iOS 应用程序中常见的方法：

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

此代码指示 Objective C 运行时使用名为的类型`AppDelegate`作为应用程序的委托类。 Objective C 运行时能够创建的实例的C#`AppDelegate`类，类必须进行注册。

Xamarin.iOS 自动执行注册，在运行时 （动态注册） 或在编译时 （静态注册）。

动态注册使用反射在启动时若要查找所有类和方法，若要注册，将其传递给 Objective C 运行时。 对模拟器生成，默认情况下使用动态注册。

静态注册在编译时检查应用程序使用的程序集。 它确定的类和方法来注册到 OBJECTIVE-C 的并生成一个代码图，它嵌入到你的二进制文件。
然后，在启动时，它将映射注册到 OBJECTIVE-C 运行时。 静态注册用于设备的生成。

### <a name="categories"></a>类别

从 Xamarin.iOS 8.10，就可以创建使用 Objective C 类别C#语法。

若要创建一个类别，请使用`[Category]`属性，指定要扩展的类型。 例如，下面的代码扩展`NSString`:

```csharp
[Category (typeof (NSString))]
```

每个类别的方法有`[Export]`属性，使其可供 Objective C 运行时：

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有托管的扩展方法必须是静态的但就可以创建使用标准的 Objective C 实例方法C#的扩展方法语法：

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

扩展方法的第一个参数是在其调用方法的实例：

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
 }
 ```

此示例将添加一个本机`toUpper`实例方法到`NSString`类。 可以从目标 c： 调用此方法

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

### <a name="protocols"></a>协议

从 Xamarin.iOS 8.10 开始，接口与`[Protocol]`属性将作为协议导出到 OBJECTIVE-C 的：

```csharp
[Protocol ("MyProtocol")]
interface IMyProtocol
{
    [Export ("method")]
    void Method ();
}

class MyClass : IMyProtocol
{
    void Method ()
    {
    }
}
```

此代码将导出`IMyProtocol`作为协议的 Objective C 到名为`MyProtocol`和类名为`MyClass`实现协议。

## <a name="new-registration-system"></a>新的注册系统

从稳定 6.2.6 版本和测试 6.3.4 版中，我们添加了新的静态注册机构。 在 7.2.1 版本中，我们所做的新的注册机构默认值。

此新的注册系统提供了以下新功能：

- 编译时检测到的程序员错误：
    - 正在注册具有相同名称的两个类。
    - 多个方法导出响应相同的选择器
- 删除未使用的本机代码：
    - 新的注册系统将添加在静态库中使用的代码允许本机链接器删除其中的未使用的本机代码，从生成的二进制文件的强引用。 在 Xamarin 的示例绑定上的大多数应用程序会成为至少 300 k 较小。

- 支持的通用子类`NSObject`; 请参阅[NSObject 泛型](~/ios/internals/api-design/nsobject-generics.md)有关详细信息。 此外新的注册系统会捕获，这以前会导致随机行为，在运行时不支持泛型构造。

### <a name="errors-caught-by-the-new-registrar"></a>错误捕获的新的注册机构

下面是一些示例捕获到新的注册机构的错误。

- 在同一个类超过一次导出相同的选择器：

    ```csharp
    [Register]
    class MyDemo : NSObject
    {
        [Export ("foo:")]
        void Foo (NSString str);
        [Export ("foo:")]
        void Foo (string str)
    }
    ```

- 导出多个同名的 Objective C 的托管的类：

    ```csharp
    [Register ("Class")]
    class MyClass : NSObject {}

    [Register ("Class")]
    class YourClass : NSObject {}
    ```

- 导出泛型方法：

    ```csharp
    [Register]
    class MyDemo : NSObject
    {
        [Export ("foo")]
        void Foo<T> () {}
    }
    ```

### <a name="limitations-of-the-new-registrar"></a>新的注册机构的限制

需要有关新的注册机构的注意一些事项：

- 某些第三方库必须更新为使用新的注册系统。 请参阅[所需的修改](#required-modifications)下面的更多详细信息。

- 短期缺点也是如果该帐户框架使用，必须使用 Clang (这是因为 Apple **accounts.h** Clang 可以仅编译标头)。 添加`--compiler:clang`到其他 mtouch 参数，以使用 Clang，如果使用 Xcode 4.6 或更早版本 （Xamarin.iOS 将自动选择 Clang Xcode 5.0 或更高版本。）

- 如果 Xcode 4.6 （或更早版本） 为使用 GCC / G + + 如果必须选择导出类型名称包含非 ASCII 字符 （这是因为使用 Xcode 4.6 Clang 附带的版本不支持 Objective C 代码中的非 ASCII 字符，内部标识符）。 添加`--compiler:gcc`到其他 mtouch 参数，以使用 gcc 高级版。

## <a name="selecting-a-registrar"></a>选择注册机构

可以通过将以下选项之一添加到项目中的其他 mtouch 参数选择其他注册机构**iOS 生成**设置：

- `--registrar:static` – 默认为设备版本
- `--registrar:dynamic` – 默认对模拟器生成

> [!NOTE]
> Xamarin 的经典 API 支持其他选项，如`--registrar:legacystatic`和`--registrar:legacydynamic`。 但是，通过统一的 API 不支持这些选项。

## <a name="shortcomings-in-the-old-registration-system"></a>在旧的注册系统的不足之处

旧的注册系统带来以下缺点：

- Objective C 类和第三方本机库，这意味着我们无法获得本机链接器中，若要删除第三方 （因为所有内容将被删除） 不实际使用的本机代码中的方法不 （本机） 的静态引用时出现。 这是为了增大`-force_load libNative.a`的每个第三方绑定所要做 (或等效`ForceLoad=true`中`[LinkWith]`属性)。
- 无法导出两个具有相同的 Objective C 名称不显示任何警告的托管的类型。 少见的方案是最终会有两个`AppDelegate`不同的命名空间中的类。 在运行时它完全是应用的随机的哪一个已选择 （事实上，它甚至不重新生成-这使得非常人困惑且令人沮丧的调试体验的运行之间随其而变化）。
- 无法导出两个具有相同的 Objective C 签名方法。 再次从 OBJECTIVE-C 调用哪一个是随机 （但此问题主要是因为实际上会遇到此 bug 的唯一方法是重写也托管的方法不是常见与上一个）。
- 已导出的方法集是动态和静态生成之间略有不同。
- 它不会无法正常工作时导出泛型类 （在运行时执行的确切的泛型实现是随机的有效地导致不确定的行为）。

<a name="required-modifications" />

## <a name="new-registrar-required-changes-to-bindings"></a>新的注册机构： 所需绑定到的更改

本部分介绍绑定必须为了使用新的注册机构所做的更改。

### <a name="protocols-must-have-the-protocol-attribute"></a>协议必须具有 [协议] 属性

现在必须具有协议`[Protocol]`属性。 如果不这样做，如将本机链接器错误：

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>选择器必须具有有效数量的参数

所有选择器必须正确指示参数的数量。 以前，这些错误被忽略，并且可能会导致运行时的问题。

简单地说，多个冒号必须匹配参数的数目：

- 任何参数： `foo`
- 一个参数： `foo:`
- 两个参数： `foo:parameterName2:`

以下是不正确的使用：

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>在导出中使用 IsVariadic 参数

可变参数函数必须使用`IsVariadic`自变量`[Export]`属性：

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>必须链接到现有的符号

就无法将绑定本机库中不存在的类。
如果已从删除或在本机库中重命名一个类，请确保更新以匹配的绑定。

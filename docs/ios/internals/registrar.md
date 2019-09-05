---
title: Xamarin 的类型注册机构
description: 本文档介绍了 Xamarin iOS 类型注册器，使C#类可供目标-C 运行时使用。
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 0d8e16c2a651df293b13e7f7586d5a643caa1c9c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291832"
---
# <a name="type-registrar-for-xamarinios"></a>Xamarin 的类型注册机构

本文档介绍了 Xamarin 使用的类型注册系统。

## <a name="registration-of-managed-classes-and-methods"></a>托管类和方法的注册

在启动期间，Xamarin 将注册：

- 将[[Register]](xref:Foundation.RegisterAttribute)特性用作目标-C 类的类。
- 具有[[Category]](xref:ObjCRuntime.CategoryAttribute)特性的类作为目标-C 类别。
- 使用[[协议]](xref:Foundation.ProtocolAttribute)属性作为目标-C 协议的接口。
- 带有[[Export]](xref:Foundation.ExportAttribute)的成员，使得客观-C 可以访问它们。

例如，考虑在 Xamarin iOS `Main`应用程序中常见的托管方法：

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

此代码告知目标 C 运行时使用称为`AppDelegate`应用程序的委托类的类型。 为了使目标 C 运行时能够创建C# `AppDelegate`类的实例，必须注册该类。

Xamarin 会在运行时（动态注册）或在编译时（静态注册）自动执行注册。

动态注册在启动时使用反射查找要注册的所有类和方法，并将它们传递给目标 C 运行时。 默认情况下，为模拟器生成使用动态注册。

静态注册在编译时检查应用程序使用的程序集。 它确定要向目标 C 注册的类和方法，并生成嵌入到二进制文件中的映射。
然后，在启动时，它将向目标 C 运行时注册映射。 静态注册用于设备生成。

### <a name="categories"></a>Categories

从 Xamarin 8.10 开始，可以使用C#语法创建目标为 C 的类别。

若要创建类别，请使用`[Category]`属性，并指定要扩展的类型。 例如，下面的代码扩展`NSString`：

```csharp
[Category (typeof (NSString))]
```

类别的每个方法都有一个`[Export]`属性，使其可用于目标 C 运行时：

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

所有托管扩展方法必须是静态的，但可以使用扩展方法的标准C#语法来创建目标 C 实例方法：

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

扩展方法的第一个参数是在其上调用方法的实例：

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

此示例将向`toUpper` `NSString`类添加本机实例方法。 可以从目标-C 调用此方法：

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

从 Xamarin 8.10 开始，具有属性的`[Protocol]`接口将作为协议导出到目标 C：

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

此代码将`IMyProtocol`作为名`MyProtocol`为的协议导出到目标 C，并将`MyClass`一个名为的类作为实现协议的类。

## <a name="new-registration-system"></a>新注册系统

从稳定的6.2.6 版本和 beta 6.3.4 版本开始，我们添加了一个新的静态注册器。 在7.2.1 版本中，我们将新的注册机构设为默认值。

此新注册系统提供以下新增功能：

- 编程错误的编译时检测：
  - 两个用相同名称注册的类。
  - 已导出多个方法来响应同一个选择器
- 删除未使用的本机代码：
  - 新的注册系统将添加对静态库中使用的代码的强引用，这允许本机链接器从生成的二进制文件中去除未使用的本机代码。 在 Xamarin 的示例绑定上，大多数应用程序的30万个都至少更小。

- 支持的泛型子类`NSObject`; 有关详细信息，请参阅[NSObject 泛型](~/ios/internals/api-design/nsobject-generics.md)。 此外，新的注册系统将捕获先前在运行时导致随机行为的不受支持的泛型构造。

### <a name="errors-caught-by-the-new-registrar"></a>新注册机构捕获的错误

下面是新注册机构捕获的错误的一些示例。

- 在同一个类中多次导出同一选择器：

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

- 导出多个具有相同目标 C 名称的托管类：

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

### <a name="limitations-of-the-new-registrar"></a>新注册机构的限制

有关新注册机构，请注意以下事项：

- 某些第三方库必须进行更新才能使用新的注册系统。 有关更多详细信息，请参阅下面[所需的修改](#required-modifications)。

- 短期缺点也是在使用帐户框架时必须使用 Clang （这是因为 Apple 的**帐户**头只能由 Clang 编译）。 如果`--compiler:clang`使用的是 Xcode 4.6 或更早版本，请将添加到附加的 mtouch 参数以使用 Clang （Xamarin 会自动选择 Xcode 5.0 或更高版本中的 Clang）。

- 如果使用 Xcode 4.6 （或更低版本），则如果导出的类型名称包含非 ASCII 字符，则必须选择 GCC/G + + （这是因为与 Xcode 4.6 一起提供的 Clang 版本不支持在目标 C 代码中的标识符内使用非 ASCII 字符）。 添加`--compiler:gcc`到其他 mtouch 参数以使用 GCC。

## <a name="selecting-a-registrar"></a>选择注册机构

可以通过将以下选项之一添加到项目的**IOS 生成**设置中的其他 mtouch 参数，选择其他注册机构：

- `--registrar:static`–设备生成的默认值
- `--registrar:dynamic`–模拟器生成的默认值

> [!NOTE]
> Xamarin 的 Classic API 支持其他选项`--registrar:legacystatic` ，例如和。 `--registrar:legacydynamic` 但 Unified API 不支持这些选项。

## <a name="shortcomings-in-the-old-registration-system"></a>旧注册系统中的缺点

旧注册系统具有以下缺点：

- 对于第三方本机库中的目标 C 类和方法，不存在（本机）静态引用，这意味着我们无法要求本机链接器删除实际使用的第三方本机代码（因为所有内容都将被删除）。 这是每个第三`-force_load libNative.a`方绑定必须执行的操作（或`[LinkWith]`属性中的等效`ForceLoad=true`项）的原因。
- 您可以导出两个具有相同目标 C 名称的托管类型，无警告。 在不同的命名空间中，有两`AppDelegate`个类的情况很少见。 在运行时，将会完全随机选择哪一种方法（事实上，它在不重新生成的应用程序运行之间有所不同，这是一项非常令困惑、令人沮丧的调试体验）。
- 您可以导出两个具有相同的目标 C 签名的方法。 但再次重申，从目标-C 调用的是随机的（但此问题并不像以前的问题那样常见，这主要是因为实际遇到此错误的唯一方法是重写不幸托管方法）。
- 在动态和静态生成之间导出的方法集略有不同。
- 导出泛型类时，它不能正常工作（在运行时执行的确切泛型实现将是随机的，因而会有效地导致不确定的行为）。

<a name="required-modifications" />

## <a name="new-registrar-required-changes-to-bindings"></a>新注册机构：所需的绑定更改

本部分介绍了必须进行的绑定更改才能使用新的注册机构。

### <a name="protocols-must-have-the-protocol-attribute"></a>协议必须具有 [协议] 特性

协议现在必须具有`[Protocol]`属性。 如果不这样做，则会出现本机链接器错误，如：

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>选择器必须具有有效的参数数量

所有选择器必须正确指示参数的数目。 以前，这些错误被忽略，并可能导致运行时问题。

简而言之，冒号的数目必须与参数的数目匹配：

- 无参数：`foo`
- 一个参数：`foo:`
- 两个参数：`foo:parameterName2:`

下面是不正确的用法：

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>在导出中使用 IsVariadic 参数

可变参数函数必须使用`IsVariadic` `[Export]`属性的参数：

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>必须链接到现有符号

不可能绑定本机库中不存在的类。
如果类已从本机库中删除或重命名，请确保更新绑定以使其匹配。

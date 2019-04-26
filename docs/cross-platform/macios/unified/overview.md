---
title: 统一的 API 概述
description: Xamarin 的统一 API 使可能 Mac、 iOS 和支持 32 位和 64 位应用程序使用相同的二进制文件之间共享代码。
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 1d159d280bd3b8855c32e3e437dfdefcbe0463cb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261124"
---
# <a name="unified-api-overview"></a>统一的 API 概述

Xamarin 的统一 API 使可能 Mac、 iOS 和支持 32 位和 64 位应用程序使用相同的二进制文件之间共享代码。 默认情况下，在新的 Xamarin.iOS 和 Xamarin.Mac 项目中使用统一 API。

> [!IMPORTANT]
> Xamarin 经典 API 进行，前面有统一 API，已弃用。 
> - Xamarin.iOS 支持经典 API (monotouch.dll) 的最后一个版本是 Xamarin.iOS 9.10。
> - Xamarin.Mac 仍支持传统的 API，但将不再更新。 因为它已被弃用，开发人员应移动到 Unified API 其应用程序。

## <a name="updating-classic-api-based-apps"></a>更新经典的基于 API 的应用程序

按照你的平台的相关说明：

- [更新现有应用](updating-apps.md)
- [更新现有 iOS 应用](updating-ios-apps.md)
- [更新现有 Mac 应用](updating-mac-apps.md)
- [更新现有 Xamarin.Forms 应用](updating-xamarin-forms-apps.md)
- [将绑定迁移到 Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[将代码更新为 Unified API 的提示](updating-tips.md)

而不考虑哪些应用程序进行迁移，请查看[这些提示](updating-tips.md)以帮助您成功更新到 Unified API。

## <a name="library-split"></a>库拆分

从该点开始，我们的 Api 不会出现两种方式：

-  **经典 API:** 限制为 32 位 （仅限），并且以公开`monotouch.dll`和`XamMac.dll`程序集。
-  **统一的 API:** 支持通过单个 API 中提供的 32 位和 64 位开发`Xamarin.iOS.dll`和`Xamarin.Mac.dll`程序集。

这意味着，对于企业开发人员 （不是针对应用商店），你可以继续使用现有的经典 Api，因为我们将保留维护它们下去，或您可以升级到新的 Api。

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Namespace 更改

若要减少摩擦我们 Mac 和 iOS 的产品之间共享代码，我们将适用于产品中的 Api 更改命名空间。

我们将要删除前缀"MonoTouch"我们的 iOS 产品和"MonoMac"从我们的 Mac 产品上的数据类型。

这使得更容易条件编译的情况下在 Mac 和 iOS 平台之间共享代码，并将降噪在源代码文件的顶部。

-  **经典 API:** 命名空间使用`MonoTouch.`或`MonoMac.`前缀。
-  **统一的 API:** 没有命名空间前缀

## <a name="runtime-defaults"></a>运行时的默认值

默认使用统一 API **SGen**垃圾回收器和[新引用计数](~/ios/internals/newrefcount.md)系统，用于跟踪对象所有权。 此相同功能已经移植到 Xamarin.Mac。

这解决了许多问题的开发人员面临着旧系统和同时简化[内存管理](~/cross-platform/deploy-test/memory-perf-best-practices.md)。

请注意，可以启用新的引用计数甚至对于经典 API，但默认值是保守，并且不需要用户进行任何更改。 使用统一 API 时，我们更改默认的机会，并为开发人员提供的改进，同时它们重构和重新测试其代码。

## <a name="api-changes"></a>API 更改

API 删除了弃用的方法和有几个实例方面存在拼写错误中的 API 名称时它们已绑定到经典 Api 的原始 MonoTouch 和 MonoMac 命名空间。 这些实例已在新的统一 Api 得到纠正，并且将需要更新组件、 iOS 和 Mac 应用程序中。 下面是可能会遇到的最常见事件的列表：

|经典 API 方法名称|统一的 API 方法名称|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

有关从经典部署模型切换到 Unified API 时的更改的完整列表，请参阅我们[经典 (monotouch.dll) vs 统一 (Xamarin.iOS.dll) API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)文档。

## <a name="updating-to-unified"></a>更新到统一

多个旧/损坏/已弃用的 API 中**经典**中不可用**统一**API。 可更轻松地修复`CS0616`警告之后再启动应用 （手动或自动） 升级，因为您必须`[Obsolete]`属性消息 （该警告的一部分），用于指导你找到正确的 API。

请注意，我们将发布[*差异*](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)的经典 vs 统一 API 的可用之前或之后项目更新更改。 仍修复废弃经典将中的调用通常是节约了时间 （小于文档查找）。

请按照这些说明[更新现有 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)，或[Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)到 Unified API。
查看此页的其余部分和[这些提示](~/cross-platform/macios/unified/updating-tips.md)有关迁移您的代码的其他信息。

### <a name="nuget"></a>NuGet

以前支持通过经典 API 的 Xamarin.iOS 的 NuGet 包发布及其使用的程序集**Monotouch10**平台名字对象。

Unified API 引入了兼容包的一个新的平台标识符**Xamarin.iOS10**。 现有的 NuGet 包将需要更新以添加对此平台，支持通过构建针对统一 API。

> [!IMPORTANT]
> 如果窗体中有错误 _"错误 3 不能在相同的 Xamarin.iOS 项目中包含 monotouch.dll 和 Xamarin.iOS.dll'-'Xamarin.iOS.dll 显式引用，而 monotouch.dll 引用的 ' xxx，版本 = 0.0.000，区域性 = 中性，PublicKeyToken = null"_ 后转换到统一的 Api 应用程序，它通常是因为尚未更新到 Unified API 的项目中具有的组件或 NuGet 包。 你将需要删除现有的组件/NuGet、 更新为支持统一 Api 的版本和生成已清理。

### <a name="the-road-to-64-bits"></a>64 位之路

有关背景信息支持 32 位和 64 位应用程序和框架有关的信息，请参阅[32 和 64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md)。

 <a name="new-data-types" />

#### <a name="new-data-types"></a>新数据类型

不同之处的核心，在 Mac 和 iOS Api 使用始终是 32 位在 32 位平台上和 64 位平台上的 64 位体系结构特定的数据类型。

例如，映射 Objective C`NSInteger`数据类型设置为`int32_t`32 位系统和`int64_t`64 位系统上。

若要匹配此行为，请在我们统一的 API，我们要替换的上一次使用`int`(在.NET 中被定义为始终`System.Int32`) 到新的数据类型： `System.nint`。  您可以将"n"是有意义"本机"，因此平台的本机整数类型。

我们引入了`nint`，`nuint`和`nfloat`在必要时还提供了数据类型构建在它们之上。

若要了解有关这些数据类型更改的详细信息，请参阅[本机类型](~/cross-platform/macios/nativetypes.md)文档。

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>如何检测的 iOS 应用的体系结构

可能你的应用程序需要知道它正在运行 32 位或 64 位 iOS 系统上的情况。 下面的代码可以用于检查体系结构：

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>数组和 System.Collections.Generic

因为C#索引器期望的类型`int`，您必须显式转换`nint`值复制到`int`若要访问的集合或数组中的元素。 例如：

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

这是预期的行为，因为从强制转换`int`到`nint`是有损 64 位上的隐式转换不执行操作。

### <a name="converting-datetime-to-nsdate"></a>将 DateTime 转换为 NSDate

使用统一的 Api，隐式转换时才`DateTime`到`NSDate`值不再执行。 这些值需要显式转换从一种类型到另一个。 以下扩展方法可用于自动执行此过程：

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>已弃用的 Api 和拼写错误

内部 Xamarin.iOS 经典 API (monotouch.dll)`[Obsolete]`属性使用两个不同的方式：

-  **不推荐使用的 iOS API:** 这是当 Apple 提示你停止使用一个 API，因为它已被较新版本所取代。 经典 API 是仍然不错，通常需要 （如果支持较旧 iOS 版本）。
 此类 API (和`[Obsolete]`属性) 都将包括新的 Xamarin.iOS 程序集。
-  **不正确的 API**某些 API 上其名称中有拼写错误。

我们的原始程序集 （monotouch.dll 和 XamMac.dll） 保持兼容性的旧代码，但它们已被删除从统一的 API 程序集 （Xamarin.iOS.dll 和 Xamarin.Mac）

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject 子类.ctor(IntPtr)

每个`NSObject`子类具有构造函数接受`IntPtr`。 这是如何我们可以实例化新的托管的实例，从本机 ObjC 句柄。

这是在经典`public`构造函数。 但是，这很容易被不当地使用此功能在用户代码中的，例如，创建多个托管实例的单个 ObjC 实例*或*创建托管的实例会缺少所需的受管理的状态 （适用于子类）。

若要避免这些类型的问题`IntPtr`构造函数现`protected`中**统一**API，用于仅进行子类化。 这将确保更正/安全 API 用于从句柄，即创建托管的实例

    var label = Runtime.GetNSObject<UILabel> (handle);

此 API 将返回现有的托管的实例 （如果它已存在） 或将创建一个新密码 （如果需要）。 已在经典和统一的 API 中可用。

请注意，`.ctor(NSObjectFlag)`现也`protected`但这个很少使用，外部子类化。

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction 替换操作

使用统一 Api 中，`NSAction`已经消除了标准.net `Action`。 这是一个巨大的进步，因为`Action`是一个常见的.NET 类型，而`NSAction`是特定于 Xamarin.iOS。 它们都执行完全相同的操作，但它们是不同的和不兼容的类型，导致更多代码，无需编写来实现相同的结果。

例如，如果现有的 Xamarin 应用程序包含以下代码：

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
它现在可以替换的简单 lambda:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

以前，就是编译器错误，因为`Action`不能分配给`NSAction`，但由于`UITapGestureRecognizer`现在只需`Action`而不是`NSAction`都有效的统一 Api。

### <a name="custom-delegates-replaced-with-actiont"></a>替换操作的自定义委托<T>

在中**统一**一些简单的 （例如一个参数） 已替换为.net 委托`Action<T>`。 例如，

    public delegate void NSNotificationHandler (NSNotification notification);

现在可以用作`Action<NSNotification>`。 此升级代码重用，并减少代码重复 Xamarin.iOS 和自己的应用程序内。

### <a name="taskbool-replaced-with-taskbooleannserror"></a>任务<bool>替换为任务 < 布尔型、 NSError >>

在中**经典**没有某些异步 Api 返回`Task<bool>`。 但一些它们的位置，则在使用时`NSError`即已签名的一部分`bool`已`true`，您必须捕获异常可获得`NSError`。

因为某些错误都很常见，返回值不会起中更改此模式**统一**返回`Task<Tuple<Boolean,NSError>>`。 这允许您检查成功和异步调用期间可能发生的任何错误。

### <a name="nsstring-vs-string"></a>NSString vs 字符串

在少数情况下一些常量必须从更改`string`到`NSString`，例如 `UITableViewCell`

**经典**

    public virtual string ReuseIdentifier { get; }

**统一**

    public virtual NSString ReuseIdentifier { get; }

一般情况下，我们更喜欢.NET`System.String`类型。 但是，某些本机 API Apple 准则，尽管比较常量指针 （而不是字符串本身） 和这只能处理时我们公开常量作为`NSString`。

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Objective C 协议

原始 MonoTouch 没有非最佳完全支持 ObjC 协议中，某些，，已添加 API 以支持最常见的方案。 此限制不不再存在，但保留了几个 Api 是为了向后兼容，内部`monotouch.dll`和`XamMac.dll`。

删除和统一 Api 上清理这些限制。 大多数更改将如下所示：

**经典**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**统一**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

`I`前缀表示**统一**公开一个接口，而不是特定类型，ObjC 协议。 这将简化在您不希望为子类 Xamarin.iOS 提供的特定类型的情况。

它还允许某些 API，以使其更精确、 易于使用，例如：

**经典**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**统一**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

此类 API 现在更容易，而不引用文档，并且 IDE 代码补全功能将为您提供更有用的建议基于协议/接口。

#### <a name="nscoding-protocol"></a>NSCoding 协议

我们的原始绑定包括每个类型-.ctor(NSCoder)，即使它不支持`NSCoding`协议。  将单个`Encode(NSCoder)`方法中已存在`NSObject`进行编码对象。
但如果该实例符合 NSCoding 协议，此方法将仅会起作用。

统一的 api，我们已解决此。  新的程序集将仅具有`.ctor(NSCoder)`如果该类型符合`NSCoding`。 此类类型现在还有`Encode(NSCoder)`方法，这符合`INSCoding`接口。

低影响：在大多数情况下此更改不会影响应用程序，无法使用旧的、 将其删除，构造函数。

## <a name="further-tips"></a>更多提示

其他更改，需要注意的列入[更新应用到统一 API 的提示](~/cross-platform/macios/unified/updating-tips.md)。

## <a name="sample-code"></a>代码示例

截至 7 月 31 日中，我们已发布到此新 API 的 iOS 示例的端口上`magic-types`在分支[monotouch 示例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。

对于 Mac，我们会检查在这种示例[mac 示例](https://github.com/xamarin/mac-samples)存储库 （Mavericks/Yosemite 中显示新的 Api），以及魔力类型分支中的 32/64 位示例[mac 示例](https://github.com/xamarin/monotouch-samples/commits/magic-types)。

## <a name="related-links"></a>相关链接

- [更新 iOS 应用](updating-ios-apps.md)
- [更新 Mac 应用程序](updating-mac-apps.md)
- [更新 Xamarin.Forms 应用](updating-xamarin-forms-apps.md)
- [更新绑定](update-binding.md)
- [正在更新提示](updating-tips.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)

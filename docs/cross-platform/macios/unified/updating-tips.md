---
title: 将代码更新为 Unified API 的提示
description: 更新应用程序使用 Xamarin 的统一 API 时，本文档将讨论常见的错误和各种有用的提示。
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: asb3993
ms.author: amburns
ms.openlocfilehash: 62ef02d276e9c98e07f5e0d1b9ddec1b0874a99a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829617"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>将代码更新为 Unified API 的提示

当更新到 Unified API 的较旧的 Xamarin 解决方案，可能会遇到以下错误。

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException 找不到情节提要错误

没有[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569)在当前版本的 Visual Studio for Mac 中使用自动化的迁移工具将项目转换为统一 Api 后可能会出现。 更新后，如果窗体中出现一条错误消息：

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

您可以执行以下操作来解决此问题，找到以下生成目标文件：

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

在此文件中，您需要查找以下目标声明：

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

并添加`DependsOnTargets="_CollectBundleResources"`到该属性。 如：

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

保存该文件、 for Mac 中，重新启动 Visual Studio 以及执行清理和重新生成的项目。 此问题的修复程序应由 Xamarin 很快发布。

## <a name="useful-tips"></a>有用的提示

使用迁移工具之后, 仍可能会收到某些编译器错误，需要手动干预。
可能需要手动修复一些事项包括：

* 比较`enum`可能需要`(int)`强制转换。

* `NSDictionary.IntValue` 现在，将返回`nint`，没有`Int32Value`其中可改为使用。

* `nfloat` 并`nint`类型不能标记为`const`;  `static readonly nint`是合理的替代方法。

* 要直接在使用的事情`MonoTouch.`命名空间现在是通常`ObjCRuntime.`命名空间 (例如：`MonoTouch.Constants.Version`现在`ObjCRuntime.Constants.Version`)。

* 尝试进行序列化时，将对象序列化的代码可能会破坏`nint`和`nfloat`类型。 请务必检查你的序列化代码按预期在迁移后运行。

* 自动的工具，有时会漏掉内的代码`#if #else`条件编译器指令。 在这种情况下你将需要手动进行修补程序 （请参阅下面的常见错误）。

* 使用手动导出的方法`[Export]`可能不会自动修复通过迁移工具，例如在你必须手动更新返回类型为此代码 snippert `nfloat`:

    ```csharp
    [Export("tableView:heightForRowAtIndexPath:")]
    public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
    ```

* Unified API 不提供 NSDate 和.NET 日期时间之间的隐式转换，因为它不是无损转换。 若要防止与相关的错误`DateTimeKind.Unspecified`转换.NET`DateTime`为本地或之前强制转换为 UTC `NSDate`。

* Objective C 类别方法现在生成的统一 API 中的扩展方法。 例如，代码之前使用`UIView.DrawString`现在会引用`NSString.DrawString`统一 API 中。

* 使用具有 AVFoundation 类代码`VideoSettings`应改为使用`WeakVideoSettings`属性。 这要求`Dictionary`，这是可作为属性上设置类，例如：

    ```csharp
    vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
    ```

* NSObject`.ctor(IntPtr)`构造函数已从公共到受保护 ([以防止不正当使用](~/cross-platform/macios/unified/overview.md#NSObject_ctor))。

* `NSAction` 已被[替换为](~/cross-platform/macios/unified/overview.md#NSAction)使用标准.NET `Action`。 一些简单 （单个参数） 的委托也已替换`Action<T>`。

最后，请参阅[经典 v 统一 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)来查找你的代码中的 Api 更改。 搜索[本页](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)将帮助查找经典 Api 和什么它们已更新为。

> [!NOTE]
> `MonoTouch.Dialog`迁移后的命名空间保持相同。 如果你的代码使用**MonoTouch.Dialog**应继续使用该命名空间-不要*不*更改`MonoTouch.Dialog`到`Dialog`！

## <a name="common-compiler-errors"></a>常见编译器错误

随解决方案一起，下面列出了常见错误的其他示例：

**错误 CS0012:类型 MonoTouch.UIKit.UIView 中未引用的程序集中定义。**

解决方法：这通常意味着该项目引用的组件或还未生成使用统一 API 的 NuGet 包。 应删除并重新添加所有组件和 NuGet 包。 如果此操作未修复错误，外部库可能尚不支持统一 API。

**错误 MT0034:不能包含相同的 Xamarin.iOS 项目-monotouch.dll 和 Xamarin.iOS.dll Xamarin.iOS.dll 显式引用，而 monotouch.dll 引用的 Xamarin.Mobile，版本 = 0.6.3.0，区域性 = 中性，PublicKeyToken = null。**

解决方法：删除导致此错误的组件并重新添加到项目。

**错误 CS0234:命名空间 MonoTouch 中不存在类型或命名空间名称基础。是否缺少程序集引用？**

解决方法：Visual Studio for Mac 中的自动化的迁移工具*应*更新所有`MonoTouch.Foundation`引用`Foundation`，但在某些情况下，这些都需要手动更新。 对于以前包含在其他命名空间可能会出现类似的错误`MonoTouch`，如`UIKit`。

**错误 CS0266:无法将 double 到 System.float 的类型隐式转换**

修复： 将类型更改，并将转换为`nfloat`。 对于 64 位的等效项与其他类型也可能出现此错误 (如`nint`，)

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**错误 CS0266:不能隐式转换类型 CoreGraphics.CGRect' 到 'System.Drawing.RectangleF。存在显式转换 （是否缺少强制转换？）**

解决方法：更改到的实例`RectangleF`到`CGRect`，`SizeF`到`CGSize`，并`PointF`到`CGPoint`。 命名空间`using System.Drawing;`应替换为`using CoreGraphics;`（如果尚未存在）。

**错误 CS1502:最佳重载与匹配的方法 CoreGraphics.CGContext.SetLineDash (System.nfloat、 System.nfloat[]) 有一些无效参数**

解决方法：数组类型更改为`nfloat[]`显式强制转换和`Math.PI`。

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**错误 CS0115: WordsTableSource.RowsInSection （UIKit.UITableView，int）' 标记为重写，但找到来重写任何合适的方法**

解决方法：更改到的返回值和参数类型`nint`。 这通常发生在如那些方法重写`UITableViewSource`，其中包括`RowsInSection`， `NumberOfSections`， `GetHeightForRow`， `TitleForHeader`， `GetViewForHeader`，等等。

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**错误 CS0508: `WordsTableSource.NumberOfSections(UIKit.UITableView)`： 返回类型必须是 System.nint 才能匹配重写的成员 `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

解决方法：如果返回类型更改为`nint`，将返回值强制转换`nint`。

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**错误 CS1061:类型 CoreGraphics.CGPath 不包含 AddElipseInRect 定义**

解决方法：更正拼写`AddEllipseInRect`。 名称的其他更改包括：

* 将 Color.Black 更改为`NSColor.Black`。
* MapKit AddAnnotation 更改为`AddAnnotations`。
* 将 AVFoundation DataUsingEncoding 更改为`Encode`。
* 将 AVFoundation AVMetadataObject.TypeQRCode 更改为`AVMetadataObjectType.QRCode`。
* 将 AVFoundation VideoSettings 更改为`WeakVideoSettings`。
* 更改到 PopViewControllerAnimated `PopViewController`。
* 将 CoreGraphics CGBitmapContext.SetRGBFillColor 更改为`SetFillColor`。

**错误 CS0546： 不能重写，因为 MapKit.MKAnnotation.Coordinate 不具有可重写的 set 访问器 (CS0546)**

通过子类化 MKAnnotation 来创建一个自定义批注时坐标字段都有没有 setter，一个 getter。

[修复](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505):

* 添加要跟踪的坐标的字段
* 返回此字段中的坐标的属性 getter
* 重写 SetCoordinate 方法并设置您的字段
* 在与传入的坐标参数在构造函数中调用 SetCoordinate

它应类似于以下内容：

```csharp
class BasicPinAnnotation : MKAnnotation
{
    private CLLocationCoordinate2D _coordinate;

    public override CLLocationCoordinate2D Coordinate
    {
        get
        {
            return _coordinate;
        }
    }

    public override void SetCoordinate(CLLocationCoordinate2D value)
    {
        _coordinate = value;
    }

    public BasicPinAnnotation (CLLocationCoordinate2D coordinate)
    {
        SetCoordinate(coordinate);
    }
}
```

## <a name="related-links"></a>相关链接

- [更新应用程序](~/cross-platform/macios/unified/updating-apps.md)
- [更新 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 应用程序](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 应用](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新绑定](~/cross-platform/macios/unified/update-binding.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)

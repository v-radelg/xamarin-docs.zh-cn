---
title: 将代码更新为 Unified API 的提示
description: 本文档讨论了在更新应用程序以使用 Xamarin 的 Unified API 时的常见错误和各种提示。
ms.prod: xamarin
ms.assetid: 8DD34D21-342C-48E9-97AA-1B649DD8B61F
ms.date: 03/29/2017
author: davidortinau
ms.author: daortin
ms.openlocfilehash: ee207ffc83f887e9c86c650b6f93fc2ff9f5b69c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014992"
---
# <a name="tips-for-updating-code-to-the-unified-api"></a>将代码更新为 Unified API 的提示

将较旧的 Xamarin 解决方案更新到 Unified API 时，可能会遇到以下错误。

## <a name="nsinvalidargumentexception-could-not-find-storyboard-error"></a>NSInvalidArgumentException 未能找到情节提要错误

Visual Studio for Mac 的当前版本中存在 bug，该[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=25569)可能会在使用自动迁移工具将项目转换为统一 api 之后发生。 更新后，如果收到如下所示的错误消息：

```console
Objective-C exception thrown. Name: NSInvalidArgumentException Reason: Could not find a storyboard named 'xxx' in bundle NSBundle...
```

可以执行以下操作来解决此问题，找到以下生成目标文件：

```console
/Library/Frameworks/Xamarin.iOS.framework/Versions/Current/lib/mono/2.1/Xamarin.iOS.Common.targets
```

在此文件中，需要查找以下目标声明：

```xml
<Target Name="_CopyContentToBundle"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

并向其添加 `DependsOnTargets="_CollectBundleResources"` 特性。 如：

```xml
<Target Name="_CopyContentToBundle"
        DependsOnTargets="_CollectBundleResources"
        Inputs = "@(_BundleResourceWithLogicalName)"
        Outputs = "@(_BundleResourceWithLogicalName -> '$(_AppBundlePath)%(LogicalName)')" >
```

保存文件，重新启动 Visual Studio for Mac，并对项目进行清理 & 重新生成。 此问题的修补程序很快会被 Xamarin 发布。

## <a name="useful-tips"></a>有用提示

使用迁移工具后，可能仍会出现一些需要手动干预的编译器错误。
可能需要手动修复的一些事项包括：

- 比较 `enum`可能需要 `(int)` 强制转换。

- `NSDictionary.IntValue` 现在返回一个 `nint`，则可以改用 `Int32Value`。

- 不能将 `nfloat` 和 `nint` 类型标记为 `const`;`static readonly nint` 是一种合理的替代方法。

- 直接位于 `MonoTouch.` 命名空间中的内容现在通常在 `ObjCRuntime.` 命名空间中（例如： `MonoTouch.Constants.Version` 现在 `ObjCRuntime.Constants.Version`）。

- 尝试序列化 `nint` 和 `nfloat` 类型时，序列化对象的代码可能会中断。 请确保在迁移后按预期检查序列化代码。

- 有时，自动工具不会在 `#if #else` 条件编译器指令内丢失代码。 在这种情况下，需要手动进行修补（请参阅下面的常见错误）。

- 迁移工具不会自动修复使用 `[Export]` 的手动导出方法，例如，在此代码中 snippert 必须手动将返回类型更新为 `nfloat`：

  ```csharp
  [Export("tableView:heightForRowAtIndexPath:")]
  public nfloat HeightForRow(UITableView tableView, NSIndexPath indexPath)
  ```

- Unified API 不会提供 NSDate 与 .NET DateTime 之间的隐式转换，因为它不是无损转换。 若要防止与 `DateTimeKind.Unspecified` 相关的错误，请在强制转换到 `NSDate`之前将 .NET `DateTime` 转换为本地或 UTC。

- 目标-C 类别方法现在作为 Unified API 中的扩展方法生成。 例如，以前使用 `UIView.DrawString` 的代码现在将引用 Unified API 中 `NSString.DrawString`。

- 使用带有 `VideoSettings` 的 AVFoundation 类的代码应更改为使用 `WeakVideoSettings` 属性。 这需要 `Dictionary`，它作为设置类的属性提供，例如：

  ```csharp
  vidrec.WeakVideoSettings = new AVVideoSettings() { ... }.Dictionary;
  ```

- NSObject `.ctor(IntPtr)` 构造函数已从公共更改为受保护（[以防不正当使用](~/cross-platform/macios/unified/overview.md#NSObject_ctor)）。

- 已将 `NSAction`[替换](~/cross-platform/macios/unified/overview.md#NSAction)为标准 .net `Action`。 一些简单的（单个参数）委托也已替换为 `Action<T>`。

最后，请参考[经典的 Unified API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)，以便在代码中查找对 api 所做的更改。 搜索[此页](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)将帮助查找经典 api 和已更新的内容。

> [!NOTE]
> 迁移后 `MonoTouch.Dialog` 命名空间保持不变。 如果你的代码使用**monotouch.dialog** ，则应该继续使用该命名空间 *-不要将 `MonoTouch.Dialog` 更改为*`Dialog`！

## <a name="common-compiler-errors"></a>常见编译器错误

下面列出了常见错误的其他示例以及解决方案：

**错误 CS0012：在未引用的程序集中定义了类型 "Monotouch.dialog. UIKit. UIView"。**

修复：这通常意味着项目引用了尚未使用 Unified API 生成的组件或 NuGet 包。 应删除并重新添加所有组件和 NuGet 包。 如果这不能解决此错误，外部库可能还不支持 Unified API。

**错误 MT0034：不能在同一个 Xamarin 项目中同时包含 "monotouch.dialog" 和 "Xamarin."，而是显式引用了 ""，而 "monotouch.dialog" 由 "Xamarin. Mobile，Version = 0.6.3.0，Culture = 中立" 引用PublicKeyToken = null '。**

修复：删除导致此错误的组件，并将其重新添加到项目。

**错误 CS0234：命名空间 "Monotouch.dialog" 中不存在类型或命名空间名称 "Foundation"。是否缺少程序集引用？**

解决方法： Visual Studio for Mac 中的自动迁移工具*应*将所有 `MonoTouch.Foundation` 引用更新为 `Foundation`，但是，在某些情况下，需要手动更新这些引用。 对于之前包含在 `MonoTouch`中的其他命名空间，可能会出现类似错误，如 `UIKit`。

**错误 CS0266：无法将类型 "double" 隐式转换为 "float"**

修复：将类型和强制转换更改为 `nfloat`。 对于包含64位等效项的其他类型（如 `nint`），也可能发生此错误

```csharp
nfloat scale = (nfloat)Math.Min(rect.Width, rect.Height);
```

**错误 CS0266：无法将类型 "CoreGraphics. CGRect" 隐式转换为 "RectangleF"。存在显式转换（是否缺少强制转换？）**

修复：将实例更改为 `RectangleF`，以 `CGRect`、`SizeF` `CGSize`和 `PointF` 更改为 `CGPoint`。 应将命名空间 `using System.Drawing;` 替换为 `using CoreGraphics;` （如果它尚不存在）。

**错误 CS1502： "CoreGraphics"、"nfloat"、"nfloat" 和 "SetLineDash" 的最佳重载方法匹配具有一些无效参数**

修复：将数组类型更改为 `nfloat[]`，并显式强制转换 `Math.PI`。

```csharp
grphc.SetLineDash (0, new nfloat[] { 0, 3 * (nfloat)Math.PI });
```

**错误 CS0115： "WordsTableSource，RowsInSection （UIKit）" 被标记为替代，但找不到合适的方法来重写**

修复：将返回值和参数类型更改为 `nint`。 这种情况通常发生在方法重写中，如 `UITableViewSource`上的，包括 `RowsInSection`、`NumberOfSections`、`GetHeightForRow`、`TitleForHeader`、`GetViewForHeader`等。

```csharp
public override nint RowsInSection (UITableView tableview, nint section) {
```

**错误 CS0508： `WordsTableSource.NumberOfSections(UIKit.UITableView)`：返回类型必须是 "nint" 才能匹配重写的成员 `UIKit.UITableViewSource.NumberOfSections(UIKit.UITableView)`**

修复：当返回类型更改为 `nint`时，将返回值强制转换为 `nint`。

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    return (nint)navItems.Count;
}
```

**错误 CS1061：类型 "CoreGraphics. CGPath" 不包含 "AddElipseInRect" 的定义**

修复：更正 `AddEllipseInRect`的拼写。 其他名称更改包括：

- 将 "Color 黑色" 改为 `NSColor.Black`。
- 将 MapKit "AddAnnotation" 更改为 `AddAnnotations`。
- 将 AVFoundation "DataUsingEncoding" 更改为 `Encode`。
- 将 AVFoundation "AVMetadataObject. TypeQRCode" 更改为 `AVMetadataObjectType.QRCode`。
- 将 AVFoundation "VideoSettings" 更改为 `WeakVideoSettings`。
- 将 PopViewControllerAnimated 更改为 `PopViewController`。
- 将 CoreGraphics "CGBitmapContext. SetRGBFillColor" 更改为 `SetFillColor`。

**错误 CS0546：无法重写，因为 "MapKit" 没有可重写的 set 访问器（CS0546）**

通过对 MKAnnotation 创建自定义批注时，坐标字段没有 setter，只有 getter。

[修复](https://forums.xamarin.com/discussion/comment/109505/#Comment_109505)：

- 添加字段以跟踪坐标
- 在坐标属性的 getter 中返回此字段
- 重写 SetCoordinate 方法并设置字段
- 在具有传入坐标参数的 ctor 中调用 SetCoordinate

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

- [正在更新应用](~/cross-platform/macios/unified/updating-apps.md)
- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 应用](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin 应用程序](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新绑定](~/cross-platform/macios/unified/update-binding.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [经典与 Unified API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)

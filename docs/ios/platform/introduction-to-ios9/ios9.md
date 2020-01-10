---
title: iOS 9 兼容性
description: 即使您不打算直接将 iOS 9 功能添加到您的应用程序，您也应该用 Xamarin 的最新版本重新生成您的应用程序。
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: edc778879aa88d420f41bd2b7b8e15b6d80b5939
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728260"
---
# <a name="ios-9-compatibility"></a>iOS 9 兼容性

_即使您不打算直接将 iOS 9 功能添加到您的应用程序，您也应该用 Xamarin 的最新版本重新生成您的应用程序。_

> [!IMPORTANT]
> 此页上的信息适用于应用商店中已面向 iOS 8 或更早版本的应用程序的客户，但尚未提交更新以实现 iOS 9 兼容性。 如果你已在使用最新版本-Xcode 7 和 Xamarin （iOS 9），请访问[iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)。

当第一个 iOS 9 测试版显示时，我们发现了两个版本的 Xamarin，它们表现为较旧的应用无法在 iOS 9 上启动。

这两个问题（如[我们的论坛中的详细信息](https://forums.xamarin.com/discussion/comment/131529/#Comment_131529)）是：

- IOS 8 或更早版本的应用生成无法在32位设备（包括用[Unified API](~/cross-platform/macios/unified/index.md)构建的应用）上启动。
- 未指定具有完整路径的 P/Invoke 失败。

将 Xamarin 安装更新到最新的稳定通道版本，然后重新生成并重新部署应用，这两个问题将得到解决。

_即使你不打算立即通过 iOS 9 功能更新应用，我们也建议你重新生成最新版本的 Xamarin，并将其重新提交到 App Store_。

这将确保你的应用在客户升级后将在 iOS 9 上运行。
你可以继续支持 iOS 8-通过最新版本重新生成，不会影响应用程序目标版本。

如果在 iOS 9 上测试现有应用时遇到问题，请阅读下面的[提高兼容性](#compat)部分。

### <a name="updating-with-visual-studio"></a>用 Visual Studio 更新

建议你显式检查是否已将 Visual Studio 更新到最新的稳定版本。

## <a name="what-about-components-nugets-and-other-libraries"></a>什么是组件、Nuget 和其他库？

**不**需要等待使用的任何组件或 nuget 的新版本来解决上述两个问题。
只需使用最新稳定版本的 Xamarin 重新构建你的应用程序，即可解决这些问题。

同样，组件供应商和 NuGet 作者**无**需提交新的生成即可解决上述两个问题。 但是，如果任何组件或 NuGet 使用**Xib**文件中的 `UICollectionView` 或加载视图，则*可能*需要更新以解决下面所述的 iOS 9 兼容性问题。

<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>提高代码中的兼容性

在 iOS 9 中，有几种代码模式*用于*在较早版本的 ios 中工作。 下面是在 iOS 9 上进行测试时可能出现的一些问题（及其解决方案）：

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell ContentView 在构造函数中为 null

**原因：** 在 iOS 9 中，UICollectionView 构造函数现在是必需 `initWithFrame:` 的，因为 iOS 9 中的行为更改为[文档状态](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果为指定标识符注册了一个类，并且必须创建新的单元格，则该单元格现在通过调用其 `initWithFrame:` 方法进行初始化。

**修复：** 添加 `initWithFrame:` 构造函数，如下所示：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相关示例： [MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)、 [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)

### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>从 Xib/笔尖加载视图时，UIView 无法使用编码员初始化

**原因：** `initWithCoder:` 构造函数是从 Interface Builder Xib 文件加载视图时调用的构造函数。 如果此构造函数不是导出的，则非托管代码不能调用我们的托管版本。 之前（例如 在 iOS 8 中）调用了 `IntPtr` 构造函数来初始化视图。

**修复：** 创建和导出 `initWithCoder:` 构造函数，如下所示：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相关示例：[聊天](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)

### <a name="dyld-message-no-cache-image-with-name"></a>Dyld 消息：没有名称为的缓存映像 。

日志中的以下信息可能会出现崩溃：

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 这是 Apple 的本机链接器中的一个 bug，在这种情况下，会发生这种情况：公共框架公开（JavaScriptCore 是在 iOS 7 中公开的，在这种情况下，它是一个专用框架），该应用的部署目标是在该框架专用时用于 iOS 版本。 在这种情况下，Apple 的链接器将链接到框架的私有版本，而不是公共版本。

**修复：** 这适用于 iOS 9，但有一种简单的解决方法，你可以在此期间自行应用：仅面向更高版本的项目中的 iOS 版本（在这种情况下，你可以尝试使用 iOS 7）。 其他框架可能会出现类似的问题，例如，WebKit 框架已在 iOS 8 中公开（因此面向 iOS 7 会导致此错误; 应以 iOS 8 为目标，以在应用中使用 WebKit）。

## <a name="related-links"></a>相关链接

- [iOS 9 兼容性发行信息](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)
- [将你的 Xamarin iOS 应用程序更新到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)

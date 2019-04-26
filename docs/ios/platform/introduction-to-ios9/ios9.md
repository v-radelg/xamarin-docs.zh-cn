---
title: iOS 9 兼容性
description: 即使您不打算立即向应用添加 iOS 9 功能，应重新生成你的最新版本的 Xamarin 应用。
ms.prod: xamarin
ms.assetid: 69A05B0E-8A0A-489F-8165-B10AC46FAF3C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 6ade1c05c8e1cc64a4d24df1284d86175083ab80
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293639"
---
# <a name="ios-9-compatibility"></a>iOS 9 兼容性

_即使您不打算立即向应用添加 iOS 9 功能，应重新生成你的最新版本的 Xamarin 应用。_

> [!IMPORTANT]
> 此页上的信息适用于具有已在应用商店应用的客户面向 iOS 8 或更早版本，具有不已提交的用户的 iOS 9 兼容性的更新。 如果你已在使用最新版本的 Xcode 7 和 Xamarin.iOS 9-针对应用开发，请访问[iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)。

如果而出现的第一个 iOS 9 测试版，我们使用较旧版本的表现为较旧的应用无法启动 iOS 9 上的 Xamarin 标识两个问题。

这两个问题 (作为[在我们的论坛上详细](http://forums.xamarin.com/discussion/comment/131529/#Comment_131529)) 是：

- 适用于 iOS 8 或更早版本无法在 32 位设备上开始生成应用 (包括使用生成的应用[Unified API](~/cross-platform/macios/unified/index.md))。
- 未指定 P/Invoke 而失败的完整路径。

Xamarin 安装更新到最新稳定通道版本中，然后重新生成并重新部署你的应用，解决了这两个问题。

_即使你不打算立即使用 iOS 9 功能更新您的应用程序，我们建议使用最新版本的 Xamarin 重新生成和重新提交到 App Store_。



这将确保你的客户在升级后，您的应用程序将运行 iOS 9 上。
可以继续支持 iOS 8 的最新版本重新生成不会影响应用程序的目标版本。

如果测试 iOS 9 上的现有应用程序时有进一步的问题，请阅读[提高兼容性](#compat)下面一节。


### <a name="updating-with-visual-studio"></a>使用 Visual Studio 更新

建议您显式检查 Visual Studio 将更新为最新稳定版本。

## <a name="what-about-components-nugets-and-other-libraries"></a>组件、 Nuget 和其他库呢？

执行操作**不**需要等待的任何组件或用来解决上面提到的两个问题的 Nuget 新版本。
只需通过重新生成最新稳定版本的 Xamarin.iOS 应用程序修复这些问题。

同样，组件供应商和 Nuget 创建者也**不**要求提交新版本只是为了解决上面提到的两个问题。 但是，如果任何组件或 Nuget 使用`UICollectionView`或从视图加载**Xib**文件，更新*可能*会需要用来处理下面所述的 iOS 9 兼容性问题。


<a name="compat" />

## <a name="improving-compatibility-in-your-code"></a>提高您的代码中的兼容性

还有少数情况下的代码模式*使用*以适用于较旧版本的 iOS 9 中的重大的 iOS。 下面是一些可能的问题 （和其解决方案），可能会出现在 iOS 9 上测试时：

### <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 是 null，在构造函数

**原因：** 在 iOS 9`initWithFrame:`构造函数现在是必需的由于为 iOS 9 中的行为更改[UICollectionView 文档中所声明](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollectionView_class/#//apple_ref/occ/instm/UICollectionView/dequeueReusableCellWithReuseIdentifier:forIndexPath)。 如果注册为指定的标识符的类，并且必须创建新的单元格，单元格现在通过调用来初始化其`initWithFrame:`方法。

**解决方法：** 添加`initWithFrame:`此类构造函数：

```csharp
[Export ("initWithFrame:")]
public YourCellClassName (CGRect frame) : base (frame)
{
    Initialize (); // refactor initialize code into a method
}
```

相关的示例：[MotionGraph](https://github.com/xamarin/monotouch-samples/commit/3c1b7a4170c001e7290db9babb2b7a6dddeb8bcb)， [TextKitDemo](https://github.com/xamarin/monotouch-samples/commit/23ea01b37326963b5ebf68bbcc1edd51c66a28d6)



### <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView init 与编码员到 Xib/Nib 从加载视图时将失败

**原因：**`initWithCoder:`构造函数是另一个名为从接口生成器 Xib 文件加载视图时。 如果此构造函数不会导出非托管的代码不能调用我们托管的版本。 以前 （例如。 在 iOS 8)`IntPtr`已调用构造函数来初始化视图。

**解决方法：** 创建和导出`initWithCoder:`此类构造函数：

```csharp
[Export ("initWithCoder:")]
public YourClassName (NSCoder coder) : base (coder)
{
    Initialize (); // refactor initialize code into a method
}
```

相关的示例：[Chat](https://github.com/xamarin/monotouch-samples/commit/7b81138d52e5f3f1aa3769fcb08f46122e9b6a88)


### <a name="dyld-message-no-cache-image-with-name"></a>Dyld 消息： 具有名称没有缓存图像...

你可能会遇到在系统崩溃时在日志中的以下信息：

```csharp
Dyld Error Message:
Dyld Message: no cache image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 这是 Apple 的本机链接器，这种情况发生时它们公开专用框架中的 bug （JavaScriptCore 被公开在 iOS 7 中, 之前，它是一个专用的框架），和框架为私有时，应用程序的部署目标是 iOS 版本。 在这种情况下 Apple 的链接器将链接而不是公共版本 framework 的专用版本。

**解决方法：** 这将解决适用于 iOS 9，但可以将自己在此期间应用简单的解决方法： 只需面向更高版本 iOS 版本在项目中的 （在这种情况下，你可以尝试 iOS 7）。 其他框架可能会表现出类似的问题，例如 WebKit framework 被公开在 iOS 8 （和以便面向 iOS 7 将导致此错误; 应面向 iOS 8 应用程序中使用易于使用的功能）。



## <a name="related-links"></a>相关链接

- [iOS 9 兼容性版本信息](https://releases.xamarin.com/ios-hotfix-for-ios-9-preview-xcode-6/)
- [iOS 9 简介](~/ios/platform/introduction-to-ios9/index.md)
- [更新你的 Xamarin.iOS 应用到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)

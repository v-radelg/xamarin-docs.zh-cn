---
title: Xamarin.iOS 9 – 故障排除
description: 本文提供使用 Xamarin.iOS 中的 iOS 9 的各种故障排除提示。 提示介绍 XML 分析、 模拟器、 布局限制、 网络问题和许多其他主题。
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: f8fae79af654339b54a8df0d2ea32eef38f34adb
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425176"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin.iOS 9 – 故障排除

_本文提供使用 Xamarin.iOS 应用程序中的 iOS 9 的多个故障排除提示。_

## <a name="there-was-a-problem-parsing-the-xml"></a>无法分析 XML

Xamarin iOS 设计器尚不支持 Xcode 7 功能。 将无法使用设计器中加载情节提要 _"时出现问题分析 XML"_ 时尝试使用新的 iOS 9 (Xcode 7) 如 StackView 设计器元素。

iOS 设计器支持 Xcode 7 功能，针对即将发布周期 6 功能版本。 周期 6 的预览版本目前以 Alpha 通道，受到有限的新 Xcode 7 功能的支持。

针对 Visual Studio for Mac 的部分解决方法：右键单击情节提要，然后选择**打开** > **Xcode Interface Builder**。

## <a name="where-are-the-ios-8-simulators"></a>在哪里？ iOS 8 模拟器

如果已安装的 Xcode 7 （或更高版本） 将自动替换所有 iOS 8 模拟器 iOS 9 模拟器默认情况下。 如果您仍需要在 iOS 8 上测试，可以启动 Xcode，然后下载并安装 iOS 8 模拟器。

在 Xcode 中，选择**Xcode**菜单然后**首选项...**  > **下载**:

[![](troubleshooting-images/ios8.png "iOS 8 模拟器下载")](troubleshooting-images/ios8.png#lightbox)

单击**检查和立即安装**按钮以重新安装 iOS 8 模拟器。

## <a name="layout-constraint-with-leftright-attribute-errors"></a>使用左/右属性错误布局约束

在 iOS 8 （及先前版本） 中的情节提要的 UI 元素可以使用这二者的融合**右** & **左侧**属性 (`NSLayoutAttributeRight` & `NSLayoutAttributeLeft`) 和**前导** & **尾随**属性 (`NSLayoutAttributeLeading` & `NSLayoutAttributeTrailing`) 中相同的布局。

如果在 iOS 9 中运行相同的情节提要中，则将导致以下窗体中出现异常：

> 终止应用，因为未捕获异常 NSInvalidArgumentException，原因: * * * + [NSLayoutConstraint constraintWithItem:attribute:relatedBy:toItem:attribute:multiplier:constant:]:不能前导/尾随属性和一个右/左属性之间建立一个约束。 使用前导/尾随的同时还是两者皆否。

iOS 9 强制执行布局以使用两种**右** & **左侧**_或者_**前导** &  **尾随**属性，但*不*两者。 若要解决此问题，请更改所有布局限制为使用相同的情节提要文件中设置的属性。

有关详细信息，请参阅[iOS 9 约束错误](https://stackoverflow.com/questions/32692841/ios-9-constraint-error)Stack Overflow 讨论。

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>错误 ITMS-90535:意外的 CFBundleExecutable 关键字

切换到 iOS 9 后, 从应用程序使用第三方组件 （特别是我们现有 Google Maps 组件） 编译并运行 ios 8 （或更早），尝试提交到 iTunes Connect 窗体中出现错误的新的生成时的：

> 错误 ITMS-90535:意外的 CFBundleExecutable 键。 在 Payload/app-name.app/component.bundle 捆绑包不包含捆绑包可执行文件...

此问题可以通常通过在项目中查找名为捆绑包来解决，然后将-就像错误消息的建议-编辑`Info.plist`捆绑包中是通过删除`CFBundleExecutable`密钥。 `CFBundlePackageType`密钥应设置为`BNDL`也。

进行这些更改之后, 执行清理和重新生成整个项目。 您应能够进行这些更改后提交到 iTunes Connect 毫无问题。

有关详细信息，请参阅此[Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key)讨论。

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake 失败 (-9824) 错误

在尝试连接到 internet，直接或从 iOS 9 中的 web 视图时，你可能会遇到错误，在窗体中：

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

或在窗体中：

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

IOS9，在应用程序传输安全 (ATS) 强制实施 internet 资源 （如应用程序的后端服务器） 和您的应用程序之间的安全连接。 此外，ATS 要求通信使用`HTTPS`协议和高级别 API 通信进行加密与正向保密使用 TLS 1.2 版。

由于默认情况下，所有连接使用适用于 iOS 9 和 OS X 10.11 (El Capitan) 都构建应用程序中启用了 ATS `NSURLConnection`，`CFURL`或`NSURLSession`将受到 ATS 安全要求。 如果你的连接不能满足这些要求，它们将失败并出现异常。

请参阅[Opting 带 ATS](~/ios/app-fundamentals/ats.md)的部分我们[应用传输安全](~/ios/app-fundamentals/ats.md)指南以获取有关如何解决此问题的信息。

## <a name="my-existing-apps-dont-run-on-ios-9"></a>我现有的应用程序不运行于 iOS 9

请参阅我们[iOS 9 兼容性信息](~/ios/platform/introduction-to-ios9/ios9.md)有关重新生成和重新部署现有应用程序以运行于 iOS 9 的说明。

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell.ContentView 是构造函数中的为 Null

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

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>UIView Init 与编码员到 Xib/Nib 从加载视图时将失败

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

## <a name="dyld-message-no-cache-image-with-name"></a>Dyld 消息：没有具有名称的缓存图像...

你可能会遇到在系统崩溃时在日志中的以下信息：

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 这是 Apple 的本机链接器，这种情况发生时它们公开专用框架中的 bug （JavaScriptCore 被公开在 iOS 7 中, 之前，它是一个专用的框架），和框架为私有时，应用程序的部署目标是 iOS 版本。 在这种情况下 Apple 的链接器将链接而不是公共版本 framework 的专用版本。

**解决方法：** 这将解决适用于 iOS 9，但可以将自己在此期间应用简单的解决方法： 只需面向更高版本 iOS 版本在项目中的 （在这种情况下，你可以尝试 iOS 7）。 其他框架可能会表现出类似的问题，例如 WebKit framework 被公开在 iOS 8 （和以便面向 iOS 7 将导致此错误; 应面向 iOS 8 应用程序中使用易于使用的功能）。

## <a name="untrusted-enterprise-developer"></a>不受信任的企业级开发版

在尝试在真实的 iOS 硬件上运行 Xamarin.iOS 应用的 iOS 9 版本时，可能会收到一条消息指出在设备上你的开发人员帐户不受信任。 例如：

[![](troubleshooting-images/untrusted01.png "不受信任的企业级开发版警报")](troubleshooting-images/untrusted01.png#lightbox)

若要解决此问题，请执行以下操作：

1. 启动 Xcode （最新 beta 版本） 上开发的 mac。
2. 选择**设备**从**窗口**菜单以打开设备窗口： 

    [![](troubleshooting-images/untrusted02.png "设备窗口")](troubleshooting-images/untrusted02.png#lightbox)
3. 下**设备**侧面板中，选择设备，右键单击并选择**显示预配配置文件...**: 

    [![](troubleshooting-images/untrusted03.png "SShow 预配配置文件")](troubleshooting-images/untrusted03.png#lightbox)
4. 选择当前在该设备并单击每个预配配置文件**-** 按钮以将其删除： 

    [![](troubleshooting-images/untrusted04.png "正在删除预配配置文件")](troubleshooting-images/untrusted04.png#lightbox)
5. 从**Xcode**菜单中，选择**首选项...** 并**帐户**: 

    [![](troubleshooting-images/untrusted05.png "Xcode 帐户首选项")](troubleshooting-images/untrusted05.png#lightbox)
6. 单击**查看详细信息...** 按钮，然后单击**下载所有**按钮： 

    [![](troubleshooting-images/untrusted06.png "下载所有配置文件")](troubleshooting-images/untrusted06.png#lightbox)
7. 更新完成列表后，单击**完成**按钮并关闭首选项窗口中。
8. 删除现有版本的 Xamarin.iOS 应用，它尝试从 iOS 设备进行测试。
9. 返回到 Visual Studio for Mac，生成已清理并尝试在设备上重新运行该应用程序。

您可能必须停止并重启 Visual Studio for Mac，才能看到由 Xcode 加载新预配配置文件。 您可能还需要调整**iOS 捆绑签名**Xamarin.iOS 应用以选择新的预配配置文件的选项。

## <a name="launch-screen-issues"></a>启动屏幕问题

iOS 9 现在强制执行的启动屏幕要求，以便不再可以重用相同的启动映像以支持不同的接口的方向。 请参阅 Apple [UILanchImage 引用](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28)有关详细信息。

（可选） 可以使用情节提要文件提供应用程序的启动屏幕而不是使用一套 **.png**图像文件。 这是现在 Apple 的首选方法，可以显示启动屏幕。 请参阅我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)指南以获取详细信息。

最后，您的应用程序必须使用其启动屏幕情节提要文件和支持全部四个界面方向 （纵向倒置纵向、 横向和横向 Right） 被视为对于滑动通过面板中或在拆分视图模式下运行。 若要了解有关 iOS 9 的新的多任务处理能力的详细信息，请参阅我们[适用于 iPad 的多任务](~/ios/platform/multitasking.md)指南。

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException 异常

编译和运行 ios 9 现有 Xamarin.iOS 应用程序时您可能会遇到错误，在窗体中：

> Objective C 中引发异常。  姓名:NSInternalInconsistencyException 原因：Windows 的应用程序应能够根视图控制器应用程序结束时启动

这是错误因为应用 Windows 应能够在应用程序启动末尾根视图控制器和现有的应用不会被引发。

有关于此问题在至少两个可能的解决方法：

1. 更新应用程序以使用情节提要文件，而不是`xib`文件以定义其用户界面。 此其中一个需要很多很多时间，具体取决于您的应用程序的大小和了解如何使用 iOS 设计器 （或 Xcode 的 Interface Builder） 到布局情节提要。 有关详细信息，请参阅我们[统一情节提要简介](~/ios/user-interface/storyboards/unified-storyboards.md)文档。
2. 安装程序`RootViewController`属性的应用程序窗口中`FinishedLaunching`中的方法`AppDelegate`类，以指向您的应用 UI 中的视图控制器。

## <a name="when-to-initialize-views-and-view-controllers"></a>何时初始化视图和视图控制器

使用 Xamarin.iOS，可以发出视图或视图控制器内的内容公开为托管代码，但这会破坏 iOS 设计时，将调用的构造函数的初始化。

一般情况下应不初始化任何可以调用返回 Objective C 代码从构造函数由于无法确定调用时。 这也意味着没有更好的位置 (其他.ctor) 或调用重写 （如 Objective C 不有任何事件），应进行此初始化。

## <a name="related-links"></a>相关链接

- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [What's New iOS 9.0 中](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [更新你的 Xamarin.iOS 应用到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)

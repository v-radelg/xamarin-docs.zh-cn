---
title: Xamarin （iOS 9）-故障排除
description: 本文提供了有关在 Xamarin 中使用 iOS 9 的各种疑难解答技巧。 提示介绍 XML 分析、模拟器、布局约束、网络问题以及许多其他主题。
ms.prod: xamarin
ms.assetid: DCE83E36-CBD9-4D96-8E7F-384CB8A54563
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 437698fcda6e85090cd7bdce90959300436e0bc2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031762"
---
# <a name="xamarinios-9--troubleshooting"></a>Xamarin （iOS 9）-故障排除

_本文提供了有关在 Xamarin iOS 应用中使用 iOS 9 的几个疑难解答技巧。_

## <a name="there-was-a-problem-parsing-the-xml"></a>分析 XML 时出现问题

Xamarin iOS 设计器尚不支持 Xcode 7 功能。 当尝试使用新的 iOS 9 （Xcode 7）设计器元素（如 System.windows.forms.toolstrip.stackview）时，演示图板将无法在设计器中加载，出现 _"分析 XML 时出现问题"_ 。

iOS Designer 对 Xcode 7 功能的支持面向即将推出的周期6功能版本。 周期6的预览版本当前在 Alpha 通道中提供，并且对新 Xcode 7 功能的支持有限。

Visual Studio for Mac 的部分解决方法：右键单击情节提要，然后选择 "**打开方式**" > **Xcode Interface Builder**"。

## <a name="where-are-the-ios-8-simulators"></a>IOS 8 模拟器位于何处？

如果安装了 Xcode 7 （或更高版本），则默认情况下会自动将所有 iOS 8 模拟器替换为 iOS 9 模拟器。 如果仍需要在 iOS 8 上进行测试，可以启动 Xcode，然后下载并安装 iOS 8 模拟器。

在 Xcode 中，选择**Xcode**菜单，然后选择 "**首选项 ...** " > **下载**：

[![](troubleshooting-images/ios8.png "iOS 8 Simulators Downloads")](troubleshooting-images/ios8.png#lightbox)

单击 "**检查并立即安装**" 按钮，重新安装 iOS 8 模拟器。

## <a name="layout-constraint-with-leftright-attribute-errors"></a>具有左/右属性错误的布局约束

在 iOS 8 （及更早的）中，情节提要中的 UI 元素可能混合使用**右侧** & **左** **特性（** `NSLayoutAttributeRight` & `NSLayoutAttributeLeft`） &  **，并在**布局相同。

如果中的同一情节提要在 iOS 9 中运行，则它将导致以下格式的异常：

> 由于未捕获的异常 "NSInvalidArgumentException"，正在终止应用，原因： "* * * + [NSLayoutConstraint constraintWithItem： attribute： relatedBy： toItem： attribute：乘法器：常量：]：不能在前导/尾随之间进行约束特性和右/左特性。 对两者均使用前导/尾随。 "

iOS 9 强制执行布局以使用**右** & **左 & ** _或_**前导** **尾随**属性，但*不*能同时使用两者。 若要解决此问题，请更改所有布局约束以在情节提要文件中使用相同的属性集。

有关详细信息，请参阅[iOS 9 约束错误](https://stackoverflow.com/questions/32692841/ios-9-constraint-error)Stack Overflow 讨论。

## <a name="error-itms-90535-unexpected-cfbundleexecutable-key"></a>错误 ITMS-90535：意外的 CFBundleExecutable 键

切换到 iOS 9 后，从应用使用在 iOS 8 （或更早版本）上编译并运行的第三方组件（特别是我们的现有 Google Maps 组件），尝试将新版本提交到 iTunes Connect 时，可能会出现以下格式的错误：

> 错误 ITMS-90535：意外的 CFBundleExecutable 键。 "负载/app-name/组件捆绑" 中的捆绑包不包含捆绑可执行文件 。

通常可以通过在项目中查找已命名的绑定来解决此问题，就像错误消息建议-通过删除 `CFBundleExecutable` 密钥来编辑绑定中的 `Info.plist`。 `CFBundlePackageType` 项还应设置为 `BNDL`。

进行这些更改后，请执行干净的操作并重新生成整个项目。 进行这些更改后，你应该能够提交到 iTunes Connect，而不会出现问题。

有关详细信息，请参阅此[Stack Overflow](https://stackoverflow.com/questions/32096130/unexpected-cfbundleexecutable-key)讨论。

## <a name="cfnetwork-sslhandshake-failed--9824-error"></a>CFNetwork SSLHandshake 失败（-9824）错误

尝试在 iOS 9 中直接或从 web 视图连接到 internet 时，可能会收到如下格式的错误：

```csharp
2015-09-04 14:38:05.757 FormsWebViewiOS[2553:30362] CFNetwork SSLHandshake failed (-9824)
2015-09-04 14:38:05.758 FormsWebViewiOS[2553:30363] NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9824)
```

或采用以下格式：

```csharp
2015-09-04 14:39:17.881 FormsWebViewiOS[2568:30974] App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure.
Temporary exceptions can be configured via your app's Info.plist file.
```

在 iOS9 中，应用传输安全（ATS）在 internet 资源（如应用的后端服务器）和应用之间强制实施安全连接。 此外，ATS 还要求使用 `HTTPS` 协议进行通信，并使用 TLS 版本1.2 和正向机密对高级 API 通信进行加密。

由于默认情况下，在为 iOS 9 和 OS X 10.11 （El Capitan）构建的应用中启用了 ATS，因此使用 `NSURLConnection`、`CFURL` 或 `NSURLSession` 的所有连接都将受到 ATS 安全要求的限制。 如果连接不满足这些要求，它们将失败并出现异常。

有关如何解决此问题的信息，请参阅我们的[应用传输安全](~/ios/app-fundamentals/ats.md)指南中的[ATS](~/ios/app-fundamentals/ats.md)部分。

## <a name="my-existing-apps-dont-run-on-ios-9"></a>我的现有应用不在 iOS 9 上运行

有关重新构建和重新部署现有应用以在 iOS 9 上运行的说明，请参阅[ios 9 兼容性信息](~/ios/platform/introduction-to-ios9/ios9.md)。

<a name="UICollectionViewCell.ContentView-is-null-in-constructors" />

## <a name="uicollectionviewcellcontentview-is-null-in-constructors"></a>UICollectionViewCell ContentView 在构造函数中为 Null

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

<a name="UIView-fails-to-Init-with-Coder-when-Loading-a-View-from-a-Xib/Nib" />

## <a name="uiview-fails-to-init-with-coder-when-loading-a-view-from-a-xibnib"></a>从 Xib/笔尖加载视图时，UIView 无法使用编码员初始化

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

## <a name="dyld-message-no-cache-image-with-name"></a>Dyld 消息：没有名称为的缓存映像 。

日志中的以下信息可能会出现崩溃：

```csharp
Dyld Error Message:
Dyld Message: no cach image with name (/System/Library/PrivateFrameworks/JavaScriptCore.framework/JavaScriptCore)
```

**原因：** 这是 Apple 的本机链接器中的一个 bug，在这种情况下，会发生这种情况：公共框架公开（JavaScriptCore 是在 iOS 7 中公开的，在这种情况下，它是一个专用框架），该应用的部署目标是在该框架专用时用于 iOS 版本。 在这种情况下，Apple 的链接器将链接到框架的私有版本，而不是公共版本。

**修复：** 这适用于 iOS 9，但有一种简单的解决方法，你可以在此期间自行应用：仅面向更高版本的项目中的 iOS 版本（在这种情况下，你可以尝试使用 iOS 7）。 其他框架可能会出现类似的问题，例如，WebKit 框架已在 iOS 8 中公开（因此面向 iOS 7 会导致此错误; 应以 iOS 8 为目标，以在应用中使用 WebKit）。

## <a name="untrusted-enterprise-developer"></a>不受信任的企业开发人员

尝试在实际 iOS 硬件上运行你的 Xamarin iOS 应用的 iOS 9 版本时，可能会收到一条消息，指出你的开发人员帐户尚未在设备上受信任。 例如:

[![](troubleshooting-images/untrusted01.png "Untrusted Enterprise Developer alert")](troubleshooting-images/untrusted01.png#lightbox)

若要解决此问题，请执行以下操作：

1. 启动开发 Mac 上的 Xcode （最新的 beta 版本）。
2. 从 "**窗口**" 菜单中选择 "**设备**"，打开 "设备" 窗口： 

    [![](troubleshooting-images/untrusted02.png "The Devices Window")](troubleshooting-images/untrusted02.png#lightbox)
3. 在 "**设备**" 侧面板中，选择你的设备，右键单击并选择 "**显示预配配置文件 ...** "： 

    [![](troubleshooting-images/untrusted03.png "SShow Provisioning Profiles")](troubleshooting-images/untrusted03.png#lightbox)
4. 选择当前设备上的每个设置配置文件，然后单击 " **-** " 按钮将其删除： 

    [![](troubleshooting-images/untrusted04.png "Deleting a provisioning profile")](troubleshooting-images/untrusted04.png#lightbox)
5. 从 " **Xcode** " 菜单中，选择 "**首选项 ...** " 和 "**帐户**"： 

    [![](troubleshooting-images/untrusted05.png "Xcode account preferences")](troubleshooting-images/untrusted05.png#lightbox)
6. 单击 "**查看详细信息 ...** " 按钮，然后单击 "**全部下载**" 按钮： 

    [![](troubleshooting-images/untrusted06.png "Download all profiles")](troubleshooting-images/untrusted06.png#lightbox)
7. 列表完成更新后，单击 "**完成**" 按钮并关闭 "首选项" 窗口。
8. 删除你尝试从 iOS 设备进行测试的现有 Xamarin iOS 应用的版本。
9. 返回 Visual Studio for Mac，执行干净的生成并尝试在设备上重新运行应用。

你可能必须先停止并重新启动 Visual Studio for Mac，然后才能查看 Xcode 加载的新预配配置文件。 你可能还需要调整 Xamarin iOS 应用的**IOS 捆绑签名**选项，以选择新的预配配置文件。

## <a name="launch-screen-issues"></a>启动屏幕问题

iOS 9 现在强制实施启动屏幕要求，这样就不能再重复使用同一个启动映像来支持不同的界面方向。 有关详细信息，请参阅 Apple 的[UILanchImage 参考](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW28)。

或者，您可以使用情节提要文件来显示应用程序的启动屏幕，而不是使用一组 **.png**图像文件。 这是一种显示启动屏幕的 Apple 首选方式。 有关详细信息，请参阅我们的[统一情节提要指南简介](~/ios/user-interface/storyboards/unified-storyboards.md)。

最后，你的应用程序必须使用情节提要文件来启动屏幕，并支持所有四种界面方向（纵向、倒置纵向、横向和横向），以便在滑动到面板或拆分视图模式下运行。 若要了解有关 iOS 9 的新多任务功能的详细信息，请参阅我们的[多任务 For iPad](~/ios/platform/multitasking.md)指南。

## <a name="nsinternalinconsistencyexception-exception"></a>NSInternalInconsistencyException 异常

在编译和运行适用于 iOS 9 的现有 Xamarin iOS 应用时，可能会出现以下格式的错误：

> 引发了目标-C 异常。  名称： NSInternalInconsistencyException 原因：应用程序窗口应在应用程序启动结束时具有根视图控制器

出现这种错误的原因是应用程序窗口在应用程序启动结束时应具有一个根视图控制器，而你的现有应用程序不会。

此问题至少有两种可能的解决方法：

1. 更新应用程序以使用情节提要文件而不是 `xib` 文件来定义其用户界面。 这种方法需要很长时间，具体取决于你的应用程序的大小，以及如何使用 iOS 设计器（或 Xcode 的 Interface Builder）来布局情节提要的知识。 有关详细信息，请参阅我们的[统一情节提要文档简介](~/ios/user-interface/storyboards/unified-storyboards.md)。
2. 设置 `AppDelegate` 类中 `FinishedLaunching` 方法的应用窗口 `RootViewController` 属性，以指向应用 UI 中的视图控制器。

## <a name="when-to-initialize-views-and-view-controllers"></a>何时初始化视图和视图控制器

借助 Xamarin，可在构造函数中进行查看或视图控制器初始化，在代码公开到托管代码中时，将调用这些构造函数，但会中断 iOS 设计。

通常，不应初始化可从构造函数中调用目标 C 代码的任何内容，因为无法确定是否要调用它。 这也意味着有更好的位置（其他 .ctor）或调用以重写（因为目标-C 没有事件），此时应执行此初始化。

## <a name="related-links"></a>相关链接

- [适用于开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新增功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [将你的 Xamarin iOS 应用程序更新到 iOS9 （视频）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)

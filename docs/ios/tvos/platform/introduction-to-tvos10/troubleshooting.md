---
title: 排查用 Xamarin 构建的 tvOS 10 应用的问题
description: 本文提供了有关在 Xamarin 应用中使用 tvOS 10 的几个疑难解答技巧。 它介绍了与应用商店、二进制兼容性、CFNetwork HttpProtocol、CloudKit、Core Image、NSUserActivity 和 UIKit 相关的问题。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a6588dee675aee3e2580b70dfdea2920c6235775
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030599"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>排查用 Xamarin 构建的 tvOS 10 应用的问题

以下部分列出了在 Xamarin 中使用 tvOS 10 时可能出现的一些已知问题，以及针对这些问题的解决方案：

- [App Store](#App-Store)
- [二进制兼容性](#Binary-Compatibility)
- [CFNetwork HTTP 协议](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [核心映像](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

已知问题：

- 在沙盒环境中测试应用内购买时，身份验证对话框可能出现两次。
- 在沙盒环境中测试应用内购买的托管内容时，每次将应用程序置于前台时都会显示密码对话框，直到内容下载完成。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二进制兼容性

已知问题：

- 调用 `NSObject.ValueForKey` 将 `null` 会导致异常。
- 调用 `UIFont.WithName` 时，按名称引用字体将导致崩溃。
- 在 `http://` Url 的 TLS 握手期间，`NSURLSession` 和 `NSURLConnection` 不再有 RC4 密码套件。
- 如果应用在 `ViewWillLayoutSubviews` 或 `LayoutSubviews` 方法中修改 superview 的几何，应用可能会挂起。
- 对于所有 SSL/TLS 连接，默认情况下，RC4 对称密码处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，建议应用程序尽快停止使用 SHA-1 和3DES 加密。

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 协议

`NSMutableURLRequest` 类的 `HTTPBodyStream` 属性必须设置为未打开的流，因为 `NSURLConnection` 和 `NSURLSession` 现在严格强制实施此要求。

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

长时间运行的操作将返回 _"您没有保存文件的权限"。_ 条.

<a name="CoreImage" />

## <a name="core-image"></a>核心映像

`CIImageProcessor` API 现在支持任意输入图像计数。 tvOS 10 beta 1 中包含的 `CIImageProcessor` API 将被删除。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

在提交操作之后，`NSUserActivity` 对象的 `UserInfo` 属性可能为空。 作为当前的解决方法，显式调用 `BecomeCurrent` NSUserActivity "对象。

<a name="UIKit" />

## <a name="uikit"></a>UIKit

已知问题：

- 对 `UINavigationBar`、`UITabBar` 或 `UIToolBar` 的背景外观的更改可能会导致布局传递以解析新的外观。 如果尝试在 `LayoutSubviews`、`UpdateConstraints`、`WillLayoutSubviews` 或 `DidUpdateSubviews` 事件内修改这些外观，可能会导致无限布局循环。
- 在 tvOS 10 中，调用 `UIView` 对象的 `RemoveGestureRecognizer` 方法会显式取消任何正在进行的笔势识别器。
- 呈现的视图控制器现在会影响状态栏的外观。
- tvOS 10 要求开发人员在 `UIViewController` 和重写 `AwakeFromNib` 方法时调用 `base.AwakeFromNib`。
- 使用自定义 `UIView` 子类覆盖 `LayoutSubviews` 并在调用 `base.LayoutSubviews` 之前更新布局的应用程序可能会在 tvOS 10 中触发无限布局循环。
- 指定到 `UIButton` 对象时，方向特定或 flippable 图像资产不进行翻转。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)

---
title: Xamarin.Mac-macOS Sierra 故障排除
description: 本文档提供使用 macOS Sierra Xamarin.Mac 应用中的多个故障排除提示。 提示与 Mac App Store、 Apple Pay，二进制文件兼容性、 CFNetwork、 CloudKit，和的详细信息。
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 09/22/2016
ms.openlocfilehash: 1b379bef98e498df4c58ba7209aa46b0b2542fe1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031416"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin.Mac-macOS Sierra 故障排除

_本文提供使用 macOS Sierra Xamarin.Mac 应用中的多个故障排除提示。_

以下各节列出 Xamarin.mac 和这些问题的解决方案中使用 macOS Sierra 时可能发生的一些已知的问题：

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [二进制文件兼容性](#Binary-Compatibility)
- [CFNetwork HTTP 协议](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Core 映像](#CoreImage)
- [通知](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [Safari](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

已知问题：

- 在沙盒环境中测试应用内购买时, 可能会两次出现身份验证对话框。
- 时使用的沙盒环境中承载的内容测试应用内购买，每次应用转至前台，直到内容下载完成时将显示密码对话框中。

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

如果将新的支付卡添加到 Apple Pay 时输入了不正确的到期日期或安全代码 (CW)，则将终止卡预配过程。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二进制文件兼容性

已知问题：

- 调用`NSObject.ValueForKey`将`null`密钥将导致异常。
- 这两`NSURLSession`和 NSURLConnection` no longer RC4 cipher suites during the TLS handshake for `http:// Url。
- 如果他们要更改超级视图的几何图形中的应用可以挂起`ViewWillLayoutSubviews`或`LayoutSubviews`方法。
- 对于所有 SSL/TLS 连接，RC4 对称密码现在默认处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，建议应用程序停止使用 sha-1 和 3DES 加密方法越早越好。

<a name="CFNetwork-HTTP-Protocol" />

## <a name="cfnetwork-http-protocol"></a>CFNetwork HTTP 协议

`HTTPBodyStream`的属性`NSMutableURLRequest`类必须设置为以来的未打开流`NSURLConnection`和`NSURLSession`现在严格强制实施此要求。

<a name="CloudKit" />

## <a name="cloudkit"></a>CloudKit

长时间运行的操作将返回 _"你无权保存文件。"_ 出现错误。

<a name="CoreImage" />

## <a name="core-image"></a>Core 映像

`CIImageProcessor` API 现在支持任意输入的图像计数。 `CIImageProcessor` MacOS Sierra beta 1 中包含的 API 将被删除。

<a name="Notifications" />

## <a name="notifications"></a>通知

在使用通知内容扩展，视图控制器不被正确释放和达到扩展内存限制时，可能会导致崩溃。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

提交操作后,`UserInfo`属性的`NSUserActivity`对象可能为空。 显式调用`BecomeCurrent``NSUserActivity`对象作为当前的解决方法。

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation 需要一种安全的 (`https://`) 功能在 iOS 10 和 macOS Sierra 以防止恶意使用位置数据的 URL。







## <a name="related-links"></a>相关链接

- [Mac 示例](https://developer.xamarin.com/samples/mac/)
- [什么是 OS X 10.12 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)

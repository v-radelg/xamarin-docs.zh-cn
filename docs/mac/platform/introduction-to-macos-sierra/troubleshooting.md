---
title: Xamarin macOS Sierra 疑难解答
description: 本文档提供了几个用于在 Xamarin 应用程序中处理 macOS Sierra 的故障排除提示。 与 Mac 应用商店、Apple Pay、二进制兼容性、CFNetwork、CloudKit 等相关的提示。
ms.prod: xamarin
ms.assetid: 323DD5EE-87CE-48E4-B234-1CF61B45A019
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 09/22/2016
ms.openlocfilehash: a4e7f7169e4c7ec0ec2947e17b1434179f47488f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017026"
---
# <a name="xamarinmac---macos-sierra-troubleshooting"></a>Xamarin macOS Sierra 疑难解答

_本文提供了有关在 Xamarin for Mac 应用中使用 macOS Sierra 的几个疑难解答技巧。_

以下部分列出了在将 macOS Sierra 与 Xamarin 一起使用时可能会发生的一些已知问题以及这些问题的解决方案：

- [App Store](#App-Store)
- [Apple Pay](#Apple-Pay)
- [二进制兼容性](#Binary-Compatibility)
- [CFNetwork HTTP 协议](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [核心映像](#CoreImage)
- [通知](#Notifications)
- [NSUserActivity](#NSUserActivity)
- [免费](#Safari)

<a name="App-Store" />

## <a name="app-store"></a>App Store

已知问题：

- 在沙盒环境中测试应用内购买时，身份验证对话框可能出现两次。
- 在沙盒环境中测试应用内购买的托管内容时，每次将应用程序置于前台时都会显示密码对话框，直到内容下载完成。

<a name="Apple-Pay" />

## <a name="apple-pay"></a>Apple Pay

如果在将新的付款卡添加到 Apple Pay 时输入了不正确的过期日期或安全代码（CW），则将终止卡预配过程。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二进制兼容性

已知问题：

- 调用 `NSObject.ValueForKey` 将 `null` 会导致异常。
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

`CIImageProcessor` API 现在支持任意输入图像计数。 将删除 macOS Sierra beta 1 中包含 `CIImageProcessor` API。

<a name="Notifications" />

## <a name="notifications"></a>通知

使用通知内容扩展时，视图控制器未正确释放，并且可能导致在达到扩展内存限制时出现故障。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

在提交操作之后，`NSUserActivity` 对象的 `UserInfo` 属性可能为空。 将 `BecomeCurrent` `NSUserActivity` 对象显式调用为当前解决方法。

<a name="Safari" />

## <a name="safari"></a>Safari

WebGeolocation 要求在 iOS 10 和 macOS Sierra 上都使用安全（`https://`） URL，以防止恶意使用位置数据。

## <a name="related-links"></a>相关链接

- [Mac 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [MacOS 10.12 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)

---
title: watchOS 3 故障排除
description: 本文档提供了一些在 Xamarin 中使用 watchOS 3 时有用的故障排除提示。 与活动、Apple Pay、背景刷新、NSURLConnection、隐私等相关的技巧。
ms.prod: xamarin
ms.assetid: 5911D898-0E23-40CC-9F3C-5F61B4D50ADC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 323aad42832a70bf80e7a5bf3508bb9b65e77b5a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655193"
---
# <a name="watchos-3-troubleshooting"></a>watchOS 3 故障排除

_本文提供了有关在 Xamarin Apple Watch 应用中使用 watchOS 3 的几个疑难解答技巧。_

此页列出了将 watchOS 3 与 Xamarin 一起使用时可能会发生的一些已知问题以及针对这些问题的解决方案。

## <a name="activities"></a>活动

要使活动共享正确工作, 所有配对的 Apple 手表都必须运行 watchOS 3。

已知问题：

- 回复活动共享通知有时会失败。
- 使用消息回复活动共享通知可能会失败。
- 活动共享通知消息上方的上下文文本将不正确。

## <a name="apple-pay"></a>Apple Pay

已知问题：

- 如果在 Apple Pay 中输入的到期日期或 CW 代码不正确, 则在**下一次**运行进程时, 将会发生崩溃。
- 需要 PIN 号的应用内 Apple Pay 购买可能会崩溃。

## <a name="auto-mac-unlock"></a>自动 Mac 解锁

通过使用 watchOS 3 beta 2 (或更高版本) 和 macOS Sierra beta 2 (或更高版本), 如果在用户的 iCloud 帐户上启用了双因素身份验证, 则他们可以使用其 Apple Watch 自动解锁其 Mac。

## <a name="background-refresh"></a>后台刷新

违反系统资源会导致 watchOS 3 应用崩溃, 并引发以下异常代码:

- **0xc51bad01** -应用消耗了过多的 CPU 时间。
- **0xc51bad02** -应用消耗的时间太长。
- **0xc51bad03** -应用程序没有足够的运行时来完成当前任务。

## <a name="clock"></a>Clock

新安装的 Apple Watch 应用的复杂性可能显示为空白。 重新启动 Apple Watch 以解决此问题。

## <a name="connectivity"></a>连接性

已知问题：

- watchOS 不会提示用户提供对 Apple Watch 上受保护用户数据的访问权限。 在监视应用中使用数据之前, 请先在 iPhone 应用上授予访问权限。
- Apple Watch 可以输入一个状态, 在所有 WatchConnectivity 传输均失败时, 重新启动 Apple Watch 以解决问题。

## <a name="notifications"></a>通知

如果媒体附件太大, 则会显示在用户的 iPhone 上, 但不会显示在其 Apple Watch 上。

## <a name="nsurlconnection"></a>NSURLConnection

使用`NSURLConnection`较旧 TLS 协议的任何连接都将失败。 对于所有 SSL/TLS 连接, 默认情况下, RC4 对称密码处于禁用状态。 此外, 安全传输 API 不再支持 SSLv3, 建议应用程序尽快停止使用 SHA-1 和3DES 加密。

从 watchOS 3 起, Apple 将严格强制实施 SSL/TLS 连接安全性。 受影响的服务和应用应更新 web 服务器以使用最新的 TLS 协议版本。

## <a name="nsurlsession"></a>NSURLSession

从 watchOS 3 `HTTPBodyStream` `NSURLSession`开始, 必须将`NSMutableURLRequest`类的属性设置为未打开的流, 因为`NSURLConnection`现在会严格强制实施此要求。

## <a name="privacy"></a>隐私

已知问题：

`https://`使用`NSURLConnection` url 时,和在TLS握手期间不再支持`NSURLSession` RC4 密码套件。 可能会生成以下错误代码之一:

- **-1200 或-98** -对于`NSURLErrorSecurityConnectionFailed`和 SecureTransport 错误。
- **-1200 [3:-9824]** -Http 加载失败。
- **-**  -  1200`NSURLConnection`已完成, 但出现错误。

从 watchOS 3 起, Apple 将严格强制实施 SSL/TLS 连接安全性。 受影响的服务和应用应更新 web 服务器以使用最新的 TLS 协议版本。 有关详细信息, 请参阅上面的[NSURLConnection](#nsurlconnection) 。

## <a name="snapshots"></a>快照

未采用新`HandelBackgroundTask` API 的 WatchKit 应用将不再收到 watchOS 3 中的定期更新。 

## <a name="watchkit"></a>WatchKit

当应用进入 watchOS 停靠中的背景时, SpriteKit 和 SceneKit 场景将暂停。

## <a name="related-links"></a>相关链接

- [watchOS 示例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+watchOS)
- [WatchOS 3 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)

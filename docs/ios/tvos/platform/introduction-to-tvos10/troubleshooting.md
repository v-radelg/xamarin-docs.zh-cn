---
title: 故障排除 tvOS 10 使用 Xamarin 生成应用
description: 本文提供使用 tvOS 10 Xamarin 应用中的多个故障排除提示。 它介绍了与应用商店、 二进制文件兼容性、 CFNetwork HttpProtocol、 CloudKit、 Core 映像、 NSUserActivity 和 UIKit 相关的问题。
ms.prod: xamarin
ms.assetid: EA5564BB-C415-49A2-B70C-3DBF5E0F3FAB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3815790cfb73f93f399c14d3da44aa3210725388
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932433"
---
# <a name="troubleshooting-tvos-10-apps-built-with-xamarin"></a>故障排除 tvOS 10 使用 Xamarin 生成应用

以下部分列出了一些可以使用 Xamarin 和这些问题的解决方案中使用 tvOS 10 时可能发生的已知的问题：

- [App Store](#App-Store)
- [二进制文件兼容性](#Binary-Compatibility)
- [CFNetwork HTTP 协议](#CFNetwork-HTTP-Protocol)
- [CloudKit](#CloudKit)
- [Core 映像](#CoreImage)
- [NSUserActivity](#NSUserActivity)
- [UIKit](#UIKit)

<a name="App-Store" />

## <a name="app-store"></a>App Store

已知问题：

 - 在沙盒环境中测试应用内购买时, 可能会两次出现身份验证对话框。
 - 时使用的沙盒环境中承载的内容测试应用内购买，每次应用转至前台，直到内容下载完成时将显示密码对话框中。

<a name="Binary-Compatibility" />

## <a name="binary-compatibility"></a>二进制文件兼容性

已知问题：

 - 调用`NSObject.ValueForKey`将`null`密钥将导致异常。
 - 调用时，按名称引用字体`UIFont.WithName`将会导致故障发生。
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

`CIImageProcessor` API 现在支持任意输入的图像计数。 `CIImageProcessor` TvOS 10 beta 1 中包含的 API 将被删除。

<a name="NSUserActivity" />

## <a name="nsuseractivity"></a>NSUserActivity

提交操作后,`UserInfo`属性的`NSUserActivity`对象可能为空。 显式调用`BecomeCurrent`NSUserActivity 作为当前的解决方法的对象。

<a name="UIKit" />

## <a name="uikit"></a>UIKit

已知问题：

 - 将更改为背景的外观`UINavigationBar`，`UITabBar`或`UIToolBar`可能会导致布局处理过程以解决新的外观。 尝试修改这些外观的内部`LayoutSubviews`， `UpdateConstraints`，`WillLayoutSubviews`或`DidUpdateSubviews`事件可能会导致无限布局循环。
 - TvOS 10 中，调用在`RemoveGestureRecognizer`方法的`UIView`对象显式取消任何正在进行中的笔势识别器。
 - 显示的视图控制器现在可能会影响状态条的外观。
 - tvOS 10 要求开发人员调用`base.AwakeFromNib`时创建的子类`UIViewController`并重写`AwakeFromNib`方法。
 - 使用自定义应用程序`UIView`子类重写`LayoutSubviews`和更新之前，调用布局`base.LayoutSubviews`可能会触发 tvOS 10 中的无限布局循环。
 - 特定于方向的或 flippable 图像资产是翻转时分配给`UIButton`对象。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [什么是 tvOS 10 中的新增功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)

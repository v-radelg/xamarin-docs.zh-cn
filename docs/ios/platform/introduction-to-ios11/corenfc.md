---
title: 在 Xamarin.iOS 中的核心 NFC
description: 本文档介绍如何附近字段中使用 iOS 11 中引入的 Api 的 Xamarin.iOS 通信标记读取。
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 846B59D3-F66A-48F3-A78C-84217697194E
author: lobrien
ms.author: laobri
ms.date: 09/25/2017
ms.openlocfilehash: 6888f7147796d3c00752d10387c19d0d9f269cad
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61170818"
---
# <a name="core-nfc-in-xamarinios"></a>在 Xamarin.iOS 中的核心 NFC

_使用 iOS 11 读取近场通信 (NFC) 标记_

CoreNFC 是新框架，在 iOS 11 中提供访问权限_近场通信_(NFC) 单选读取来自应用程序中的标记。 它适用于 iPhone 7、 8、 8 Plus，和 X 7 Plus。

IOS 设备中的 NFC 标记读取器支持所有 NFC 标记类型 1 至 5 包含_NFC 数据交换格式_(NDEF) 信息。

有一些限制需要注意的：

- CoreNFC 仅支持读取 （未编写或格式设置） 的标记。
- 标记扫描必须由用户启动，并在 60 秒后的超时。
- 应用必须在前台，让扫描中可见。
- CoreNFC 仅了测试 （而不是在模拟器中） 的真实设备上。

此页介绍了使用 CoreNFC 所需的配置，并演示如何使用 API 通过["NFCTagReader"示例代码](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)。

## <a name="configuration"></a>配置

若要启用 CoreNFC，必须在项目中配置三个项：

- **Info.plist**隐私密钥。
- **Entitlements.plist**条目。
- 包含的预配配置文件**NFC 标记读取**功能。

### <a name="infoplist"></a>Info.plist

添加**NFCReaderUsageDescription**隐私密钥和扫描正在进行中时，向用户显示的文本。 使用适用于你的应用程序的消息 （例如，说明用途的扫描）：

```xml
<key>NFCReaderUsageDescription</key>
<string>NFC tag to read NDEF messages into the application</string>
```

### <a name="entitlementsplist"></a>Entitlements.plist

您的应用程序必须请求**附近字段通信标记读取**功能是使用以下键/值对中你**Entitlements.plist**:

```xml
<key>com.apple.developer.nfc.readersession.formats</key>
<array>
  <string>NDEF</string>
</array>
```

### <a name="provisioning-profile"></a>设置配置文件

创建一个新**应用程序 ID**并确保**NFC 标记读取**勾选了服务：

[![使用 NFC 标记读取所选的开发人员门户新的应用 ID 页](corenfc-images/app-services-nfc-sml.png)](corenfc-images/app-services-nfc.png#lightbox)

然后应为此应用 ID，创建新的预配配置文件，然后下载并将其安装在您的开发 mac。

## <a name="reading-a-tag"></a>读取标记

你的项目配置后，添加`using CoreNFC;`页首的文件并遵循以下三个步骤来实现 NFC 标记读取功能：

### <a name="1-implement-infcndefreadersessiondelegate"></a>1.实现 `INFCNdefReaderSessionDelegate`

此接口具有两种方法来实现：

- `DidDetect` – 当成功读取标记时调用。
- `DidInvalidate` – 当出现错误，或者达到 60 秒超时时调用。

#### <a name="diddetect"></a>DidDetect

在示例代码中，每个扫描生成的消息添加到表格视图：

```csharp
public void DidDetect(NFCNdefReaderSession session, NFCNdefMessage[] messages)
{
    foreach (NFCNdefMessage msg in messages)
    {  // adds the messages to a list view
        DetectedMessages.Add(msg);
    }
    DispatchQueue.MainQueue.DispatchAsync(() =>
    {
        this.TableView.ReloadData();
    });
}
```

可能会多次调用此方法 （和可能会在传递的消息数组） 如果该会话允许多个标记读数。 这使用第三个参数的设置`Start`方法 (中所述[第 2 步](#step2))。

#### <a name="didinvalidate"></a>DidInvalidate

多种原因可能失效：

- 扫描时出错。
- 应用程序保持在前台。
- 用户选择取消扫描。
- 扫描已取消应用程序。

下面的代码演示如何处理错误：

```csharp
public void DidInvalidate(NFCNdefReaderSession session, NSError error)
{
    var readerError = (NFCReaderError)(long)error.Code;
    if (readerError != NFCReaderError.ReaderSessionInvalidationErrorFirstNDEFTagRead &&
        readerError != NFCReaderError.ReaderSessionInvalidationErrorUserCanceled)
    {
      // some error handling
    }
}
```

后一个会话已失效，必须创建新的会话对象以再次扫描。

<a name="step2" />

### <a name="2-start-an-nfcndefreadersession"></a>2.启动 `NFCNdefReaderSession`

扫描应开始与用户请求，如按下按钮。
下面的代码创建并启动扫描的会话：

```csharp
Session = new NFCNdefReaderSession(this, null, true);
Session?.BeginSession();
```

为参数`NFCNdefReaderSession`构造函数如下所示：

- `delegate` – 的实现`INFCNdefReaderSessionDelegate`。 在示例代码中，委托在中实现表视图控制器，因此`this`用作委托参数。
- `queue` – 处理的回叫队列。 它可以是`null`，在这种情况下，一定要使用`DispatchQueue.MainQueue`更新用户界面控件 （如示例所示） 时。
- `invalidateAfterFirstRead` – 当`true`，扫描将在第一次成功扫描; 后停止时`false`扫描将继续，直到扫描被取消或达到 60 秒超时返回多个结果。


### <a name="3-cancel-the-scanning-session"></a>3.取消了扫描的会话

用户可取消通过用户界面中的系统提供的按钮的扫描会话：

![扫描时的取消按钮](corenfc-images/scan-cancel-sml.png)

应用程序可以通过调用以编程方式取消扫描`InvalidateSession`方法：

```csharp
Session.InvalidateSession();
```

在这两种情况下，该委托的`DidInvalidate`将调用方法。

## <a name="summary"></a>总结

CoreNFC 让你的应用从 NFC 标记读取数据。 它支持读取以不同的标记格式 （NDEF 类型 1 到 5），但不支持写入或格式设置。


## <a name="related-links"></a>相关链接

- [NFCTagReader （示例）](https://developer.xamarin.com/samples/monotouch/ios11/NFCTagReader/)
- [引入了核心 NFC (WWDC) （视频）](https://developer.apple.com/videos/play/wwdc2017/718/)

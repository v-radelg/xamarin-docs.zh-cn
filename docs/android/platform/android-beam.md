---
title: Android 无线发送
ms.prod: xamarin
ms.assetid: 4172A798-89EC-444D-BC0C-0A7DD67EF98C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/06/2017
ms.openlocfilehash: 9fcabc90875dda28ecdd5d94f1ca2f263ffe4886
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60954171"
---
# <a name="android-beam"></a>Android 无线发送

Android 无线发送是使应用程序可以通过在邻近的 NFC 共享信息的 Android 4.0 中引入的近场通信 (NFC) 技术。

[![说明两个设备中共享的信息较近的关系图](android-beam-images/androidbeam.png)](android-beam-images/androidbeam.png#lightbox)

Android 无线发送的工作原理是通过 NFC 推送消息，当两个设备在范围内时。 设备占用约 4 厘米彼此可以共享使用 Android 无线发送的数据。 一台设备上的活动创建一条消息，并指定一个活动 （或活动），它可以处理将其推送。 如果设备处于范围内指定的活动是在前台，Android 无线发送会将消息推送到另一台设备。 接收设备上包含消息数据调用意向。

Android 支持两种方法的设置与 Android 无线发送的消息：

-   `SetNdefPushMessage` -启动 Android 无线发送之前，应用程序可以调用 SetNdefPushMessage 指定 NdefMessage 推送通过 NFC 和将其推送的活动。 当一条消息不会更改应用程序正在使用中时，最好使用此机制。

-   `SetNdefPushMessageCallback` -当启动 Android 无线发送时，应用程序可以处理一个回调，以创建 NdefMessage。 此机制允许为消息创建可延迟到设备处于范围。 它支持基于应用程序中发生的情况，消息可能会有所不同的方案。


在任一情况下，将数据与 Android 无线发送，发送应用程序发送`NdefMessage`，在多个将数据打包`NdefRecords`。 让我们看看我们可以触发 Android 无线发送之前必须解决的关键点。 首先，我们将使用创建的回调样式`NdefMessage`。


## <a name="creating-a-message"></a>创建一条消息

我们可以注册使用回调`NfcAdapter`中的活动`OnCreate`方法。 例如，假设`NfcAdapter`名为`mNfcAdapter`声明为类变量在活动中，我们可以编写以下代码以创建将构造消息的回调：

```csharp
mNfcAdapter = NfcAdapter.GetDefaultAdapter (this);
mNfcAdapter.SetNdefPushMessageCallback (this, this);
```

实现的活动`NfcAdapter.ICreateNdefMessageCallback`，传递给`SetNdefPushMessageCallback`上述方法。 启动 Android 无线发送时，系统将调用`CreateNdefMessage`，从该活动可以构造`NdefMessage`，如下所示：

```csharp
public NdefMessage CreateNdefMessage (NfcEvent evt)
{
    DateTime time = DateTime.Now;
    var text = ("Beam me up!\n\n" + "Beam Time: " +
        time.ToString ("HH:mm:ss"));
    NdefMessage msg = new NdefMessage (
        new NdefRecord[]{ CreateMimeRecord (
            "application/com.example.android.beam",
            Encoding.UTF8.GetBytes (text)) });
        } };
    return msg;
}

public NdefRecord CreateMimeRecord (String mimeType, byte [] payload)
{
    byte [] mimeBytes = Encoding.UTF8.GetBytes (mimeType);
    NdefRecord mimeRecord = new NdefRecord (
        NdefRecord.TnfMimeMedia, mimeBytes, new byte [0], payload);
    return mimeRecord;
}
```


## <a name="receiving-a-message"></a>接收消息

在接收端，系统会调用与意向`ActionNdefDiscovered`操作，从中我们可以提取 NdefMessage，如下所示：

```csharp
IParcelable [] rawMsgs = intent.GetParcelableArrayExtra (NfcAdapter.ExtraNdefMessages);
NdefMessage msg = (NdefMessage) rawMsgs [0];
```

使用 Android 无线发送，下面的屏幕截图中所示的完整代码示例，请参阅[Android 无线发送演示](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)示例库中。

[![从 Android 无线发送演示的示例屏幕快照](android-beam-images/24.png)](android-beam-images/24.png#lightbox)



## <a name="related-links"></a>相关链接

- [Android 无线发送演示 （示例）](https://developer.xamarin.com/samples/monodroid/AndroidBeamDemo/)
- [引入 Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)

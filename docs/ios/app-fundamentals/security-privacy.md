---
title: iOS 安全和隐私功能
description: 本文档介绍安全和隐私功能的 Io，并讨论了如何使用 Xamarin.iOS。 它介绍了 iOS 10 和如何访问私有用户数据中所做的更新。
ms.prod: xamarin
ms.assetid: 718C8721-C359-4650-878A-D68E159A3F53
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 119fd478727a002622861c94462b93b75720a992
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400554"
---
# <a name="ios-security-and-privacy-features"></a>iOS 安全和隐私功能

_本文介绍如何使用安全和隐私的 iOS 和它们如何影响 Xamarin.iOS 应用程序中。_

Apple 已与安全和隐私 iOS 10 （及更高版本） 中存在多项增强的功能，可帮助开发人员提高他们的应用的安全性，并确保最终用户的隐私。 本文将介绍 Xamarin.iOS 应用程序中实现这些功能。
    
<a name="General-Enhancements" />

## <a name="general-enhancements"></a>常规增强功能

与安全和隐私已进行了以下常规更改 iOS 10 中：

- 常见数据安全体系结构 (CDSA) API 已弃用，并应替换为要生成非对称密钥的 SecKey API。
- 新`NSAllowsArbitraryLoadsInWebContent`键可以添加到应用程序**Info.plist**文件，并将允许网页时的应用程序的其余部分仍启用 Apple 传输安全 (ATS) 保护正确加载。 有关详细信息，请参阅我们[应用程序传输安全](~/ios/app-fundamentals/ats.md)文档。
- 由于新剪贴板中 iOS 10 和 macOS Sierra 允许用户设备之间进行复制和粘贴，该 API 已扩展为允许剪贴板限制为特定设备和加盖时间戳来给定时点会自动清除。 此外，命名的选不再保留，并且应替换为共享粘贴板容器。
- 对于所有 SSL/TLS 连接，RC4 对称密码现在默认处于禁用状态。 此外，安全传输 API 不再支持 SSLv3，建议开发人员停止尽可能快地使用 sha-1 和 3DES 加密方法。

<a name="Accessing-Private-User-Data" />

## <a name="accessing-private-user-data"></a>访问私有用户数据

必须以静态方式声明其意图，通过输入中的一个或多个隐私密钥来访问特定功能或用户信息 iOS 10 （或更高版本） 上运行的应用，其**Info.plist**向该应用程序为何希望获得的用户说明的文件访问权限。

> [!IMPORTANT]
> 没有提供当用户尝试访问受限制的功能或用户信息之一时，系统将以无提示方式终止所需的密钥的应用_不会出错_！ 如果应用程序启动时意外失败的 iOS 10 上，确保所需的所有**Info.plist**已指定。

以下隐私相关的密钥都是可用：

- **隐私-Apple Music 使用说明**(`NSAppleMusicUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的媒体库。
- **隐私-蓝牙外围设备使用说明**(`NSBluetoothPeripheralUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的设备上的蓝牙。
- **隐私-日历使用说明**(`NSCalendarsUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的日历。
- **隐私-照相机使用说明**(`NSCameraUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问设备的照相机。
- **隐私-联系人使用说明**(`NSContactsUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的联系人。
- **隐私-健康分享使用说明**(`NSHealthShareUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的运行状况数据。 有关详细信息，请参阅 Apple [HKHealthStore 类引用](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隐私-健康更新使用说明**(`NSHealthUpdateUsageDescription`)-允许开发人员介绍了为什么应用要编辑用户的运行状况数据。 有关详细信息，请参阅 Apple [HKHealthStore 类引用](https://developer.apple.com/reference/healthkit/hkhealthstore)。
- **隐私-HomeKit 使用说明**(`NSHomeKitUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的 HomeKit 配置数据。
- **隐私-位置始终使用说明**(`NSLocationAlwaysUsageDescription`)-允许开发人员介绍了为什么应用要始终有权访问用户的位置。
- [弃用]**隐私-位置使用说明**(`NSLocationUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户位置。 *注意：IOS 8 （和更高版本） 中已弃用此密钥。使用`NSLocationAlwaysUsageDescription`或`NSLocationWhenInUseUsageDescription`相反。*
- **隐私-位置时在使用情况说明**(`NSLocationWhenInUseUsageDescription`)-允许开发人员介绍了为什么应用要在运行时访问用户的位置。
- [弃用]**隐私-媒体库使用说明**-允许开发人员介绍了为什么应用程序需要访问对用户的媒体库。 *注意：IOS 8 （和更高版本） 中已弃用此密钥。使用`NSAppleMusicUsageDescription`相反。*
- **隐私-麦克风使用说明**(`NSMicrophoneUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问设备麦克风。
- **隐私-运动使用说明**(`NSMotionUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问设备的加速感应器。
- **隐私-照片库使用说明**(`NSPhotoLibraryUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的照片库。
- **隐私-提醒使用说明**(`NSRemindersUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的提醒。
- **隐私-Siri 使用说明**(`NSSiriUsageDescription`)-允许开发人员介绍了为什么应用要将用户数据发送到使用 Siri。
- **隐私-语音识别使用说明**(`NSSpeechRecognitionUsageDescription`)-允许开发人员介绍了为什么应用要将用户数据发送到 Apple 的语音识别服务器。
- **隐私-电视提供商使用说明**(`NSVideoSubscriberAccountUsageDescription`)-允许开发人员介绍了为什么应用程序需要访问用户的电视提供商帐户。

有关使用的详细信息**Info.plist**密钥，请参阅 Apple 的[信息属性列表键引用](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009248-SW1)。

<a name="Setting-Privacy-Keys" />

## <a name="setting-privacy-keys"></a>设置隐私密钥

需要在 iOS 10 （及更高版本） 上访问 HomeKit 的下面的示例，开发人员将需要添加`NSHomeKitUsageDescription`到应用程序的关键**Info.plist**文件，并提供应用程序需要访问用户的 HomeKit 为什么字符串声明数据库。 此字符串将显示在运行应用的第一个用户时间：

![警报示例 NSHomeKitUsageDescription](security-privacy-images/info01.png "示例 NSHomeKitUsageDescription 警报")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin.iOS for Visual Studio 中的当前不支持编辑**Info.plist**默认 iOS 中的隐私密钥清单编辑器。 而是需要使用通用 PList 编辑器，因此执行以下操作：

1. 右键单击**Info.plist**中的文件**解决方案资源管理器**，然后选择**打开方式...**.
2. 选择**泛型 PList 编辑器**从列表中的程序打开该文件，然后单击**确定**。

    ![选择泛型 PList 编辑器](security-privacy-images/InfoEditorSelectionVs.png "选择泛型 PList 编辑器")
3. 单击 **+** 编辑器将新条目添加到列表中的最后一行上的按钮。 这称为"自定义属性"，类型设置为`String`和空值。
4. 单击属性名称，并将显示一个下拉列表。
5. 从下拉列表中，选择隐私密钥 (如**隐私-HomeKit 使用说明**): 

    ![选择隐私密钥](security-privacy-images/InfoPListEditorSelectKey.png "选择隐私密钥")
6. 为什么应用要访问给定的功能或用户信息到值列输入描述： 

    ![输入描述](security-privacy-images/InfoPListSetValue.png "输入的说明")
7. 保存对文件所做的更改。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要设置的任何隐私密钥，请执行以下步骤：

1. 在“解决方案资源管理器”中，双击 **Info.plist** 文件，打开它进行编辑。
2. 在屏幕的底部，切换到**源**视图。
3. 添加一个新**条目**到列表。
4. 从下拉列表中，选择隐私密钥 (如**隐私-HomeKit 使用说明**): 

    ![选择隐私密钥](security-privacy-images/info02.png "选择隐私密钥")
5. 输入应用程序想要访问给定的功能或用户信息的原因的说明： 

    ![输入描述](security-privacy-images/info03.png "输入的说明")
6. 保存对文件所做的更改。

-----

> [!IMPORTANT]
> 在示例中上面给出，设置失败`NSHomeKitUsageDescription`中的键**Info.plist**文件将导致应用程序_以静默方式失败_（正在关闭系统会在运行时） 而无需在 iOS 10 （运行时错误或更高版本）。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了安全和隐私 Apple 已在 iOS 10 以及它们如何影响 Xamarin.iOS 应用程序中进行的更改。

## <a name="related-links"></a>相关链接

- [iOS 10 示例](https://developer.xamarin.com/samples/ios/iOS10/)

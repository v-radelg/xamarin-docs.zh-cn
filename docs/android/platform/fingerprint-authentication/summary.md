---
title: 指纹身份验证指南
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027430"
---
# <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

## <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

现在，我们已经了解了关于 Android 6.0 指纹身份验证的概念和 Api，接下来我们将讨论有关使用指纹 Api 的一些一般建议。

1. **使用 Android 支持库 V4 兼容性 api** &ndash; 这将通过从代码中删除 API 检查来简化应用程序代码，并允许应用程序以可能的最大设备为目标。
2. **提供指纹身份验证的替代**方法 &ndash; 指纹身份验证是应用程序对用户进行身份验证的一种非常简单的方法，但是，不能假定它将始终有效或可用。 指纹扫描器可能会失败，镜头可能会变得脏，用户可能未将设备配置为使用指纹身份验证，或者指纹自丢失。 此外，用户可能不希望对应用程序使用指纹身份验证。 由于这些原因，Android 应用程序应提供备用身份验证过程，例如用户名和密码。
3. **使用 google 的指纹图标**&ndash; 所有应用程序应使用 google 提供的同一指纹图标。 使用标准图标可以使 Android 用户轻松识别在应用中使用指纹身份验证的位置： 
    
    ![Android 指纹图标](summary-images/ic-fp-40px.png)
    
4. **通知用户**&ndash; 应用程序应向用户显示指纹扫描器处于活动状态并等待触摸或刷的通知。 

## <a name="summary"></a>总结

指纹身份验证是允许 Xamarin Android 应用程序快速验证用户的绝佳方法，使用户可以更轻松地与敏感功能（如应用内购买）交互。 本指南讨论了在 Xamarin Android 应用程序中合并 Android 6.0 指纹 API 所需的概念和代码。

首先，我们讨论了指纹 API 本身，`FingerprintManager` （和 `FingerprintManagerCompat`）。 我们检查了 `FingerprintManager.AuthenticationCallbacks` 抽象类必须如何由应用程序扩展，并用作指纹硬件和应用程序本身之间的媒介。 然后，我们已检查了如何使用 Java `Cipher` 对象验证指纹扫描器结果的完整性。 最后，我们通过说明如何在设备上注册指纹并使用**adb**在模拟器上模拟指纹刷，对测试进行了一些探讨。 

如果尚未执行此操作，应查看本指南附带的[示例应用程序](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)。 [指纹对话框示例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)已从 Java 移植到 Xamarin，还提供了另一个示例，说明如何向 android 应用程序添加指纹身份验证。

## <a name="related-links"></a>相关链接

- [指纹指南示例应用](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指纹对话示例](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [指纹图标](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)

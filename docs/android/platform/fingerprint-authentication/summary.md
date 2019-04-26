---
title: 指纹身份验证指南
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 535eabe07cb4f4d36e6a6f918b5717efcc99185d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023529"
---
# <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

## <a name="fingerprint-authentication-guidance"></a>指纹身份验证指南

现在，我们已了解概念和 Api 围绕 Android 6.0 指纹身份验证，让我们讨论的指纹 Api 使用一些常规建议。

1. **使用 Android 支持库 v4 兼容性 Api** &ndash;这将简化应用程序代码通过从代码中删除 API 检查，并允许应用程序以面向最可能的设备。
2. **提供指纹身份验证的替代方法**&ndash;指纹身份验证是一种很好、 快速的方法，应用程序用户进行身份验证，但是，它不能假定将始终运行或可用。 很可能，指纹扫描程序可能会失败、 可重用功能区可能变脏，用户可能未配置设备使用指纹身份验证或指纹以来已丢失。 还有可能用户可能不希望与您的应用程序使用指纹身份验证。 出于这些原因，Android 应用程序应提供用户名和密码等一个备用身份验证过程。
3. **使用 Google 的指纹图标**&ndash;所有应用程序应使用 Google 提供的相同指纹图标。 使用一个标准图标轻松 Android 用户，以便识别在应用中使用指纹身份验证的位置： 
    
    ![Android 指纹图标](summary-images/ic-fp-40px.png)
    
4. **通知用户**&ndash;应用程序应显示给用户的通知指纹扫描程序处于活动状态的某种类型和等待触摸或轻扫。 

## <a name="summary"></a>总结

指纹身份验证是允许在 Xamarin.Android 应用程序来快速验证用户，使用户更轻松地使用敏感功能进行交互，如应用内购买的好办法。 本指南介绍的概念和合并在 Xamarin.Android 应用程序中的 API 的 Android 6.0 指纹所需的代码。

首先，我们讨论了 API 的本身，指纹`FingerprintManager`(和`FingerprintManagerCompat`)。 我们探讨了如何将`FingerprintManager.AuthenticationCallbacks`必须由应用程序扩展抽象类，并将其用作指纹硬件和应用程序本身之间的中介。 然后介绍了如何验证指纹扫描程序结果使用 Java 的完整性`Cipher`对象。 最后，我们谈及有点测试通过描述如何注册设备上的指纹，并使用**adb**来模拟指纹轻扫的仿真程序上。 

如果尚未这样做，则应查看[示例应用程序](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)随附本指南。 [指纹对话框示例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)已经通过 Java 移植到 Xamarin.Android 和提供有关如何将指纹身份验证添加到 Android 应用程序的另一个示例。



## <a name="related-links"></a>相关链接

- [指纹指南示例应用](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [指纹对话框示例](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [指纹图标](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)

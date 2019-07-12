---
title: 哪个版本的 Xamarin.Android 添加了 Lollipop 支持？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: db0cb221ee18998fbb98449ae03c442c84bcfcd7
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829703"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>哪个版本的 Xamarin.Android 添加了 Lollipop 支持？

> [!NOTE]
> 本指南是最初编写 Android L 预览版。

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/)添加 Android L 预览版的支持。
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/)添加了 Android Lollipop 支持。

Xamarin 才主动支持 Xamarin 工具的当前稳定版本。 提供以下信息"作为-是"较旧版本的工具。 有关 Xamarin 版本的最新信息，请查看[此处](http://releases.xamarin.com/)。

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"缺少 android.jar API 级别 21"Android L 预览版

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

以下错误消息 （或类似） 可能会出现：

```cmd
Error 1 Could not find android.jar for API Level 21.
```

此消息表示未安装 Android SDK 平台的 API 级别 21。 可以将其安装在 Android SDK 管理器 (**工具 > 打开 Android SDK 管理器...** )，或更改 Xamarin.Android 项目以面向已安装的 API 版本。

有几个针对此问题的解决方法：

1. 更改你的项目，以便它面向 API 19 或更低。

2. 文件夹重命名你 android 21 android 21 为 android l。 （充其量，这应只用作临时性修补程序，并且它根本不工作很好地。）

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. 暂时降级回 Android API 级别 21"L"预览 [1]:

    1.  删除 **%LOCALAPPDATA%\\Android\\android sdk\\平台\\android 21** 
    2.  提取 [1] 到**c:\\用户\\&lt;用户名&gt;\\AppData\\本地\\Android\\android sdk\\平台**来创建**android-L**文件夹。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

以下错误消息 （或类似） 可能会出现：

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

这意味着未安装 Android SDK 平台的 API 级别 21。可以将其安装在 Android SDK 管理器 (工具 > SDK 管理器...)，或更改 Xamarin.Android 项目以面向已安装的 API 版本。

有几个针对此问题的解决方法：

1. 更改你的项目，以便它面向 API 19 或更低。

2. 文件夹重命名你 android 21 android 21 为 android l。 （充其量，这应只用作临时性修补程序，并且它根本不工作很好地。）

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. 暂时降级回 Android API 级别 21"L"预览 [1]:

    1.  删除 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  提取 [1] 到 **/Users/username/Library/Developer/Xamarin/android-sdk-macosx**来创建**android-L**文件夹。

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)

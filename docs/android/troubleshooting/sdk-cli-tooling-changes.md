---
title: 对 Android SDK 工具的更改
description: 更改 Android SDK 管理已安装的 API 级别和 Avd 的方式。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 5446f8e7bbdf2a5f625852bb61637be392bc6bc2
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523210"
---
# <a name="changes-to-the-android-sdk-tooling"></a>对 Android SDK 工具的更改

_更改 Android SDK 管理已安装的 API 级别和 Avd 的方式。_

## <a name="changes-to-android-sdk-tooling"></a>Android SDK 工具的更改

在适用于 Android 的最新版本的 SDK Tools 中, Google 已删除现有的 AVD 和 SDK 管理器, 以支持新的 CLI (命令行接口) 工具。 **Android**程序已删除, 并且 Visual Studio Tools for Xamarin Visual Studio for Mac 和更低版本的 Google GUI (图形用户界面) 管理器将不再 Android SDK Tools 的版本25.2.5。 例如, 尝试通过命令行使用**android**程序将导致错误消息, 如下所示:

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

以下部分介绍如何使用 Android SDK 25.3.0 和更高版本管理 Android SDK 和 Android 虚拟设备。

### <a name="ui-tools"></a>UI 工具

Visual Studio 和 Visual Studio for Mac 现在为不再使用的基于 Google GUI 的管理器提供 Xamarin 替换:

- 若要下载用于开发 Xamarin Android 应用程序所需 Android SDK 工具、平台和其他组件, 请使用[Xamarin Android SDK Manager](~/android/get-started/installation/android-sdk.md) , 而不是旧的 Google SDK 管理器。

- 若要创建和配置 Android 虚拟设备, 请使用[Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md) , 而不是使用旧版 Google 仿真器管理器。

这些工具在功能上等同于它们替代的 Google 基于 GUI 的管理器。

### <a name="cli-tools"></a>CLI 工具

或者, 您可以使用 CLI 工具来管理和更新您的模拟器, 并 Android SDK。 以下程序现在为 Android SDK 工具生成命令行界面:

#### <a name="sdkmanager"></a>sdkmanager

**添加于:** Android SDK Tools 25.2.3 (2016 年11月) 和更高版本。

Android SDK 的 "**工具"/"bin** " 文件夹中有一个名为 " **sdkmanager** " 的新程序。 此工具用于在命令行中维护 Android SDK。 有关使用此工具的详细信息, 请参阅[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**添加于:** Android SDK Tools 25.3.0 (三月, 2017) 及更高版本。

Android SDK 的 "**工具"/"bin** " 文件夹中有一个名为 " **avdmanager** " 的新程序。 此工具用于维护 Android Emulator 的 Avd。 有关使用此工具的详细信息, 请参阅[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降级

您可以通过从[Android 开发人员网站](https://developer.android.com/studio/index.html)安装 Android SDK 的以前版本来降级您的**Android SDK Tools**版本。

### <a name="using-the-old-gui"></a>使用旧的 GUI

你仍可以通过在**tools**文件夹内运行**android**程序来使用原始 GUI, 只要**Android SDK Tools**版本**25.2.5**或更低版本。


## <a name="related-links"></a>相关链接

- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)
- [了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)

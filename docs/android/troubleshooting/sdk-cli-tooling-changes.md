---
title: 对 Android SDK 工具的更改
description: 对 Android SDK 管理的已安装的 API 级别和 Avd 的方式的更改。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5AC61C00-0FF6-4C2D-80E7-D67A3EE30A5A
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: dbd3287e7c646be7fd969699eab685906a1c6c1a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093929"
---
# <a name="changes-to-the-android-sdk-tooling"></a>对 Android SDK 工具的更改

_对 Android SDK 管理的已安装的 API 级别和 Avd 的方式的更改。_

## <a name="changes-to-android-sdk-tooling"></a>对 Android SDK 工具的更改

在最新版本的 Android SDK Tools，Google 已删除的现有 AVD 和 SDK 管理器以支持新的 CLI （命令行接口） 工具。 **Android**程序已删除，并在 Visual Studio for Mac 和 Visual Studio Tools for Xamarin 的较旧版本的 Google GUI （图形用户界面） 管理器将不再有效的 Android SDK Tools 版本 25.2.5 过去。 例如，尝试使用**android**通过命令行程序将导致错误消息如下所示：

```shell
The "android" command is deprecated.
For manual SDK, AVD, and project management, please use Android Studio.
For command-line tools, use tools\bin\sdkmanager.bat
and tools\bin\avdmanager.bat
```

以下各节说明如何管理的 Android SDK 和 Android 虚拟设备使用 Android SDK 25.3.0 及更高版本。

### <a name="ui-tools"></a>用户界面工具

Visual Studio 和 Visual Studio for Mac 现提供 Xamarin 已停止使用基于 Google GUI 的管理器的替代项：

-   若要下载 Android SDK 工具、 平台和其他组件所需的开发 Xamarin.Android 应用程序，请使用[Xamarin Android SDK 管理器](~/android/get-started/installation/android-sdk.md)而不是旧版 Google SDK 管理器。

-   若要创建和配置 Android 虚拟设备，请使用[Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)而不是旧版 Google 仿真器管理器。

这些工具在功能上等效于基于 Google GUI 的管理器它们替换。

### <a name="cli-tools"></a>CLI 工具

或者，可以使用 CLI 工具来管理和更新你的仿真程序和 Android SDK。 以下程序现在构成 Android SDK 工具在命令行接口：

#### <a name="sdkmanager"></a>sdkmanager

**在中添加：** Android SDK Tools 25.2.3 (2016 年 11 月) 和更高版本。

还有一个名为的新程序**sdkmanager**中 **/bin** Android SDK 文件夹。 使用此工具来维护 Android SDK 在命令行。 有关使用此工具的详细信息，请参阅[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)。

#### <a name="avdmanager"></a>avdmanager

**在中添加：** Android SDK Tools 25.3.0 (2017 年 3 月) 和更高版本。

还有一个名为的新程序**avdmanager**中 **/bin** Android SDK 文件夹。 此工具用于为 Android 仿真程序中维护 Avd。 有关使用此工具的详细信息，请参阅[avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)。

### <a name="downgrading"></a>降级

你可以降级你**Android SDK Tools**通过安装以前版本的 Android sdk 版本[Android 开发人员网站](https://developer.android.com/studio/index.html)。

### <a name="using-the-old-gui"></a>使用旧的 GUI

您仍然可以通过运行使用原始 GUI **android**程序内你**工具**文件夹，只要你位于**Android SDK Tools**版本**25.2.5**或更低。


## <a name="related-links"></a>相关链接

- [Android SDK 安装](~/android/get-started/installation/android-sdk.md)
- [Android 设备管理器](~/android/get-started/installation/android-emulator/device-manager.md)
- [了解 Android API 级别](~/android/app-fundamentals/android-api-levels.md)
- [SDK Tools 发行说明 (Google)](https://developer.android.com/studio/releases/sdk-tools.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)

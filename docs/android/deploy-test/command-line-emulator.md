---
title: 命令行仿真器
ms.prod: xamarin
ms.assetid: E592AA32-5E83-B7E5-1753-12416551B23C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 11/05/2018
ms.openlocfilehash: 35e10ffc20e075e0245c7e42f7fd0aff24de4abb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021583"
---
# <a name="command-line-emulator"></a>命令行仿真器

## <a name="running-the-android-emulator-from-the-command-line"></a>从命令行运行 Android 仿真器

要从命令行运行 Android 仿真器，可以使用 Android SDK 提供的“仿真器”工具。 此工具可用于从 OS X 上的终端或 Windows 计算机上的命令提示符运行仿真器。

要启动特定的 Android 仿真器，请从 Android SDK 位置的 tools 目录（例如 C:\android-sdk-windows\tools）运行以下命令：

在 Windows 上

```cmd
emulator.exe -avd NameOfYourEmulator -partition-size 512
```

在 macOS 上

```bash
./emulator -avd NameOfYourEmulator -partition-size 512
```

需要分区大小的原因是为了让仿真器具有足够的空间，以在仿真器上安装 Xamarin.Android 平台，因为默认情况下仿真器的大小很小。

有关额外参数的详细信息，可访问此处的 Android 站点 - [https://developer.android.com/studio/run/emulator-commandline](https://developer.android.com/studio/run/emulator-commandline)

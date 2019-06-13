---
title: Xamarin 和 iOS 13 故障排除
description: 本部分包含 Xamarin 功能与 iOS 13 相关的故障排除的提示。
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: 99fd7e41e1521c6a9254d286bf989281658ccf24
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039784"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>故障排除提示适用于 iOS 13 和 Xamarin.iOS

![预览版功能](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>停止更新到 Xcode 11 启动模拟器

为更新后**Xcode 11 beta 1**每次启动模拟器会引发以下异常，并且模拟器未启动。 与所有模拟器都发生这种情况。

### <a name="exception"></a>例外

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>解决方法

直到出现[修复](https://github.com/xamarin/xamarin-macios/issues/6216)，可以执行以下步骤来重新安装旧的模拟器框架，以允许开发人员能够继续工作：

> [!NOTE]
> 这些步骤假定你有两个 Xcode 应用程序：
> - **Xcode11 beta1.app** – beta 版本并不适用于模拟器和 Visual Studio for mac。
> - **Xcode102.app** – 稳定版本的 Xcode 10。 您可能还会调用**Xcode.app**。
>
> 更改下面的命令行示例，根据你的配置。

1. 确保 Xcode 11 xcode-select-通过选择各项：

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. 运行时，如果需要安装程序工具第一次。

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. 删除以下框架：

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. 切换回旧的 Xcode 版本

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. 重新运行的第一个启动工具_旧_刚才所选的 Xcode 版本

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

执行这些步骤后，您应能够再次使用 iOS 模拟器。
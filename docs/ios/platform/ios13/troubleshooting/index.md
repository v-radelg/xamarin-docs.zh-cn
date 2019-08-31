---
title: Xamarin 和 iOS 13 故障排除
description: 本部分包含与 iOS 13 相关的 Xamarin 功能的疑难解答提示。
ms.prod: xamarin
ms.assetid: 00DE8C33-1407-45C0-A0C7-32AF1E490034
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/12/2019
ms.openlocfilehash: d2a2146a0b7345475e2eb93d52fb02387c833224
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200048"
---
# <a name="troubleshooting-tips-for-ios-13-and-xamarinios"></a>IOS 13 和 Xamarin 的故障排除提示

![预览版功能](~/media/shared/preview.png)

## <a name="updating-to-xcode-11-stops-the-simulator-from-launching"></a>更新到 Xcode 11 会使模拟器停止启动

在每次启动模拟器时更新到**Xcode 11 beta 1**后, 将引发以下异常, 且模拟器不会启动。 所有模拟器都会发生这种情况。

### <a name="exception"></a>例外

`Foundation.ObjCException: NSInvalidArgumentException: -[SimDevice registerNotificationHandler:]: unrecognized selector sent to instance 0x7ffbf5d1e110`

### <a name="workaround"></a>解决方法

在[修复](https://github.com/xamarin/xamarin-macios/issues/6216)之前, 可以遵循以下步骤重新安装旧的模拟器框架, 以允许开发人员继续工作:

> [!NOTE]
> 这些步骤假定你有两个 Xcode 应用程序:
> - **Xcode11-beta1** –无法与模拟器和 Visual Studio for Mac 一起使用的 beta 版本。
> - **Xcode102** – Xcode 10 的稳定版本。 您也可以调用**Xcode**。
>
> 根据你的配置, 更改下面的命令行示例。

1. 请确保已通过 Xcode 选择 Xcode 11:

   `sudo xcode-select -s /Applications/Xcode11-beta1.app/Contents/Developer/`

2. 如果需要, 则首次运行安装工具。

    `/Applications/Xcode11-beta1.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

3. 删除以下框架:

    `sudo rm -Rf  /Library/Developer/PrivateFrameworks/CoreSimulator.framework/Versions/*`

4. 切换回旧的 Xcode 版本

   `sudo xcode-select -s /Applications/Xcode102.app/Contents/Developer/`

5. 为刚选择的_旧_Xcode 版本重新运行第一个启动工具

   `/Applications/Xcode102.app/Contents/Developer/usr/bin/xcodebuild -runFirstLaunch`

执行这些步骤后, 你应该能够再次使用 iOS 模拟器。

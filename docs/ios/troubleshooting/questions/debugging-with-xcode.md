---
title: 使用 Xcode 调试 Xamarin.iOS 应用程序
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5FDDEDB3-AEB9-4D9C-9F7B-FEFAA9AF0031
ms.technology: xamarin-ios
author: migueldeicaza
ms.author: miguel
ms.date: 02/22/2018
ms.openlocfilehash: e0127d4b24236d350e5fa967110316544c320d0f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61422433"
---
# <a name="debugging-xamarinios-apps-with-xcode"></a>使用 Xcode 调试 Xamarin.iOS 应用程序

可能想要使用 Xcode 调试 Xamarin.iOS 应用程序的某些部分的方案。 尽管你将无法调试.NET 代码中的，仍将能够调试本机代码，并在 Xcode 中使用某些本机可视化工具。

## <a name="walkthrough"></a>演练

虽然没有在 Visual Studio for Mac 的 Xcode 调试不内置支持，但可以使用以下步骤来实现此目的：

1. 使用与你的 Xamarin 应用中的一个相同的捆绑 ID 创建 Xcode iOS 应用。
   
    - 可以通过打开查找 Xamarin.iOS 项目的捆绑包标识符**Info.plist**文件：

        ![编辑 Info.plist](debugging-with-xcode-images/vsmac-infoplist.png "编辑 Info.list")

    - 在 Xcode 中，您创建项目时或在项目中选择你的目标设置的捆绑标识符：

        ![在 Xcode 中设置的捆绑包标识符](debugging-with-xcode-images/xcode-bundle.png "在 Xcode 中设置的捆绑标识符")

2. 更改要等待启动而不是自动启动该应用的 Xcode 项目：

    - 打开**编辑方案面板**通过选择**产品 > 方案 > 编辑方案**也可以使用**cmd⌘ + <** 键盘快捷方式。

    - 选择**运行**方案，并在右侧面板你应会看到**启动**选项。 选择**等待可执行文件以启动**然后单击**关闭**。

        ![等待可执行文件启动](debugging-with-xcode-images/xcode-schemes.png "等待可执行文件启动")

3. 运行 Xcode 项目。

    这将在设备上安装虚拟 Xcode 应用，但不是会启动。

4. 运行 Xamarin 应用。

    启动后，Xcode 应附加到 Xamarin 应用程序。

### <a name="caveats"></a>注意事项

您可能需要每次启动时向 Xamarin.iOS 应用程序进行少量更改。 否则，Visual Studio for Mac 将检测应用程序不需要生成*和*已安装，它不会通过 Xcode 虚拟应用程序重新安装它。

## <a name="alternative---using-lldb"></a>替代项-使用 lldb

如果你熟悉使用**lldb**从命令行中，没有太多更简单的解决方案。

在外壳程序中，键入以下命令：

```bash
touch ~/.mtouch-launch-with-lldb
```

将中获取相关说明**应用程序输出**窗口中在要执行的操作，但基本上，当运行应用程序中，你将能够使用**lldb**从命令行来调试应用程序。

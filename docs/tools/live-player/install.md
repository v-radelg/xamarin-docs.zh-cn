---
title: Xamarin Live Player 的 Visual Studio 配置
description: 本文档介绍如何使用 Xamarin Live Player 对正在运行的应用程序进行实时编辑。
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: lobrien
ms.author: laobri
ms.date: 06/13/2019
ms.openlocfilehash: a29a637526c2829b44ae89d505dac37a648dee77
ms.sourcegitcommit: 93b1e2255d59c8ca6674485938f26bd425740dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67157739"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Xamarin Live Player 的 Visual Studio 配置

![预览版功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 预览已结束。 应用程序不再可用。 为客户继续使用 Visual Studio 2017，可以使用预览提供了下面的说明。

> [!TIP]
> 可以使用[XAML 预览程序](~/xamarin-forms/xaml/xaml-previewer/index.md)在 Visual Studio 2019 或 Visual Studio for Mac 以查看您的屏幕设计，如编辑它们。

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

在设备上，必须已具有 Xamarin Live Player 应用。 不再可供下载。

1. 打开**Visual Studio 2017**。
2. 转到**工具 > 选项...** ，然后选择**Xamarin > 其他**选项卡。
3. 刻度线**启用 Xamarin Live Player**:

    ![选中选项窗口中的启用 Xamarin Live Player 框](install-images/vs2017-options.png)

4. 创建或打开 Xamarin 项目 (或[示例](~/tools/live-player/samples.md))。
5. 选择**Live Player**设备列表中：

    ![设备列表包括 Xamarin Live Player 选项](install-images/devices-empty-windows.png)

    - 如果已具有配对设备，它将作为一个选项可用。
    - 否则系统将提示进行配对时所需的设备。

6. 按**运行**按钮或选择下列任一选项从**运行**或右键单击菜单：

    - **启动但不调试**– 你可以编辑该应用程序并在设备发生的更改，请参阅 （如进行更改并保存该文件，应用程序重新启动）。
    - **开始调试**– 你可以设置断点并检查变量，但不能编辑代码。

    或者，选择**工具 > Xamarin Live Player > 实时运行当前视图**，从中可以编辑该应用程序并在设备发生的更改，请参阅。 （而不是应用程序的主屏幕） 显示当前视图。

7. 如果已配对设备和设备上运行 Xamarin Live Player 应用，将立即执行的代码 ！

    如果配对设备没有，则将收到有关如何对设备的显示 QR 码：

    ![对设备窗口](install-images/manage-empty-windows.png)

    如果设备不能连接的配对，则可能会出现错误。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

在设备上，必须已具有 Xamarin Live Player 应用。 不再可供下载。

1. 打开**Visual Studio for Mac**。
2. 转到**Visual Studio > 首选项...** ，然后选择**项目 > Xamarin Live Player （预览）** 选项卡。
3. 刻度线**启用 Xamarin Live Player**:

    [![选中选项窗口中的启用 Xamarin Live Player 框](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. 创建或打开 Xamarin 项目 (或[示例](~/tools/live-player/samples.md))。
5. 选择**Live Player**设备列表中。

    ![设备列表包括 Xamarin Live Player 选项](install-images/devices.png)

    - 如果已具有配对设备，它将作为一个选项可用。
    - 否则系统将提示进行配对时所需的设备。
    - 选择**Xamarin Live Player 设备...** 来管理你想要使用 Xamarin Live Player 设备。

6. 按**运行**按钮或选择下列任一选项从**运行**或右键单击菜单：

    ![运行菜单选项](install-images/run-menu.png)

    - **启动但不调试**– 你可以编辑该应用程序并在设备发生的更改，请参阅 （如进行更改并保存该文件，应用程序重新启动）。
    - **开始调试**– 你可以设置断点并检查变量，但不能编辑代码。
    - **实时运行当前视图**– 你可以编辑该应用程序并在设备发生的更改，请参阅。 （而不是应用程序的主屏幕） 显示当前视图。

7. 如果已配对设备和设备上运行 Xamarin Live Player 应用，将立即执行的代码 ！

    如果配对设备没有，则将收到有关如何对设备的显示 QR 码：

    ![对设备窗口](install-images/manage-empty.png)

    如果设备不能连接的配对，则将出现错误：

    ![无法连接到设备的错误消息](install-images/error-cannot-connect.png)

-----

如果遇到任何问题或无法连接，请参阅[限制和故障排除](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相关链接

- [疑难解答](~/tools/live-player/troubleshooting.md)

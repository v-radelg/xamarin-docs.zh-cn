---
title: Xamarin Live Player Visual Studio 配置
description: 本文档介绍如何使用 Xamarin Live Player 对正在运行的应用程序进行实时编辑。
ms.prod: xamarin
ms.assetid: 5DDF9203-8826-4B04-93F5-B8D07EDE3873
author: conceptdev
ms.author: crdun
ms.date: 06/13/2019
ms.openlocfilehash: 94f1d36bf97aab7eabb57e6f2712c9850b390ab1
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290487"
---
# <a name="xamarin-live-player-visual-studio-configuration"></a>Xamarin Live Player Visual Studio 配置

![预览版功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 预览已结束。 应用不再可用。 以下说明适用于继续使用 Visual Studio 2017 预览的客户。

> [!TIP]
> 你可以在 Visual Studio 2019 或 Visual Studio for Mac 中使用[XAML 预览](~/xamarin-forms/xaml/xaml-previewer/index.md)器来查看你编辑的屏幕设计。

# <a name="visual-studio-2017tabwindows"></a>[Visual Studio 2017](#tab/windows)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

你的设备上必须已有 Xamarin Live Player 应用。 它不再可用于下载。

1. 打开**Visual Studio 2017**。
2. 中转到 "**工具" > 选项 ...** "，然后选择" **Xamarin "> 其他**选项卡。
3. 勾选标记**Xamarin Live Player**：

    ![选中 "选项" 窗口中的 "启用 Xamarin Live Player" 框](install-images/vs2017-options.png)

4. 创建或打开 Xamarin 项目（或[示例](~/tools/live-player/samples.md)）。
5. 在设备列表中选择 " **Live Player** "：

    ![设备列表包括 Xamarin Live Player 选项](install-images/devices-empty-windows.png)

    - 如果已配对某个设备，则该设备将作为一个选项提供。
    - 否则，系统会提示你在需要时将设备配对。

6. 按 "**运行**" 按钮，或者从 "**运行**" 或右键单击菜单中选择以下选项之一：

    - **启动但不调试**–您可以编辑应用程序并查看设备上发生的更改（应用程序在进行更改和保存文件时重新启动）。
    - **开始调试**–可以设置断点和检查变量，但不能编辑代码。

    或者，选择 "**工具" > Xamarin Live Player > "实时运行" "当前视图**"，这允许编辑应用并查看设备上发生的更改。 显示当前视图（而不是应用程序的主屏幕）。

7. 如果设备已配对并且 Xamarin Live Player 应用正在设备上运行，则代码将立即执行！

    如果未配对任何设备，则会出现一个 QR 码，其中包含有关如何配对设备的说明：

    ![配对设备窗口](install-images/manage-empty-windows.png)

    如果无法联系设备进行配对，则可能会出现错误。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="using-xamarin-live-player"></a>使用 Xamarin Live Player

你的设备上必须已有 Xamarin Live Player 应用。 它不再可用于下载。

1. 打开**Visual Studio for Mac**。
2. 切换到 " **Visual Studio > 首选项 ...** "，然后选择 "**项目" > Xamarin Live Player （预览）** "选项卡。
3. 勾选标记**Xamarin Live Player**：

    [![选中 "选项" 窗口中的 "启用 Xamarin Live Player" 框](install-images/vsmac-options-sml.png)](install-images/vsmac-options.png#lightbox)

4. 创建或打开 Xamarin 项目（或[示例](~/tools/live-player/samples.md)）。
5. 在设备列表中选择 " **Live Player** "。

    ![设备列表包括 Xamarin Live Player 选项](install-images/devices.png)

    - 如果已配对某个设备，则该设备将作为一个选项提供。
    - 否则，系统会提示你在需要时将设备配对。
    - 选择 " **Xamarin Live Player 设备 ...** " 以管理要用于 Xamarin Live Player 的设备。

6. 按 "**运行**" 按钮，或者从 "**运行**" 或右键单击菜单中选择以下选项之一：

    ![运行菜单选项](install-images/run-menu.png)

    - **启动但不调试**–您可以编辑应用程序并查看设备上发生的更改（应用程序在进行更改和保存文件时重新启动）。
    - **开始调试**–可以设置断点和检查变量，但不能编辑代码。
    - **实时运行当前视图**–可以编辑应用并查看设备上发生的更改。 显示当前视图（而不是应用程序的主屏幕）。

7. 如果设备已配对并且 Xamarin Live Player 应用正在设备上运行，则代码将立即执行！

    如果未配对任何设备，则会出现一个 QR 码，其中包含有关如何配对设备的说明：

    ![配对设备窗口](install-images/manage-empty.png)

    如果无法联系设备进行配对，则会出现错误：

    ![无法连接到设备错误消息](install-images/error-cannot-connect.png)

-----

如果遇到任何问题或无法连接，请参阅[限制和故障排除](~/tools/live-player/troubleshooting.md)。

## <a name="related-links"></a>相关链接

- [疑难解答](~/tools/live-player/troubleshooting.md)

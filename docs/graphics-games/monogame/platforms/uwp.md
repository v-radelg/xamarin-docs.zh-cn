---
title: 创建 MonoGame UWP 项目
description: MonoGame 可以用于创建通用 Windows 平台、 面向多个设备使用一个基本代码和一组内容的游戏和应用程序。
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 7db73759cb4a1b1a8d7fe40426b03a163c3ebdc4
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831063"
---
# <a name="creating-a-monogame-uwp-project"></a>创建 MonoGame UWP 项目

_MonoGame 可以用于创建通用 Windows 平台、 面向多个设备使用一个基本代码和一组内容的游戏和应用程序。_

本演练介绍 MonoGame 通用 Windows 平台 (UWP) 项目的创建和加载的内容。 UWP 应用可以在所有 Windows 10 设备，包括台式计算机、 平板电脑、 Windows Phone 和 Xbox One 上运行。

本演练将创建一个空的项目，其中显示*青蓝色*背景 （XNA 应用程序的传统的背景色）。

## <a name="requirements"></a>要求

开发 MonoGame UWP 应用需要：

- Windows 10 操作系统
- 任何版本的 Visual Studio 2017
- Windows 10 开发人员工具
- 设置设备到开发人员模式
- [用于 Visual Studio MonoGame 3.7.1](http://community.monogame.net/t/monogame-3-7-1-release/11173)或更高版本

有关详细信息，请参阅此[页上设置 Windows 10 UWP 开发](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)。

可在 Xbox One 的游戏开发零售 Xbox One 硬件上。 开发 PC 和 Xbox One 上需要其他软件。 配置 Xbox One 的游戏开发的信息，请参阅此页上[设置 Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index)。

## <a name="creating-an-empty-template"></a>创建一个空的模板

一旦安装了所有必需的资源和 Windows 10 计算机上启用了开发人员模式下，我们可以创建新 MonoGame 项目使用 Visual Studio 通过执行以下步骤：

1. 选择**文件** > **新** > **项目...**
1. 选择**安装** > **模板** > **Visual C#**   >  **MonoGame**类别：

    ![](uwp-images/image1.png "MonoGame 类别")

1. 选择**MonoGame Windows 10 通用项目**选项：

    ![](uwp-images/image2.png "选择 MonoGame Windows 10 通用项目选项")

1. 输入新项目的名称，然后单击**确定**。
如果 Visual Studio 中单击确定后显示任何错误，请验证安装了 Windows 10 工具和在设备处于开发人员模式。

完成 Visual Studio 创建模板后，我们可以运行它以查看运行的空项目：

![](uwp-images/image3.png "完成 Visual Studio 创建模板后，运行它以查看运行的空项目")

角中的数字提供诊断信息。 此信息可通过删除中的代码中移除`App.xaml.cs`中`DEBUG`块中`OnLaunched`方法：


```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>在 Xbox One 上运行

UWP 项目可以从同一个项目部署到任意 Windows 10 设备。 设置 Windows 10 开发计算机和 Xbox One 之后, 可通过切换到远程计算机的目标，然后输入 Xbox One 的 IP 地址部署 UWP 应用：

![](uwp-images/remote.png "可以通过切换到远程计算机的目标并进入 Xbox 的 IP 地址部署 UWP 应用")

Xbox One 上的白色边框表示电视的非安全区域。 有关详细信息，请参阅[安全区域部分](#safe-area-on-xbox-one)。

![](uwp-images/safearea.png "Xbox One 上的白色边框所表示的电视的非安全区域")

### <a name="safe-area-on-xbox-one"></a>在 Xbox One 上的安全区域

开发适用于控制台游戏需要考虑为中心的屏幕应包含所有关键的视觉对象 （如 UI 或 HUD） 中的某个区域的安全区域。 安全区域之外的区域不是保证在所有电视上可见，因此，放置在此区域中的视觉对象可能会部分或完全不可见一些显示器上。

适用于 Xbox One MonoGame 模板会考虑安全区域，并将其呈现为白色边框。 此区域也会反映在运行时在游戏中`Window.ClientBounds`属性在 Visual Studio 中监视窗口此图中所示。 请注意，客户端边界的高度是 1016，尽管 1920 x 1080 显示分辨率：

![](uwp-images/clientbounds.png "请注意，客户端边界的高度是 1016，尽管 1920 x 1080 显示分辨率")

## <a name="referencing-content-in-uwp-projects"></a>在 UWP 项目中引用内容

可以引用 MonoGame 项目中的内容，直接从文件或通过[MonoGame 内容管道](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。 小型游戏项目可能受益于从文件加载的简单性。 较大的项目将受益于使用内容管道以优化内容，以减小大小并加载时间。 与 XNA Xbox 360 上不同`System.IO.File`类是 Xbox 一个 UWP 应用上可用。

正在加载内容的管道的内容的详细信息，请参阅[内容管道指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

### <a name="loading-content-from-file"></a>从文件加载内容

与不同的是 iOS 和 Android，UWP 项目可以引用相对于可执行文件的文件。 简单的游戏可以使用此技术负载内容而无需修改并生成内容管道项目。

若要加载`Texture2D`文件中：

1. 将.png 文件添加到 UWP 项目中的内容文件夹。 将内容添加到 Content 文件夹是 XNA 和 MonoGame 中的约定。
1. 右键单击新添加 PNG，然后选择属性。
1. 更改**复制到输出目录**到**如果较新则复制**。
1. 将以下代码添加到您的游戏的 Initialize 方法，以加载`Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

有关使用的详细信息`Texture2D`，请参阅[MonoGame 指南对简介](~/graphics-games/monogame/introduction/index.md)。

## <a name="summary"></a>总结

本指南介绍如何加载文件时创建新的 UWP 项目和特定于 UWP 的注意事项。 有兴趣创建完整的 UWP 游戏开发人员可以阅读更多有关在 MonoGame [MonoGame 指南简介](~/graphics-games/monogame/introduction/index.md)。

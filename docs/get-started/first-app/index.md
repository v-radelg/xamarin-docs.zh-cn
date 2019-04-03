---
title: 生成第一个 Xamarin.Forms 应用
description: 视频指南介绍如何在 Visual Studio 中生成你的第一个 Xamarin.Forms 应用程序。
zone_pivot_groups: platform-dev16
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 04/02/2019
ms.openlocfilehash: 0031cb7fb46cf5ad35872963fd3c3def0a2ae9a6
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855297"
---
# <a name="build-your-first-xamarinforms-app"></a>生成第一个 Xamarin.Forms 应用

_观看此视频，跟着介绍一起操作以使用 Xamarin.Forms 创建第一个移动应用。_

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Windows 分步说明

[![Download 示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

请按照以下步骤以及上面的视频操作：

1. 选择**文件 > 新建 > 项目...** 或按**创建新项目...** 按钮：

    [![C创建一个新的项目](images/win-2019/01-sml.png)](images/win-2019/01.png#lightbox)

2. 搜索"Xamarin"或选择**Mobile**从**项目类型**菜单。 选择**移动应用 (Xamarin.Forms)** 项目类型：

    [![F对于 Xamarin 项目 ilter](images/win-2019/02-sml.png)](images/win-2019/02.png#lightbox)

3. 选择项目名称&ndash;该示例使用"AwesomeApp":

    [![C选择项目名称](images/win-2019/03-sml.png)](images/win-2019/03.png#lightbox)

4. 单击**黑色**项目类型，并确保**Android**并**iOS**选择：

    [![Android 和 iOS，通过使用.NET Standard](images/win-2019/04-sml.png)](images/win-2019/04.png#lightbox)

5. 等到 NuGet 包还原（状态栏中将出现“还原已完成”消息）。

6. 按调试按钮（或“调试”>“开始调试”菜单项）启动 Android Emulator。

7. 编辑 MainPage.xaml，在 `</StackLayout>` 结束之前添加此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

8. 编辑 MainPage.xaml.cs，将此代码添加到类的末尾：

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

9. 调试 Android 上的应用：

    ![Android 应用](images/win/07-sml.png)

    > [!TIP]
    > 可使用联网的 Mac 计算机从 Visual Studio 生成和调试 iOS 应用。 有关详细信息，请参阅[安装说明](~/ios/get-started/installation/windows/index.md)。

::: zone-end
::: zone pivot="win-vs2017"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Windows 分步说明

[![Download 示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

请按照以下步骤以及上面的视频操作：

1. 选择“文件”>“新建”>“项目...”或按“创建新项目...”按钮，然后选择“Visual C#”>“跨平台”>“移动应用(Xamarin.Forms)”：

    [![Mobile 应用 (Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. 请确保选中“Android”和“iOS”且勾选了“.NET Standard”代码共享策略：

    [![Android 和 iOS，通过使用.NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. 等到 NuGet 包还原（状态栏中将出现“还原已完成”消息）。

4. 按调试按钮（或“调试”>“开始调试”菜单项）启动 Android Emulator。

5. 编辑 MainPage.xaml，在 `</StackLayout>` 结束之前添加此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Button_Clicked" />
    ```

6. 编辑 MainPage.xaml.cs，将此代码添加到类的末尾：

    ```csharp
    int count = 0;
    void Button_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. 调试 Android 上的应用：

    ![Android 应用](images/win/07-sml.png)

    > [!TIP]
    > 可使用联网的 Mac 计算机从 Visual Studio 生成和调试 iOS 应用。 有关详细信息，请参阅[安装说明](~/ios/get-started/installation/windows/index.md)。

::: zone-end
::: zone pivot="macos"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-iOS--Android-App-in-Visual-Studio-for-Mac/player]

## <a name="step-by-step-instructions-for-mac"></a>Mac 分步说明

[![Download 示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)

请按照以下步骤以及上面的视频操作：

1. 选择“文件”>“新建解决方案...”或按“新建项目...”按钮，然后选择“多平台”>“应用”>“空白窗体应用”：

    [![Blank 窗体应用程序](images/01-sml.png)](images/01.png#lightbox)

2. 请确保选中“Android”和“iOS”且勾选了“.NET Standard”代码共享策略：

    [![Android 和 iOS，通过使用.NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. 右键单击解决方案，还原 NuGet 包：

    ![Android 应用](images/03-sml.png)

4. 按调试按钮（或“运行”>“开始调试”）启动 Android Emulator。

5. 编辑 MainPage.xaml，在 `</StackLayout>` 结束之前添加此 XAML：

    ```xaml
    <Button Text="Click Me" Clicked="Handle_Clicked" />
    ```

6. 编辑 MainPage.xaml.cs，将此代码添加到类的末尾：

    ```csharp
    int count = 0;
    void Handle_Clicked(object sender, System.EventArgs e)
    {
        count++;
        ((Button)sender).Text = $"You clicked {count} times.";
    }
    ```

7. 调试 Android 上的应用：

    ![Android 应用](images/07-sml.png)

8. 右键单击，将 iOS 设置为“启动项目”：

    [![S设置启动项目到 iOS](images/08-sml.png)](images/08.png#lightbox)

9. 调试 iOS 上的应用：

    ![iOS 应用](images/09-sml.png)

::: zone-end

可从[示例库](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)下载或在 [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp) 上查看完整代码。

## <a name="next-steps"></a>后续步骤

- [单一页快速入门](~/get-started/quickstarts/single-page.md)&ndash;构建功能更强的应用程序。
- [Xamarin.Forms 示例](~/xamarin-forms/samples/index.yml) &ndash; 下载并运行代码示例和示例应用。
- [创建移动应用电子书](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; 深入介绍 Xamarin.Forms 开发的章节（PDF 格式），包括数百个其他示例。

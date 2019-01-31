---
title: 生成第一个 Xamarin.Forms 应用
description: 视频指南介绍如何在 Visual Studio 中生成你的第一个 Xamarin.Forms 应用程序。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 72B6AF82-4D98-47E5-AB54-0A35B3253468
ms.technology: xamarin-forms
ms.custom: video
author: conceptdev
ms.author: crdun
ms.date: 09/24/2018
ms.openlocfilehash: d89488e3f6e42f84fc9519eedaa38c99b90ae068
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55292816"
---
# <a name="build-your-first-xamarinforms-app"></a>生成第一个 Xamarin.Forms 应用

观看此视频，然后按照视频中的步骤使用 Xamarin.Forms 创建第一个移动应用。

::: zone pivot="windows"

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Building-Your-First-Android--iOS-App-in-Visual-Studio-2017/player]

## <a name="step-by-step-instructions-for-windows"></a>Windows 分步说明

请按照以下步骤以及上面的视频操作：

1. 选择“文件”>“新建”>“项目...”或按“创建新项目...”按钮，然后选择“Visual C#”>“跨平台”>“移动应用(Xamarin.Forms)”：

    [![移动应用(Xamarin.Forms)](images/win/01-sml.png)](images/win/01.png#lightbox)

2. 请确保选中“Android”和“iOS”且勾选了“.NET Standard”代码共享策略：

    [![Android 和 iOS 以及 .NET Standard](images/win/02-sml.png)](images/win/02.png#lightbox)

3. 等到 NuGet 包还原（状态栏中将出现“还原已完成”消息）。

4. 按调试按钮（或“调试”>“开始调试”菜单项）启动 Android Emulator。

5. 编辑 MainPage.xaml，在 `</StackPanel>` 结束之前添加此 XAML：

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

请按照以下步骤以及上面的视频操作：

1. 选择“文件”>“新建解决方案...”或按“新建项目...”按钮，然后选择“多平台”>“应用”>“空白窗体应用”：

    [![空白窗体应用](images/01-sml.png)](images/01.png#lightbox)

2. 请确保选中“Android”和“iOS”且勾选了“.NET Standard”代码共享策略：

    [![Android 和 iOS 以及 .NET Standard](images/02-sml.png)](images/02.png#lightbox)

3. 右键单击解决方案，还原 NuGet 包：

    ![Android 应用](images/03-sml.png)

4. 按调试按钮（或“运行”>“开始调试”）启动 Android Emulator。

5. 编辑 MainPage.xaml，在 `</StackPanel>` 结束之前添加此 XAML：

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

    [![将启动项目设置为 IOS](images/08-sml.png)](images/08.png#lightbox)

9. 调试 iOS 上的应用：

    ![iOS 应用](images/09-sml.png)

::: zone-end

可从[示例库](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/FirstApp/)下载或在 [GitHub](https://github.com/xamarin/xamarin-forms-samples/tree/master/GetStarted/FirstApp) 上查看完整代码。

## <a name="next-steps"></a>后续步骤

- [单一页快速入门](~/get-started/quickstarts/single-page.md)&ndash;构建功能更强的应用程序。
- [Xamarin.Forms 示例](~/xamarin-forms/samples/index.yml) &ndash; 下载并运行代码示例和示例应用。
- [创建移动应用电子书](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) &ndash; 深入介绍 Xamarin.Forms 开发的章节（PDF 格式），包括数百个其他示例。

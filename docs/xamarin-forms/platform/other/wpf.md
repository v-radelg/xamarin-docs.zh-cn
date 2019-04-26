---
title: WPF 平台安装程序
description: Xamarin.Forms 现在具有对 WPF 平台的预览支持
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 2bef13e7f465dd213649f88deb572eb661895250
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61245396"
---
# <a name="wpf-platform-setup"></a>WPF 平台安装程序

![预览](~/media/shared/preview.png)

Xamarin.Forms 现在提供预览版支持的 Windows Presentation Foundation (WPF)。 本文演示如何将 WPF 项目添加到 Xamarin.Forms 解决方案。

启动、 在 Visual Studio 2019 中, 创建新的 Xamarin.Forms 解决方案或使用现有的 Xamarin.Forms 解决方案，例如之前, [ **BoxViewClock**](https://developer.xamarin.com/samples/xamarin-forms/BoxView/BoxViewClock/)。 只能向 Xamarin.Forms 解决方案在 Windows 中添加 WPF 应用程序。

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>将 WPF 项目添加到包含 Xamarin.University 的 Xamarin.Forms 应用

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin.Forms 3.0 WPF 支持，通过[Xamarin 学院课程](https://university.xamarin.com/)**

## <a name="adding-a-wpf-app"></a>添加一个 WPF 应用程序

按照这些说明添加将在 7、 8 和 10 的 Windows 台式计算机运行的 WPF 应用程序：

1. 在 Visual Studio 2019，右键单击解决方案名称在**解决方案资源管理器**，然后选择**添加 > 新建项目...**.

2. 在中**新的项目**窗口中的，在左侧选择**Visual C#** 并**Windows 经典桌面**。 在项目类型列表中，选择**WPF 应用 (.NET Framework)**。 

3. 键入与项目的名称**WPF**扩展，例如， **BoxViewClock.WPF**。 单击**浏览**按钮，选择**BoxViewClock**文件夹，然后按**选择文件夹**。 这会在解决方案中的其他项目所在的同一目录中将 WPF 项目。

    ![添加新的 WPF 项目](wpf-images/add-new-project.png "添加新的 WPF 项目")

    按确定以创建项目。

4. 在中**解决方案资源管理器**，右键单击新**BoxViewClock.WPF**项目，然后选择**管理 NuGet 包**。 选择**浏览**选项卡上，单击**包括预发行版**复选框，并搜索**Xamarin.Forms**。

    ![选择 NuGet 包](wpf-images/select-nuget-package.png "选择 NuGet 包")

    选择的程序包，再单击**安装**按钮。

5. 在其中搜索**Xamarin.Forms.Platform.WPF**包并将同时安装，其中一个。 请确保包是从 Microsoft ！

6. 右键单击解决方案的名称**解决方案资源管理器**，然后选择**为解决方案管理 NuGet 包**。 选择**更新**选项卡和**Xamarin.Forms**包。 选择所有项目，并将其更新为相同的 Xamarin.Forms 版本：

    ![更新 NuGet 包](wpf-images/update-nuget-package.png "更新 NuGet 包") 

7. 在 WPF 项目中，右键单击**引用**。 在中**引用管理器**对话框中，选择**项目**左侧，并检查与相邻的复选框**BoxViewClock**项目：

    ![引用共享的项目](wpf-images/reference-shared-project.png "引用共享的项目")

8. 编辑**MainWindow.xaml** WPF 项目文件。 在中`Window`标记中，添加的 XML 命名空间声明**Xamarin.Forms.Platform.WPF**程序集和命名空间：

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    现在，更改`Window`标记为`wpf:FormsApplicationPage`。 更改`Title`设置为应用程序，例如，名称**BoxViewClock**。 已完成的 XAML 文件应如下所示：

    ```xaml
    <wpf:FormsApplicationPage x:Class="BoxViewClock.WPF.MainWindow"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
            xmlns:local="clr-namespace:BoxViewClock.WPF"
            mc:Ignorable="d"
            Title="BoxViewClock" Height="450" Width="800">
        <Grid>
        
        </Grid>
    </wpf:FormsApplicationPage>
    ```

9. 编辑**MainWindow.xaml.cs** WPF 项目文件。 添加两个新`using`指令：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    更改类的基类`MainWindow`从`Window`到`FormsApplicationPage`。 以下`InitializeComponent`调用中，添加以下两个语句：

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    除了注释和未使用`using`指令，完整**MainWindows.xaml.cs**文件应如下所示：

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;

    namespace BoxViewClock.WPF
    {
        public partial class MainWindow : FormsApplicationPage
        {
            public MainWindow()
            {
                InitializeComponent();

                Forms.Init();
                LoadApplication(new BoxViewClock.App());
            }
        }
    }
    ```

10. 右键单击中的 WPF 项目**解决方案资源管理器**，然后选择**设为启动项目**。 按 f5 键以与 Visual Studio 调试器在 Windows 桌面上运行该程序：

    ![WPF 字数时钟](wpf-images/wpf-boxviewclock.png "WPF 字数时钟" )

## <a name="next-steps"></a>后续步骤

### <a name="platform-specifics"></a>平台特定信息

您可以确定从代码或 XAML 在 Xamarin.Forms 应用程序运行哪些平台。 这样，您可以在 WPF 上运行时更改程序特征。 在代码中，进行比较的值`Device.RuntimePlatform`与`Device.WPF`常量 （它等于字符串"WPF"）。 如果没有匹配项，在 WPF 上运行应用程序。

在 XAML 中，你可以使用`OnPlatform`标记来选择特定于平台的属性值：

```xaml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White" />
        <On Platform="macOS" Value="White" />
        <On Platform="Android" Value="Black" />
        <On Platform="WPF" Value="Blue" />
    </OnPlatform>
</Button.TextColor>
```

### <a name="window-size"></a>窗口大小

您可以调整在 WPF 窗口的初始大小**MainWindow.xaml**文件：

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>问题

这是预览版，因此可以预见，并非所有内容都可用于生产。 并非所有的 Xamarin.Forms NuGet 包已准备对于 WPF，和某些功能可能无法完全正常。


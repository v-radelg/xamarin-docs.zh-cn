---
title: WPF 平台安装
description: Xamarin 现已对 WPF 平台提供预览版支持
ms.prod: xamarin
ms.assetid: 650723F2-4279-4B7B-B0A1-D7F8FF26BF1E
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 04/05/2018
ms.openlocfilehash: 35652c0fc0e64686a79b2ea0fe96a1fca0b2825d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652839"
---
# <a name="wpf-platform-setup"></a>WPF 平台安装

![预览](~/media/shared/preview.png)

Xamarin 目前对 Windows Presentation Foundation (WPF) 提供预览版支持。 本文演示如何向 Xamarin 解决方案添加 WPF 项目。

在开始之前, 请先在 Visual Studio 2019 中创建新的 Xamarin 解决方案, 或使用现有的 Xamarin Forms 解决方案, 例如[**BoxViewClock**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/boxview-boxviewclock)。 只能将 WPF 应用添加到 Windows 中的 Xamarin 解决方案。

## <a name="add-a-wpf-project-to-a-xamarinforms-app-with-xamarinuniversity"></a>使用 Xamarin 将 WPF 项目添加到 Xamarin 应用程序

> [!VIDEO https://youtube.com/embed/Fy9N6OSxK64]

**Xamarin 3.0 WPF 支持视频**

## <a name="adding-a-wpf-app"></a>添加 WPF 应用

按照这些说明添加将在 Windows 7、8和10桌面上运行的 WPF 应用程序:

1. 在 Visual Studio 2019 中, 右键单击 "**解决方案资源管理器**中的解决方案名称, 然后选择"**添加 > 新项目 ...** "。

2. 在 "**新建项目**" 窗口中, 在左侧选择 "**视觉对象C#** 和**Windows 经典桌面**"。 在项目类型列表中, 选择 " **WPF 应用 (.NET Framework)** "。 

3. 使用**wpf**扩展键入项目的名称, 例如**BoxViewClock**。 单击 "**浏览**" 按钮, 选择 " **BoxViewClock** " 文件夹, 然后按 "**选择文件夹**"。 这会将 WPF 项目放在与解决方案中的其他项目相同的目录中。

    ![添加新的 WPF 项目](wpf-images/add-new-project.png "添加新的 WPF 项目")

    按 "确定" 创建项目。

4. 在**解决方案资源管理器**中, 右键单击新的 " **BoxViewClock** " 项目, 然后选择 "**管理 NuGet 包**"。 选择 "**浏览**" 选项卡, 单击 "**包括预发行**版" 复选框, 然后搜索 " **Xamarin**"。

    ![选择 NuGet 包](wpf-images/select-nuget-package.png "选择 NuGet 包")

    选择该程序包, 然后单击 "**安装**" 按钮。

5. 现在搜索 " **Xamarin** ", 然后安装该程序包。 请确保此包来自 Microsoft!

6. 在**解决方案资源管理器**中右键单击解决方案名称, 然后选择 "**管理解决方案的 NuGet 包**"。 选择 "**更新**" 选项卡和 " **Xamarin** " 包。 选择所有项目, 并将其更新到相同的 Xamarin 版本:

    ![更新 NuGet 包](wpf-images/update-nuget-package.png "更新 NuGet 包") 

7. 在 WPF 项目中, 右键单击 "**引用**"。 在 "**引用管理器**" 对话框中, 选择左侧的 "**项目**", 然后选中 " **BoxViewClock** " 项目旁边的复选框:

    ![引用共享项目](wpf-images/reference-shared-project.png "引用共享项目")

8. 编辑 WPF 项目的**mainwindow.xaml**文件。 在标记中, 为 Xamarin 程序集和命名空间添加 XML 命名空间声明: `Window`

    ```xaml
    xmlns:wpf="clr-namespace:Xamarin.Forms.Platform.WPF;assembly=Xamarin.Forms.Platform.WPF"
    ```

    现在, `Window`请将标记`wpf:FormsApplicationPage`更改为。 将设置更改为应用程序的名称, 例如, BoxViewClock。 `Title` 已完成的 XAML 文件应如下所示:

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

9. 编辑 WPF 项目的**MainWindow.xaml.cs**文件。 添加两个`using`新指令:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.WPF;
    ```

    将的`MainWindow`基类从`Window`更改为`FormsApplicationPage`。 `InitializeComponent`调用后, 添加以下两个语句:

    ```csharp
    Forms.Init();
    LoadApplication(new BoxViewClock.App());
    ```
    
    完整的 MainWindows.xaml.cs 文件应如下所示: `using`

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

10. 在**解决方案资源管理器**中右键单击 WPF 项目, 然后选择 "**设为启动项目**"。 按 F5, 在 Windows 桌面上通过 Visual Studio 调试器运行程序:

    ![WPF BoxView 时钟](wpf-images/wpf-boxviewclock.png "WPF BoxView 时钟" )

## <a name="next-steps"></a>后续步骤

### <a name="platform-specifics"></a>平台特定信息

可以从代码或 XAML 确定你的 Xamarin 应用程序应用程序在哪个平台上运行。 这使您可以在 WPF 上运行时更改程序特征。 在代码中, 将的值`Device.RuntimePlatform` `Device.WPF`与常量 (等于字符串 "WPF") 进行比较。 如果存在匹配项, 则应用程序在 WPF 上运行。

在 XAML 中, 可以使用`OnPlatform`标记来选择特定于平台的属性值:

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

可以在 WPF **mainwindow.xaml**文件中调整窗口的初始大小:

```xaml
Title="BoxViewClock" Height="450" Width="800"
```

## <a name="issues"></a>问题

这是一种预览版, 因此, 你应预计并非一切都准备就绪。 并非所有适用于 Xamarin 的 NuGet 包。窗体已准备好用于 WPF, 某些功能可能无法完全正常工作。


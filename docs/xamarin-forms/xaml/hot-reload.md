---
title: 适用于 Xamarin 的 XAML 热重载
description: 立即在正在运行的应用程序上重新加载对 XAML 文件所做的更改，以便在每次 XAML 更改后无需生成 Xamarin. Forms 项目。
ms.prod: xamarin
ms.assetid: E220F054-32EE-424C-A7E5-6156BE271519
ms.technology: xamarin-forms
author: maddyleger1
ms.author: maleger
ms.date: 08/13/2019
ms.openlocfilehash: 0f3ff5357d3fb4c60a910dda6befa8c699c6fc07
ms.sourcegitcommit: 5f7749d6ba1db79aa0fbf4ee7c90f2dcd4de812b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74884119"
---
# <a name="xaml-hot-reload-for-xamarinforms-preview"></a>适用于 Xamarin 的 XAML 热重载（预览）

XAML 热重载将插入现有的工作流，以提高工作效率并节省时间。 如果不使用 XAML 热重载，则必须在每次想要查看 XAML 更改时生成并部署应用。 如果是热重载，则保存 XAML 文件时，所做的更改将反映在正在运行的应用程序中。 此外，还将保留你的导航状态和数据，从而使你可以快速循环访问 UI，而不会丢失你在应用中的位置。 因此，通过 XAML 热重载，你将花费更少的时间来重新生成和部署应用，以验证 UI 更改。

> [!NOTE]
> 如果要编写 WPF 或 UWP 应用，请参阅[适用于 UWP 和 WPF 的 XAML 热重载](/visualstudio/debugger/xaml-hot-reload)。

## <a name="system-requirements"></a>系统需求

| IDE/框架 | 所需版本 |
|------|------------------|
|Visual Studio 2019 | 16.4 或更高版本
Visual Studio 2019 for Mac | 8.4 或更高版本
Xamarin.Forms | 4.1 或更高版本

## <a name="use-xaml-hot-reload-for-xamarinforms"></a>使用适用于 Xamarin 的 XAML 热重载

使用 XAML 热重载不需要额外的安装或设置。 它内置于 Visual Studio 中，并可在 IDE 设置中启用。 启用后，可以通过在模拟器、模拟器或物理设备上调试应用，开始使用 XAML 热重载。 目前，XAML 热重载仅适用于在 iOS 或 Android 上调试时的工作。

在 Windows 上，可以通过选中 "在 > **工具**上**启用 xamarin 热重载**" 复选框来启用 XAML 热重载 **， > ** **Xamarin** > "**热重载**"。

在 Mac 上，可以通过选中 "在**Visual Studio**上**启用 Xamarin 热重载**" 复选框来启用 XAML 热重载 > **首选项** > **项目** > **Xamarin 热重载**。

## <a name="resilient-reloading"></a>复原重装

如果进行了更改，则无法重新加载 XAML 热重载，它将使用 IntelliSense 显示错误消息。 这些更改称为 "强制编辑"，其中包括在 XAML 中键入的内容，或者将控件布线到不存在的事件处理程序。 即使使用强制编辑，也可以继续重新加载，而无需重新启动应用程序-在 XAML 文件中的其他位置进行另一个更改，然后点击 "保存"。 不会重新加载 "强制编辑"，但会继续应用其他更改。

## <a name="known-limitations"></a>已知限制

- 在 XAML 热重载会话期间，无法添加、删除或重命名文件或 NuGet 包。 如果添加或删除文件或 NuGet 包，请重新生成并重新部署应用，以便继续使用 XAML 热重载。
- 将链接器设置为 "**不链接**" 以获得最佳体验。 **仅限链接 SDK**设置工作正常，但在某些情况下可能会失败。
- 在物理 iPhone 上调试需要解释器使用 XAML 热重载。 为此，请打开项目设置，选择 "iOS 生成" 选项卡，并确保启用 **"Mono 解释器"** 设置。 可能需要将属性页顶部的 "**平台**" 选项更改为 " **iPhone**"。
- 通过使用 `x:Name` 值将控件分配给另一个字段或属性而创建的任何引用都不会重新加载。
- 在**AppShell**中更新 Shell 应用程序的可视层次结构可能会导致应用程序的状态保持有问题。 重新生成应用程序以继续重新加载。
- XAML 热重载无法重新C#加载代码，包括事件处理程序、自定义控件、页代码隐藏以及其他类。

## <a name="migrate-from-the-private-preview"></a>从个人预览版迁移

如果你是个人预览版的一部分，则在 Visual Studio 更新时，你的 XAML 热重载扩展将自动更新。 如果你选择不更新 Visual Studio，你可以继续使用当前版本的 XAML 热重载，但你不会通过专用预览扩展源接收任何进一步的更新。

## <a name="troubleshooting"></a>故障排除

- 如果 XAML 热重载未能初始化：
  - 更新 Xamarin. Forms 版本。
  - 确保你处于最新版本的 IDE。
  - 将 Android 或 iOS 链接器设置设置为 "**不链接**项目的生成设置"。
- 如果保存 XAML 文件后没有任何反应，请确保在 IDE 中启用了热重载。
- 如果是在物理 iPhone 上进行调试，而应用程序无响应，请检查是否已启用解释器。 若要启用此设置，请打开项目设置，选择 "iOS 生成" 选项卡，然后选中 "**启用 Mono 解释**器" 设置。

若要报告 bug，请使用**帮助** > **发送反馈** > 在 Windows 上**报告问题**菜单，以及**帮助** > 报告 Mac 上**的问题**菜单。

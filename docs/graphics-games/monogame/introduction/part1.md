---
title: 第1部分-创建跨平台 MonoGame
description: 本演练演示如何使用 MonoGame 创建适用于 iOS 和 Android 的新项目。 其结果是一个 Visual Studio for Mac 解决方案，其中包含跨平台共享代码项目以及每个平台的一个项目。 执行时，此项目将显示一个空的蓝屏。
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d72c428bb4b8c88365180c5c3c50b107eed2b21d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68978443"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>第1部分-创建跨平台 MonoGame

_本演练演示如何使用 MonoGame 创建适用于 iOS 和 Android 的新项目。其结果是一个 Visual Studio for Mac 解决方案，其中包含跨平台共享代码项目以及每个平台的一个项目。此项目在运行时将显示空的蓝屏。_

MonoGame 允许开发跨平台游戏，其中包含大量代码重用。 本演练将重点介绍如何设置一个解决方案，其中包含适用于 iOS 和 Android 的项目，以及用于跨平台代码的共享代码项目。

完成后，该项目具有在每秒30帧上执行游戏更新逻辑和游戏绘制逻辑的适当结构。 它可用作任何 MonoGame 项目的基本项目。 运行时，项目将如下所示：

![空白蓝屏](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio"></a>将 MonoGame 添加到 Visual Studio

> [!IMPORTANT]
> 默认情况下，Visual Studio 2019 或 Visual Studio for Mac 中未安装 MonoGame。
>
> 你应从 http://www.monogame.net/downloads/ 中手动下载并安装最新版本，然后运行安装程序。 可能需要重新启动 Visual Studio 才能使模板出现。
>
> "**游戏开发**" 部分应该出现在 "**外接程序管理器**" 中。

若要为 Visual Studio for Mac 启用 MonoGame 外接程序，请选择 " **Visual Studio for Mac**  > **外接程序管理器 ...** "。 对于 Windows 上的 Visual Studio 2019，请选择 "**工具**"  >  "**外接程序管理器 ...** "。选择 "**库**" 选项卡，展开 "**游戏开发**" 类别，然后选择 " **MonoGame 外接**程序"，然后单击 "**安装 ...** "：

![选择 MonoGame 的 Visual Studio for Mac 扩展库](part1-images/image2.png)

安装后，MonoGame 模板将显示在 Visual Studio for Mac 中，如以下部分中所示。

## <a name="creating-a-new-solution"></a>创建新解决方案

在 Visual Studio for Mac 选择 "**文件 >" 新建解决方案**"。 在 "**新建项目**" 对话框中，单击 "**杂项**"，滚动到 "**常规**" 部分，选择 "**通用 MonoGame 移动应用程序**" 选项，然后单击 "下一步"。

!["新建项目" 对话框创建 MonoGame 应用程序](part1-images/image3.png)

将项目命名为 WalkingGame，然后单击 "创建"：

![选择名称和位置的 "新建项目" 对话框](part1-images/image4.png)

现在，我们的项目将像任何其他 iOS 或 Android 项目一样执行。 项目应显示青的蓝色背景：

![空白蓝色应用背景](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>修复 Android 编译错误

MonoGame 的模板的当前版本包含 Android `Activity1.cs` 文件中的几个语法错误。 若要解决这些问题，请将 `OnCreate` 函数替换为以下内容：

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>总结

本演练介绍了如何使用 Visual Studio for Mac 创建跨平台的 MonoGame 项目。 这是一个空的蓝屏。 此项目可用作任何 iOS 和 Android 游戏的起始点。

## <a name="related-links"></a>相关链接

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [MonoGame API 文档](http://www.monogame.net/documentation/?page=main)

---
title: 在 Xamarin.Mac 中 OpenTK 简介
description: 本文提供了在 Xamarin.Mac 应用程序中使用 OpenTK 简介。 它介绍了创建和维护游戏窗口、 呈现一个简单的对象和向用户显示该对象。
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 835b8cd0f2e689c4d7d4cace1d846543863b7393
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032641"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>在 Xamarin.Mac 中 OpenTK 简介

OpenTK （打开的工具包） 是一个高级、 更低级别 C# 库帮助使用 OpenGL 和 OpenCL OpenAL 更加轻松。 OpenTK 可以用于游戏、 科学记数法的应用程序或其他项目需要 3D 图形、 音频或计算的功能。 本文介绍 Xamarin.Mac 应用中使用 OpenTK 简介。

[![](opentk-images/intro01.png "运行示例应用")](opentk-images/intro01.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中的 OpenTK 基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="About_OpenTK" />

## <a name="about-opentk"></a>有关 OpenTK

如上面所述，OpenTK （打开的工具包） 是一个高级、 更低级别 C# 库帮助使用 OpenGL 和 OpenCL OpenAL 更加轻松。 在 Xamarin.Mac 应用中使用 OpenTK 提供以下功能：

- **快速开发**-OpenTK 提供强大的数据类型和内联文档，以提高您的编码工作流，变得更容易和更快地发现错误。
- **轻松集成**-OpenTK 旨在与.NET 应用程序轻松集成。
- **许可授权**-OpenTK 在 MIT/X11 许可证下发布，并且是完全免费的。
- **丰富的、 类型安全绑定**-OpenTK 支持最新版本的 OpenGL，OpenGL | ES、 OpenAL 和 OpenCL 自动扩展加载后，错误检查和内联文档。
- **灵活的 GUI 选项**-OpenTK 提供本机、 高性能游戏窗口专为游戏和 Xamarin.Mac 设计。
- **完全托管的符合 Cls 的代码**-OpenTK 支持 32 位和 64 位版本的 macOS 与任何非托管库。
- **3D 数学 Toolkit** OpenTK 提供`Vector`， `Matrix`，`Quaternion`和`Bezier`结构通过其 3D 数学工具包。

OpenTK 可以用于游戏、 科学记数法的应用程序或其他项目需要 3D 图形、 音频或计算的功能。

有关详细信息，请参阅[的开放工具包](http://www.opentk.com)网站。

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>OpenTK 快速入门

作为在 Xamarin.Mac 应用中使用 OpenTK 简介，我们要创建简单应用程序打开一个游戏视图时，Mac 应用的主窗口，以向用户显示的三角形上呈现该视图和 attachs 游戏视图中一个简单的三角形。

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>开始一个新项目

启动 Visual Studio for Mac，并创建新的 Xamarin.Mac 解决方案。 选择**Mac** > **应用** > **常规** > **Cocoa 应用**:

[![](opentk-images/sample01.png "添加新 Cocoa 应用")](opentk-images/sample01.png#lightbox)

输入`MacOpenTK`有关**项目名称**:

[![](opentk-images/sample02.png "设置项目名称")](opentk-images/sample02.png#lightbox)

单击**创建**按钮以生成新项目。

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>包括 OpenTK

在 Xamarin.Mac 应用程序中使用打开 TK 之前，需要包括 OpenTK 程序集的引用。 在中**解决方案资源管理器**，右键单击**引用**文件夹，然后选择**编辑引用...**.

通过选中`OpenTK`然后单击**确定**按钮：

[![](opentk-images/sample03.png "编辑项目引用")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>使用 OpenTK

创建新项目后，双击`MainWindow.cs`文件中**解决方案资源管理器**打开进行编辑。 使`MainWindow`类外观如下所示：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

让我们回顾此代码在下方提供详细信息。

<a name="Required_APIs" />

### <a name="required-apis"></a>所需的 Api

多个引用所需使用 OpenTK Xamarin.Mac 类中。 在定义的开始我们提供以下`using`语句：

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```
此最小集将需要使用 OpenTK 任何类。

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>添加游戏视图

接下来我们需要创建游戏视图以包含所有与 OpenTK 我们交互并显示结果。 我们使用下面的代码：

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

此处我们所做的游戏视图 Main Mac 窗口具有相同的大小并更换为新的窗口的内容视图`MonoMacGameView`。 由于我们替换现有的窗口内容，因此主 Windows 在调整大小时，将自动调整为指定视图。

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>对事件作出响应

有几个应响应每个游戏视图的默认事件。 在本部分将介绍所需的主要事件。

<a name="The_Load_Event" />

### <a name="the-load-event"></a>加载事件

`Load`事件是从磁盘图像、 纹理或音乐等加载资源的位置。 对于我们简单测试应用，我们不使用`Load`事件，但包含它的引用：

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>调整大小事件

`Resize`每次调整游戏视图时，应调用事件。 对于我们的示例应用，我们做出了 GL 视区游戏视图 （它是自动通过 Mac 主窗口的宽度调整) 的大小相同，以下代码：

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>UpdateFrame 事件

`UpdateFrame`事件用于处理用户输入、 更新对象位置、 运行的物理或 AI 计算。 对于我们简单测试应用，我们不使用`UpdateFrame`事件，但包含它的引用： 

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> OpenTK Xamarin.Mac 实现不包括`Input API`，因此将需要使用 Apple 提供的 Api 来添加键盘和鼠标支持。 或者，您可以创建自定义的实例`MonoMacGameView`并重写`KeyDown`和`KeyUp`方法。

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>RenderFrame 事件

`RenderFrame`事件包含用于呈现 （绘制） 的代码在图形。 对于我们的示例应用，我们将使用一个简单的三角形填充游戏视图： 

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

呈现代码通常会通过调用正在`GL.Clear`若要删除的任何现有元素之前绘制新元素。

> [!IMPORTANT]
> OpenTK 的 Xamarin.Mac 版本**不这样做**调用`SwapBuffers`方法在`MonoMacGameView`呈现代码结束时的实例。 执行此操作将导致游戏视图到 strobe 快速而不是显示在呈现的视图。

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>运行游戏视图

与所有所需的事件定义和游戏视图附加到我们的应用程序的 Main Mac 窗口，我们读取运行游戏视图，并显示我们的图形。 使用以下代码：

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

我们传递所需的帧速率，我们想要在更新的游戏视图中，对于我们的示例中我们已选择`60`每秒帧数 （作为正常电视相同刷新频率）。

让我们运行我们的应用程序并查看输出：

[![](opentk-images/intro01.png "一个示例应用程序输出")](opentk-images/intro01.png#lightbox)

如果我们调整窗口的大小，游戏视图也将驻留和将调整大小和也更新实时三角形。

<a name="Where_to_Next" />

### <a name="where-to-next"></a>下一步？

使用 OpenTk 在 Xamarin.mac 应用程序中完成的基础知识，以下是一些建议的内容来试用下一步：

- 请尝试更改的三角形的颜色和中的游戏视图的背景色`Load`和`RenderFrame`事件。
- 使用户按中的键时更改颜色的三角形`UpdateFrame`并`RenderFrame`事件或使自己的自定义`MonoMacGameView`类并重写`KeyUp`和`KeyDown`方法。
- 请使用中的注意键在屏幕上移动的三角形`UpdateFrame`事件。 提示： 使用`Matrix4.CreateTranslation`方法来创建平移矩阵并调用`GL.LoadMatrix`方法以加载其`RenderFrame`事件。
- 使用`for`循环，以呈现中的多个三角形`RenderFrame`事件。
- 旋转照相机能够在 3D 空间中的三角形的不同视图。 提示： 使用`Matrix4.CreateTranslation`方法来创建平移矩阵并调用`GL.LoadMatrix`方法以加载它。 此外可以使用`Vector2`， `Vector3`，`Vector4`和`Matrix4`对相机的操作的类。

有关更多示例，请参阅[OpenTK 示例 GitHub](https://github.com/opentk/opentk/tree/master/Source/Examples)存储库。 它包含使用 OpenTK 的示例的官方列表。 您必须调整这些示例中使用的 OpenTK 的 Xamarin.Mac 版本。

OpenTK 实现的更复杂 Xamarin.Mac 示例，请参阅我们[MonoMacGameView](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)示例。

<a name="Summary" />

## <a name="summary"></a>总结

本文已介绍一下在 Xamarin.Mac 应用程序中使用 OpenTK。 我们已了解如何创建游戏窗口中，如何将游戏窗口附加到 Mac 窗口以及如何呈现游戏窗口中的一个简单形状。

## <a name="related-links"></a>相关链接

- [MacOpenTK （示例）](https://developer.xamarin.com/samples/mac/MacOpenTK/)
- [MonoMacGameView （示例）](https://developer.xamarin.com/samples/mac/MonoMacGameWindow/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [使用 Windows](~/mac/user-interface/window.md)
- [打开工具包](http://www.opentk.com)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)

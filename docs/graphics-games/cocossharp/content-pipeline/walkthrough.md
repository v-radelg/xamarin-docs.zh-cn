---
title: 使用 MonoGame 管道工具
description: MonoGame 管道工具用于创建和管理 MonoGame 内容项目。 通过 MonoGame 管道工具处理和输出为.xnb 文件，以使用 CocosSharp 和 MonoGame 应用程序中内容的项目中的文件。
ms.prod: xamarin
ms.assetid: CACFBF5F-BBD4-4D46-8DDA-1F46466725FD
author: conceptdev
ms.author: crdun
ms.date: 03/27/2017
ms.openlocfilehash: 2227e2f56071b92c209c5d9fda994faa5a97a03d
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827770"
---
# <a name="using-the-monogame-pipeline-tool"></a>使用 MonoGame 管道工具

_MonoGame 管道工具用于创建和管理 MonoGame 内容项目。通过 MonoGame 管道工具处理和输出为.xnb 文件，以使用 CocosSharp 和 MonoGame 应用程序中内容的项目中的文件。_

MonoGame 管道工具提供了一个简单易用的环境，用于将转换到的内容文件 **.xnb** CocosSharp 和 MonoGame 应用程序中使用的文件。 有关内容管道以及它们为何会在游戏开发中有用的信息，请参阅[本内容管道简介](~/graphics-games/cocossharp/content-pipeline/introduction.md)

本演练将介绍以下内容：

 - 安装 MonoGame 管道工具
 - 创建 CocosSharp 项目
 - 创建内容的项目
 - 处理 MonoGame 管道工具中的文件
 - 在运行时使用的文件

本演练使用 CocosSharp 项目演示如何 **.xnb**文件可以加载和使用应用程序中。 MonoGame 的用户还能够一样 CocosSharp 和 MonoGame 都使用相同引用本演练 **.xnb**内容文件。

完成的应用程序将显示单个 sprite 显示从纹理 **.xnb**文件和单个标签显示从子画面字体 **.xnb**文件：

![](walkthrough-images/image1.png "完成的应用程序将显示单个 sprite 显示.xnb 文件中的纹理")


## <a name="monogame-pipeline-tool-discussion"></a>MonoGame 管道工具的讨论

在 Windows、 OS X 和 Linux 上，此 MonoGame 管道工具均可用。 本演练将运行该工具在 Windows，但它可以后接 Mac 和 Linux 上也。 获取最大或 Linux 上设置该工具的信息，请参阅[本页](http://www.monogame.net/2015/01/09/monogame-pipeline-tool-available-for-macos-and-linux/)。

MonoGame 管道工具是能够为 iOS 应用程序甚至时上运行 Windows，使用的是开发人员创建的内容[Xamarin Mac 代理](~/ios/get-started/installation/windows/connecting-to-mac/index.md)将能够继续在 Windows 上进行开发。


## <a name="installing-the-monogame-pipeline-tool"></a>安装 MonoGame 管道工具

我们将首先安装 MonoGame，其中包括 MonoGame 内容管道。 请注意，MonoGame 内容管道是单独的下载 for mac。 可以上找到所有 MonoGame 安装程序[MonoGame 下载页](http://www.monogame.net/downloads/)。 我们将下载 MonoGame 的 Visual Studio 中，但一旦安装开发人员也可以使用 MonoGame 在 Visual Studio for Mac:

![](walkthrough-images/image2.png "对于 Visual Studio 中，但一旦安装开发人员使用 MonoGame 在 Visual Studio for Mac 过下载 MonoGame")

下载完成后，我们将通过安装程序运行，并接受默认值选项。 安装程序完成后，MonoGame 管道工具已经安装，并且可以在搜索开始菜单中找到：

![](walkthrough-images/image3.png "MonoGame 管道工具安装程序完成后，已安装，并且可在开始菜单中搜索")

启动 MonoGame 管道工具：

![](walkthrough-images/image4.png "启动 MonoGame 管道工具")

MonoGame 管道工具运行后，我们可以开始，让我们游戏和内容的项目。


## <a name="creating-an-empty-cocossharp-project"></a>创建一个空的 CocosSharp 项目

下一步是创建 CocosSharp 项目。 非常重要，我们创建 CocosSharp 项目第一次，以便我们可以在创建 CocosSharp 项目的文件夹结构中保存我们内容的项目。 若要了解 CocosSharp 项目的结构，看一看[BouncingGame](~/graphics-games/cocossharp/bouncing-game.md)，这将使用本指南中。 但是，如果你想要将内容添加到现有 CocosSharp 项目，可使用该项目而不是 BouncingGame。

一旦创建项目后，我们将运行它以验证它生成和我们拥有的所有内容正确设置：

![](walkthrough-images/image5.png "一旦创建项目后，运行它来确认生成和所有内容设置正确")


## <a name="creating-a-content-project"></a>创建内容的项目

现在，我们有游戏项目，我们可以创建 MonoGame 管道项目。 为此，请在 MonoGame 管道工具选择**文件 > 新建...** 并导航到你的项目的内容文件夹。 对于 Android，文件夹位于 **[项目 root]\BouncingGame.Android\Assets\Content\\** 。 对于 iOS，文件夹位于 **[项目 root]\BouncingGame.iOS\Content\\** 。

更改**文件名**到**ContentProject**然后单击**保存**按钮：

![](walkthrough-images/image8.png "到 ContentProject 更改的文件的名称，然后单击保存按钮")

MonoGame 管道工具创建项目后，将显示有关项目的信息时根**ContentProject**选定项：

![](walkthrough-images/image9.png "选择根 ContentProject 项时 MonoGame 管道工具创建项目后，将显示有关项目的信息")

让我们看一些内容项目的最重要选项。


### <a name="output-folder"></a>输出文件夹

是 （相对于内容项目本身） 的文件夹，其中输出 **.xnb**将保存文件。 为了简单起见，我们将使用相同的文件夹以保存我们的输入和输出文件。 换而言之，我们将更改**输出文件夹**要 **。\\** :

![](walkthrough-images/image10.png "")


### <a name="platform"></a>平台

这会定义内容的目标平台。 请注意，这是**Windows**默认情况下，因此我们需要将其更改为我们的目标平台即**Android** （或如果以下 iOS 项目以及 iOS）。

![](walkthrough-images/image11.png "请注意，这是 Windows，默认情况下，因此将其更改为目标平台，这是一个 iOS 项目一起在 Android 或 iOS")


## <a name="processing-files-in-the-monogame-pipeline-tool"></a>处理 MonoGame 管道工具中的文件

接下来，我们将添加到内容我们**ContentProject**。 对于此项目，我们会将文件添加到项目的根目录，但较大的项目通常将组织中的文件夹及其内容。

我们将向我们的项目添加两个文件：

 - 一个 **.png**文件，用于绘制一个精灵。 此文件可以[从此处下载](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)。
 - 一个 **.spritefont**文件，用于在屏幕上绘制文本。 内容管道工具支持创建新.spritefont 文件，因此没有要下载的文件。


### <a name="adding-a-png-file"></a>添加.png 文件

若要添加 **.png**文件到项目中，我们将首先将其复制到与管道项目，它具有相同的目录 **.mgcb**扩展。

![](walkthrough-images/image12.png ".Png 文件添加到项目")

接下来，我们将添加到管道项目的文件。 若要执行此操作在 MonoGame 管道工具中，选择**编辑 > 添加项...** ，选择**ball.png**文件，并单击**打开**。 该文件现在将内容项目的一部分，并选中，将显示其属性：

![](walkthrough-images/image13.png "该文件现在将内容项目的一部分，然后选中，将显示其属性")

我们将按需加载 CocosSharp 中的.xnb 文件不进行任何更改正在保留为其默认值的所有值。 我们可以通过选择生成文件**生成 > 生成**菜单选项，它将开始生成过程，并显示有关生成的输出。 我们可以验证是否在生成正常通过检查**内容**一个新文件夹**ball.xnb**文件：

![](walkthrough-images/image14.png "验证在生成正常通过检查新 ball.xnb 文件的内容文件夹")


### <a name="adding-a-spritefont-file"></a>添加.spritefont 文件

我们可以创建通过 MonoGame 管道工具.spritefont 文件。 CocosSharp 需要字体位于**字体**文件夹，然后 CocosSharp 模板自动自动创建的字体文件夹。 我们可以通过选择此文件夹添加到 MonoGame 管道工具**编辑 > 添加 > 现有文件夹...** .浏览到**内容**文件夹，然后选择**字体**文件夹，然后单击**确定**:

![](walkthrough-images/browsetofonts.png "浏览到 Content 文件夹并选择字体文件夹并单击确定")

若要添加新的.sprintefont 文件，右键单击字体文件夹并选择**添加 > 新建项...** ，选择**SpriteFont 说明**选项中，输入名称**arial 36**，然后单击**确定**。 CocosSharp 需要非常特定的方式命名的字体文件 – 它们必须是格式为 [FontType]-[FontSize]。 如果字体与此命名格式，它将不加载的 CocosSharp 在运行时不匹配。

![](walkthrough-images/image15.png "如果字体与此命名格式，它将不加载的 CocosSharp 在运行时不匹配")

.Spritefont 文件是实际的 XML 文件可编辑在任何文本编辑器中，包括 Visual Studio for mac。 最常见变量.spritefont 文件中编辑`FontName`和`Size`属性：


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>

    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->
    <Size>12</Size> 
```

我们将在任何文本编辑器中打开该文件。 作为我们**arial 36.spritefont**顾名思义，我们将暂时`FontName`作为`Arial`但更改`Size`值设为`36`:


```xml
    <!-- Modify this string to change the font that will be imported. -->
    <FontName>Arial</FontName>   
  
    <!-- Size is a float value, measured in points. 
    Modify this value to change the size of the font. -->4/10/2016 12:57:28 PM 
    <Size>36</Size>
```
 
## <a name="using-files-at-runtime"></a>在运行时使用的文件

.Xnb 文件现在构建并准备好在我们的项目中使用。 我们会将文件添加到 Visual Studio for Mac，然后我们会将代码添加到我们`GameScene.cs`文件来加载这些文件并显示它们。


### <a name="adding-xnb-files-to-visual-studio-for-mac"></a>.Xnb 将文件添加到 Visual Studio for Mac

首先我们将向我们的项目添加文件。 在 Visual Studio for Mac 中，我们将展开**BouncingGame.Android**项目中，展开**资产**文件夹，右键单击**内容**文件夹，然后选择**添加 > 添加文件...** 首先，我们将选择**ball.xnb**我们之前生成，并单击**打开**。 然后重复上述步骤，但将添加**arial 36.xnb**文件。 我们将选择**其当前子目录中保留的文件**选项如果 Visual Studio for Mac 会询问如何将文件添加。 完成后，这两个文件应该是我们的项目的一部分：

![](walkthrough-images/image20.png "完成后，这两个文件应为项目的一部分")


### <a name="adding-gamescenecs"></a>添加**GameScene.cs**

我们将创建一个名为类`GameScene,`它将包含我们子画面和文本的对象。 若要执行此操作，右键单击**BouncingGame** (不 BouncingGame.Android) 项目，然后选择**添加 > 新建文件...** .选择**常规**类别中，选择**空类**选项，然后输入的名称**GameScene**。

创建后，我们将修改`GameScene.cs`文件，以包含以下代码：


```csharp
using System;
using CocosSharp;

namespace BouncingGame
{
    public class GameScene : CCScene
    {
        // All visual elements must be added to a CCLayer:
        CCLayer mainLayer;

        // The CCSprite is used to display the "ball" texture
        CCSprite sprite;
        // The CCLabelTtf is used to display the Arial36 sprite font
        CCLabelTtf label;

        public GameScene(CCWindow mainWindow) : base(mainWindow)
        {
            // Instantiate the CCLayer first:
            mainLayer = new CCLayer ();
            AddChild (mainLayer);

            // Now we can create the Sprite using the ball.xnb file:
            sprite = new CCSprite ("ball");
            sprite.PositionX = 200;
            sprite.PositionY = 200;
            mainLayer.AddChild (sprite);

            // The font name (arial) and size (36) need to match 
            // the .spritefont definition and file name.  
            label = new CCLabelTtf ("Using font 36", "arial", 36);
            label.PositionX = 200;
            label.PositionY = 300;
            mainLayer.AddChild (label);
        }
    }
} 
```

我们将不会讨论上面的代码由于中介绍了使用 CocosSharp 视觉对象，如 CCSprite 和 CCLabelTtf [BouncingGame 指南](~/graphics-games/cocossharp/bouncing-game.md)。

我们还需要添加代码以加载我们新创建`GameScene`。 若要这样做，我们将打开`GameAppDelegate.cs`文件 (位于**BouncingGame** PCL) 和修改`ApplicationDidFinishLaunching`方法，以便它如下所示：


```csharp
public override void ApplicationDidFinishLaunching (CCApplication application, CCWindow mainWindow)
{
    application.PreferMultiSampling = false;
    application.ContentRootDirectory = "Content";

    // New code:
    GameScene gameScene = new GameScene (mainWindow);
    mainWindow.RunWithScene (gameScene);
} 
```

运行时，我们的游戏中将如下所示：

![](walkthrough-images/image1.png "运行时，游戏将如下所示")


## <a name="summary"></a>总结

本演练演示了如何使用 MonoGame 管道工具从输入的.png 文件创建.xnb 文件，以及如何从新创建.sprintefont 文件创建新的.xnb 文件。 它还介绍了如何构建 CocosSharp 项目使用.xnb 文件以及如何将这些文件在运行时加载。

## <a name="related-links"></a>相关链接

- [MonoGame 下载](http://www.monogame.net/downloads/)
- [MonoGame 管道文档](http://www.monogame.net/documentation/?page=Pipeline)
- [启动 BouncingGame 项目适用于 Android （示例）](https://developer.xamarin.com/samples/monodroid/BouncingGameCompleteAndroid/)
- [ball.png 图 （示例）](https://github.com/xamarin/mobile-samples/blob/master/BouncingGame/Resources/ball.png?raw=true)

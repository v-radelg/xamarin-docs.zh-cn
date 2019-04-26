---
title: 使用 tvOS 图标和在 Xamarin 中的图像
description: 本文档介绍如何使用图标和在 tvOS 应用程序中使用 Xamarin 生成的图像。 它讨论了启动映像、 分层的映像、 应用程序图标中，和的详细信息。
ms.prod: xamarin
ms.assetid: A2DA4347-0563-4C72-A8D7-5B9DE9E28712
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 96af7fab366c3fd3493cf5adbf183d80b7c1ee26
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61418112"
---
# <a name="working-with-tvos-icons-and-images-in-xamarin"></a>使用 tvOS 图标和在 Xamarin 中的图像

创建极富感染力的图标和图像是开发你的 Apple TV 应用的引人入胜的用户体验的重要组成部分。 本指南将介绍如何创建并将包含所需的图形资产为 Xamarin.tvOS 应用所需的步骤：

- [启动映像](#Launch-Image)-时您的应用程序首次启动和完成启动后替换为应用程序的第一个屏幕显示的启动映像。
- [分层映像](#Layered-Images)-特定于 Apple TV 中，要创建选定项的三维效果的视差效果与 Apple 的新的分层映像工作。 有几种方式[创建分层映像](#Creating-Layered-Images)。
- [应用图标](#App-Icons)-图标所需的不只 Apple TV 主页屏幕上，但针对应用商店。 因此必须提供作为分层映像。
- [Top Shelf 图像](#Top-Shelf-Image)-如果在主屏幕的顶部行上放置您的应用程序，它将需要顶架图像以突出显示应用的功能。 （可选） 可以提供[动态 Top Shelf 内容](#Dynamic-Top-Shelf-Content)以突出显示应用程序中的内容。
- [游戏中心映像](#Game-Center-Images)-如果您的应用程序是一个游戏，并使用 Game Center，将需要多个其他映像。
- [设置 Xamarin.tvOS 项目图像](#Setting-Xamarin.tvOS-Project-Images)-介绍为 Xamarin.tvOS 应用设置的启动映像和应用程序图标所需的步骤。

> [!IMPORTANT]
> Apple TV 上的所有映像都都以 1 x 分辨率 (`@1x`)，您应_仅_使用此大小的图像。 包括更大更高分辨率的图形不仅需要时间才能下载和使用更多内存和存储，但它们必须在运行时将重新动态缩放和绘图性能会产生负面影响。

<a name="Launch-Image" />

## <a name="launch-image"></a>启动映像

启动映像是 Xamarin.tvOS 应用最初启动 Apple TV 上时显示的第一个操作，在这种情况下，每个 tvOS 应用必须提供启动映像。 

启动映像会快速显示，并提供您的应用程序是快速且响应迅速的印象。 Apple TV 将启动映像将替换为您的应用程序的第一个屏幕稍后那里之后。

启动图像不是广告或艺术表达式的机会，它们的存在只为了意味着您的应用程序快速启动并准备使用。

|启动映像大小|说明|
|---|---|
|1920x1080px<br>|仅限非分层.png 文件|

Apple 可以设计应用程序的启动映像的以下建议：

- **几乎与第一个屏幕的相同**-您启动屏幕应尽可能地应用的第一个屏幕。 包括不同的图形或元素可能会导致令人讨厌的"刷新"时出现的第一个屏幕。
- **避免使用文本**的启动映像是静态的在这种情况下，将不会本地化在显示之前。
- **让启动**-因为 Apple TV 用户经常切换应用程序，您不应引起对应用程序启动进程。
- **任何广告或品牌**-您的启动映像不应为关于屏幕或包含任何品牌，除非它是静态的应用程序的第一个屏幕。 严格禁止广告。

<a name="Setting-the-Launch-Image" />

### <a name="setting-the-launch-image"></a>设置启动图像

若要设置启动图像可查看你的 tvOS 项目，请执行以下操作：

1. 在中**解决方案资源管理器**，双击`Assets.xcassets`打开进行编辑： 

    [![](icons-images-images/asset01.png "Assets.xcassets 文件")](icons-images-images/asset01.png#lightbox)
2. 在中**资产编辑器**，单击`LaunchImages`资产： 

    [![](icons-images-images/asset02.png "LaunchImages 资产")](icons-images-images/asset02.png#lightbox)
3. 单击**1x Apple TV**条目和选择启动映像或有选择地从文件系统拖动新的映像： 

    [![](icons-images-images/asset03.png "选择启动映像")](icons-images-images/asset03.png#lightbox)
4. 保存更改。

<a name="Layered-Images" />

## <a name="layered-images"></a>分层的映像

新建 Apple TV 中，要生成可帮助用户保持在躺椅上在室内脑海连接到在屏幕上的内容的三维效果的视差效果的分层映像使用。

分层的映像包含从两个 （2） 到五 （5） 单独的合并以形成完整的映像层。 背景图层，除了每一层使用其 Z-顺序以及透明度来创建深度的错觉。 当用户交互使用分层映像时，更高版本 Z 排序图层是缩放和重叠，若要创建这种效果。

[![](icons-images-images/layered01.png "分层的映像 Z 顺序关系图")](icons-images-images/layered01.png#lightbox)

> [!IMPORTANT]
> 分层的映像所需的应用程序的图标，并且是可选的其他[可获得焦点项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)（如 Top Shelf 图像）。 但是，Apple 建议在为应用程序中可以获得焦点的任何映像使用分层映像。




Apple 可以设计分层映像的以下建议：

- **使背景层不透明**-在后台层 （第 1 层）**必须**是不透明或当您尝试在 Apple TV 上使用分层映像时，将收到错误。 所有其他层可以包含多个级别的透明度以增强的 3D 效果。
- **前景色、 中间和背景的元素隔离**的重要元素 （例如游戏个字符） 在前台，请使用中间的辅助元素或阴影。 最后，包括非特定语言的背景，以便为您的上层提供一个阶段。
- **将文本保存在前台**-除非您希望文本变得模糊由较高级别，通常它应为最顶层的层上。
- **使用简单分层**-视差效果的设计目标是细微因此为最小，以防止出现不协调、 不切实际的效果，请您的层。
- **包括安全区域**-较高的层可以裁剪期间视差效果，因为需要将每一层内置的安全区域边框。 如果您得太近层边缘获取你的内容，它可以成为裁剪。 多个缩放和裁剪比较低的层，会遇到较高的层。 请参阅[大小调整映像层](#Sizing-Image-Layers)下面一节。
- **预览通常**-应预览分层映像通常以确保所需的三维效果发生，并且没有任何单个层上的内容进行裁剪。 您应预览分层映像 Apple TV 的真实硬件上以确保它们按预期呈现。

只要有可能，应始终使用内置`UIKit`控件以显示分层映像，因为它们将自动获取视差效果时它们进入焦点。

<a name="Sizing-Image-Layers" />

### <a name="sizing-image-layers"></a>大小调整映像层

务必要记得加入_安全区域_到每个层构成分层映像的边框。 因为可以缩放和裁剪期间视差效果的各个层，层的内容可以裁剪下是否离得太近到层的边缘：

[![](icons-images-images/layered02.png "35 像素的边框")](icons-images-images/layered02.png#lightbox)

<a name="Creating-Layered-Images" />

### <a name="creating-layered-images"></a>创建分层映像

tvOS 分层映像采用以下格式：

- **汽车文件**-这是由 Apple 创建专用的资产目录格式。 不会直接创建汽车文件，它们是从任何 LSR 文件在编译时创建，包含在应用程序捆绑包。
- **LSR 映像**-这是由 Apple 创建的专用图像格式。 使用[视差导出程序 Adobe Photoshop 插件](https://itunespartner.apple.com/assets/downloads/ParallaxExporter_Apps.zip)或[视差预览程序](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)创建和预览分层映像 LSR 格式。
- **Assets.xcassets** -从两个 （2） 到五 （5） 标准`.png`带格式的图像将在汽车或 LSR 编译资产目录中包含在编译时设置格式，分层映像。
- **LCR 文件**-这是由 Apple 创建的专用文件格式。 LCR 文件旨在用作从内容服务器下载其他内容。 LCR 文件应永远不会包含在应用程序捆绑包中。 从使用 LSR 或 Photoshop 文件生成 LCR 文件`layerutil`附带 Xcode 命令行工具。

<a name="The-Parallax-Previewer" />

### <a name="the-parallax-previewer"></a>视差预览程序

创建 Apple[视差预览程序](http://itunespartner.apple.com/assets/downloads/Parallax%20Previewer.dmg)预览和创建所需的应用图标和可选可获得焦点项的分层映像。 预览程序显示了窗体已完成的分层映像每一层：

[![](icons-images-images/layered03.png "视差预览程序")](icons-images-images/layered03.png#lightbox)

在预览分层映像，可以使用鼠标来旋转图像，并预览视差效果。 使用 **+** （加） 和**-** （减号） 按钮来添加和删除层。

创建新的分层映像时，它可以采用 LSR 格式导出并包含在应用的捆绑包。

有关创建和预览分层映像的详细信息，请参阅 Apple 的[创建视差图稿](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/CreatingParallaxArtwork.html#//apple_ref/doc/uid/TP40015241-CH19-SW1)一部分[适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)。

<a name="App-Icons" />

## <a name="app-icons"></a>应用图标

Xamarin.tvOS 应用将需要 Apple 电视主页屏幕，但还针对应用商店图标不仅应用图标。 应用图标是您的第一个更改，对潜在用户让良好印象，并应传达一目了然的应用程序的用途。

[![](icons-images-images/icon01.png "应用图标")](icons-images-images/icon01.png#lightbox)

每个应用程序必须提供小型和大型版本的应用程序图标。 安装应用后，将 Apple 电视主页屏幕上使用的小图标。 最大版本可供 App Store。 大型应用图标应模拟的小图标版本的外观。

|小图标||大图标||
|---|---|---|---|
|实际大小|400x240px|大小|1280x768px|
|安全区域大小|370x222px|||
|失去焦点的大小|300x180px|||
|已设定焦点的大小|370x222px|||

> [!IMPORTANT]
> 应用图标必须提供作为**分层映像**。 请参阅[分层映像](#Layered-Images)上面部分的更多详细信息。




Apple 提供了用于创建应用图标的以下建议：

- **提供单一的焦点位置**– 设计与单个焦点图标直接放在中心的图标。
- **使用简单背景**– 简化您的图标背景，以便上层脱颖而出。请考虑使用简单的颜色或细微渐变。
- **限制文本量**– 因为当用户选择时，应用程序的名称将显示在图标下面，您只应包括文本时对图标的设计是至关重要的。
- **不要使用屏幕截图**-屏幕截图对于图标而言过于复杂，但不允许用户查看一眼对应用程序的用途。
- **保持图标正方形**– tvOS 自动应用略有为圆角的图标的掩码。 不包含这种舍入自己。
- **小心 Your 层分隔**– 文本应在右上角，大多数层、 中间和允许上层擅长的非特定背景中的第二项。
- **使用渐变和阴影仔细**– 渐变和阴影可以冲突与视差效果因此应谨慎使用。 简单上到下，浅到深渐变样式效果最佳。 Shadows 通常更适合清晰、 硬色调。
- **不同层的透明度**– 使用不同的应用程序的图标以增加三维效果的较高级别上的透明度级别。 背景图层必须是不透明或它将导致错误。

<a name="Setting-the-App-Icons" />

### <a name="setting-the-app-icons"></a>设置应用图标

若要设置应用图标所需的 tvOS 项目，请执行以下操作：

1. 在中**解决方案资源管理器**，双击`Assets.xcassets`打开进行编辑： 

    [![](icons-images-images/asset01.png "Assets.xcassets fileg")](icons-images-images/asset01.png#lightbox)
2. 在中**资产编辑器**，展开`App Icon & Top Shelf Image`资产： 

    [![](icons-images-images/asset04.png "展开顶架图像资产")](icons-images-images/asset04.png#lightbox)
3. 接下来，展开`App Icon - Small`资产： 

    [![](icons-images-images/asset05.png "展开应用程序图标的小型资产")](icons-images-images/asset05.png#lightbox)
4. 然后展开`Back`资产，并单击`Contents`条目： 

    [![](icons-images-images/asset06.png "然后展开后资产")](icons-images-images/asset06.png#lightbox)
5. 单击**1x Apple TV 条目**和选择的图像文件。
6. 重复上述步骤对于`Front`和`Middle`资产。
7. 然后，重复相同的步骤来定义`App Icon - Large`资产。
4. 保存更改。

<a name="Top-Shelf-Image" />

## <a name="top-shelf-image"></a>顶架图像

如果用户具有 Apple 电视主页屏幕上的顶部行上放置 Xamarin.tvOS 应用，则当用户选择您的应用程序时将显示大型的顶架图像。 此映像应突出显示您的应用程序的功能，或提供指向其内容的直接链接。

[![](icons-images-images/topshelf01.png "最上面的架图像示例")](icons-images-images/topshelf01.png#lightbox)

可以为单个静态提供顶架图像`.png`或`.lsr`文件 (请参阅[创建分层映像](#Creating-Layered-Images)) 或它可以动态创建在运行时作为可获得焦点项的单个行 (请参阅[动态顶层架子内容](#Dynamic-Top-Shelf-Content)下面)。

|顶层架子图像大小|说明|
|---|---|
|1920x720px|静态.png 或分层的.lsr 文件|

Apple 提供了以下建议以获得创建 Top Shelf 图像：

- **使用丰富的静态图像**– 如果您的应用程序不提供动态内容，其顶架图像将非可获得焦点。 使用此图像以突出显示应用程序或自己的品牌的功能。
- **链接到应用程序内容**– 动态 Top Shelf 布局提供你的用户查找应用程序中最重要的内容的快速链接。 使用此区域提供用于启动应用并立即跳转到给定内容的快速链接。
- **展示最新内容**– 丰富 Top Shelf 内容可以绘制到您的应用程序的用户并使其想要使用更多。 使用此区域显示最高评级或最新内容。
- **个性化内容**– 用户位置及其最常用的或主页屏幕的顶部行中最喜爱的应用。 使用 Top Shelf 来显示它们是最感兴趣的内容。
- **不允许使用广告**– 广告严格禁止显示在顶部货架。 可能会显示最新的可购买内容，但应显示任何定价信息。

### <a name="setting-the-top-shelf-image"></a>设置顶架图像

若要设置所需的 tvOS 项目顶架图像，请执行以下操作：

1. 在中**解决方案资源管理器**，双击`Assets.xcassets`打开进行编辑： 

    [![](icons-images-images/asset01.png "Assets.xcassets 文件")](icons-images-images/asset01.png#lightbox)
2. 在中**资产编辑器**，展开`App Icon & Top Shelf Image`资产： 

    [![](icons-images-images/asset04.png "展开顶架图像资产")](icons-images-images/asset04.png#lightbox)
3. 单击`Top Shelf Image`资产： 

    [![](icons-images-images/asset07.png "Top Shelf 图像资产")](icons-images-images/asset07.png#lightbox)
5. 单击**1x Apple TV 条目**和选择的图像文件。
6. 保存更改。

<a name="Dynamic-Top-Shelf-Content" />

### <a name="dynamic-top-shelf-content"></a>动态顶层架子内容

而不是显示静态的顶架图像，Top Shelf 可以包含的动态行[可获得焦点项](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection)或一组动态的滚动横幅。 这两个这些动态样式，可以突出显示应用程序或跳转到其最常用功能提供的内容。

<a name="Sectioned-Content-Row" />

#### <a name="sectioned-content-row"></a>分节的内容行

此动态顶层内容类型显示单个行的滚动，可获得焦点的项 （可选） 分解成不同的部分。 它通常用于突出显示新的、 收藏夹或最近查看的应用程序内容。

内容均显示为单个、 水平滚动内容的列表，包含标签下方的当前选定内容一部分 （即当前具有焦点）。 如果用户选择特定的内容，将启动你的应用，他们应考虑直接到该内容。

将需要以下内容大小：

||海报 (2:3)|Square (1:1)|高清晰度电视 (16:9)|
|---|---|---|---|
|实际大小|404x608px|608x608px|908x512px|
|安全区域大小|380x570px|570x570px|852x479px|
|失去焦点的大小|333x500px|500x500px|782x440px|
|已设定焦点的大小|380x570px|570x570px|852x479px|

Apple 提供了以下建议以获得内容剖切行：

- **完成行**– 应提供足够跨越整个屏幕宽度的内容。
- **缩放混合图像**– 剖切内容的行设计用来存放各种图像大小 （从上面提供的列表）。 如果您执行但是混合映像大小，请注意，其他缩放也会应用规范化显示内容。

<a name="Scrolling-Inset-Banners" />

#### <a name="scrolling-inset-banners"></a>滚动嵌入横幅

（可选） Xamarin.tvOS 应用可以作为自动滚动和循环的几乎填满整个屏幕的横幅集合顶部货架中存在其内容。 此样式通常用于展示新电视节目等丰富的新内容。

除了自动滚动，用户可以控制横幅并使用 Siri 远程任一方向滚动。 使一个较小，循环手势 Siri 远程时处于活动状态横幅上的将激活该横幅视差效果。

**横幅图像 （额外范围）**

|   |   |
|---|---|
|实际大小|1940x624px|
|安全区域大小|1740x620px|
|失去焦点的大小|1740x560px|
|已设定焦点的大小|1740x620px|

滚动嵌入横幅可以是作为提供一个静态`.png`或分层`.lsr`文件。

Apple 提供了以下建议以获得滚动嵌入横幅：

- **内容量**-应为滚动显得自然，提供最少三 （3） 横幅。 应包含不超过 8 个横幅或其导航硬为最终用户。
- **内容文本**-如果你横幅要求中的文本应包括在横幅图像。 如果要使用分层映像，最上面的层应为文本。

请参阅 Apple [TVServices 框架引用](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)将 Top Shelf 扩展添加到您的应用程序提供动态顶层内容的详细信息。

<a name="Game-Center-Images" />

## <a name="game-center-images"></a>游戏中心映像

如果已添加 Game Center 支持 Xamarin.tvOS 应用是一个游戏，需要几个更多的图像资产：

- **成就图标**-不透明图像是所必需的每个将自动剪裁插入圆形的成就。 获得的成就是非可获得焦点的项。
- **仪表板图稿**的可选图像可以是提供，将显示在游戏中心内应用的仪表板的顶部。 这些映像可以非可获得焦点。
- **排行榜图稿**-您必须提供之间一 （1） 到三 （3） 16:9 纵横比图像有关你的应用支持每个排行榜。 这些可能是静态`.png`或分层`.lsr`文件。 排行榜图稿是可获得焦点。

||成就图标|仪表板图稿|排行榜图稿|
|---|---|---|---|
|可见的大小|200x200px|923x150px|n/a|
|实际大小|320x320px|n/a|659x371px|
|安全区域大小|n/a|n/a|618x348px|
|失去焦点的大小|n/a|n/a|548x309px|
|已设定焦点的大小|n/a|n/a|618x348px|

有关如何使用 Game Center 的详细信息，请参阅 Apple[游戏中心编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/NetworkingInternet/Conceptual/GameKit_Guide/Introduction/Introduction.html)。

<a name="Working-with-Images" />

## <a name="working-with-images"></a>使用图像

TvOS 9 为 iOS 9 的子集，因为使用要包含在 Xamarin.iOS 应用中，显示图像的相同技术也适用于 Xamarin.tvOS 应用。 请参阅我们[显示图像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)文档了解详细信息。

<a name="Setting-Xamarin.tvOS-Project-Images" />

## <a name="setting-xamarintvos-project-images"></a>设置 Xamarin.tvOS 项目图像

如上面所述，所有 tvOS 应用都需要[启动映像](#Launch-Image)，并[应用图标](#App-Icons)。 本部分介绍 Xamarin.tvOS 应用项目选择的启动映像和应用图标后设置资产目录中。

请执行以下操作：

1. 在中**解决方案资源管理器**，双击`Info.plist`打开进行编辑： 

    [![](icons-images-images/info01.png "Info.plist 文件")](icons-images-images/info01.png#lightbox)
2. 中**Info.Plist 编辑器**，选择资产目录 (上述配置中[设置应用图标](#Setting-the-App-Icons)部分) 对于**应用图标**: 

    [![](icons-images-images/info02.png "Info.Plist 编辑器")](icons-images-images/info02.png#lightbox)
3. 接下来，选择资产目录 (在上面配置[设置启动图像](#Setting-the-Launch-Image)部分) 对于**启动图像**。
4. 保存更改。

<a name="Summary" />

## <a name="summary"></a>总结

本文介绍的所有类型的映像和 Xamarin.tvOS 应用程序中使用的大小。 首先，它包括启动映像、 分层映像、 应用程序图标、 Top Shelf 图像和游戏中心映像。 然后，它包括使用 Xamarin.tvOS 应用程序中的映像。

## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)

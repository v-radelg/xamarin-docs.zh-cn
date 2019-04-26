---
title: 在 Xamarin.Mac 中的工具栏
description: 本文介绍 Xamarin.Mac 应用程序中的工具栏。 它介绍了创建和维护工具栏在 Xcode 和 Interface Builder 中，将它们公开给代码中，并以编程方式使用它们。
ms.prod: xamarin
ms.assetid: C8D228CE-C860-47E1-85FD-69864BF91F20
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6cb17ae0f60390564a8aa6bdb64ea612aae51b55
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61294793"
---
# <a name="toolbars-in-xamarinmac"></a>在 Xamarin.Mac 中的工具栏

_本文介绍 Xamarin.Mac 应用程序中的工具栏。它介绍了创建和维护工具栏在 Xcode 和 Interface Builder 中，将它们公开给代码中，并以编程方式使用它们。_

如何使用 Visual Studio for Mac 的 Xamarin.Mac 开发人员有权向 macOS 开发人员使用 Xcode，包括工具栏控件可用的相同 UI 控件。 由于 Xamarin.Mac 与 Xcode 直接集成，则可以使用 Xcode 的 Interface Builder 来创建和维护工具栏项。 也可以在 C# 中创建这些工具栏项。

在 macOS 中的工具栏添加到一个窗口的上半部分和您可以轻松访问其功能相关的命令。 工具栏可以隐藏、 显示，或通过应用程序的用户自定义和它们可以以各种方式显示工具栏项。

本文介绍如何使用工具栏和工具栏项的 Xamarin.Mac 应用程序中的基本知识。 

在继续之前，通读[Hello，Mac](~/mac/get-started/hello-mac.md)文章 — 具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分 — 因为它介绍主要概念和将本指南中使用的技术。

此外看一看[公开 C# 类 / 方法 objective-c](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档。 它还说明了`Register`和`Export`用来连接到 OBJECTIVE-C 的类的 C# 类属性。

## <a name="introduction-to-toolbars"></a>工具栏简介

MacOS 应用程序中的任何窗口可以包括一个工具栏：

![示例窗口工具栏](toolbar-images/info01.png "示例窗口工具栏")

工具栏的应用程序的用户可以快速访问重要或常用的功能提供了一种简便方法。 例如，文档编辑的应用程序可能会提供用于设置文本颜色、 字体、 更改或打印当前文档的工具栏项。

工具栏可以以下三种方式显示项：

1. **图标和文本** 

     ![图标和文本与工具栏](toolbar-images/info02.png "带有图标和文本的工具栏")

2. **仅图标** 

     ![仅限图标的工具栏](toolbar-images/info03.png "仅限图标的工具栏")

3. **仅文本** 

     ![仅限文本的工具栏](toolbar-images/info04.png "纯文本工具栏")

通过右键单击工具栏上，从上下文菜单中选择一种显示模式这些模式之间切换：

![工具栏的上下文菜单](toolbar-images/info05.png "工具栏的上下文菜单")

使用同一个菜单工具栏显示在较小的大小：

![带有小图标的工具栏](toolbar-images/info06.png "带有小图标的工具栏")

在菜单也允许自定义工具栏：

![用于自定义工具栏对话框](toolbar-images/info07.png "用于自定义工具栏对话框")

当设置 Xcode 的 Interface Builder 中的工具栏，开发人员可以提供不是其默认配置的一部分的额外工具栏项。 用户的应用程序然后可以自定义工具栏中，添加和删除这些预定义为必需项。 当然，在工具栏可重置为其默认配置。

工具栏将自动连接到**视图**菜单中，从而允许用户将其隐藏、 显示，但和自定义它：

![在视图菜单中与工具栏相关的项](toolbar-images/info08.png "工具栏相关项视图菜单中")

请参阅[菜单上的内置功能](~/mac/user-interface/menu.md)文档了解详细信息。

此外，如果 Interface Builder 中正确配置工具栏，则该应用程序会自动将保留工具栏自定义跨多个应用程序的启动。

本指南后续部分介绍如何创建和维护与 Xcode 的 Interface Builder 的工具栏以及如何在代码中使用它们。

## <a name="setting-a-custom-main-window-controller"></a>设置自定义主窗口控制器

若要公开给 C# 代码通过输出口和操作、 Xamarin.Mac 应用的 UI 元素必须使用自定义窗口控制器：

1. 在 Xcode 的 Interface Builder 中打开应用的情节提要。
2. 选择设计图面上的窗口控制器。
3. 切换到**标识检查器**并输入"WindowController"作为**类名**: 

    [![设置窗口控制器的自定义类名称](toolbar-images/windowcontroller01.png "设置窗口控制器的自定义类名称")](toolbar-images/windowcontroller01-large.png#lightbox) 

4. 保存所做的更改并返回到 Visual Studio for Mac 进行同步。
5. 一个 **WindowController.cs** 文件将被添加到Visual Studio for Mac中 **解决方案板** 中的项目中： 

    ![在 Solution Pad 中选择 WindowController.cs](toolbar-images/windowcontroller02.png "Solution Pad 中选择 WindowController.cs")

6. 重新打开在 Xcode 的 Interface Builder 情节提要。
7. **WindowController.h**文件将可供使用： 

    [![WindowController.h 文件](toolbar-images/windowcontroller03.png "WindowController.h 文件")](toolbar-images/windowcontroller03-large.png#lightbox)

## <a name="creating-and-maintaining-toolbars-in-xcode"></a>创建和维护在 Xcode 中的工具栏

创建和维护与 Xcode 的 Interface Builder 工具栏。 若要将工具栏添加到应用程序，编辑应用程序的主要情节提要 (在这种情况下**Main.storyboard**) 中双击**Solution Pad**:

![在 Solution Pad 中打开 Main.storyboard](toolbar-images/edit01.png "在 Solution Pad 中打开 Main.storyboard")

在中**库检查器**，输入中的"工具"**搜索框**以便更轻松地查看所有可用工具栏项：

![库检查器，筛选，以显示工具栏项](toolbar-images/edit02.png "通过库检查器，筛选，以显示工具栏项")

拖动到窗口中工具栏**界面编辑器**。 使用选定的工具栏，通过设置属性来配置其行为**属性检查器**:

![属性检查器工具栏](toolbar-images/edit04.png "通过属性检查器工具栏")

以下属性可用：

1. **显示**-控制工具栏是否显示图标、 文本，或两者
2. **在启动中可见**-如果选择，工具栏将默认情况下可见。
3. **可自定义**-如果选中，用户可以编辑和自定义工具栏。
4. **分隔符**-如果选择，一条细横线区分开来工具栏窗口的内容。
5. **大小**-设置工具栏的大小
6. **自动保存**-如果选中，应用程序将持续到应用程序启动的用户的工具栏上的配置更改。

选择**自动保存**选项，并将所有其他属性保留为其默认设置。 

打开中的工具栏后**界面层次结构**，通过选择工具栏上的项目显示在自定义对话框：

![自定义工具栏](toolbar-images/edit05.png "自定义工具栏")

若要设置的工具栏中，设计应用程序，默认的工具栏中包含的项的属性并提供额外的工具栏项供用户选择自定义工具栏时，请使用此对话框。 若要将项添加到工具栏中，将其从拖**库检查器**:

![库检查器](toolbar-images/edit06.png "库检查器")

可以添加以下工具栏项：

- **图像工具栏项**-使用自定义映像为图标的工具栏项。
- **灵活的空间工具栏项**-灵活空间，用来证明后续工具栏项。 例如，一个或多个工具栏项灵活空间工具栏项后, 跟另一个工具栏项固定到右侧的工具栏上的最后一项。
- **空间工具栏项**-修复了在工具栏上的项之间的空间
- **分隔符工具栏项**-两个或多个工具栏项，用于分组之间的可见分隔符
- **自定义工具栏项**-允许用户自定义工具栏
- **打印工具栏项**-允许用户打开文档进行打印
- **显示颜色工具栏项**-显示标准系统颜色选取器： 

     ![系统颜色选取器](toolbar-images/edit07.png "系统颜色选取器")

- **显示字体工具栏项**-显示标准系统字体对话框： 

     ![字体选择器](toolbar-images/edit08.png "字体选择器")

> [!IMPORTANT]
> 将看到更高版本，但是很多标准的 Cocoa UI 控件，例如搜索字段、 分段的控件和水平滑块还可以添加到工具栏中。

### <a name="adding-an-item-to-a-toolbar"></a>将项添加到工具栏

若要将项添加到工具栏中，选择中的工具栏**界面层次结构**并单击其中一个项，从而导致出现在自定义对话框。 接下来，将从新项**库检查器**到**允许工具栏项**区域：

![在工具栏自定义对话框中的允许工具栏项](toolbar-images/add01.png "工具栏自定义对话框中的允许工具栏项")

若要确保新的项是默认的工具栏的一部分，将其拖到**默认工具栏项**区域： 

![通过拖动的工具栏项重新排序](toolbar-images/add02.png "通过拖动的工具栏项重新排序")

若要重新排序默认工具栏项，将左或向右拖动它们。

接下来，使用**属性检查器**设置项的默认属性：

![自定义使用属性检查器的工具栏项](toolbar-images/add03.png "自定义使用属性检查器的工具栏项")

以下属性可用：

- **映像名**-要用作项图标图像
- **标签**-要显示的工具栏中项的文本
- **调色板标签**-显示有关中的项的文本**允许工具栏项**区域
- **标记**-可帮助识别代码中的项的可选、 唯一标识符。
- **标识符**-定义工具栏项类型。 自定义的值可以用于选择在代码中的工具栏项。
- **可选择**-如果选中，该项将充当开/关按钮。

> [!IMPORTANT]
> 将项添加到**允许工具栏项**区域，但未默认工具栏提供的用户自定义选项。 

### <a name="adding-other-ui-controls-to-a-toolbar"></a>将其他 UI 控件添加到工具栏

多个 Cocoa UI 元素，如搜索字段和分段的控件还可以添加到工具栏。

若要尝试此操作，请打开中的工具栏**界面层次结构**和选择要打开自定义对话框中的工具栏项。 拖动**搜索字段**从**库检查器**到**允许工具栏项**区域：

![使用工具栏自定义对话框](toolbar-images/add05.png "使用工具栏自定义对话框")

在这里，使用 Interface Builder 来配置搜索字段并将其公开给代码中通过一个操作或电源插座。

## <a name="built-in-toolbar-item-support"></a>内置工具栏项支持

多个 Cocoa UI 元素默认情况下与标准工具栏项进行交互。 例如，拖动**文本视图**到应用程序的窗口上并将其以填充内容区域位置：

[![将文本视图添加到应用程序](toolbar-images/edit09.png "将文本视图添加到应用程序")](toolbar-images/edit09-large.png#lightbox)

保存文档时，返回到 Visual Studio for Mac 与 Xcode 同步，运行该应用程序中，输入一些文本，选择它，然后单击**颜色**工具栏项。 请注意，在文本视图会自动适用于颜色选取器：

![使用文本视图和颜色选取器内置工具栏功能](toolbar-images/edit10.png "内置工具栏功能与文本视图和颜色选取器")

## <a name="using-images-with-toolbar-items"></a>使用包含工具栏项的映像

使用**图像工具栏项**，任何位图图像添加到**资源**文件夹 (和给定的生成操作为**捆绑资源**) 可以显示为图标，在工具栏上：

1. 在 Visual Studio for Mac，在**Solution Pad**，右键单击**资源**文件夹，然后选择**添加** > **添加文件**.
2. 从**添加文件**对话框中，导航到所需的映像，请将其选定，单击**打开**按钮： 

    [![选择要添加的映像](toolbar-images/edit11.png "选择要添加的映像")](toolbar-images/edit11-large.png#lightbox)

3. 选择**副本**，检查**为所有选定文件使用相同的操作**，然后单击**确定**:

    ![选择添加图像的复制操作](toolbar-images/edit12.png "选择添加图像的复制操作")

4. 在中**Solution Pad**，双击**MainWindow.xib**在 Xcode 中打开它。

5. 选择中的工具栏**界面层次结构**单击的一个项以打开自定义对话框。

6. 拖动**图像工具栏项**从**库检查器**到工具栏**允许工具栏项**区域： 

    ![图像工具栏项添加到允许工具栏项区域](toolbar-images/edit14.png "图像工具栏项添加到允许工具栏项区域")

7. 在中**属性检查器**，选择刚添加在 Visual Studio for Mac 的映像： 

    ![设置工具栏项的自定义映像](toolbar-images/edit15.png "设置工具栏项的自定义映像")

8. 设置**标签**到"回收站"和**调色板标签**"擦除文档": 

    ![设置工具栏项标签和调色板标签](toolbar-images/edit16.png "标签和调色板标签设置工具栏项")

9. 拖动**分隔符工具栏项**从**库检查器**到工具栏**允许工具栏项**区域： 

    [![分隔符工具栏项添加到允许工具栏项区域](toolbar-images/edit17.png "分隔符工具栏项添加到允许工具栏项区域")](toolbar-images/edit17-large.png#lightbox)

10. 拖动到"回收站"项和分隔符项**默认工具栏项**区域，然后设置中的项的工具栏顺序从左到右 （颜色、 字体、 分隔符、 回收站、 灵活的空间，打印），按如下所示： 

    ![默认工具栏项](toolbar-images/edit18.png "默认工具栏项")

11. 保存更改并返回到 Visual Studio for Mac 与 Xcode 同步。

运行应用程序以验证新的工具栏显示默认情况下：

![带有自定义的默认项的工具栏](toolbar-images/edit19.png "带有自定义的默认项的工具栏")

## <a name="exposing-toolbar-items-with-outlets-and-actions"></a>公开与输出口和操作的工具栏项

若要访问工具栏或在代码中的工具栏项，必须将它附加到电源插座或某项操作：

1. 在中**Solution Pad**，双击**Main.storyboard**在 Xcode 中打开它。
2. 确保"WindowController"已分配到中的主窗口控制器的自定义类**标识检查器**:

    [![使用标识检查器窗口控制器设置的自定义类](toolbar-images/edit20a.png "使用标识检查器窗口控制器设置的自定义类")](toolbar-images/edit20a-large.png#lightbox)

3. 接下来，选择工具栏项处于**界面层次结构**: 

    ![选择接口层次结构中的工具栏项](toolbar-images/edit20.png "接口层次结构中选择的工具栏项")  

4. 打开**助手视图**，选择**WindowController.h**文件，并控件拖动到工具栏项从**WindowController.h**文件。
5. 设置**连接**键入到**操作**，输入"trashDocument"**名称**，然后单击**Connect**按钮： 

    [![配置工具栏项的一项操作](toolbar-images/edit23.png "配置工具栏项的操作")](toolbar-images/edit23-large.png#lightbox)

6. 公开**文本视图**作为在中称为"documentEditor"输出口**ViewController.h**文件： 

    [![配置文本视图上的电源插座](toolbar-images/edit24.png "配置文本视图输出口")](toolbar-images/edit24-large.png#lightbox)

7. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

在 Visual Studio for Mac 中，编辑**ViewController.cs**文件，并添加以下代码：

```csharp
public void EraseDocument() {
    documentEditor.Value = "";
}
```

接下来，编辑**WindowController.cs**文件，并将以下代码添加到底部`WindowController`类：

```csharp
[Export ("trashDocument:")]
void TrashDocument (NSObject sender) {

    var controller = ContentViewController as ViewController;
    controller.EraseDocument ();
}
```

运行该应用程序时**垃圾桶**工具栏项将处于活动状态：

![与 active 回收站项工具栏](toolbar-images/edit25.png "与 active 回收站项工具栏")

请注意，**垃圾桶**工具栏项现在可用于删除的文本。

## <a name="disabling-toolbar-items"></a>禁用工具栏项

若要禁用工具栏上的项，创建自定义`NSToolbarItem`类并重写`Validate`方法。 然后，Interface Builder 中为你想要启用/禁用的项中指定自定义的类型。

若要创建自定义`NSToolbarItem`类，右键单击该项目并选择**添加** > **新文件...**.选择**常规** > **空类**，输入"ActivatableItem"**名称**，然后单击**新建**按钮： 

![在 Visual Studio for Mac 添加一个空类](toolbar-images/custom01.png "for Mac 在 Visual Studio 中添加一个空类")

接下来，编辑**ActivatableItem.cs**文件中读取，如下所示：

```csharp
using System;

using Foundation;
using AppKit;

namespace MacToolbar
{
    [Register("ActivatableItem")]
    public class ActivatableItem : NSToolbarItem
    {
        public bool Active { get; set;} = true;

        public ActivatableItem ()
        {
        }

        public ActivatableItem (IntPtr handle) : base (handle)
        {
        }

        public ActivatableItem (NSObjectFlag  t) : base (t)
        {
        }

        public ActivatableItem (string title) : base (title)
        {
        }

        public override void Validate ()
        {
            base.Validate ();
            Enabled = Active;
        }
    }
}
```

双击**Main.storyboard**在 Xcode 中打开它。 选择**垃圾桶**工具栏项上面创建，并将其类更改为"ActivatableItem"，在**标识检查器**:

![设置工具栏项的自定义类](toolbar-images/custom02.png "设置工具栏项的自定义类")

创建名为输出口`trashItem`有关**回收站**工具栏项。 保存更改并返回到 Visual Studio for Mac 与 Xcode 同步。 最后，打开**了构成**，并更新`AwakeFromNib`方法来读取，如下所示：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Disable trash
    trashItem.Active = false;
}
```

运行应用程序，并请注意，**垃圾桶**项现已禁用工具栏中：

![与非活动状态的回收站项工具栏](toolbar-images/custom03.png "与非活动状态的回收站项工具栏")

## <a name="summary"></a>总结

本文已使用工具栏和 Xamarin.Mac 应用程序中的工具栏项的详细的信息。 它描述如何创建和维护 Xcode 的 Interface Builder 中的工具栏、 某些 UI 控件如何自动处理的工具栏项、 如何使用 C# 代码中中的工具栏和如何启用和禁用工具栏项。


## <a name="related-links"></a>相关链接

- [MacToolbar （示例）](https://developer.xamarin.com/samples/mac/MacToolbar/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [工具栏的人机接口指南](https://developer.apple.com/macos/human-interface-guidelines/windows-and-views/toolbars/)
- [工具栏简介](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Toolbars/Toolbars.html)

---
title: 复制并粘贴在 Xamarin.Mac 中
description: 本文介绍如何使用粘贴板提供复制并粘贴在 Xamarin.Mac 应用程序中。 它演示如何在多个应用程序和如何支持给定应用内的自定义数据之间共享的标准数据类型。
ms.prod: xamarin
ms.assetid: 7E9C99FB-B7B4-4C48-B20F-84CB48543083
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f9e05b6d16210021257fe3958966739e526aed18
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61378559"
---
# <a name="copy-and-paste-in-xamarinmac"></a>复制并粘贴在 Xamarin.Mac 中

_本文介绍如何使用粘贴板提供复制并粘贴在 Xamarin.Mac 应用程序中。它演示如何在多个应用程序和如何支持给定应用内的自定义数据之间共享的标准数据类型。_

## <a name="overview"></a>概述

在 Xamarin.Mac 应用程序中使用 C# 和.NET，必须对相同的粘贴板 （复制和粘贴） 支持使用 Objective C 中的开发人员具有的访问。

在本文中我们将介绍两种主要的方法，以在 Xamarin.Mac 应用中使用粘贴板：

1. **标准数据类型**-都不应用粘贴板操作通常会执行两个不相关的应用程序之间，由于知道的另一种支持的数据类型。 若要最大限度地共享的可能性，粘贴板可以保存多个表示形式 （使用一组标准的常见数据类型） 的给定项，这允许正在使用的应用，以选择最适合其需求的版本。
2. **自定义数据**-若要支持的复制和粘贴可以定义将由粘贴板的自定义数据类型在 Xamarin.Mac 中复杂的数据。 例如，矢量绘图应用程序，允许用户复制并粘贴由多个数据类型和点组成的复杂形状。

[![正在运行的应用程序示例](copy-paste-images/intro01.png "的正在运行的应用程序示例")](copy-paste-images/intro01-large.png#lightbox)

在本文中，我们将介绍使用 Xamarin.Mac 应用程序以支持复制和粘贴操作中粘贴板的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`属性使用绑定于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

## <a name="getting-started-with-the-pasteboard"></a>粘贴板入门

粘贴板提供标准化的机制用于在交换中给定的应用程序或应用程序之间的数据。 粘贴板 Xamarin.Mac 应用程序中的典型用途是处理复制和粘贴操作，但是很多其他操作还支持 （如拖动和删除与应用程序服务）。

可帮助你开始运行快速，我们将开始在 Xamarin.Mac 应用中使用选的简单、 实用的介绍。 更高版本，我们将提供深入讲解粘贴板的工作原理和使用的方法。

对于此示例中，我们将创建简单的文档基于应用程序管理包含的图像视图的窗口。 用户将能够复制并粘贴文档在应用程序和到或从其他应用或在同一个应用程序的多个窗口之间的映像。

### <a name="creating-the-xamarin-project"></a>创建 Xamarin 项目

首先，我们将创建新的文档基于 Xamarin.Mac 应用，我们将会添加复制和粘贴的支持。

请执行以下操作：

1. 启动 Visual Studio for Mac，然后单击**新建项目...** 链接。
2. 选择**Mac** > **应用** > **Cocoa 应用**，然后单击**下一步**按钮： 

    [![创建一个新的 Cocoa 应用程序项目](copy-paste-images/sample01.png "创建一个新的 Cocoa 应用程序项目")](copy-paste-images/sample01-large.png#lightbox)
3. 输入`MacCopyPaste`有关**项目名称**并保留所有其他默认项目。 单击下一步: 

    [![设置项目的名称](copy-paste-images/sample01a.png "设置项目的名称")](copy-paste-images/sample01a-large.png#lightbox)

4. 单击**创建**按钮： 

    [![确认新的项目设置](copy-paste-images/sample02.png "确认新的项目设置")](copy-paste-images/sample02-large.png#lightbox)

### <a name="add-an-nsdocument"></a>添加 NSDocument

接下来我们将添加自定义`NSDocument`将充当应用程序的用户界面的后台存储的类。 它将包含一个图像视图，并知道如何将图像复制到默认粘贴板视图中以及如何创建从默认粘贴板映像并将其显示在图像视图。

在 Xamarin.Mac 项目中，右键单击**Solution Pad** ，然后选择**添加** > **新文件...**:

![向项目添加 NSDocument](copy-paste-images/sample03.png "向项目添加 NSDocument")

对“名称”输入 `ImageDocument`，然后单击“新建”按钮。 编辑**ImageDocument.cs**类，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using Foundation;
using ObjCRuntime;

namespace MacCopyPaste
{
    [Register("ImageDocument")]
    public class ImageDocument : NSDocument
    {
        #region Computed Properties
        public NSImageView ImageView {get; set;}

        public ImageInfo Info { get; set; } = new ImageInfo();

        public bool ImageAvailableOnPasteboard {
            get {
                // Initialize the pasteboard
                NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
                Class [] classArray  = { new Class ("NSImage") };

                // Check to see if an image is on the pasteboard
                return pasteboard.CanReadObjectForClasses (classArray, null);
            }
        }
        #endregion

        #region Constructor
        public ImageDocument ()
        {
        }
        #endregion

        #region Public Methods
        [Export("CopyImage:")]
        public void CopyImage(NSObject sender) {

            // Grab the current image
            var image = ImageView.Image;

            // Anything to process?
            if (image != null) {
                // Get the standard pasteboard
                var pasteboard = NSPasteboard.GeneralPasteboard;

                // Empty the current contents
                pasteboard.ClearContents();

                // Add the current image to the pasteboard
                pasteboard.WriteObjects (new NSImage[] {image});

                // Save the custom data class to the pastebaord
                pasteboard.WriteObjects (new ImageInfo[] { Info });

                // Using a Pasteboard Item
                NSPasteboardItem item = new NSPasteboardItem();
                string[] writableTypes = {"public.text"};

                // Add a data provier to the item
                ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
                var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

                // Save to pasteboard
                if (ok) {
                    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
                }
            }

        }

        [Export("PasteImage:")]
        public void PasteImage(NSObject sender) {

            // Initialize the pasteboard
            NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
            Class [] classArray  = { new Class ("NSImage") };

            bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
                NSImage image = (NSImage)objectsToPaste[0];

                // Display the new image
                ImageView.Image = image;
            }

            Class [] classArray2 = { new Class ("ImageInfo") };
            ok = pasteboard.CanReadObjectForClasses (classArray2, null);
            if (ok) {
                // Read the image off of the pasteboard
                NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
                ImageInfo info = (ImageInfo)objectsToPaste[0];

            }

        }
        #endregion
    }
}
```

下面详细地看看一些代码。

下面的代码提供的属性来测试是否存在默认粘贴板上的图像数据可用，映像是否`true`else 返回`false`:

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

下面的代码会将映像复制到默认粘贴板附加的图像视图：

```csharp
[Export("CopyImage:")]
public void CopyImage(NSObject sender) {

    // Grab the current image
    var image = ImageView.Image;

    // Anything to process?
    if (image != null) {
        // Get the standard pasteboard
        var pasteboard = NSPasteboard.GeneralPasteboard;

        // Empty the current contents
        pasteboard.ClearContents();

        // Add the current image to the pasteboard
        pasteboard.WriteObjects (new NSImage[] {image});

        // Save the custom data class to the pastebaord
        pasteboard.WriteObjects (new ImageInfo[] { Info });

        // Using a Pasteboard Item
        NSPasteboardItem item = new NSPasteboardItem();
        string[] writableTypes = {"public.text"};

        // Add a data provider to the item
        ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
        var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

        // Save to pasteboard
        if (ok) {
            pasteboard.WriteObjects (new NSPasteboardItem[] { item });
        }
    }

}
```

和以下代码粘贴来自默认粘贴板的图像并将其显示在附加的图像视图 （如果粘贴板包含有效的图像）：

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0]
    }
}
```

使用本文档中的位置中，我们将创建 Xamarin.Mac 应用的用户界面。

### <a name="building-the-user-interface"></a>构建用户界面

双击**Main.storyboard**文件以在 Xcode 中打开它。 接下来，还添加一个工具栏和一个图像，并将其配置如下：

[![编辑工具栏](copy-paste-images/sample04.png "编辑工具栏")](copy-paste-images/sample04-large.png#lightbox)

添加副本，然后粘贴**图像工具栏项**到左侧和右侧的工具栏。 我们将使用那些作为快捷方式进行复制和粘贴从编辑菜单。 接下来，添加了四个**图像工具栏项**工具栏的右侧。 我们将使用这些来填充也具有一些默认图像的图像。

使用工具栏的详细信息，请参阅我们[工具栏](~/mac/user-interface/toolbar.md)文档。

接下来，让我们也公开以下输出口和操作我们工具栏项和映像：

[![创建输出口和操作](copy-paste-images/sample05.png "创建输出口和操作")](copy-paste-images/sample05-large.png#lightbox)

有关如何使用输出口和操作的详细信息，请参阅[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)一部分我们[Hello，Mac](~/mac/get-started/hello-mac.md)文档。

### <a name="enabling-the-user-interface"></a>启用用户界面

与我们在 Xcode 和我们 UI 元素通过输出口和操作中创建用户界面，我们需要添加代码以启用 UI。 双击**ImageWindow.cs**中的文件**Solution Pad**并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCopyPaste
{
    public partial class ImageWindow : NSWindow
    {
        #region Private Variables
        ImageDocument document;
        #endregion

        #region Computed Properties
        [Export ("Document")]
        public ImageDocument Document {
            get {
                return document;
            }
            set {
                WillChangeValue ("Document");
                document = value;
                DidChangeValue ("Document");
            }
        }

        public ViewController ImageViewController {
            get { return ContentViewController as ViewController; }
        }

        public NSImage Image {
            get {
                return ImageViewController.Image;
            }
            set {
                ImageViewController.Image = value;
            }
        }
        #endregion

        #region Constructor
        public ImageWindow (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create a new document instance
            Document = new ImageDocument ();

            // Attach to image view
            Document.ImageView = ImageViewController.ContentView;
        }
        #endregion

        #region Public Methods
        public void CopyImage (NSObject sender)
        {
            Document.CopyImage (sender);
        }

        public void PasteImage (NSObject sender)
        {
            Document.PasteImage (sender);
        }

        public void ImageOne (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image01.jpg");

            // Set image info
            Document.Info.Name = "city";
            Document.Info.ImageType = "jpg";
        }

        public void ImageTwo (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image02.jpg");

            // Set image info
            Document.Info.Name = "theater";
            Document.Info.ImageType = "jpg";
        }

        public void ImageThree (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image03.jpg");

            // Set image info
            Document.Info.Name = "keyboard";
            Document.Info.ImageType = "jpg";
        }

        public void ImageFour (NSObject sender)
        {
            // Load image
            Image = NSImage.ImageNamed ("Image04.jpg");

            // Set image info
            Document.Info.Name = "trees";
            Document.Info.ImageType = "jpg";
        }
        #endregion
    }
}
```

让我们看看这些代码在下方提供详细信息。

首先，我们公开的一个实例`ImageDocument`我们在上面创建的类：

```csharp
private ImageDocument _document;
...

[Export ("Document")]
public ImageDocument Document {
    get { return _document; }
    set {
        WillChangeValue ("Document");
        _document = value;
        DidChangeValue ("Document");
    }
}
```

通过使用`Export`，`WillChangeValue`并`DidChangeValue`，我们已设置`Document`属性，以允许键-值编码和 Xcode 中的数据绑定。

我们还公开映像从映像也我们已添加到我们的 UI 在 Xcode 中使用下面的属性：

```csharp
public ViewController ImageViewController {
    get { return ContentViewController as ViewController; }
}

public NSImage Image {
    get {
        return ImageViewController.Image;
    }
    set {
        ImageViewController.Image = value;
    }
}
```

当主窗口可以加载并显示时，我们创建的一个实例我们`ImageDocument`类，并将 UI 的映像附加至其与以下代码：

```csharp
public override void AwakeFromNib ()
{
    base.AwakeFromNib ();

    // Create a new document instance
    Document = new ImageDocument ();

    // Attach to image view
    Document.ImageView = ImageViewController.ContentView;
}
```

最后，在响应用户单击复制和粘贴工具栏项，我们调用的实例`ImageDocument`类来执行实际工作：

```csharp
partial void CopyImage (NSObject sender) {
    Document.CopyImage(sender);
}

partial void PasteImage (Foundation.NSObject sender) {
    Document.PasteImage(sender);
}
```

### <a name="enabling-the-file-and-edit-menus"></a>启用文件和编辑菜单

我们需要做的最后一件事是启用**新建**菜单项从**文件**菜单 （若要创建主窗口的新实例），并启用**剪切**，**复制**并**粘贴**的菜单项，从**编辑**菜单。

若要启用**新建**菜单项，编辑**AppDelegate.cs**文件，并添加以下代码：

```csharp
public int UntitledWindowCount { get; set;} =1;
...

[Export ("newDocument:")]
void NewDocument (NSObject sender) {
    // Get new window
    var storyboard = NSStoryboard.FromName ("Main", null);
    var controller = storyboard.InstantiateControllerWithIdentifier ("MainWindow") as NSWindowController;

    // Display
    controller.ShowWindow(this);

    // Set the title
    controller.Window.Title = (++UntitledWindowCount == 1) ? "untitled" : string.Format ("untitled {0}", UntitledWindowCount);
}
```

有关详细信息，请参阅[使用多个 Windows](~/mac/user-interface/window.md)一部分我们[Windows](~/mac/user-interface/window.md)文档。

若要启用**剪切**，**副本**并**粘贴**菜单项，请编辑**AppDelegate.cs**文件，并添加以下代码：

```csharp
[Export("copy:")]
void CopyImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);
}

[Export("cut:")]
void CutImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Copy the image to the clipboard
    window.Document.CopyImage (sender);

    // Clear the existing image
    window.Image = null;
}

[Export("paste:")]
void PasteImage (NSObject sender)
{
    // Get the main window
    var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;

    // Anything to do?
    if (window == null)
        return;

    // Paste the image from the clipboard
    window.Document.PasteImage (sender);
}
```

对于每个菜单项，我们获取密钥的最顶层当前窗口并将其转换为我们`ImageWindow`类：

```csharp
var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
```

从此处，我们调用`ImageDocument`该窗口来处理复制和粘贴操作的类实例。 例如： 

```csharp
window.Document.CopyImage (sender);
```

我们只想**剪切**，**副本**并**粘贴**菜单项可以访问是否存在图像数据默认粘贴板上或在图中框的当前活动窗口。

让我们添加**EditMenuDelegate.cs**到 Xamarin.Mac 项目文件，并使其看起来如下所示：

```csharp
using System;
using AppKit;

namespace MacCopyPaste
{
    public class EditMenuDelegate : NSMenuDelegate
    {
        #region Override Methods
        public override void MenuWillHighlightItem (NSMenu menu, NSMenuItem item)
        {
        }

        public override void NeedsUpdate (NSMenu menu)
        {
            // Get list of menu items
            NSMenuItem[] Items = menu.ItemArray ();

            // Get the key window and determine if the required images are available
            var window = NSApplication.SharedApplication.KeyWindow as ImageWindow;
            var hasImage = (window != null) && (window.Image != null);
            var hasImageOnPasteboard = (window != null) && window.Document.ImageAvailableOnPasteboard;

            // Process every item in the menu
            foreach(NSMenuItem item in Items) {
                // Take action based on the menu title
                switch (item.Title) {
                case "Cut":
                case "Copy":
                case "Delete":
                    // Only enable if there is an image in the view
                    item.Enabled = hasImage;
                    break;
                case "Paste":
                    // Only enable if there is an image on the pasteboard
                    item.Enabled = hasImageOnPasteboard;
                    break;
                default:
                    // Only enable the item if it has a sub menu
                    item.Enabled = item.HasSubmenu;
                    break;
                }
            }
        }
        #endregion
    }
}
```

同样，我们获取当前的最顶层窗口，并使用其`ImageDocument`类实例，以查看是否存在所需的图像数据。 然后我们使用`MenuWillHighlightItem`方法可启用或禁用每一项基于此状态。

编辑**AppDelegate.cs**文件，然后进行`DidFinishLaunching`方法看起来像以下：
 
```csharp
public override void DidFinishLaunching (NSNotification notification)
{
    // Disable automatic item enabling on the Edit menu
    EditMenu.AutoEnablesItems = false;
    EditMenu.Delegate = new EditMenuDelegate ();
}
```

首先，我们禁用自动启用和禁用编辑菜单中的菜单项。 接下来，我们将附加的实例`EditMenuDelegate`我们在上面创建的类。

有关详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)文档。

### <a name="testing-the-app"></a>测试应用程序

一切就绪后，我们已做好测试应用程序。 生成和运行应用并显示主界面：

![运行应用程序](copy-paste-images/run01.png "运行应用程序")

如果您打开编辑菜单，请注意**剪切**，**副本**并**粘贴**被禁用，因为没有图像中没有图像良好，甚至在默认粘贴板：

![打开编辑菜单](copy-paste-images/run02.png "打开编辑菜单")

如果您还将图像添加到映像，并重新打开编辑菜单，现在要启用的项目：

![显示编辑菜单项之外的所有](copy-paste-images/run03.png "显示编辑菜单项已启用")

如果复制的映像并选择**新建**您可以从文件菜单中，粘贴到新窗口中的该映像：

![将图像粘贴到新的窗口](copy-paste-images/run04.png "粘贴到新的窗口的图像")

在以下部分中，我们将使用在 Xamarin.Mac 应用程序中粘贴板的详细的信息。

## <a name="about-the-pasteboard"></a>有关粘贴板

MacOS （以前称为 OS X） 中粘贴板 (`NSPasteboard`) 提供多个服务器处理，如复制和粘贴、 拖动和删除和应用程序服务的支持。 在以下部分中，我们将详细介绍几个关键粘贴板概念。

### <a name="what-is-a-pasteboard"></a>粘贴板是什么？

`NSPasteboard`类提供标准化的机制来交换信息的应用程序之间或给定应用内。 粘贴板的主要功能是用于处理复制和粘贴操作：

1. 当用户在应用中选择某个项，并使用**剪切**或**副本**菜单项的选定项的一个或多个表示形式放置在剪贴板上。
2. 当用户使用**粘贴**菜单项 （在同一应用程序或另一个），它可以处理的数据的版本已从剪贴板复制，已添加到应用。

不太明显粘贴板用途包括查找的拖放、 拖放，和应用程序服务操作：

- 当用户启动拖动操作时，拖动数据复制到剪贴板。 如果拖动操作结束拖放到另一个应用，该应用将复制粘贴板中的数据。
- 对于翻译服务，要转换的数据复制到粘贴板由请求的应用。 应用程序服务，从剪贴板中检索数据，然后将数据重新粘贴板上执行转换。

在最简单的形式，选用于将数据在给定应用内或应用程序之间移动并因此位于应用程序的进程之外的特殊的全局内存区域。 虽然很容易被选概念 grasps，有几个必须考虑的更复杂详细信息。 这些将在下面详细介绍。

### <a name="named-pasteboards"></a>命名的选

粘贴板可以是公共或专用，并可用于多种应用程序中或多个应用间的用途。 macOS 提供了几个标准选，每个都有特定的定义完善的使用情况：

- `NSGeneralPboard` -为默认剪贴板**剪切**，**副本**并**粘贴**操作。
- `NSRulerPboard` -支持**剪切**，**副本**并**粘贴**上的操作**标尺**。
- `NSFontPboard` -支持**剪切**，**副本**并**粘贴**上的操作`NSFont`对象。
- `NSFindPboard` -支持特定于应用程序查找可以共享搜索文本的面板。
- `NSDragPboard` -支持**拖放**操作。

大多数情况下，您将使用其中一个系统定义选。 但可能会要求您创建您自己选的情况。 在这些情况下，你可以使用`FromName (string name)`方法的`NSPasteboard`类，以创建具有给定名称的自定义粘贴板。

（可选） 可以调用`CreateWithUniqueName`方法的`NSPasteboard`类，以创建唯一的命名粘贴板。

### <a name="pasteboard-items"></a>粘贴板项

每个应用程序写入粘贴板的数据片段被视为_粘贴板项_粘贴板同时又可以容纳多个项。 这样一来，应用可以写入多个版本的数据复制到剪贴板 （例如，纯文本和格式的文本） 中，并且检索应用程序可以读取关闭仅将数据 （例如仅限纯文本） 可以处理。

### <a name="data-representations-and-uniform-type-identifiers"></a>数据表示形式和统一的类型标识符

粘贴板操作通常需要两个 （或多个） 之间的应用程序无需了解每个其他或类型的数据，每个可以处理。 如上面的部分中所述，若要最大限度地共享信息，可能粘贴板可保留正在复制和粘贴的数据的多种表示形式。

每个表示形式标识通过统一类型标识符 (UTI)，这就是一个简单字符串，唯一标识显示的日期类型 (有关详细信息，请参阅 Apple 的[统一类型标识符概述](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文档)。 

如果要创建自定义数据类型 （例如，矢量图形应用程序中的绘图对象），则可以创建自己的 UTI 来唯一地标识中复制和粘贴操作。

当应用程序正在准备将数据从剪贴板复制粘贴时，它必须找到最适合其功能 （如果存在） 的表示形式。 通常，这将是最丰富的类型可用 （例如格式化文本的文字处理应用程序），回退到所需 （纯文本的形式的简单文本编辑器） 可用的最简单窗体。

<a name="Promised_Data" />

### <a name="promised-data"></a>承诺的数据

通常情况下，应提供尽可能多的要复制，以最大限度地在应用之间共享的数据的表示形式。 但是，由于时间或内存约束，可能不切合实际实际写入粘贴板每种数据类型。

在此情况下，可以将第一个数据表示形式放置在剪贴板上并接收应用程序可以请求不同的表示形式，可生成上动态之前粘贴操作。

当你将初始项放在剪贴板中时，您将指定一个或多个可用的其他表示由一个对象，符合`NSPasteboardItemDataProvider`接口。 接收应用程序的要求，这些对象将按需提供额外的表示形式。

### <a name="change-count"></a>更改计数

每个粘贴板维护_更改计数_递增每个时间的新所有者声明。 应用程序可以确定剪贴板的内容是否已更改自最后一次它会检查它通过检查的值更改计数。

使用`ChangeCount`并`ClearContents`方法的`NSPasteboard`类来修改给定粘贴板更改计数。

## <a name="copying-data-to-a-pasteboard"></a>将数据复制到剪贴板

第一个访问粘贴板中，清除任何现有内容并写入的数据粘贴到需要的任意多个表示形式来执行复制操作。

例如：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add the current image to the pasteboard
pasteboard.WriteObjects (new NSImage[] {image});
```

通常情况下，您将只写入到常规粘贴板，因为我们已经在上面的示例。 将发送到任何对象`WriteObjects`方法*必须*符合`INSPasteboardWriting`接口。 多个内置类 (如`NSString`， `NSImage`， `NSURL`， `NSColor`， `NSAttributedString`，并`NSPasteboardItem`) 自动符合此接口。

如果要将自定义数据类写入粘贴板必须遵守`INSPasteboardWriting`接口或包装在的实例`NSPasteboardItem`类 (请参阅[自定义数据类型](#Custom_Data_Types)下面一节)。

## <a name="reading-data-from-a-pasteboard"></a>从剪贴板读取数据

如上所述，若要最大化应用之间共享数据的可能性所复制数据的多种表示形式可能会写入到粘贴板。 负责接收应用程序中，选择使其功能最丰富的版本 （如果存在）。

### <a name="simple-paste-operation"></a>简单粘贴操作

粘贴板使用读取数据`ReadObjectsForClasses`方法。 它将需要两个参数：

1. 一个数组`NSObject`基于你想要读取从粘贴板的类类型。 您应这最所需的数据类型与第一次，且排序减少首选项中的任何剩余类型。
2. 一个包含其他约束 （例如，因此被限制为特定 URL 的内容类型） 的字典或如果没有其他约束所需的空字典。

该方法返回满足我们传入的条件的项的数组，并因此最多包含相同数量的请求的数据类型。 还有可能不存在任何请求的类型，并将返回空数组。

例如，下面的代码检查以查看是否`NSImage`常规粘贴板中存在并将其显示在 image well — 图像中，如果是这样：

```csharp
[Export("PasteImage:")]
public void PasteImage(NSObject sender) {

    // Initialize the pasteboard
    NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
    Class [] classArray  = { new Class ("NSImage") };

    bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
        NSImage image = (NSImage)objectsToPaste[0];

        // Display the new image
        ImageView.Image = image;
    }

    Class [] classArray2 = { new Class ("ImageInfo") };
    ok = pasteboard.CanReadObjectForClasses (classArray2, null);
    if (ok) {
        // Read the image off of the pasteboard
        NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
        ImageInfo info = (ImageInfo)objectsToPaste[0];
            }
}
```

### <a name="requesting-multiple-data-types"></a>请求多个数据类型

根据正在创建 Xamarin.Mac 应用程序的类型，它可能能够处理正在粘贴的数据的多种表示形式。 在这种情况下，有两种方案从剪贴板检索数据：

1. 执行一次调用到`ReadObjectsForClasses`方法并提供所有所需 （按首选顺序） 的表示形式的数组。
2. 多次调用`ReadObjectsForClasses`方法要求的另一个数组类型定义每个时间。

请参阅**简单粘贴操作**上面部分有关从剪贴板检索数据的详细信息。

### <a name="checking-for-existing-data-types"></a>检查现有数据类型

有时你可能想要检查是否粘贴板包含给定的数据表示形式，而实际上从剪贴板读取数据 (例如启用**粘贴**菜单项仅在有效的数据存在时)。

调用`CanReadObjectForClasses`粘贴板以查看它是否包含给定的类型的方法。

例如，以下代码确定是否包含常规粘贴板`NSImage`实例：

```csharp
public bool ImageAvailableOnPasteboard {
    get {
        // Initialize the pasteboard
        NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
        Class [] classArray  = { new Class ("NSImage") };

        // Check to see if an image is on the pasteboard
        return pasteboard.CanReadObjectForClasses (classArray, null);
    }
}
```

### <a name="reading-urls-from-the-pasteboard"></a>从剪贴板读取 url

根据给定的 Xamarin.Mac 应用的函数，可能会需要 Url 读取粘贴板，但只有当分区满足一组给定的条件 （如指向文件或 Url 的特定数据类型）。 在这种情况下，可以指定使用的第二个参数的其他搜索条件`CanReadObjectForClasses`或`ReadObjectsForClasses`方法。

<a name="Custom_Data_Types" />

## <a name="custom-data-types"></a>自定义数据类型

有些的时候您将需要 Xamarin.Mac 应用中将你自己的自定义类型保存到剪贴板。 例如，矢量图形，则允许复制和粘贴对象绘制到用户的应用。

在此情况下，你将需要设计您的数据自定义类，以便它继承自`NSObject`且符合的几个接口 (`INSCoding`，`INSPasteboardWriting`和`INSPasteboardReading`)。 （可选） 可以使用`NSPasteboardItem`封装要复制或粘贴的数据。

这两种选项将在下面详细介绍。

### <a name="using-a-custom-class"></a>使用自定义类

在本部分中我们将我们在本文档的开始处创建简单的示例应用程序扩展并添加自定义类来跟踪之间 windows 映像，我们将复制和粘贴信息。

向项目添加新类并调用其**ImageInfo.cs**。 编辑文件，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfo")]
    public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
    {
        #region Computed Properties
        [Export("name")]
        public string Name { get; set; }

        [Export("imageType")]
        public string ImageType { get; set; }
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfo ()
        {
        }
        
        public ImageInfo (IntPtr p) : base (p)
        {
        }

        [Export ("initWithCoder:")]
        public ImageInfo(NSCoder decoder) {

            // Decode data
            NSString name = decoder.DecodeObject("name") as NSString;
            NSString type = decoder.DecodeObject("imageType") as NSString;

            // Save data
            Name = name.ToString();
            ImageType = type.ToString ();
        }
        #endregion

        #region Public Methods
        [Export ("encodeWithCoder:")]
        public void EncodeTo (NSCoder encoder) {

            // Encode data
            encoder.Encode(new NSString(Name),"name");
            encoder.Encode(new NSString(ImageType),"imageType");
        }

        [Export ("writableTypesForPasteboard:")]
        public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
            string[] writableTypes = {"com.xamarin.image-info", "public.text"};
            return writableTypes;
        }

        [Export ("pasteboardPropertyListForType:")]
        public virtual NSObject GetPasteboardPropertyListForType (string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSKeyedArchiver.ArchivedDataWithRootObject(this);
            case "public.text":
                return new NSString(string.Format("{0}.{1}", Name, ImageType));
            }

            // Failure, return null
            return null;
        }

        [Export ("readableTypesForPasteboard:")]
        public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
            string[] readableTypes = {"com.xamarin.image-info", "public.text"};
            return readableTypes;
        }

        [Export ("readingOptionsForType:pasteboard:")]
        public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return NSPasteboardReadingOptions.AsKeyedArchive;
            case "public.text":
                return NSPasteboardReadingOptions.AsString;
            }

            // Default to property list
            return NSPasteboardReadingOptions.AsPropertyList;
        }

        [Export ("initWithPasteboardPropertyList:ofType:")]
        public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

            // Take action based on the requested type
            switch (type) {
            case "com.xamarin.image-info":
                return new ImageInfo();
            case "public.text":
                return new ImageInfo();
            }

            // Failure, return null
            return null;
        }
        #endregion
    }
}
    
```

以下部分中，我们将详细的介绍了此类。

#### <a name="inheritance-and-interfaces"></a>继承和接口

可以写入到自定义数据类，或将其从剪贴板读取之前，必须遵守`INSPastebaordWriting`和`INSPasteboardReading`接口。 此外，它必须继承自`NSObject`并且也符合`INSCoding`接口：

```csharp
[Register("ImageInfo")]
public class ImageInfo : NSObject, INSCoding, INSPasteboardWriting, INSPasteboardReading
...
```

类还必须公开到 Objective C 使用`Register`指令和它必须公开任何所需的属性或方法使用`Export`。 例如：

```csharp
[Export("name")]
public string Name { get; set; }

[Export("imageType")]
public string ImageType { get; set; }
```

我们将公开此类将包含数据的图像的名称和其类型 （jpg、 png 等） 的两个字段。 

有关详细信息，请参阅[公开 C# 类 / objective-c 方法](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它说明了`Register`和`Export`属性使用绑定于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

#### <a name="constructors"></a>构造函数

以便它可以从剪贴板读取则在我们的自定义数据类时将需要使用两个构造函数 （正确公开到 Objective C）：

```csharp
[Export ("init")]
public ImageInfo ()
{
}

[Export ("initWithCoder:")]
public ImageInfo(NSCoder decoder) {

    // Decode data
    NSString name = decoder.DecodeObject("name") as NSString;
    NSString type = decoder.DecodeObject("imageType") as NSString;

    // Save data
    Name = name.ToString();
    ImageType = type.ToString ();
}
```

首先，我们公开_空_构造函数下的默认 Objective C 方法`init`。

接下来，我们公开`NSCoding`符合要求的构造函数将用于从粘贴板上创建对象的新实例，导出名称下方粘贴时`initWithCoder`。

此构造函数采用`NSCoder`(如创建的`NSKeyedArchiver`写入到粘贴板时)，将提取的键/值配对的数据和将其保存到的数据类的属性字段。

#### <a name="writing-to-the-pasteboard"></a>写入粘贴板

通过符合`INSPasteboardWriting`接口，我们需要公开两种方法，和 （可选） 第三种方法，类可以写入到粘贴板。

首先，我们需要告诉粘贴板哪种数据类型的表示形式，可以写入自定义类：

```csharp
[Export ("writableTypesForPasteboard:")]
public virtual string[] GetWritableTypesForPasteboard (NSPasteboard pasteboard) {
    string[] writableTypes = {"com.xamarin.image-info", "public.text"};
    return writableTypes;
}
```

每个表示形式标识通过统一类型标识符 (UTI)，这就是一个简单字符串，唯一标识要显示的数据类型 (有关详细信息，请参阅 Apple 的[统一类型标识符概述](https://developer.apple.com/library/prerelease/mac/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_intro/understand_utis_intro.html#//apple_ref/doc/uid/TP40001319)文档)。

对于我们自定义格式，我们将创建我们自己的 UTI:"com.xamarin.image-info"（请注意，就像应用标识符反向表示法）。 我们的类也是支持的标准字符串写入粘贴板 (`public.text`)。 

接下来，我们需要以请求获取实际写入剪贴板的格式创建对象：

```csharp
[Export ("pasteboardPropertyListForType:")]
public virtual NSObject GetPasteboardPropertyListForType (string type) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSKeyedArchiver.ArchivedDataWithRootObject(this);
    case "public.text":
        return new NSString(string.Format("{0}.{1}", Name, ImageType));
    }

    // Failure, return null
    return null;
}
```

有关`public.text`类型，我们要返回一个简单、 格式化`NSString`对象。 自定义`com.xamarin.image-info`类型，我们将使用`NSKeyedArchiver`和`NSCoder`接口进行编码的键/值配对存档到自定义数据类。 我们将需要实现以下方法来实际处理的编码：

```csharp
[Export ("encodeWithCoder:")]
public void EncodeTo (NSCoder encoder) {

    // Encode data
    encoder.Encode(new NSString(Name),"name");
    encoder.Encode(new NSString(ImageType),"imageType");
}
```

单个键/值对写入到编码器，将使用我们前面添加的第二个构造函数进行解码。

根据需要，我们可以包含以下方法，以向剪贴板写入数据时定义的任何选项：

```csharp
[Export ("writingOptionsForType:pasteboard:"), CompilerGenerated]
public virtual NSPasteboardWritingOptions GetWritingOptionsForType (string type, NSPasteboard pasteboard) {
    return NSPasteboardWritingOptions.WritingPromised;
}
```

目前仅`WritingPromised`选项可用，因此应仅承诺并不实际写入粘贴板给定的类型时。 有关详细信息，请参阅[承诺数据](#Promised_Data)上面一节。

使用这些方法后，下面的代码可用于向剪贴板写入自定义类：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Empty the current contents
pasteboard.ClearContents();

// Add info to the pasteboard
pasteboard.WriteObjects (new ImageInfo[] { Info });
```

#### <a name="reading-from-the-pasteboard"></a>从剪贴板进行读取

通过符合`INSPasteboardReading`接口，我们需要公开三个方法，以便可以从剪贴板读取自定义数据类。

首先，我们需要告诉粘贴板哪种数据类型的表示形式，可以从剪贴板读取自定义类：

```csharp
[Export ("readableTypesForPasteboard:")]
public static string[] GetReadableTypesForPasteboard (NSPasteboard pasteboard){
    string[] readableTypes = {"com.xamarin.image-info", "public.text"};
    return readableTypes;
}
```

同样，这些定义为简单的 Uti 和中所定义的类型相同**写入粘贴板**上面一节。

接下来，我们需要告诉粘贴板_如何_将使用以下方法读取每个 UTI 类型：

```csharp
[Export ("readingOptionsForType:pasteboard:")]
public static NSPasteboardReadingOptions GetReadingOptionsForType (string type, NSPasteboard pasteboard) {

    // Take action based on the requested type
    switch (type) {
    case "com.xamarin.image-info":
        return NSPasteboardReadingOptions.AsKeyedArchive;
    case "public.text":
        return NSPasteboardReadingOptions.AsString;
    }

    // Default to property list
    return NSPasteboardReadingOptions.AsPropertyList;
}
```

有关`com.xamarin.image-info`类型，我们将指示粘贴板进行解码我们创建包含的键/值对`NSKeyedArchiver`类时写入粘贴板中通过调用`initWithCoder:`我们添加到类的构造函数。

最后，我们需要添加以下方法从剪贴板读取其他 UTI 数据表示形式：

```csharp
[Export ("initWithPasteboardPropertyList:ofType:")]
public NSObject InitWithPasteboardPropertyList (NSObject propertyList, string type) {

    // Take action based on the requested type
    switch (type) {
    case "public.text":
        return new ImageInfo();
    }

    // Failure, return null
    return null;
}
```

与所有这些方法中的位置，可以从使用以下代码粘贴板读取自定义数据类：

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
var classArrayPtrs = new [] { Class.GetHandle (typeof(ImageInfo)) };
NSArray classArray = NSArray.FromIntPtrs (classArrayPtrs);

// NOTE: Sending messages directly to the base Objective-C API because of this defect:
// https://bugzilla.xamarin.com/show_bug.cgi?id=31760
// Check to see if image info is on the pasteboard
ok = bool_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("canReadObjectForClasses:options:"), classArray.Handle, IntPtr.Zero);

if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = NSArray.ArrayFromHandle<Foundation.NSObject>(IntPtr_objc_msgSend_IntPtr_IntPtr (pasteboard.Handle, Selector.GetHandle ("readObjectsForClasses:options:"), classArray.Handle, IntPtr.Zero));
    ImageInfo info = (ImageInfo)objectsToPaste[0];
}
```

### <a name="using-a-nspasteboarditem"></a>使用 NSPasteboardItem

可能需要自定义项写入粘贴板，不保证创建的自定义类或你想要提供数据的一种通用格式，仅在需要时的时间。 对于这些情况下，你可以使用`NSPasteboardItem`。

一个`NSPasteboardItem`提供细粒度控制对的数据写入到粘贴板并设计用于临时访问权限-它应释放的写入到粘贴板后。

#### <a name="writing-data"></a>将数据写入

要写入到自定义数据`NSPasteboardItem`都需要提供自定义`NSPasteboardItemDataProvider`。 向项目添加新类并调用其**ImageInfoDataProvider.cs**。 编辑文件，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacCopyPaste
{
    [Register("ImageInfoDataProvider")]
    public class ImageInfoDataProvider : NSPasteboardItemDataProvider
    {
        #region Computed Properties
        public string Name { get; set;}
        public string ImageType { get; set;}
        #endregion

        #region Constructors
        [Export ("init")]
        public ImageInfoDataProvider ()
        {
        }

        public ImageInfoDataProvider (string name, string imageType)
        {
            // Initialize
            this.Name = name;
            this.ImageType = imageType;
        }

        protected ImageInfoDataProvider (NSObjectFlag t){
        }

        protected internal ImageInfoDataProvider (IntPtr handle){

        }
        #endregion

        #region Override Methods
        [Export ("pasteboardFinishedWithDataProvider:")]
        public override void FinishedWithDataProvider (NSPasteboard pasteboard)
        {
            
        }

        [Export ("pasteboard:item:provideDataForType:")]
        public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
        {

            // Take action based on the type
            switch (type) {
            case "public.text":
                // Encode the data to string 
                item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
                break;
            }

        }
        #endregion
    }
}
```

正如我们做的自定义数据类，我们需要使用`Register`和`Export`指令以将其公开到 Objective-c。 类必须继承`NSPasteboardItemDataProvider`和必须实现`FinishedWithDataProvider`和`ProvideDataForType`方法。

使用`ProvideDataForType`方法以提供将包装在数据`NSPasteboardItem`，如下所示：

```csharp
[Export ("pasteboard:item:provideDataForType:")]
public override void ProvideDataForType (NSPasteboard pasteboard, NSPasteboardItem item, string type)
{

    // Take action based on the type
    switch (type) {
    case "public.text":
        // Encode the data to string 
        item.SetStringForType(string.Format("{0}.{1}", Name, ImageType),type);
        break;
    }

}
```

在这种情况下，我们会存储有关我们的映像 （名称和映像类型） 的两条信息并写入到一个简单的字符串 (`public.text`)。

类型来将数据写入到剪贴板，并使用以下代码：

```csharp
// Get the standard pasteboard
var pasteboard = NSPasteboard.GeneralPasteboard;

// Using a Pasteboard Item
NSPasteboardItem item = new NSPasteboardItem();
string[] writableTypes = {"public.text"};

// Add a data provider to the item
ImageInfoDataProvider dataProvider = new ImageInfoDataProvider (Info.Name, Info.ImageType);
var ok = item.SetDataProviderForTypes (dataProvider, writableTypes);

// Save to pasteboard
if (ok) {
    pasteboard.WriteObjects (new NSPasteboardItem[] { item });
}
```

#### <a name="reading-data"></a>读取数据

若要从剪贴板读取数据，使用以下代码：

```csharp
// Initialize the pasteboard
NSPasteboard pasteboard = NSPasteboard.GeneralPasteboard;
Class [] classArray  = { new Class ("NSImage") };

bool ok = pasteboard.CanReadObjectForClasses (classArray, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray, null);
    NSImage image = (NSImage)objectsToPaste[0];

    // Do something with data
    ...
}
            
Class [] classArray2 = { new Class ("ImageInfo") };
ok = pasteboard.CanReadObjectForClasses (classArray2, null);
if (ok) {
    // Read the image off of the pasteboard
    NSObject [] objectsToPaste = pasteboard.ReadObjectsForClasses (classArray2, null);
    
    // Do something with data
    ...
}
```

## <a name="summary"></a>总结

本文已使用 Xamarin.Mac 应用程序以支持复制和粘贴操作中粘贴板的详细的信息。 首先，它引入了一个简单的示例来帮助你熟悉标准选操作。 接下来，花费粘贴板以及如何读取和从其写入数据的详细的信息。 最后，它介绍了使用自定义数据类型支持的复制和粘贴应用程序内的复杂数据类型。



## <a name="related-links"></a>相关链接

- [MacCopyPaste （示例）](https://developer.xamarin.com/samples/mac/MacCopyPaste/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [粘贴板编程指南](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/PasteboardGuide106/Articles/pbGettingStarted.html)
- [macOS 人机界面指南](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)

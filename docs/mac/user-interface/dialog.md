---
title: 在 Xamarin.Mac 中的对话框
description: 本文介绍如何使用对话框和模式窗口中的 Xamarin.Mac 应用程序。 它介绍如何创建在 Xcode 和 Interface builder 中，使用标准对话框，并在 C# 代码中的这些控件与之交互的模式窗口。
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: f53c1afd7c3f835bff2aae507b9937067bde4bec
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865737"
---
# <a name="dialogs-in-xamarinmac"></a>在 Xamarin.Mac 中的对话框

使用时C#和.NET 在 Xamarin.Mac 应用程序中，有权访问相同的对话框和模式的 Windows，使用的开发人员*Objective C*并*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护模式 Windows （或选择通过 C# 代码中直接创建）。

一个对话框将出现在响应用户操作，并且通常会使方法的用户可以完成该操作。 它可以关闭之前，对话框需要来自用户的响应。

Windows 可以是无模式状态 （例如可以同时打开多个文档的文本编辑器） 中使用或模式 （如应用程序可以继续操作之前必须关闭导出对话框）。

[![](dialog-images/dialog03.png "打开的对话框")](dialog-images/dialog03.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中使用对话框和模式的 Windows 的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>简介对话框

一个对话框，将出现在响应用户操作 （如保存文件） 并为用户提供方法以完成该操作。 它可以关闭之前，对话框需要来自用户的响应。

根据 Apple，有三种方法可以向用户显示：

- **记录模式**的文档模式对话框会阻止用户执行任何其他给定文档中操作，直到它关闭。
- **应用模式**-一个模式对话框可防止用户与应用程序进行交互，直到它关闭的应用。
- **无模式**无模式对话框使用户能够与文档窗口仍进行交互时更改该对话框中的设置。

### <a name="modal-window"></a>模式窗口

任何标准`NSWindow`可用作通过有模式地显示一个自定义对话框：

[![](dialog-images/modal01.png "示例模式窗口")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>文档模式对话框工作表

一个_表_是一个模式对话框，附加到给定的文档窗口，从而阻止用户交互窗口，直到他们关闭该对话框。 表将附加到从它出现，并只有一个工作表可以在任何时候打开窗口的窗口。

[![](dialog-images/sheet08.png "示例模式页面")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>首选项 Windows

首选项窗口是无模式对话框，其中包含用户不常更改的应用程序的设置。 首选项 Windows 通常包括一个工具栏，它允许用户切换不同的设置组：

[![](dialog-images/dialog02.png "示例首选项窗口")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>打开对话框

打开对话框中为用户提供一致的方法来查找和打开应用程序中的项：

[![](dialog-images/dialog03.png "打开对话框")](dialog-images/dialog03.png#lightbox)


### <a name="print-and-page-setup-dialogs"></a>打印和页面设置对话框

macOS 提供标准打印和页面设置对话框，你的应用程序可以显示，以便用户可以拥有一致的打印体验它们使用每个应用程序中。

可以为这两个免费浮动的对话框中显示打印对话框：

[![](dialog-images/print01.png "打印对话框")](dialog-images/print01.png#lightbox)

或者，它可以显示工作表的形式：

[![](dialog-images/print02.png "打印工作表")](dialog-images/print02.png#lightbox)

可以为这两个免费浮动的对话框中显示页面设置对话框：

[![](dialog-images/print03.png "页面设置对话框")](dialog-images/print03.png#lightbox)

或者，它可以显示工作表的形式：

[![](dialog-images/print04.png "页设置表")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>保存对话框

保存对话框中为用户提供一致的方法来保存应用程序中的项。 保存对话框中有两种状态：**最小**（也称为折叠）：

[![](dialog-images/save01.png "一个保存对话框")](dialog-images/save01.png#lightbox)

并**扩展**状态：

[![](dialog-images/save02.png "展开保存对话框")](dialog-images/save02.png#lightbox)

**最小**保存对话框还可以显示为一个工作表：

[![](dialog-images/save03.png "保存工作表的最小")](dialog-images/save03.png#lightbox)

因为可以**扩展**保存对话框：

[![](dialog-images/save04.png "一个扩展保存工作表")](dialog-images/save04.png#lightbox)

有关详细信息，请参阅[对话框](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1)部分中的 Apple 的[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>向项目添加一个模式窗口

主文档窗口中，除了 Xamarin.Mac 应用程序可能需要向用户，如首选项或检查器面板显示其他类型的窗口。

若要添加新的窗口，请执行以下操作：

1. 在中**解决方案资源管理器**，打开`Main.storyboard`文件在 Xcode 的 Interface Builder 中进行编辑。
2. 将一个新**视图控制器**到设计图面：

    [![](dialog-images/new01.png "从库中选择视图控制器")](dialog-images/new01.png#lightbox)
3. 在中**标识检查器**，输入`CustomDialogController`有关**类名**: 

    [![](dialog-images/new02.png "设置类名称")](dialog-images/new02.png#lightbox)
4. 切换回 Visual Studio for Mac，允许它与 Xcode 同步并创建`CustomDialogController.h`文件。
5. 返回到 Xcode，并设计你的接口： 

    [![](dialog-images/new03.png "设计在 Xcode 中的 UI")](dialog-images/new03.png#lightbox)
6. 创建**模式 Segue**从新的视图控制器，通过将控件拖到将应用程序，将打开到对话框的窗口的对话框的 UI 元素从主窗口。 将分配**标识符** `ModalSegue`: 

    [![](dialog-images/new06.png "模式的 segue")](dialog-images/new06.png#lightbox)
7. 布置任何**操作**并**输出口**: 

    [![](dialog-images/new04.png "配置操作")](dialog-images/new04.png#lightbox)
8. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

使`CustomDialogController.cs`文件外观如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class CustomDialogController : NSViewController
    {
        #region Private Variables
        private string _dialogTitle = "Title";
        private string _dialogDescription = "Description";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string DialogTitle {
            get { return _dialogTitle; }
            set { _dialogTitle = value; }
        }

        public string DialogDescription {
            get { return _dialogDescription; }
            set { _dialogDescription = value; }
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public CustomDialogController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial title and description
            Title.StringValue = DialogTitle;
            Description.StringValue = DialogDescription;
        }
        #endregion

        #region Private Methods
        private void CloseDialog() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptDialog (Foundation.NSObject sender) {
            RaiseDialogAccepted();
            CloseDialog();
        }

        partial void CancelDialog (Foundation.NSObject sender) {
            RaiseDialogCanceled();
            CloseDialog();
        }
        #endregion

        #region Events
        public EventHandler DialogAccepted;

        internal void RaiseDialogAccepted() {
            if (this.DialogAccepted != null)
                this.DialogAccepted (this, EventArgs.Empty);
        }

        public EventHandler DialogCanceled;

        internal void RaiseDialogCanceled() {
            if (this.DialogCanceled != null)
                this.DialogCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

此代码会公开一些属性来设置标题和说明的对话框和几个事件以响应为正在取消或已接受对话框。

接下来，编辑`ViewController.cs`文件中，重写`PrepareForSegue`方法，并使其如下所示：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    }
}
```

此代码初始化 Xcode 的 Interface Builder 中所定义到我们对话框 segue 并设置了标题和说明。 它还处理用户在对话框中进行的选择。

我们可以运行我们的应用程序，并显示自定义对话框：

[![](dialog-images/new05.png "示例对话框")](dialog-images/new05.png#lightbox)

有关使用 windows 中的 Xamarin.Mac 应用程序的详细信息，请参阅我们[使用 Windows](~/mac/user-interface/window.md)文档。

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>创建自定义工作表

一个_表_是一个模式对话框，附加到给定的文档窗口，从而阻止用户交互窗口，直到他们关闭该对话框。 表将附加到从它出现，并只有一个工作表可以在任何时候打开窗口的窗口。 

若要在 Xamarin.Mac 中创建自定义表单，让我们执行以下操作：

1. 在中**解决方案资源管理器**，打开`Main.storyboard`文件在 Xcode 的 Interface Builder 中进行编辑。
2. 将一个新**视图控制器**到设计图面：

    [![](dialog-images/new01.png "从库中选择视图控制器")](dialog-images/new01.png#lightbox)
3. 设计用户界面：

    [![](dialog-images/sheet01.png "UI 设计")](dialog-images/sheet01.png#lightbox)
4. 创建**表 Segue**从主窗口到新的视图控制器： 

    [![](dialog-images/sheet02.png "选择表 segue 类型")](dialog-images/sheet02.png#lightbox)
5. 在中**标识检查器**，命名为视图控制器**类** `SheetViewController`: 

    [![](dialog-images/sheet03.png "设置类名称")](dialog-images/sheet03.png#lightbox)
6. 定义任何所需**输出口**并**操作**: 

    [![](dialog-images/sheet04.png "定义所需的输出口和操作")](dialog-images/sheet04.png#lightbox)
7. 保存所做的更改并返回到 Visual Studio for Mac 进行同步。

接下来，编辑`SheetViewController.cs`文件，并使其看起来如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDialog
{
    public partial class SheetViewController : NSViewController
    {
        #region Private Variables
        private string _userName = "";
        private string _password = "";
        private NSViewController _presentor;
        #endregion

        #region Computed Properties
        public string UserName {
            get { return _userName; }
            set { _userName = value; }
        }

        public string Password {
            get { return _password;}
            set { _password = value;}
        }

        public NSViewController Presentor {
            get { return _presentor; }
            set { _presentor = value; }
        }
        #endregion

        #region Constructors
        public SheetViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear ()
        {
            base.ViewWillAppear ();

            // Set initial values
            NameField.StringValue = UserName;
            PasswordField.StringValue = Password;

            // Wireup events
            NameField.Changed += (sender, e) => {
                UserName = NameField.StringValue;
            };
            PasswordField.Changed += (sender, e) => {
                Password = PasswordField.StringValue;
            };
        }
        #endregion

        #region Private Methods
        private void CloseSheet() {
            Presentor.DismissViewController (this);
        }
        #endregion

        #region Custom Actions
        partial void AcceptSheet (Foundation.NSObject sender) {
            RaiseSheetAccepted();
            CloseSheet();
        }

        partial void CancelSheet (Foundation.NSObject sender) {
            RaiseSheetCanceled();
            CloseSheet();
        }
        #endregion

        #region Events
        public EventHandler SheetAccepted;

        internal void RaiseSheetAccepted() {
            if (this.SheetAccepted != null)
                this.SheetAccepted (this, EventArgs.Empty);
        }

        public EventHandler SheetCanceled;

        internal void RaiseSheetCanceled() {
            if (this.SheetCanceled != null)
                this.SheetCanceled (this, EventArgs.Empty);
        }
        #endregion
    }
}
```

接下来，编辑`ViewController.cs`文件中，编辑`PrepareForSegue`方法，并使其如下所示：

```csharp
public override void PrepareForSegue (NSStoryboardSegue segue, NSObject sender)
{
    base.PrepareForSegue (segue, sender);

    // Take action based on the segue name
    switch (segue.Identifier) {
    case "ModalSegue":
        var dialog = segue.DestinationController as CustomDialogController;
        dialog.DialogTitle = "MacDialog";
        dialog.DialogDescription = "This is a sample dialog.";
        dialog.DialogAccepted += (s, e) => {
            Console.WriteLine ("Dialog accepted");
            DismissViewController (dialog);
        };
        dialog.Presentor = this;
        break;
    case "SheetSegue":
        var sheet = segue.DestinationController as SheetViewController;
        sheet.SheetAccepted += (s, e) => {
            Console.WriteLine ("User Name: {0} Password: {1}", sheet.UserName, sheet.Password);
        };
        sheet.Presentor = this;
        break;
    }
}
```

如果我们运行我们的应用程序，并打开工作表，它将附加到窗口：

[![](dialog-images/sheet08.png "示例表")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>创建首选项对话框

我们设置 Interface Builder 中的首选项视图布局之前，我们需要添加自定义的 segue 类型来处理切出首选项。 向项目添加一个新类，调用它`ReplaceViewSeque`。 编辑类，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using Foundation;

namespace MacWindows
{
    [Register("ReplaceViewSeque")]
    public class ReplaceViewSeque : NSStoryboardSegue
    {
        #region Constructors
        public ReplaceViewSeque() {

        }

        public ReplaceViewSeque (string identifier, NSObject sourceController, NSObject destinationController) : base(identifier,sourceController,destinationController) {

        }

        public ReplaceViewSeque (IntPtr handle) : base(handle) {
        }

        public ReplaceViewSeque (NSObjectFlag x) : base(x) {
        }
        #endregion

        #region Override Methods
        public override void Perform ()
        {
            // Cast the source and destination controllers
            var source = SourceController as NSViewController;
            var destination = DestinationController as NSViewController;

            // Is there a source?
            if (source == null) {
                // No, get the current key window
                var window = NSApplication.SharedApplication.KeyWindow;

                // Swap the controllers
                window.ContentViewController = destination;

                // Release memory
                window.ContentViewController?.RemoveFromParentViewController ();
            } else {
                // Swap the controllers
                source.View.Window.ContentViewController = destination;

                // Release memory
                source.RemoveFromParentViewController ();
            }
        
        }
        #endregion

    }

}
```

创建自定义 segue，我们可以在 Xcode 的 Interface Builder 来处理我们首选项中添加一个新的窗口。

若要添加新的窗口，请执行以下操作：

1. 在中**解决方案资源管理器**，打开`Main.storyboard`文件在 Xcode 的 Interface Builder 中进行编辑。
2. 将一个新**窗口控制器**到设计图面：

    [![](dialog-images/pref01.png "从库中选择窗口控制器")](dialog-images/pref01.png#lightbox)
3. 排列窗口附近**菜单栏**设计器：

    [![](dialog-images/pref02.png "添加新的窗口")](dialog-images/pref02.png#lightbox)
4. 创建附加的视图控制器的副本，因为在首选项视图中将选项卡：

    [![](dialog-images/pref03.png "添加所需的视图控制器")](dialog-images/pref03.png#lightbox)
5. 将一个新**工具栏控制器**从**库**:

    [![](dialog-images/pref04.png "从库中选择工具栏控制器")](dialog-images/pref04.png#lightbox)
6. 并将其放在设计图面中的窗口上：

    [![](dialog-images/pref05.png "添加新的工具栏控制器")](dialog-images/pref05.png#lightbox)
7. 布局的设计您的工具栏：

    [![](dialog-images/pref06.png "布局工具栏")](dialog-images/pref06.png#lightbox)
8. 单击并从每个拖动**工具栏按钮**到上面创建的视图。 选择**自定义**segue 类型：

    [![](dialog-images/pref07.png "设置 segue 类型")](dialog-images/pref07.png#lightbox)
9. 选择新的 Segue 并设置**类**到`ReplaceViewSegue`:

    [![](dialog-images/pref08.png "设置 segue 类")](dialog-images/pref08.png#lightbox)
10. 在中**设计器菜单栏**设计图面上，从应用程序菜单中选择**首选项...** 、 单击和拖动到首选项窗口来创建**显示**segue:

    [![](dialog-images/pref09.png "设置 segue 类型")](dialog-images/pref09.png#lightbox)
11. 保存所做的更改并返回到 Visual Studio for Mac 进行同步。

如果我们运行代码，并选择**首选项...** 从**应用程序菜单**，此时将显示窗口：

[![](dialog-images/pref10.png "示例首选项窗口")](dialog-images/pref10.png#lightbox)

有关使用 Windows 和工具栏的详细信息，请参阅我们[Windows](~/mac/user-interface/window.md)并[工具栏](~/mac/user-interface/toolbar.md)文档。

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>保存和加载首选项

在典型 macOS 应用，在用户做出的更改到任何应用程序的用户首选项，这些更改将自动保存。 在 Xamarin.Mac 应用中，处理此的最简单方法是创建系统范围内的单个类中以管理所有用户的首选项并将其共享。

首先，添加一个新`AppPreferences`向项目中类和继承`NSObject`。 首选项将设计为使用[数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)维护首选项窗体变得更简单和这会使创建的过程。 首选项将包含少量的简单数据类型的因为使用内置的`NSUserDefaults`来存储和检索值。

编辑`AppPreferences.cs`文件，并使其看起来如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    [Register("AppPreferences")]
    public class AppPreferences : NSObject
    {
        #region Computed Properties
        [Export("DefaultLanguage")]
        public int DefaultLanguage {
            get { 
                var value = LoadInt ("DefaultLanguage", 0);
                return value; 
            }
            set {
                WillChangeValue ("DefaultLanguage");
                SaveInt ("DefaultLanguage", value, true);
                DidChangeValue ("DefaultLanguage");
            }
        }

        [Export("SmartLinks")]
        public bool SmartLinks {
            get { return LoadBool ("SmartLinks", true); }
            set {
                WillChangeValue ("SmartLinks");
                SaveBool ("SmartLinks", value, true);
                DidChangeValue ("SmartLinks");
            }
        }

        // Define any other required user preferences in the same fashion
        ...

        [Export("EditorBackgroundColor")]
        public NSColor EditorBackgroundColor {
            get { return LoadColor("EditorBackgroundColor", NSColor.White); }
            set {
                WillChangeValue ("EditorBackgroundColor");
                SaveColor ("EditorBackgroundColor", value, true);
                DidChangeValue ("EditorBackgroundColor");
            }
        }
        #endregion

        #region Constructors
        public AppPreferences ()
        {
        }
        #endregion

        #region Public Methods
        public int LoadInt(string key, int defaultValue) {
            // Attempt to read int
            var number = NSUserDefaults.StandardUserDefaults.IntForKey(key);

            // Take action based on value
            if (number == null) {
                return defaultValue;
            } else {
                return (int)number;
            }
        }
            
        public void SaveInt(string key, int value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetInt(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public bool LoadBool(string key, bool defaultValue) {
            // Attempt to read int
            var value = NSUserDefaults.StandardUserDefaults.BoolForKey(key);

            // Take action based on value
            if (value == null) {
                return defaultValue;
            } else {
                return value;
            }
        }

        public void SaveBool(string key, bool value, bool sync) {
            NSUserDefaults.StandardUserDefaults.SetBool(value, key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }

        public string NSColorToHexString(NSColor color, bool withAlpha) {
            //Break color into pieces
            nfloat red=0, green=0, blue=0, alpha=0;
            color.GetRgba (out red, out green, out blue, out alpha);

            // Adjust to byte
            alpha *= 255;
            red *= 255;
            green *= 255;
            blue *= 255;

            //With the alpha value?
            if (withAlpha) {
                return String.Format ("#{0:X2}{1:X2}{2:X2}{3:X2}", (int)alpha, (int)red, (int)green, (int)blue);
            } else {
                return String.Format ("#{0:X2}{1:X2}{2:X2}", (int)red, (int)green, (int)blue);
            }
        }

        public NSColor NSColorFromHexString (string hexValue)
        {
            var colorString = hexValue.Replace ("#", "");
            float red, green, blue, alpha;

            // Convert color based on length
            switch (colorString.Length) {
            case 3 : // #RGB
                red = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(0, 1)), 16) / 255f;
                green = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(1, 1)), 16) / 255f;
                blue = Convert.ToInt32(string.Format("{0}{0}", colorString.Substring(2, 1)), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 6 : // #RRGGBB
                red = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, 1.0f);
            case 8 : // #AARRGGBB
                alpha = Convert.ToInt32(colorString.Substring(0, 2), 16) / 255f;
                red = Convert.ToInt32(colorString.Substring(2, 2), 16) / 255f;
                green = Convert.ToInt32(colorString.Substring(4, 2), 16) / 255f;
                blue = Convert.ToInt32(colorString.Substring(6, 2), 16) / 255f;
                return NSColor.FromRgba(red, green, blue, alpha);
            default :
                throw new ArgumentOutOfRangeException(string.Format("Invalid color value '{0}'. It should be a hex value of the form #RBG, #RRGGBB or #AARRGGBB", hexValue));
            }
        }

        public NSColor LoadColor(string key, NSColor defaultValue) {

            // Attempt to read color
            var hex = NSUserDefaults.StandardUserDefaults.StringForKey(key);

            // Take action based on value
            if (hex == null) {
                return defaultValue;
            } else {
                return NSColorFromHexString (hex);
            }
        }

        public void SaveColor(string key, NSColor color, bool sync) {
            // Save to default
            NSUserDefaults.StandardUserDefaults.SetString(NSColorToHexString(color,true), key);
            if (sync) NSUserDefaults.StandardUserDefaults.Synchronize ();
        }
        #endregion
    }
}
```

此类包含几个帮助器例程，如`SaveInt`， `LoadInt`， `SaveColor`， `LoadColor`，等等，以便使`NSUserDefaults`更容易。 此外，由于`NSUserDefaults`不具有内置的方法来处理`NSColors`，则`NSColorToHexString`并`NSColorFromHexString`方法用于将颜色转换为基于 web 的十六进制字符串 (`#RRGGBBAA`其中`AA`是 alpha 透明度)，您可能轻松存储和检索。

在中`AppDelegate.cs`文件中，创建的实例**AppPreferences**将使用应用程序范围的对象：

```csharp
using AppKit;
using Foundation;
using System.IO;
using System;

namespace SourceWriter
{
    [Register ("AppDelegate")]
    public class AppDelegate : NSApplicationDelegate
    {
        #region Computed Properties
        public int NewWindowNumber { get; set;} = -1;

        public AppPreferences Preferences { get; set; } = new AppPreferences();
        #endregion

        #region Constructors
        public AppDelegate ()
        {
        }
        #endregion
        
        ...
```

<a name="Wiring-Preferences-to-Preference-Views" />

### <a name="wiring-preferences-to-preference-views"></a>连接到首选视图首选项

接下来，连接到的首选项窗口和上面创建的视图上的 UI 元素的首选项类。 在接口生成器中，选择首选项的视图控制器，然后切换到**标识检查器**，创建控制器的自定义类： 

[![](dialog-images/prefs12.png "标识检查器")](dialog-images/prefs12.png#lightbox)

切换回 Visual Studio for Mac 同步所做的更改并打开新创建的类以进行编辑。 使该类如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class EditorPrefsController : NSViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        [Export("Preferences")]
        public AppPreferences Preferences {
            get { return App.Preferences; }
        }
        #endregion

        #region Constructors
        public EditorPrefsController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

请注意，此类已完成两件事情：首先，没有一个帮助程序`App`属性以使访问**AppDelegate**更容易。 第二个，`Preferences`属性公开的全局**AppPreferences**类的数据绑定与任何 UI 控件放置在此视图上。

接下来，双击该情节提要文件以在 Interface Builder 中重新打开 （请参阅只是上面所做的更改）。 将拖到视图生成的首选项接口所需的任何 UI 控件。 每个控件，切换到**绑定检查器**并将绑定到的各个属性**AppPreference**类：

[![](dialog-images/prefs13.png "绑定检查器")](dialog-images/prefs13.png#lightbox)

所有面板 （视图控制器） 重复上述步骤和要求首选项属性。

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>应用首选项更改为所有打开的 Windows

如上面所述，在典型的 macOS 应用，当用户对任何应用程序的用户首选项，这些更改进行更改时自动保存并应用于任何 windows 用户可能在应用程序中打开。

仔细的规划和设计的应用程序的首选项和 windows 将允许此过程发生这种情况平稳地以透明方式向最终用户，使用最少的编码工作。

对于任何窗口中，将使用应用程序首选项，将以下帮助程序属性添加到其内容的视图控制器，以使访问我们**AppDelegate**更容易：

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

接下来，添加用于配置的内容或基于用户的首选项的行为的类：

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

需要调用配置方法，在窗口第一次打开以确保它符合用户的首选项：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

接下来，编辑`AppDelegate.cs`文件，并添加以下方法，以应用任何首选项更改为所有打开的窗口：

```csharp
public void UpdateWindowPreferences() {

    // Process all open windows
    for(int n=0; n<NSApplication.SharedApplication.Windows.Length; ++n) {
        var content = NSApplication.SharedApplication.Windows[n].ContentViewController as ViewController;
        if (content != null ) {
            // Reformat all text
            content.ConfigureEditor ();
        }
    }

}
```

接下来，添加`PreferenceWindowDelegate`类到项目，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using System.IO;
using Foundation;

namespace SourceWriter
{
    public class PreferenceWindowDelegate : NSWindowDelegate
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public NSWindow Window { get; set;}
        #endregion

        #region constructors
        public PreferenceWindowDelegate (NSWindow window)
        {
            // Initialize
            this.Window = window;

        }
        #endregion

        #region Override Methods
        public override bool WindowShouldClose (Foundation.NSObject sender)
        {
            
            // Apply any changes to open windows
            App.UpdateWindowPreferences();

            return true;
        }
        #endregion
    }
}
```

这会导致任何首选项更改首选项窗口关闭时要发送到所有打开的 Windows。

最后，编辑首选项窗口控制器，并添加上面创建的委托：

```csharp
using System;
using Foundation;
using AppKit;

namespace SourceWriter
{
    public partial class PreferenceWindowController : NSWindowController
    {
        #region Constructors
        public PreferenceWindowController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void WindowDidLoad ()
        {
            base.WindowDidLoad ();

            // Initialize
            Window.Delegate = new PreferenceWindowDelegate(Window);
            Toolbar.SelectedItemIdentifier = "General";
        }
        #endregion
    }
}
```

与所有这些更改后，如果用户编辑应用程序的首选项并关闭首选项窗口中，所做的更改将应用于所有打开的 Windows:

[![](dialog-images/prefs14.png "示例首选项窗口")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>打开对话框

打开对话框中为用户提供一致的方法来查找和打开应用程序中的项。 若要在 Xamarin.Mac 应用程序中显示打开的对话框，请使用以下代码：

```csharp
var dlg = NSOpenPanel.OpenPanel;
dlg.CanChooseFiles = true;
dlg.CanChooseDirectories = false;
dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

if (dlg.RunModal () == 1) {
    // Nab the first file
    var url = dlg.Urls [0];

    if (url != null) {
        var path = url.Path;

        // Create a new window to hold the text
        var newWindowController = new MainWindowController ();
        newWindowController.Window.MakeKeyAndOrderFront (this);

        // Load the text into the window
        var window = newWindowController.Window as MainWindow;
        window.Text = File.ReadAllText(path);
        window.SetTitleWithRepresentedFilename (Path.GetFileName(path));
        window.RepresentedUrl = url;

    }
}
```

在上述代码中，我们要打开新的文档窗口，显示文件的内容。 将需要替换此代码与功能所需的应用程序。

使用时，以下属性都是可用`NSOpenPanel`:

- **CanChooseFiles** -如果`true`用户可以选择的文件。
- **CanChooseDirectories** -如果`true`用户可以选择目录。
- **AllowsMultipleSelection** -如果`true`用户可以一次选择多个文件。
- **ResolveAliases** -如果`true`选择和别名，则将其解析为原始文件的路径。
- **AllowedFileTypes** -是的用户可以选择作为任一扩展的文件类型的字符串数组或_UTI_。 默认值是`null`，这允许要打开任何文件。

`RunModal ()`方法会显示打开对话框并允许用户选择文件或目录 （如由属性指定） 并返回`1`如果用户单击**打开**按钮。

打开对话框中的 Url 的数组形式返回用户的所选的文件或目录`URL`属性。

如果我们运行程序并选择**打开...** 项从**文件**菜单中，以下显示： 

[![](dialog-images/dialog03.png "打开的对话框")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>打印和页面设置对话框

macOS 提供标准打印和页面设置对话框，你的应用程序可以显示，以便用户可以拥有一致的打印体验它们使用每个应用程序中。

下面的代码将显示标准打印对话框：

```csharp
public bool ShowPrintAsSheet { get; set;} = true;
...

[Export ("showPrinter:")]
void ShowDocument (NSObject sender) {
    var dlg = new NSPrintPanel();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet(new NSPrintInfo(),this,this,null,new IntPtr());
    } else {
        if (dlg.RunModalWithPrintInfo(new NSPrintInfo()) == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}

```

如果我们设置`ShowPrintAsSheet`属性设置为`false`，运行该应用程序并显示打印对话框中，将显示以下：

[![](dialog-images/print01.png "打印对话框")](dialog-images/print01.png#lightbox)

如果设置`ShowPrintAsSheet`属性设置为`true`，运行该应用程序并显示打印对话框中，将显示以下：

[![](dialog-images/print02.png "打印工作表")](dialog-images/print02.png#lightbox)

以下代码将显示页面布局对话框：

```csharp
[Export ("showLayout:")]
void ShowLayout (NSObject sender) {
    var dlg = new NSPageLayout();

    // Display the print dialog as dialog box
    if (ShowPrintAsSheet) {
        dlg.BeginSheet (new NSPrintInfo (), this);
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to print the document here...",
                MessageText = "Print Document",
            };
            alert.RunModal ();
        }
    }
}
```

如果我们设置`ShowPrintAsSheet`属性设置为`false`，运行该应用程序并显示打印布局对话框中，将显示以下：

[![](dialog-images/print03.png "页面设置对话框")](dialog-images/print03.png#lightbox)

如果设置`ShowPrintAsSheet`属性设置为`true`，运行该应用程序并显示打印布局对话框中，将显示以下：

[![](dialog-images/print04.png "页设置表")](dialog-images/print04.png#lightbox)

有关如何使用打印和页面设置对话框的详细信息，请参阅 Apple [NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092)， [NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080)并[简介打印](http://sdg.mesonet.org/people/brad/XCode3/Documentation/DocSets/com.apple.adc.documentation.AppleSnowLeopard.CoreReference.docset/Contents/Resources/Documents/#documentation/Cocoa/Conceptual/Printing/Printing.html#//apple_ref/doc/uid/10000083-SW1)文档。

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>保存对话框

保存对话框中为用户提供一致的方法来保存应用程序中的项。

下面的代码将显示标准的保存对话框：

```csharp
public bool ShowSaveAsSheet { get; set;} = true;
...

[Export("saveDocumentAs:")]
void ShowSaveAs (NSObject sender)
{
    var dlg = new NSSavePanel ();
    dlg.Title = "Save Text File";
    dlg.AllowedFileTypes = new string[] { "txt", "html", "md", "css" };

    if (ShowSaveAsSheet) {
        dlg.BeginSheet(mainWindowController.Window,(result) => {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        });
    } else {
        if (dlg.RunModal () == 1) {
            var alert = new NSAlert () {
                AlertStyle = NSAlertStyle.Critical,
                InformativeText = "We need to save the document here...",
                MessageText = "Save Document",
            };
            alert.RunModal ();
        }
    }

}
```

`AllowedFileTypes`属性是用户可以选择将保存为文件的文件类型的字符串数组。 可以作为扩展或者指定的文件类型或_UTI_。 默认值是`null`，它允许使用任何文件类型。

如果我们设置`ShowSaveAsSheet`属性设置为`false`，运行该应用程序并选择**另存为...** 从**文件**菜单中，将显示以下：

[![](dialog-images/save01.png "一个保存对话框")](dialog-images/save01.png#lightbox)

用户可以展开对话框中：

[![](dialog-images/save02.png "展开保存对话框")](dialog-images/save02.png#lightbox)

如果我们设置`ShowSaveAsSheet`属性设置为`true`，运行该应用程序并选择**另存为...** 从**文件**菜单中，将显示以下：

[![](dialog-images/save03.png "保存工作表")](dialog-images/save03.png#lightbox)

用户可以展开对话框中：

[![](dialog-images/save04.png "一个扩展保存工作表")](dialog-images/save04.png#lightbox)

有关使用保存对话框中的详细信息，请参阅 Apple [NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098)文档。

<a name="Summary" />

## <a name="summary"></a>总结

本文已使用模式的 Windows、 表和 Xamarin.Mac 应用程序中的标准系统对话框的详细的信息。 我们已了解不同的类型和使用模式的 Windows、 表和对话，如何创建和维护模式的 Windows 和表在 Xcode 的 Interface Builder 和如何使用模式的 Windows，表和 C# 代码中的对话。

## <a name="related-links"></a>相关链接

- [MacWindows （示例）](https://developer.xamarin.com/samples/mac/MacWindows/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [菜单](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [工具栏](~/mac/user-interface/toolbar.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [表的简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [打印简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)

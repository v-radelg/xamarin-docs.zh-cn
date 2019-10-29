---
title: Xamarin 中的对话框
description: 本文介绍如何在 Xamarin. Mac 应用程序中使用对话框和模式窗口。 它介绍了如何在 Xcode 和 Interface builder 中创建模式窗口，如何处理标准对话框，以及如何在C#代码中与这些控件交互。
ms.prod: xamarin
ms.assetid: 55451990-B77B-4D44-B8BB-F874EC503B0C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: a50445307156fc051edbab7abaea6b7bd21aa1fd
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032836"
---
# <a name="dialogs-in-xamarinmac"></a>Xamarin 中的对话框

在 Xamarin Mac C#应用*程序中使用*和 .net 时，可以访问在 Xcode 和中工作的开发人员所使用的相同对话框和模式窗口。 由于 Xamarin 与 Xcode 直接集成，因此可以使用 Xcode 的_Interface Builder_来创建和维护模式窗口（或者在代码中C#直接创建）。

将显示一个对话框，以响应用户操作，并通常提供用户完成该操作的方法。 对话框需要用户的响应才能关闭。

Windows 可以在无模式状态（如可以同时打开多个文档的文本编辑器）或模式（例如，在应用程序可以继续之前必须关闭的 "导出" 对话框）中使用。

[![](dialog-images/dialog03.png "An open dialog box")](dialog-images/dialog03.png#lightbox)

在本文中，我们将介绍在 Xamarin. Mac 应用程序中使用对话框和模式窗口的基本知识。 强烈建议您先完成[Hello，Mac](~/mac/get-started/hello-mac.md)一文，特别是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)及[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分的简介，因为它涵盖了我们将在本文。

你可能想要查看[Xamarin](~/mac/internals/how-it-works.md)示例文档的 " C# [公开C#类/方法到目标-c](~/mac/internals/how-it-works.md) " 部分，并说明用于将类连接到目标的`Register`和`Export`命令-c对象和 UI 元素。

<a name="Introduction_to_Dialogs" />

## <a name="introduction-to-dialogs"></a>对话框简介

将显示一个对话框，以响应用户操作（如保存文件），并为用户提供完成该操作的方法。 对话框需要用户的响应才能关闭。

根据 Apple，有三种方法可提供对话：

- **文档模式**-文档模式对话框阻止用户在给定文档中执行任何其他操作，直到它被消除。
- **应用模式**-应用模式对话框可防止用户与应用程序进行交互，直到它被消除。
- **无模式**无模式对话框使用户能够更改对话框中的设置，同时仍与文档窗口交互。

### <a name="modal-window"></a>模式窗口

任何标准 `NSWindow` 都可以通过模式显示来用作自定义对话框：

[![](dialog-images/modal01.png "An example modal window")](dialog-images/modal01.png#lightbox)

### <a name="document-modal-dialog-sheets"></a>文档模式对话框工作表

_工作表_是附加到给定文档窗口的模式对话框，阻止用户在关闭该对话框之前与窗口交互。 工作表附加到其所从其上发现的窗口，并且一次只能为窗口打开一个工作表。

[![](dialog-images/sheet08.png "An example modal sheet")](dialog-images/sheet08.png#lightbox)

### <a name="preferences-windows"></a>首选项窗口

"首选项" 窗口是一个无模式对话框，其中包含用户不常更改的应用程序设置。 首选项 Windows 通常包含一个工具栏，使用户能够在不同的设置组之间切换：

[![](dialog-images/dialog02.png "An example preference window")](dialog-images/dialog02.png#lightbox)

### <a name="open-dialog"></a>打开对话框

"打开" 对话框为用户提供了在应用程序中查找和打开项的一致方式：

[![](dialog-images/dialog03.png "A open dialog box")](dialog-images/dialog03.png#lightbox)

### <a name="print-and-page-setup-dialogs"></a>打印和页面设置对话框

macOS 提供了应用程序可以显示的标准打印和页面设置对话框，使用户能够在其使用的每个应用程序中具有一致的打印体验。

"打印" 对话框可以同时显示为 "自由浮动" 对话框：

[![](dialog-images/print01.png "A print dialog box")](dialog-images/print01.png#lightbox)

也可以将其显示为工作表：

[![](dialog-images/print02.png "A print sheet")](dialog-images/print02.png#lightbox)

"页面设置" 对话框可以同时显示为 "自由浮动" 对话框：

[![](dialog-images/print03.png "A page setup dialog")](dialog-images/print03.png#lightbox)

也可以将其显示为工作表：

[![](dialog-images/print04.png "A page setup sheet")](dialog-images/print04.png#lightbox)

### <a name="save-dialogs"></a>保存对话框

"保存" 对话框为用户提供了在应用程序中保存项的一致方式。 "保存" 对话框有两种状态： "**最小**" （也称为 "折叠"）：

[![](dialog-images/save01.png "A save dialog")](dialog-images/save01.png#lightbox)

并且**展开**状态：

[![](dialog-images/save02.png "An expanded save dialog")](dialog-images/save02.png#lightbox)

"**最小**保存" 对话框也可以显示为工作表：

[![](dialog-images/save03.png "A minimal save sheet")](dialog-images/save03.png#lightbox)

与**展开**的 "保存" 对话框一样：

[![](dialog-images/save04.png "An expanded save sheet")](dialog-images/save04.png#lightbox)

有关详细信息，请参阅 Apple [OS X 人体学接口准则](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)的[对话框](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/WindowDialogs.html#//apple_ref/doc/uid/20000957-CH43-SW1)部分

<a name="Adding_a_Modal_Window_to_a_Project" />

## <a name="adding-a-modal-window-to-a-project"></a>向项目添加模式窗口

除了主文档窗口以外，Xamarin 应用程序可能还需要向用户显示其他类型的 windows，如首选项或检查器面板。

若要添加新窗口，请执行以下操作：

1. 在**解决方案资源管理器**中，打开 `Main.storyboard` 文件，以便在 Xcode 的 Interface Builder 中进行编辑。
2. 将新的**视图控制器**拖动到 Design Surface：

    [![](dialog-images/new01.png "Selecting a View Controller from the Library")](dialog-images/new01.png#lightbox)
3. 在**标识检查器**中，输入**类名**`CustomDialogController`： 

    [![](dialog-images/new02.png "Setting the class name")](dialog-images/new02.png#lightbox)
4. 切换回 Visual Studio for Mac，使其与 Xcode 同步，并创建 `CustomDialogController.h` 文件。
5. 返回到 Xcode 并设计接口： 

    [![](dialog-images/new03.png "Designing the UI in Xcode")](dialog-images/new03.png#lightbox)
6. 从应用程序的主窗口中创建一个**模式 Segue** ，方法是：从应用程序的主窗口拖到新视图控制器。 为指定**标识符**`ModalSegue`： 

    [![](dialog-images/new06.png "A modal segue")](dialog-images/new06.png#lightbox)
7. 连接任何**操作**和**插座**： 

    [![](dialog-images/new04.png "Configuring an Action")](dialog-images/new04.png#lightbox)
8. 保存更改并返回到 Visual Studio for Mac 以与 Xcode 同步。

使 `CustomDialogController.cs` 文件如下所示：

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

此代码公开几个属性，以设置对话框的标题和说明，并提供几个事件来响应取消或接受的对话框。

接下来，编辑 `ViewController.cs` 文件，重写 `PrepareForSegue` 方法并使其类似于以下内容：

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

此代码会将在 Xcode Interface Builder 中定义的 segue 初始化为对话框，并设置标题和说明。 它还处理用户在对话框中所做的选择。

我们可以运行应用程序并显示自定义对话框：

[![](dialog-images/new05.png "An example dialog")](dialog-images/new05.png#lightbox)

有关在 Xamarin Mac 应用程序中使用 windows 的详细信息，请参阅使用[windows](~/mac/user-interface/window.md)文档。

<a name="Creating_a_Custom_Sheet" />

## <a name="creating-a-custom-sheet"></a>创建自定义工作表

_工作表_是附加到给定文档窗口的模式对话框，阻止用户在关闭该对话框之前与窗口交互。 工作表附加到其所从其上发现的窗口，并且一次只能为窗口打开一个工作表。 

若要在 Xamarin 中创建自定义工作表，请执行以下操作：

1. 在**解决方案资源管理器**中，打开 `Main.storyboard` 文件，以便在 Xcode 的 Interface Builder 中进行编辑。
2. 将新的**视图控制器**拖动到 Design Surface：

    [![](dialog-images/new01.png "Selecting a View Controller from the Library")](dialog-images/new01.png#lightbox)
3. 设计用户界面：

    [![](dialog-images/sheet01.png "The UI design")](dialog-images/sheet01.png#lightbox)
4. 从主窗口创建**工作表 Segue**到新的视图控制器： 

    [![](dialog-images/sheet02.png "Selecting the Sheet segue type")](dialog-images/sheet02.png#lightbox)
5. 在 "**标识检查器**" 中，将视图控制器的**类**命名为 `SheetViewController`： 

    [![](dialog-images/sheet03.png "Setting the class name")](dialog-images/sheet03.png#lightbox)
6. 定义任何所需的**插座**和**操作**： 

    [![](dialog-images/sheet04.png "Defining the required Outlets and Actions")](dialog-images/sheet04.png#lightbox)
7. 保存更改并返回到要同步的 Visual Studio for Mac。

接下来，编辑 `SheetViewController.cs` 文件，使其类似于以下内容：

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

接下来，编辑 `ViewController.cs` 文件，编辑 `PrepareForSegue` 方法并使其类似于以下内容：

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

如果运行应用程序并打开工作表，则该工作表将附加到窗口：

[![](dialog-images/sheet08.png "An example sheet")](dialog-images/sheet08.png#lightbox)

<a name="Creating_a_Preferences_Dialog" />

## <a name="creating-a-preferences-dialog"></a>创建首选项对话框

在 Interface Builder 中布局首选项之前，我们需要添加自定义的 segue 类型来处理首选项。 向项目中添加一个新类，并 `ReplaceViewSeque`调用它。 编辑类，使其类似于以下内容：

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

创建自定义 segue 后，可以在 Xcode 的 Interface Builder 中添加新窗口来处理我们的首选项。

若要添加新窗口，请执行以下操作：

1. 在**解决方案资源管理器**中，打开 `Main.storyboard` 文件，以便在 Xcode 的 Interface Builder 中进行编辑。
2. 将新的**窗口控制器**拖动到 Design Surface：

    [![](dialog-images/pref01.png "Select a Window Controller from the Library")](dialog-images/pref01.png#lightbox)
3. 在**菜单栏**设计器附近排列窗口：

    [![](dialog-images/pref02.png "Adding the new Window")](dialog-images/pref02.png#lightbox)
4. 创建附加视图控制器的副本，因为首选项视图中将显示选项卡：

    [![](dialog-images/pref03.png "Adding the required View Controllers")](dialog-images/pref03.png#lightbox)
5. 从**库**中拖动新的**工具栏控制器**：

    [![](dialog-images/pref04.png "Select a Toolbar Controller from the Library")](dialog-images/pref04.png#lightbox)
6. 并将其放在 Design Surface 的窗口中：

    [![](dialog-images/pref05.png "Adding a new Toolbar Controller")](dialog-images/pref05.png#lightbox)
7. 布局工具栏的设计：

    [![](dialog-images/pref06.png "Layout the toolbar")](dialog-images/pref06.png#lightbox)
8. 按住 ctrl 并从每个**工具栏按钮**拖动到前面创建的视图。 选择**自定义**segue 类型：

    [![](dialog-images/pref07.png "Setting the segue type")](dialog-images/pref07.png#lightbox)
9. 选择新的 Segue 并将**类**设置为 `ReplaceViewSegue`：

    [![](dialog-images/pref08.png "Setting the segue class")](dialog-images/pref08.png#lightbox)
10. 在 Design Surface 上的菜单**栏设计器**中，从 "应用程序" 菜单中选择 "**首选项 ...** "，然后单击并拖动到 "首选项" 窗口，创建**Show** segue：

    [![](dialog-images/pref09.png "Setting the segue type")](dialog-images/pref09.png#lightbox)
11. 保存更改并返回到要同步的 Visual Studio for Mac。

如果运行代码并从 "**应用程序" 菜单**中选择 "**首选项 ...** "，则将显示该窗口：

[![](dialog-images/pref10.png "An example preferences window")](dialog-images/pref10.png#lightbox)

有关使用 Windows 和工具栏的详细信息，请参阅我们的[windows](~/mac/user-interface/window.md)和[工具栏](~/mac/user-interface/toolbar.md)文档。

<a name="Saving-and-Loading-Preferences" />

### <a name="saving-and-loading-preferences"></a>保存和加载首选项

在典型的 macOS 应用中，当用户对应用的任何用户首选项进行更改时，这些更改会自动保存。 在 Xamarin 应用程序中处理此类应用程序的最简单方法是创建一个类来管理用户的所有首选项，并在整个系统范围内共享它。

首先，将一个新的 `AppPreferences` 类添加到项目中，并从 `NSObject`继承。 首选项将设计为使用[数据绑定和键值编码，](~/mac/app-fundamentals/databinding.md)这会使创建和维护首选项的过程更加简单。 由于首选项将包含少量简单的数据类型，因此请使用内置 `NSUserDefaults` 来存储和检索值。

编辑 `AppPreferences.cs` 文件，使其类似于以下内容：

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

此类包含一些 helper 例程，如 `SaveInt`、`LoadInt`、`SaveColor`、`LoadColor`等，以便更轻松地使用 `NSUserDefaults`。 此外，由于 `NSUserDefaults` 没有用于处理 `NSColors`的内置方法，因此，`NSColorToHexString` 和 `NSColorFromHexString` 方法用于将颜色转换为基于 web 的十六进制字符串（`#RRGGBBAA` 其中 `AA` 是 alpha 透明度），可以轻松地进行存储和检索。

在 `AppDelegate.cs` 文件中，创建**AppPreferences**对象的实例，该实例将在应用范围内使用：

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

### <a name="wiring-preferences-to-preference-views"></a>将首选项布线到首选项视图

接下来，将首选项类连接到首选项窗口和上面创建的视图上的 UI 元素。 在 Interface Builder 中，选择一个首选项视图控制器并切换到**标识检查器**，为控制器创建一个自定义类： 

[![](dialog-images/prefs12.png "The Identity Inspector")](dialog-images/prefs12.png#lightbox)

切换回 Visual Studio for Mac 以同步您的更改并打开新创建的类以进行编辑。 使类看起来如下所示：

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

请注意，此类在此处完成了两项任务：首先，有一个帮助器 `App` 属性，以便更轻松地访问**AppDelegate** 。 其次，`Preferences` 属性公开全局**AppPreferences**类，以便与放置在此视图中的任何 UI 控件进行数据绑定。

接下来，双击情节提要文件以 Interface Builder 中将其重新打开（并查看上面刚刚做出的更改）。 将生成首选项接口所需的所有 UI 控件拖到视图中。 对于每个控件，切换到**绑定检查器**并绑定到**AppPreference**类的各个属性：

[![](dialog-images/prefs13.png "The Binding Inspector")](dialog-images/prefs13.png#lightbox)

针对所有面板（"视图控制器"）和所需的首选项属性，重复以上步骤。

<a name="Applying-Preference-Changes-to-All-Open-Windows" />

### <a name="applying-preference-changes-to-all-open-windows"></a>将首选项更改应用于所有打开的窗口

如上所述，在典型的 macOS 应用程序中，当用户对应用程序的任何用户首选项进行更改时，这些更改会自动保存并应用于用户可能已在应用程序中打开的任何 windows。

仔细规划和设计你的应用的首选项和 windows 将允许此过程以流畅且透明的方式对最终用户进行，只需最少的编码工作。

对于将使用应用首选项的任何窗口，将以下 helper 属性添加到其内容视图控制器，以便更轻松地访问**AppDelegate** ：

```csharp
#region Application Access
public static AppDelegate App {
    get { return (AppDelegate)NSApplication.SharedApplication.Delegate; }
}
#endregion
```

接下来，添加一个类，以便基于用户的首选项配置内容或行为：

```csharp
public void ConfigureEditor() {

    // General Preferences
    TextEditor.AutomaticLinkDetectionEnabled = App.Preferences.SmartLinks;
    TextEditor.AutomaticQuoteSubstitutionEnabled = App.Preferences.SmartQuotes;
    ...

}
``` 

首次打开窗口时，需要调用配置方法，以确保它符合用户的首选项：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Configure editor from user preferences
    ConfigureEditor ();
    ...
}
```

接下来，编辑 `AppDelegate.cs` 文件并添加以下方法，以将任何首选项更改应用于所有打开的窗口：

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

接下来，将 `PreferenceWindowDelegate` 类添加到项目，并使其类似于以下内容：

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

当首选项窗口关闭时，这将导致任何首选项更改发送到所有打开的窗口。

最后，编辑首选项窗口控制器并添加上面创建的委托：

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

进行所有这些更改后，如果用户编辑应用的首选项并关闭首选项窗口，则所做的更改将应用于所有打开的窗口：

[![](dialog-images/prefs14.png "An example preferences window")](dialog-images/prefs14.png#lightbox)

<a name="The_Open_Dialog" />

## <a name="the-open-dialog"></a>"打开" 对话框

"打开" 对话框为用户提供了一种在应用程序中查找和打开项的一致方式。 若要在 Xamarin Mac 应用程序中显示打开的对话框，请使用以下代码：

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

在上面的代码中，我们将打开新的文档窗口以显示该文件的内容。 你需要将此代码替换为应用程序所需的功能。

使用 `NSOpenPanel`时，可以使用以下属性：

- **CanChooseFiles** -如果 `true` 用户可以选择文件。
- **CanChooseDirectories** -如果 `true` 用户可以选择目录。
- **AllowsMultipleSelection** -如果 `true` 用户可以一次选择多个文件。
- **ResolveAliases** -如果选择和别名 `true`，则将其解析为原始文件的路径。
- **AllowedFileTypes** -文件类型的字符串数组，用户可以选择扩展或_UTI_。 默认值为 `null`，这允许打开任何文件。

`RunModal ()` 方法将显示 "打开" 对话框，并允许用户选择文件或目录（由属性指定），并在用户单击 "**打开**" 按钮时返回 `1`。

"打开" 对话框将用户选择的文件或目录作为 `URL` 属性中的 Url 数组返回。

如果运行该程序，并从 "**文件**" 菜单中选择 "**打开 ...** " 项，则会显示以下内容： 

[![](dialog-images/dialog03.png "An open dialog box")](dialog-images/dialog03.png#lightbox)

<a name="The_Print_and_Page_Setup_Dialogs" />

## <a name="the-print-and-page-setup-dialogs"></a>"打印" 和 "页面设置" 对话框

macOS 提供了应用程序可以显示的标准打印和页面设置对话框，使用户能够在其使用的每个应用程序中具有一致的打印体验。

下面的代码将显示标准的 "打印" 对话框：

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

如果将 `ShowPrintAsSheet` 属性设置为 `false`，请运行应用程序并显示 "打印" 对话框，将显示以下内容：

[![](dialog-images/print01.png "A print dialog box")](dialog-images/print01.png#lightbox)

如果将 `ShowPrintAsSheet` 属性设置为 `true`，则运行应用程序并显示 "打印" 对话框，将显示以下内容：

[![](dialog-images/print02.png "A print sheet")](dialog-images/print02.png#lightbox)

以下代码将显示 "页面布局" 对话框：

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

如果将 `ShowPrintAsSheet` 属性设置为 `false`，则运行应用程序并显示 "打印布局" 对话框，将显示以下内容：

[![](dialog-images/print03.png "A page setup dialog")](dialog-images/print03.png#lightbox)

如果将 `ShowPrintAsSheet` 属性设置为 `true`，则运行应用程序并显示 "打印布局" 对话框，将显示以下内容：

[![](dialog-images/print04.png "A page setup sheet")](dialog-images/print04.png#lightbox)

有关使用 "打印" 和 "页面设置" 对话框的详细信息，请参阅 Apple 的[NSPrintPanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPrintPanel_Class/index.html#//apple_ref/doc/uid/TP40004092)和[NSPageLayout](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSPageLayout_Class/index.html#//apple_ref/doc/uid/TP40004080)文档。

<a name="The_Save_Dialog" />

## <a name="the-save-dialog"></a>"保存" 对话框

"保存" 对话框为用户提供了在应用程序中保存项的一致方式。

以下代码将显示标准的 "保存" 对话框：

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

`AllowedFileTypes` 属性是文件类型的字符串数组，用户可选择将文件另存为。 文件类型可以指定为 extension 或_UTI_。 默认值为 `null`，这允许使用任何文件类型。

如果将 `ShowSaveAsSheet` 属性设置为 `false`，请运行应用程序，并从 "**文件**" 菜单中选择 "**另存为 ...** "，将显示以下内容：

[![](dialog-images/save01.png "A save dialog box")](dialog-images/save01.png#lightbox)

用户可以展开对话框：

[![](dialog-images/save02.png "An expanded save dialog box")](dialog-images/save02.png#lightbox)

如果将 `ShowSaveAsSheet` 属性设置为 `true`，请运行应用程序，并从 "**文件**" 菜单中选择 "**另存为 ...** "，将显示以下内容：

[![](dialog-images/save03.png "A save sheet")](dialog-images/save03.png#lightbox)

用户可以展开对话框：

[![](dialog-images/save04.png "An expanded save sheet")](dialog-images/save04.png#lightbox)

有关使用 "保存" 对话框的详细信息，请参阅 Apple 的[NSSavePanel](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSSavePanel_Class/index.html#//apple_ref/doc/uid/TP40004098)文档。

<a name="Summary" />

## <a name="summary"></a>总结

本文详细介绍了如何在 Xamarin. Mac 应用程序中使用模式窗口、工作表和标准系统对话框。 我们看到了不同的模式窗口、工作表和对话框的使用方法，如何在 Xcode 的 Interface Builder 中创建和维护模式窗口和表，以及如何在代码中C#使用模式窗口、工作表和对话框。

## <a name="related-links"></a>相关链接

- [MacWindows （示例）](https://docs.microsoft.com/samples/xamarin/mac-samples/macwindows)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [菜单](~/mac/user-interface/menu.md)
- [Windows](~/mac/user-interface/window.md)
- [工具栏](~/mac/user-interface/toolbar.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Windows 简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
- [工作表简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Sheets/Sheets.html#//apple_ref/doc/uid/10000002i)
- [打印简介](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Printing/osxp_aboutprinting/osxp_aboutprt.html)

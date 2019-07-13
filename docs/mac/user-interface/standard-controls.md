---
title: 在 Xamarin.Mac 中标准控件
description: 本文介绍如何使用标准的 AppKit 控件，如按钮、 标签、 文本字段、 复选框和分段控件中的 Xamarin.Mac 应用程序。 它介绍了将其添加到使用 Interface Builder 的接口以及与其进行交互的代码中。
ms.prod: xamarin
ms.assetid: d2593883-d255-431f-9781-75f04d8cecea
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 696432fb51060a236780eedee0b60661e3a86712
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865195"
---
# <a name="standard-controls-in-xamarinmac"></a>在 Xamarin.Mac 中标准控件

_本文介绍如何使用标准的 AppKit 控件，如按钮、 标签、 文本字段、 复选框和分段控件中的 Xamarin.Mac 应用程序。它介绍了将其添加到使用 Interface Builder 的接口以及与其进行交互的代码中。_

当使用C#和.NET 中的 Xamarin.Mac 应用程序中，您可以访问相同的 AppKit 控件的开发人员*Objective C*和*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，可以使用 Xcode 的_Interface Builder_创建和维护 Appkit 控件 （或选择通过 C# 代码中直接创建）。

AppKit 控件是用于创建 Xamarin.Mac 应用程序的用户界面的 UI 元素。 它们包含的元素，如按钮、 标签、 文本字段、 复选框和分段控件和导致出现时用户操作它们即时操作或显示结果。

[![](standard-controls-images/intro01.png "示例应用程序主屏幕")](standard-controls-images/intro01.png#lightbox)

在本文中，我们将介绍 Xamarin.Mac 应用程序中使用的 AppKit 控件的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它介绍了关键概念和技术，我们将在本文中使用。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="Introduction_to_Controls_and_Views" />

## <a name="introduction-to-controls-and-views"></a>控件和视图简介

macOS （以前称为 Mac OS X） 提供一组标准的用户界面控件通过 AppKit 框架。 它们包含的元素，如按钮、 标签、 文本字段、 复选框和分段控件和导致出现时用户操作它们即时操作或显示结果。

所有的 AppKit 控件具有标准的内置的外观将适用于大多数使用、 某些指定用于在窗口框架区中或在具有备用的外观_自然饱和度效果_上下文中，例如侧栏区域中或在通知中心小组件。

Apple 建议时使用的 AppKit 控件的以下准则：

- 避免混合在同一视图中的控件大小。
- 一般情况下，避免调整控件的垂直大小。
- 使用系统字体并在控件内的正确的文本大小。
- 使用控件之间适当的间距。

有关详细信息，请参阅 pleas[关于控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Using_Controls_in_a_Window_Frame" />

### <a name="using-controls-in-a-window-frame"></a>在窗口框架中使用控件

有包含允许它们以包括在窗口框架区中的显示样式的 AppKit 控件的子集。 有关示例，请参阅邮件应用程序的工具栏：

[![](standard-controls-images/mailapp.png "Mac 窗口框架")](standard-controls-images/mailapp.png#lightbox)

- **舍入设置纹理按钮**-`NSButton`使用样式的`NSTexturedRoundedBezelStyle`。
- **设置纹理舍入分段控件**-`NSSegmentedControl`使用样式的`NSSegmentStyleTexturedRounded`。
- **设置纹理舍入分段控件**-`NSSegmentedControl`使用样式的`NSSegmentStyleSeparated`。
- **舍入设置纹理弹出菜单**-`NSPopUpButton`使用样式的`NSTexturedRoundedBezelStyle`。
- **舍入设置纹理的下拉菜单**-`NSPopUpButton`使用样式的`NSTexturedRoundedBezelStyle`。
- **搜索栏**- `NSSearchField`。

使用在窗口框架中的 AppKit 控件时，Apple 建议以下准则：

- 不要使用在窗口正文中的窗口框架特定控件样式。
- 不要使用窗口正文控件或样式在窗口框架中。

有关详细信息，请参阅 pleas[关于控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

<a name="Creating_a_User_Interface_in_Interface_Builder" />

## <a name="creating-a-user-interface-in-interface-builder"></a>在接口生成器中创建用户界面

在创建新的 Xamarin.Mac 的 Cocoa 应用程序时，默认情况下获得标准保留为空，窗口。 在中定义此 windows`.storyboard`自动包含在项目中的文件。 若要编辑您的 windows 设计、 在**解决方案资源管理器**，双击`Main.storyboard`文件：

[![](standard-controls-images/edit01.png "在解决方案资源管理器中选择主要情节提要")](standard-controls-images/edit01.png#lightbox)

这将在 Xcode 的 Interface Builder 中打开的窗口设计：

[![](standard-controls-images/edit02.png "编辑在 Xcode 中的情节提要")](standard-controls-images/edit02.png#lightbox)

若要创建您的用户界面，将拖动 UI 元素 （AppKit 控件） 从**库检查器**到**界面编辑器**Interface Builder 中。 在以下示例中，**垂直拆分视图**控件已经被从药物**库检查器**并置于窗口**界面编辑器**:

[![](standard-controls-images/edit03.png "从库中选择一个拆分视图")](standard-controls-images/edit03.png#lightbox)

有关在 Interface Builder 中创建用户界面的详细信息，请参阅我们[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)文档。

<a name="Sizing_and_Positioning" />

### <a name="sizing-and-positioning"></a>大小和位置

后一个控件包含在用户界面中，使用**约束编辑器**通过手动输入设置其位置和大小，并控制该控件将自动定位和大小调整时的方式的父窗口或视图调整大小：

[![](standard-controls-images/edit04.png "设置约束")](standard-controls-images/edit04.png#lightbox)

使用**红色 I 型光标**的外部**Autoresizing**框_记忆棒_将控件与给定 (x，y) 的位置。 例如: 

[![](standard-controls-images/edit05.png "编辑约束")](standard-controls-images/edit05.png#lightbox)

指定所选的控件 (在**层次结构视图** & **界面编辑器**) 调整大小或移动时受阻的窗口或视图上和右位置。 

编辑器控件属性 Height 和 Width 等其他元素：

[![](standard-controls-images/edit06.png "设置高度")](standard-controls-images/edit06.png#lightbox)

您还可以控制元素的对齐方式与使用的约束**对齐编辑器**:

[![](standard-controls-images/edit07.png "对齐方式编辑器")](standard-controls-images/edit07.png#lightbox)

> [!IMPORTANT]
> 与 iOS 不同位置 (0，0) 是右上 macOS (0，0) 中的屏幕的左下角是较低的左下角。 这是因为 macOS 使用数学的坐标系统的带向上和向右增加值中的数字值。 需要放在用户界面的 AppKit 控件时考虑这一点。

<a name="Setting_a_Custom_Class" />

### <a name="setting-a-custom-class"></a>设置自定义类

没有使用的 AppKit 控件，您将需要子类和现有的控件，并创建你自己的自定义此类版本。 例如，定义自定义版本的源列表：

```csharp
using System;
using AppKit;
using Foundation;

namespace AppKit
{
    [Register("SourceListView")]
    public class SourceListView : NSOutlineView
    {
        #region Computed Properties
        public SourceListDataSource Data {
            get {return (SourceListDataSource)this.DataSource; }
        }
        #endregion

        #region Constructors
        public SourceListView ()
        {

        }

        public SourceListView (IntPtr handle) : base(handle)
        {

        }

        public SourceListView (NSCoder coder) : base(coder)
        {

        }

        public SourceListView (NSObjectFlag t) : base(t)
        {

        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

        }
        #endregion

        #region Public Methods
        public void Initialize() {

            // Initialize this instance
            this.DataSource = new SourceListDataSource (this);
            this.Delegate = new SourceListDelegate (this);

        }

        public void AddItem(SourceListItem item) {
            if (Data != null) {
                Data.Items.Add (item);
            }
        }
        #endregion

        #region Events
        public delegate void ItemSelectedDelegate(SourceListItem item);
        public event ItemSelectedDelegate ItemSelected;

        internal void RaiseItemSelected(SourceListItem item) {
            // Inform caller
            if (this.ItemSelected != null) {
                this.ItemSelected (item);
            }
        }
        #endregion
    }
}
```

其中`[Register("SourceListView")]`指令公开`SourceListView`objective-c 这就是类可用于在 Interface Builder 中。 有关详细信息，请参阅[公开 C# 类 / objective-c 方法](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`使用的命令若要布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

与上述代码，可以将一个 AppKit 控件，要扩展的基类型拖到设计图面上 (在以下示例中，**源列表**)，切换到**标识检查器**和设置**自定义类**公开为 Objective C 的名称 (示例`SourceListView`):

[![](standard-controls-images/edit10.png "在 Xcode 中设置的自定义类")](standard-controls-images/edit10.png#lightbox)

<a name="Exposing_Outlets_and_Actions" />

### <a name="exposing-outlets-and-actions"></a>公开输出口和操作

可以在 C# 代码中访问 AppKit 控件之前，需要为公开**输出口**或并**操作**。 若要执行此给定的控件中选中**界面层次结构**或**界面编辑器**并切换到**助手视图**(确保您具有`.h`选定来进行编辑窗口的):

[![](standard-controls-images/edit11.png "选择要编辑的正确文件")](standard-controls-images/edit11.png#lightbox)

控件拖动到给定的 AppKit 控件从`.h`文件以开始创建**输出口**或**操作**:

[![](standard-controls-images/edit12.png "拖动以创建输出口或操作")](standard-controls-images/edit12.png#lightbox)

选择的暴露来创建，并为类型**输出口**或**操作** **名称**: 

[![](standard-controls-images/edit13.png "配置电源插座或操作")](standard-controls-images/edit13.png#lightbox)


有关使用的详细信息**输出口**并**操作**，，请参阅[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)一部分我们[简介 Xcode 和 Interface生成器](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)文档。

<a name="Synchronizing_Changes_with_Xcode" />

### <a name="synchronizing-changes-with-xcode"></a>与 Xcode 同步更改

时，切换回 Visual Studio for Mac 从 Xcode，则将自动与 Xamarin.Mac 项目同步在 Xcode 中所做的任何更改。

如果选择`SplitViewController.designer.cs`中**解决方案资源管理器**您将能够看到如何你**输出口**并**操作**具有在 C# 代码有线：

[![](standard-controls-images/sync01.png "与 Xcode 同步更改")](standard-controls-images/sync01.png#lightbox)

请注意如何在定义`SplitViewController.designer.cs`文件：

```csharp
[Outlet]
AppKit.NSSplitViewItem LeftController { get; set; }

[Outlet]
AppKit.NSSplitViewItem RightController { get; set; }

[Outlet]
AppKit.NSSplitView SplitView { get; set; }
```

在中定义的`MainWindow.h`在 Xcode 中的文件：

```csharp
@interface SplitViewController : NSSplitViewController {
    NSSplitViewItem *_LeftController;
    NSSplitViewItem *_RightController;
    NSSplitView *_SplitView;
}

@property (nonatomic, retain) IBOutlet NSSplitViewItem *LeftController;

@property (nonatomic, retain) IBOutlet NSSplitViewItem *RightController;

@property (nonatomic, retain) IBOutlet NSSplitView *SplitView;
```

正如您所看到的 Visual Studio for Mac 会侦听对更改`.h`文件，然后再自动同步这些更改中相应`.designer.cs`文件以将它们公开给您的应用程序。 你可能注意`SplitViewController.designer.cs`是一个分部类，以便 Visual Studio for Mac 不必修改`SplitViewController.cs`它会覆盖我们已对类进行任何更改。

通常情况下将永远不需要打开`SplitViewController.designer.cs`自己，此处提供仅为了。

> [!IMPORTANT]
> 在大多数情况下，Visual Studio for Mac将自动查看Xcode中所做的任何更改并将其同步到Xamarin.Mac项目。 如果同步不自动进行，请切换回 Xcode，然后再次切换到 Visual Studio for Mac。 这通常会开始同步周期。

<a name="Working_with_Buttons" />

## <a name="working-with-buttons"></a>使用按钮

AppKit 提供了几种类型的可在你的用户界面设计的按钮。 有关详细信息，请参阅[按钮](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/buttons01.png "下面举例说明了不同的按钮类型")](standard-controls-images/buttons01.png#lightbox)

如果已通过公开一个按钮**输出口**，下面的代码将响应它已按下：

```csharp
ButtonOutlet.Activated += (sender, e) => {
        FeedbackLabel.StringValue = "Button Outlet Pressed";
};
```

已通过公开的按钮**操作**、`public partial`与在 Xcode 中选择的名称会自动为您创建方法。 若要响应**操作**，完成类中的分部方法的**操作**上定义。 例如:

```csharp
partial void ButtonAction (Foundation.NSObject sender) {
    // Do something in response to the Action
    FeedbackLabel.StringValue = "Button Action Pressed";
}
```

对于具有一种状态的按钮 (如**上**和**关闭**)，可以检查的状态，或者将其设置与`State`属性与`NSCellStateValue`枚举。 例如:

```csharp
DisclosureButton.Activated += (sender, e) => {
    LorumIpsum.Hidden = (DisclosureButton.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是：

- **在**-按钮或选定 （如复选框中检查）。
- **关闭**-未推送的按钮或未选中该控件。
- **混合**-混合**上**并**关闭**状态。

<a name="Mark-a-Button-as-Default-and-Set-Key-Equivalent" />

### <a name="mark-a-button-as-default-and-set-key-equivalent"></a>将标记为默认值并设置密钥等效项的按钮

对于任何已添加到用户界面设计的按钮，您可以将标记为该按钮_默认_按钮，用户按下时将激活**返回/Enter**键盘上键。 在 macOS 中此按钮将默认情况下收到蓝色背景色。

要设置为默认值的一个按钮，其在 Xcode 的 Interface Builder 中进行选择。 接下来，在**属性检查器**，选择**密钥等效**字段，然后按**返回/Enter**密钥：

[![](standard-controls-images/buttons03.png "编辑密钥等效项")](standard-controls-images/buttons03.png#lightbox)

同样，您可以分配任何可用来激活该按钮而不鼠标使用键盘的按键顺序。 例如，在上图中按命令 C 键。

当应用在运行时和带有按钮的窗口是密钥时，主要目标，如果用户按下命令 C，就将激活该按钮的操作 (作为-如果用户已单击的按钮)。

<a name="Working_with_Checkboxes_and_Radio_Buttons" />

## <a name="working-with-checkboxes-and-radio-buttons"></a>使用复选框和单选按钮

AppKit 提供了几种类型的复选框和单选按钮组可以在你的用户界面设计中使用。 有关详细信息，请参阅[按钮](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsButtons.html#//apple_ref/doc/uid/20000957-CH48-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/buttons02.png "举例说明了可用的复选框类型")](standard-controls-images/buttons02.png#lightbox)


复选框和单选按钮 (通过公开**输出口**) 具有一种状态 (如**上**并**关闭**)，可以检查的状态，或者将其设置与`State`属性与`NSCellStateValue`枚举。 例如:

```csharp
AdjustTime.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Adjust Time: {0}",AdjustTime.State == NSCellStateValue.On);
};
```

其中`NSCellStateValue`可以是：

- **在**-按钮或选定 （如复选框中检查）。
- **关闭**-未推送的按钮或未选中该控件。
- **混合**-混合**上**并**关闭**状态。

若要在单选按钮组中选择一个按钮，公开此单选按钮以选择作为**输出口**并设置其`State`属性。 例如：

```csharp
partial void SelectCar (Foundation.NSObject sender) {
    TransportationCar.State = NSCellStateValue.On;
    FeedbackLabel.StringValue = "Car Selected";
}
```

若要获取的单选按钮以作为一组执行，并自动处理所选的状态的集合，请创建一个新**操作**并附加到它的组中的每个按钮：

![](standard-controls-images/buttons04.png "创建新的操作")

接下来，将分配一个唯一`Tag`为在每个单选按钮**属性检查器**:

![](standard-controls-images/buttons05.png "编辑单选按钮标记")

保存所做的更改并返回 Visual Studio for Mac，请添加代码以处理**操作**单选按钮的所有附加到：

```csharp
partial void NumberChanged(Foundation.NSObject sender)
{
    var check = sender as NSButton;
    Console.WriteLine("Changed to {0}", check.Tag);
}
```

可以使用`Tag`属性以查看已选择的单选按钮。

<a name="Working_with_Menu_Controls" />

## <a name="working-with-menu-controls"></a>使用菜单控件

AppKit 提供了几种类型的可在你的用户界面设计的菜单控件。 有关详细信息，请参阅[Menu 控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlswithMenus.html#//apple_ref/doc/uid/20000957-CH100-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/menu01.png "示例菜单控件")](standard-controls-images/menu01.png#lightbox)

<a name="Providing-Menu-Control-Data" />

### <a name="providing-menu-control-data"></a>提供的菜单控件数据

可以设置可向 macOS 的菜单控件的内部列表 （可以是预定义的 Interface Builder 中或通过代码填充） 从填充下拉列表中或通过提供你自己的自定义的外部数据源。

<a name="Working-with-Internal-Data" />

#### <a name="working-with-internal-data"></a>使用内部数据

除了定义接口生成器中，菜单控件中的项 (如`NSComboBox`)，提供一组完整的方法，可用于添加、 编辑或删除的项从它们所维护的内部列表：

- `Add` -将新项添加到列表的末尾。
- `GetItem` -返回给定索引处的项。
- `Insert` -将新项插入列表中的给定位置中。
- `IndexOf` -返回给定项的索引。
- `Remove` -从列表中删除给定的项。
- `RemoveAll` -从列表中移除所有项。
- `RemoveAt` -删除给定索引处的项。
- `Count` -在列表中返回的项数。

> [!IMPORTANT]
> 如果要使用外部数据源 (`UsesDataSource = true`)，调用任何上述方法将引发异常。

<a name="Working-with-an-External-Data-Source" />

#### <a name="working-with-an-external-data-source"></a>使用外部数据源

而不是使用内置的内部数据为菜单控件提供的行，可以根据需要使用外部数据源并提供你自己的后备存储 （如 SQLite 数据库） 的项。

若要使用外部数据源，将创建菜单控件的数据源的实例 (`NSComboBoxDataSource`例如) 和重写几种方法来提供所需的数据：

- `ItemCount` -在列表中返回的项数。
- `ObjectValueForItem` -返回给定索引的项的值。
- `IndexOfItem` -返回给定项值的索引。
- `CompletedString` -返回的部分类型化的项值的第一个匹配项值。 如果已启用自动完成仅调用此方法 (`Completes = true`)。

请参阅[数据库和组合框](~/mac/app-fundamentals/databases.md#Databases-and-ComboBoxes)一部分[使用数据库](~/mac/app-fundamentals/databases.md)文档的更多详细信息。

<a name="Adjusting-the-Lists-Appearance" />

### <a name="adjusting-the-lists-appearance"></a>调整列表的外观

以下方法可以用来调整菜单控件的外观：

- `HasVerticalScroller` -如果`true`，该控件将显示一个垂直滚动条。 
- `VisibleItems` -调整控件打开时显示的项的数目。 默认值是五 （5)。
- `IntercellSpacing` -调整给定项周围的空间量，从而`NSSize`其中`Width`指定左右边距和`Height`之前和之后项指定的空间。
- `ItemHeight` -指定在列表中的每个项的高度。

下拉列表类型的`NSPopupButtons`，第一个菜单项提供的控件的标题。 例如： 

[![](standard-controls-images/menu02.png "示例菜单控件")](standard-controls-images/menu02.png#lightbox)

若要更改的标题，公开此项作为**输出口**并使用类似于以下代码：

```csharp
DropDownSelected.Title = "Item 1";
```

<a name="Manipulating-the-Selected-Items" />

### <a name="manipulating-the-selected-items"></a>操作所选的项

下面的方法和属性使您可以操作菜单控件的列表中的选定的项：

- `SelectItem` -选择给定索引处的项。
- `Select` -选择给定的项值。
- `DeselectItem` -取消选择给定索引处的项。
- `SelectedIndex` -返回当前选定项的索引。
- `SelectedValue` -返回当前选定项的值。

使用`ScrollItemAtIndexToTop`若要显示在列表顶部的给定索引处的项和`ScrollItemAtIndexToVisible`滚动到列表，直到给定索引处的项是否可见。

<a name="Responding to Events" />

### <a name="responding-to-events"></a>对事件作出响应

菜单控件提供了以下事件以便对用户交互作出响应：

- `SelectionChanged` -被调用时用户已从列表中选择一个值。
- `SelectionIsChanging` -之前调用新的用户所选项目将成为活动选择区域。
- `WillPopup` -被调用之前显示的项的下拉列表。
- `WillDismiss` -被调用之前关闭的项的下拉列表。

有关`NSComboBox`控件，它们包含与相同的事件的所有`NSTextField`，如`Changed`每当用户编辑组合框中的文本的值时调用的事件。

（可选） 可以响应内部的数据菜单项定义中选择了附加到的项的 Interface Builder**操作**并使用如下所示的代码以响应**操作**正在由用户触发：

```csharp
partial void ItemOne (Foundation.NSObject sender) {
    DropDownSelected.Title = "Item 1";
    FeedbackLabel.StringValue = "Item One Selected";
}
```

使用菜单和菜单控件的详细信息，请参阅我们[菜单](~/mac/user-interface/menu.md)并[弹出按钮和下拉列表](~/mac/user-interface/menu.md)文档。

<a name="Working_with_Selection_Controls" />

## <a name="working-with-selection-controls"></a>使用所选内容控件

AppKit 提供了几种类型的选择控件可以在你的用户界面设计中使用。 有关详细信息，请参阅[选择控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsSelection.html#//apple_ref/doc/uid/20000957-CH49-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/select01.png "示例选择控件")](standard-controls-images/select01.png#lightbox)

有两种方法来跟踪在所选内容控件时的用户交互，通过将其作为公开**操作**。 例如:

```csharp
partial void SegmentButtonPressed (Foundation.NSObject sender) {
    FeedbackLabel.StringValue = string.Format("Button {0} Pressed",SegmentButtons.SelectedSegment);
}
```

或通过将附加**委托**到`Activated`事件。 例如:

```csharp
TickedSlider.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
};
```

若要设置或读取所选内容控件的值，请使用`IntValue`属性。 例如：

```csharp
FeedbackLabel.StringValue = string.Format("Stepper Value: {0:###}",TickedSlider.IntValue);
```

特殊控件 （如也颜色和图像也） 具有为其值类型的特定属性。 例如：

```csharp
ColorWell.Color = NSColor.Red;
ImageWell.Image = NSImage.ImageNamed ("tag.png");

```

`NSDatePicker`具有以下属性用于直接处理日期和时间：

- **DateValue** -当前日期和时间值作为`NSDate`。
- **本地**-用户的位置作为`NSLocal`。
- **TimeInterval** -将时间值作为`Double`。
- **时区**-为用户的时区`NSTimeZone`。

<a name="Working_with_Indicator_Controls" />

## <a name="working-with-indicator-controls"></a>使用的指示器控件

AppKit 提供了几种类型的指示符可以在你的用户界面设计中使用的控件。 有关详细信息，请参阅[指示器控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsIndicators.html#//apple_ref/doc/uid/20000957-CH50-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/level01.png "示例指标控件")](standard-controls-images/level01.png#lightbox)

有两种方法来跟踪在指示器控件时的用户交互，方法是将其作为公开**操作**或**Outlet**并附加**委托**到`Activated`事件。 例如：

```csharp
LevelIndicator.Activated += (sender, e) => {
    FeedbackLabel.StringValue = string.Format("Level: {0:###}",LevelIndicator.DoubleValue);
};
```

若要读取或设置指示器控件的值，请使用`DoubleValue`属性。 例如：

```csharp
FeedbackLabel.StringValue = string.Format("Rating: {0:###}",Rating.DoubleValue);
```

显示时，不确定和异步进度指示器等应进行动画处理。 使用`StartAnimation`方法以显示时启动动画。 例如:

```csharp
Indeterminate.StartAnimation (this);
AsyncProgress.StartAnimation (this);
```

调用`StopAnimation`方法将停止动画。

<a name="Working_with_Text_Controls" />

## <a name="working-with-text-controls"></a>中的文本控件

AppKit 提供了几种类型的文本控件可以在你的用户界面设计中使用。 有关详细信息，请参阅[文本控件](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsText.html#//apple_ref/doc/uid/20000957-CH51-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。 

[![](standard-controls-images/text01.png "示例文本控件")](standard-controls-images/text01.png#lightbox)

文本字段 (`NSTextField`)，以下事件可用于跟踪用户交互：

- **更改**-触发任何用户更改字段的值的时间。 例如，在键入每个字符。
- **EditingBegan** -当用户选择该字段进行编辑时触发。
- **EditingEnded** -当用户按 Enter 键在字段中的，或离开该字段。

使用`StringValue`属性来读取或设置字段的值。 例如:

```csharp
FeedbackLabel.StringValue = string.Format("User ID: {0}",UserField.StringValue);
```

对于显示或编辑数字值的字段，可以使用`IntValue`属性。 例如：

```csharp
FeedbackLabel.StringValue = string.Format("Number: {0}",NumberField.IntValue);
```

`NSTextView`提供完整的特色的文本编辑和显示与内置格式设置的区域。 像`NSTextField`，使用`StringValue`属性来读取或设置区域的值。

有关使用文本视图中的 Xamarin.Mac 应用中的复杂的示例的示例，请参阅[Sourcewriter 示例](https://developer.xamarin.com/samples/mac/SourceWriter/)。 SourceWriter 是一个非常简单的源代码编辑器，提供代码补全和简单语法突出显示支持。

SourceWriter 代码已经完全注释，且在可用时，提供了相关链接，链接涵盖了从关键技术或方法到 Xamarin.Mac 指南文档中的相关信息。

<a name="Working_with_Content_Views" />

## <a name="working-with-content-views"></a>使用内容视图

AppKit 提供了几种类型的内容可在你的用户界面设计的视图。 有关详细信息，请参阅[内容视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsView.html#//apple_ref/doc/uid/20000957-CH52-SW1)Apple 的一部分[OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)。

[![](standard-controls-images/content01.png "示例内容视图")](standard-controls-images/content01.png#lightbox)

<a name="Popovers" />

### <a name="popovers"></a>Popovers

弹出框是暂时性的 UI 元素，它提供了与特定的控件或屏幕上的某个区域直接相关的功能。 包含控件或与其相关的区域的窗口上方浮动弹出框，其边框包括一个箭头，表明出现了从该点。

若要创建弹出框，请执行以下操作：

1. 打开`.storyboard`文件的窗口，你想要添加到弹出框中双击**解决方案资源管理器**
2. 拖动**查看控制器**从**库检查器**拖到**界面编辑器**: 

    [![](standard-controls-images/content02.png "从库中选择视图控制器")](standard-controls-images/content02.png#lightbox)
3. 定义的大小和布局**的自定义视图**: 

    [![](standard-controls-images/content04.png "编辑布局")](standard-controls-images/content04.png#lightbox)
4. 单击并拖动弹出框拖到从源**视图控制器**: 

    [![](standard-controls-images/content05.png "拖动以创建 segue")](standard-controls-images/content05.png#lightbox)
5. 选择**弹出框**弹出菜单中： 

    [![](standard-controls-images/content06.png "设置 segue 类型")](standard-controls-images/content06.png#lightbox)
6. 保存所做的更改并返回到 Visual Studio for Mac 与 Xcode 同步。

<a name="Tab_Views" />

### <a name="tab-views"></a>选项卡视图

选项卡视图包含与一组称为视图结合使用 （如下所示类似于分段控件） 选项卡列表_窗格_。 当用户选择新选项卡时，将显示附加到它的窗格。 每个窗格包含其自己的控件集。

使用 Xcode 的 Interface Builder 中的选项卡视图，使用**属性检查器**设置选项卡的数目：

[![](standard-controls-images/content08.png "编辑选项卡的数目")](standard-controls-images/content08.png#lightbox)

选择在每个选项卡**界面层次结构**若要设置其**标题**添加到 UI 元素和其**窗格**:

[![](standard-controls-images/content09.png "编辑在 Xcode 中的选项卡")](standard-controls-images/content09.png#lightbox)

<a name="Data_Binding_AppKit_Controls" />

## <a name="data-binding-appkit-controls"></a>数据绑定 AppKit 控件

通过在 Xamarin.Mac 应用程序中使用的键-值编码和数据绑定技术，您可以极大地减少需要编写和维护来填充和使用 UI 元素的代码量。 此外可以进一步分离你的备份数据的好处 (_数据模型_) 从你前面结束用户界面 (_模型-视图-控制器_)，从而导致更易于维护，更灵活的应用程序设计。

键值对的编码 (KVC) 是一种机制间接访问对象的属性、 使用密钥 （特殊格式字符串） 来标识而不是通过实例变量访问它们的属性或访问器方法 (`get/set`)。 在 Xamarin.Mac 应用程序中实现键 / 值编码符合访问器，可以获得其他 macOS 功能，例如键-值观察 (KVO)、 数据绑定、 核心数据、 Cocoa 绑定和 scriptability 的访问权限。

有关详细信息，请参阅[的简单数据绑定](~/mac/app-fundamentals/databinding.md#Simple_Data_Binding)一部分我们[数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)文档。


<a name="Summary" />

## <a name="summary"></a>总结

本文已使用如按钮、 标签、 文本字段、 复选框和分段控件中的 Xamarin.Mac 应用程序的标准 AppKit 控件的详细的信息。 它包括将它们添加到 Xcode 的 Interface Builder 中用户界面设计、 将它们公开给代码中通过输出口和操作以及使用 C# 代码中的 AppKit 控件。

## <a name="related-links"></a>相关链接

- [MacControls （示例）](https://developer.xamarin.com/samples/mac/MacControls/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [Windows](~/mac/user-interface/window.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [有关控件和视图](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/ControlsAll.html#//apple_ref/doc/uid/20000957-CH46-SW1)

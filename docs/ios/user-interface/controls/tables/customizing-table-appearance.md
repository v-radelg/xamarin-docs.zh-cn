---
title: 自定义在 Xamarin.iOS 中的表的外观
description: 本文档介绍如何自定义在 Xamarin.iOS 中的表的外观。 它讨论的单元格样式、 附件、 单元格分隔符和自定义单元格布局。
ms.prod: xamarin
ms.assetid: 8A83DE38-0028-CB61-66F9-0FB9DE552286
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: e1e86918d29e12d2f34dd3008b8c1d8e47471c24
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233544"
---
# <a name="customizing-a-tables-appearance-in-xamarinios"></a>自定义在 Xamarin.iOS 中的表的外观

若要更改表的外观的最简单方法是使用不同的单元格样式。 你可以创建在每个单元格时使用的单元格样式`UITableViewSource`的`GetCell`方法。

## <a name="cell-styles"></a>单元格样式

有四个内置样式：

-  **默认值**– 支持`UIImageView`。
-  **副标题**– 支持`UIImageView`和副标题。
-  **Value1** – 右对齐的副标题支持`UIImageView`。
-  **Value2** – 标题是右对齐和子标题是左对齐 （但没有图像）。


这些屏幕截图显示了每种样式的显示方式：

 [![](customizing-table-appearance-images/image7.png "这些屏幕截图显示了每种样式的显示方式")](customizing-table-appearance-images/image7.png#lightbox)

该示例**CellDefaultTable**包含用于生成这些屏幕的代码。 在中设置的单元格样式`UITableViewCell`构造函数，如下：

```csharp
cell = new UITableViewCell (UITableViewCellStyle.Default, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Subtitle, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value1, cellIdentifier);
//cell = new UITableViewCell (UITableViewCellStyle.Value2, cellIdentifier);
```

[支持的属性](xref:UIKit.UITableViewCell)设置中的单元格样式然后：

```csharp
cell.TextLabel.Text = tableItems[indexPath.Row].Heading;
cell.DetailTextLabel.Text = tableItems[indexPath.Row].SubHeading;
cell.ImageView.Image = UIImage.FromFile("Images/" + tableItems[indexPath.Row].ImageName); // don't use for Value2
```

## <a name="accessories"></a>附件

单元格都可以具有以下附件添加到视图的右侧：

-   **复选标记**– 能用来表示表中的多选。
-   **DetailButton** – 响应触摸独立于其他单元格，以便它可以执行到接触的单元格本身的不同函数 (如打开一个弹出窗口或新的窗口，不是属于`UINavigationController`堆栈)。
-   **DisclosureIndicator** -通常用于指示接触单元格将会打开另一个视图。
-   **DetailDisclosureButton** – 的组合`DetailButton`和`DisclosureIndicator`。


这是它们的外观：

 [![](customizing-table-appearance-images/image8.png "示例附件")](customizing-table-appearance-images/image8.png#lightbox)

若要显示一个可以设置这些附件`Accessory`中的属性`GetCell`方法：

```csharp
cell.Accessory = UITableViewCellAccessory.Checkmark;
//cell.Accessory = UITableViewCellAccessory.DisclosureIndicator;
//cell.Accessory = UITableViewCellAccessory.DetailDisclosureButton; // implement AccessoryButtonTapped
//cell.Accessory = UITableViewCellAccessory.None; // to clear the accessory
```

当`DetailButton`或`DetailDisclosureButton`所示，还应该重写`AccessoryButtonTapped`接触时执行某些操作。

```csharp
public override void AccessoryButtonTapped (UITableView tableView, NSIndexPath indexPath)
{
    UIAlertController okAlertController = UIAlertController.Create ("DetailDisclosureButton Touched", tableItems[indexPath.Row].Heading, UIAlertControllerStyle.Alert);
    okAlertController.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
    owner.PresentViewController (okAlertController, true, null);

    tableView.DeselectRow (indexPath, true);
}
```

该示例**CellAccessoryTable**演示如何使用附件。

## <a name="cell-separators"></a>单元格分隔符

单元格分隔符是用于分隔表的表单元格。 在表上设置属性。

```csharp
TableView.SeparatorColor = UIColor.Blue;
TableView.SeparatorStyle = UITableViewCellSeparatorStyle.DoubleLineEtched;
```

它，还可以将模糊或活力效果添加到分隔符：

```csharp
// blur effect
TableView.SeparatorEffect =
    UIBlurEffect.FromStyle(UIBlurEffectStyle.Dark);

//vibrancy effect
var effect = UIBlurEffect.FromStyle(UIBlurEffectStyle.Light);
TableView.SeparatorEffect = UIVibrancyEffect.FromBlurEffect(effect);
```

分隔符可以嵌入在：

```csharp
TableView.SeparatorInset.InsetRect(new CGRect(4, 4, 150, 2));
```

## <a name="creating-custom-cell-layouts"></a>创建自定义单元格布局

若要更改的视觉样式的表需要提供自定义单元格，才能显示。 自定义单元格可以具有不同的颜色和控件布局。

CellCustomTable 示例实现`UITableViewCell`定义的自定义布局的子类`UILabel`s 和`UIImage`使用不同的字体和颜色。 生成的单元格如下所示：

 [![](customizing-table-appearance-images/image9.png "自定义单元格布局")](customizing-table-appearance-images/image9.png#lightbox)

自定义单元格类包含只有三个方法：

-   **构造函数**– 创建 UI 控件和设置的自定义的样式属性 （例如。 字体、 大小和颜色）。
-   **UpdateCell** – 一种方法`UITableView.GetCell`要用于设置单元格的属性。
-   **LayoutSubviews** – 设置 UI 控件的位置。 在示例中的每个单元都具有相同的布局，但更复杂的单元格 （特别是那些具有不同大小） 可能需要不同的布局位置，具体取决于所显示的内容。


中的完整示例代码**CellCustomTable > CustomVegeCell.cs**后面：

```csharp
public class CustomVegeCell : UITableViewCell  {
    UILabel headingLabel, subheadingLabel;
    UIImageView imageView;
    public CustomVegeCell (NSString cellId) : base (UITableViewCellStyle.Default, cellId)
    {
        SelectionStyle = UITableViewCellSelectionStyle.Gray;
        ContentView.BackgroundColor = UIColor.FromRGB (218, 255, 127);
        imageView = new UIImageView();
        headingLabel = new UILabel () {
            Font = UIFont.FromName("Cochin-BoldItalic", 22f),
            TextColor = UIColor.FromRGB (127, 51, 0),
            BackgroundColor = UIColor.Clear
        };
        subheadingLabel = new UILabel () {
            Font = UIFont.FromName("AmericanTypewriter", 12f),
            TextColor = UIColor.FromRGB (38, 127, 0),
            TextAlignment = UITextAlignment.Center,
            BackgroundColor = UIColor.Clear
        };
        ContentView.AddSubviews(new UIView[] {headingLabel, subheadingLabel, imageView});

    }
    public void UpdateCell (string caption, string subtitle, UIImage image)
    {
        imageView.Image = image;
        headingLabel.Text = caption;
        subheadingLabel.Text = subtitle;
    }
    public override void LayoutSubviews ()
    {
        base.LayoutSubviews ();
        imageView.Frame = new CGRect (ContentView.Bounds.Width - 63, 5, 33, 33);
        headingLabel.Frame = new CGRect (5, 4, ContentView.Bounds.Width - 63, 25);
        subheadingLabel.Frame = new CGRect (100, 18, 100, 20);
    }
}
```

`GetCell`方法的`UITableViewSource`需要对其进行修改来创建自定义单元格：

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (cellIdentifier) as CustomVegeCell;
    if (cell == null)
        cell = new CustomVegeCell (cellIdentifier);
    cell.UpdateCell (tableItems[indexPath.Row].Heading
            , tableItems[indexPath.Row].SubHeading
            , UIImage.FromFile ("Images/" + tableItems[indexPath.Row].ImageName) );
    return cell;
}
```



## <a name="related-links"></a>相关链接

- [WorkingWithTables （示例）](https://developer.xamarin.com/samples/monotouch/WorkingWithTables)

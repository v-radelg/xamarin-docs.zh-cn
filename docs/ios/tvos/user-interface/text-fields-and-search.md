---
title: 使用 tvOS 文本并在 Xamarin 中的搜索字段
description: 本文档介绍如何在 tvOS 应用程序中使用 Xamarin 生成的文本和搜索字段使用。 它提供的文本和搜索字段的高级概述，并讨论了键盘、 情节提要集成和搜索数据模型的详细信息。
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f1085044f2147bec0910a87f8a6174c4648ef9b8
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61162046"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>使用 tvOS 文本并在 Xamarin 中的搜索字段

Xamarin.tvOS 应用在需要时，可以从用户 （如用户 Id 和密码） 请求小段文本使用文本字段和屏幕键盘：

[![](text-fields-and-search-images/intro01.png "示例搜索字段")](text-fields-and-search-images/intro01.png#lightbox)

你可以选择提供应用程序的内容使用搜索字段的关键字搜索的能力：

[![](text-fields-and-search-images/intro02.png "示例搜索结果")](text-fields-and-search-images/intro02.png#lightbox)

本文档将介绍使用 Xamarin.tvOS 应用中的文本和搜索字段的详细信息。

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>有关文本和搜索字段

如上所述，如果需要，你 Xamarin.tvOS 可以提供一个或多个文本字段来从用户使用收集少量文本屏幕上 （或可选的蓝牙键盘，具体取决于 tvOS 用户已安装的版本）。 

此外，如果您的应用程序向用户 （如音乐、 电影或图片集） 提供大量的内容，您可能想要包括的搜索字段。 用户可以输入少量文本以筛选可用的项目的列表。

<a name="Text-Fields" />

## <a name="text-fields"></a>文本字段

TvOS 文本字段提供了几种会弹出一个固定高度，舍入角输入框为屏幕键盘时在用户单击它：

[![](text-fields-and-search-images/text01.png "文本字段中 tvOS")](text-fields-and-search-images/text01.png#lightbox)

当用户将[焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)到给定的文本字段，它将变得更大，并显示深度阴影。 需要记住这一点时设计用户界面，如文本字段可以覆盖其他 UI 元素时的焦点。

Apple 已使用的文本字段的以下建议：

- **使用文本尽量少条目**-的性质屏幕键盘、 输入文本的较长部分或填充多个文本字段会向用户很麻烦。 更好的解决方案是通过使用选择列表限制的文本输入量或[按钮](~/ios/tvos/user-interface/buttons.md)。
- **使用对通信目的提示**-文本字段可以为空时显示占位符"提示"。 在适用的情况下，使用提示来描述您而不是单独的标签的文本字段的用途。
- **选择适当的默认键盘类型**-tvOS 提供了几个不同，专门构建键盘类型可以指定为文本字段。 例如，电子邮件地址键盘可以简化通过允许用户从最近输入的地址的列表中选择的项。
- **在适当时使用安全的文本字段**-保护文本字段显示为点 （而不是真正的字母） 输入的字符。 收集敏感信息，如密码时，始终使用安全文本字段。

<a name="Keyboards" />

## <a name="keyboards"></a>键盘

每当用户单击文本字段的用户界面中，线性屏幕上会显示键盘。 在用户使用 Touch 面[Siri 远程](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote)从键盘选择单个字母，然后输入所需的信息：

[![](text-fields-and-search-images/keyboard01.png "Siri 远程键盘")](text-fields-and-search-images/keyboard01.png#lightbox)

如果在当前视图中，多个文本字段**下一步**按钮会自动显示，让用户可以在下一步的文本字段。 一个**完成**按钮将显示为最后一个文本字段，将结束文本输入并将用户返回到上一屏幕。 

在任何时候，用户还可以按**菜单**Siri 远程结束文本输入并再次返回到上一屏幕上的按钮。

Apple 具有以下建议以获得使用屏幕键盘：

- **选择适当的默认键盘类型**-tvOS 提供了几个不同，专门构建键盘类型可以指定为文本字段。 例如，电子邮件地址键盘可以简化通过允许用户从最近输入的地址的列表中选择的项。
- **在适当情况下，使用键盘附件视图**-除了标准始终是显示 （可选） 附件视图 （如图像或标签） 的信息可以添加到屏幕键盘来阐明用途的文本输入或设置为帮助用户输入所需的信息。

有关详细信息，了解如何使用屏幕键盘，请参阅 Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType)，[管理键盘](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1)，[数据输入的自定义视图](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1)和[文本 iOS 编程指南](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html)文档。

<a name="Search" />

## <a name="search"></a>搜索

搜索字段显示提供文本字段的专用的屏幕和屏幕键盘，允许用户筛选键盘如下所示的项的集合：

[![](text-fields-and-search-images/search01.png "示例搜索结果")](text-fields-and-search-images/search01.png#lightbox)

用户在搜索字段中输入字母，如下面的结果将自动反映搜索的结果。 在任何时候，用户可以将焦点切换到结果，并选择显示的项之一。

Apple 已使用的搜索字段的以下建议：

- **提供最近搜索**-因为 Siri 远程使用输入文本可能会很麻烦，用户往往可以重复搜索请求，请考虑添加的最新的搜索结果下的键盘区域的当前结果之前的部分。
- **如果可能，请限制结果数**-因为大型项目列表很难对用户来解析和导航，请考虑将限制返回的结果数。
- **如果需要，提供搜索结果筛选器**-如果你的应用提供的内容将租借自身，请考虑添加范围条形图，以允许用户以进一步筛选返回的搜索结果。

有关详细信息，请参阅 Apple [UISearchController 类引用](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html)。

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>使用的文本字段

若要使用 Xamarin.tvOS 应用中的文本字段的最简单方法是将它们添加到使用 iOS 设计器用户界面设计。

请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 在中**Solution Pad**，双击`Main.storyboard`文件将其打开进行编辑。
1. 将一个或多个**文本字段**int 设计图面上拖动到视图： 

    [![](text-fields-and-search-images/text02.png "文本字段")](text-fields-and-search-images/text02.png#lightbox)
1. 选择**文本字段**，并为每个提供一个唯一**名称**中**小组件**选项卡**Properties Pad**: 

    [![](text-fields-and-search-images/text03.png "小组件选项卡的属性面板")](text-fields-and-search-images/text03.png#lightbox)
1. 在中**文本字段**部分中，可以定义元素，如**占位符**提示和默认**值**: 

    [![](text-fields-and-search-images/text04.png "文本字段部分")](text-fields-and-search-images/text04.png#lightbox)
1. 向下滚动以定义属性，如**拼写检查**，**大写**和默认**键盘类型**: 

    [![](text-fields-and-search-images/text05.png "拼写检查、 大小写和默认键盘类型")](text-fields-and-search-images/text05.png#lightbox) 
1. 将所做的更改保存到你的情节提要。
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在“解决方案资源管理器”中，双击 `Main.storyboard` 文件，将其打开进行编辑。
1. 将一个或多个**文本字段**int 设计图面上拖动到视图： 

    [![](text-fields-and-search-images/text02-vs.png "文本字段")](text-fields-and-search-images/text02-vs.png#lightbox)
1. 选择**文本字段**，并为每个提供一个唯一**名称**中**小组件**选项卡**属性资源管理器**: 

    [![](text-fields-and-search-images/text03-vs.png "小组件选项卡")](text-fields-and-search-images/text03-vs.png#lightbox)
1. 在中**文本字段**部分中，可以定义元素，如**占位符**提示和默认**值**: 

    [![](text-fields-and-search-images/text04-vs.png "文本字段部分")](text-fields-and-search-images/text04-vs.png#lightbox)
1. 向下滚动以定义属性，如**拼写检查**，**大写**和默认**键盘类型**: 

    [![](text-fields-and-search-images/text05-vs.png "拼写检查、 大小写和默认键盘类型")](text-fields-and-search-images/text05-vs.png#lightbox) 
1. 将所做的更改保存到你的情节提要。
    
-----

在代码中，你可以获取或设置文本字段使用的值及其`Text`属性：

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

你可以选择使用`Started`和`Ended`文本字段事件来响应文本输入开始和结束。

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>使用搜索字段

若要使用搜索字段 Xamarin.tvOS 应用中的最简单方法是将它们添加到使用接口设计器用户界面设计。

请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
1. 在中**Solution Pad**，双击`Main.storyboard`文件将其打开进行编辑。
1. 将新的集合视图控制器拖动到情节提要来显示用户的搜索结果： 

    [![](text-fields-and-search-images/search02.png "集合视图控制器")](text-fields-and-search-images/search02.png#lightbox)
1. 中**小组件**选项卡**Properties Pad**，使用`SearchResultsViewController`有关**类**并`SearchResults`为**情节提要 ID**: 

    [![](text-fields-and-search-images/search03.png "小组件选项卡")](text-fields-and-search-images/search03.png#lightbox)
1. 选择**单元格原型**设计图面上。
1. 中**小组件**选项卡**属性资源管理器**，使用`SearchResultCell`有关**类**并`ImageCell`为**标识符**: 

    [![](text-fields-and-search-images/search04.png "小组件选项卡")](text-fields-and-search-images/search04.png#lightbox)
1. 布局设计的**单元格原型**，并公开每个元素与唯一**名称**中**小组件**选项卡**属性资源管理器**: 

    [![](text-fields-and-search-images/search05.png "布局的单元格原型设计")](text-fields-and-search-images/search05.png#lightbox)
1. 将所做的更改保存到你的情节提要。
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在“解决方案资源管理器”中，双击 `Main.storyboard` 文件，将其打开进行编辑。
1. 将新的集合视图控制器拖动到情节提要来显示用户的搜索结果： 

    [![](text-fields-and-search-images/seach02-vs.png "集合视图控制器")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. 中**小组件**选项卡**属性资源管理器**，使用`SearchResultsViewController`有关**类**并`SearchResults`为**情节提要 ID**: 

    [![](text-fields-and-search-images/search03-vs.png "小组件选项卡")](text-fields-and-search-images/search03-vs.png#lightbox)
1. 选择**单元格原型**设计图面上。
1. 中**小组件**选项卡**属性资源管理器**，使用`SearchResultCell`有关**类**并`ImageCell`为**标识符**: 

    [![](text-fields-and-search-images/search04-vs.png "小组件选项卡")](text-fields-and-search-images/search04-vs.png#lightbox)
1. 布局设计的**单元格原型**，并公开每个元素与唯一**名称**中**小组件**选项卡**属性资源管理器**: 

    [![](text-fields-and-search-images/search05-vs.png "布局的单元格原型设计")](text-fields-and-search-images/search05-vs.png#lightbox)
1. 将所做的更改保存到你的情节提要。
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>提供的数据模型

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

接下来，需要提供一个类来充当用户将搜索的数据模型的结果。 在中**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新文件...**  > **常规** > **空类**，并提供**名称**: 

[![](text-fields-and-search-images/search06.png "选择空类，并提供一个名称")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

接下来，需要提供一个类来充当用户将搜索的数据模型的结果。 在中**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新项...**  >  **Apple** > **杂项** > **类**并提供**名称**: 

[![](text-fields-and-search-images/search06-vs.png "选择类，并提供一个名称")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

例如，允许用户通过标题和关键字的图片在集合中搜索的应用可能如下所示：

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell" />

### <a name="the-collection-view-cell"></a>集合视图单元格

与数据模型，编辑**原型单元格**(`SearchResultViewCell.cs`)，并使其查看位于以下：

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

`UpdateUI`方法将用于显示每个字段的**PictureInformation**项 (`PictureInfo`属性) 中命名的 UI 元素每次更新的属性是。 例如，图像和图片与关联的标题。

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>集合视图控制器

接下来，编辑搜索结果集合视图控制器 (`SearchResultsViewController.cs`)，并使其如下所示：

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

首先，`IUISearchResultsUpdating`接口添加到类，以处理正在更新用户的搜索控制器筛选器：

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

一个常量也定义为指定的 ID**原型单元格**（的匹配上述接口设计器中定义的 ID），将在稍后集合控制器请求新的单元格时：

```csharp
public const string CellID = "ImageCell";
```

要搜索的搜索筛选器术语的项的完整列表创建存储和该词匹配的项的列表：

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

当`SearchFilter`是发生更改时，匹配项的列表会更新并重新加载集合视图的内容。 `FindPictures`例程负责查找新的搜索词匹配的项：

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

值`SearchFilter`（将更新结果集合视图） 将更新用户时更改搜索控制器中的筛选器：

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

`PopulatePictures`方法最初填充的可用项的集合：

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

对于此示例，示例数据的所有正在创建内存中集合视图控制器加载时。 在实际应用中，可能会从数据库或 web 服务，读取此数据，仅在需要时以防止无限大 Apple tv 设备有限的内存。

`NumberOfSections`和`GetItemsCount`方法提供了匹配项的数目：

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

`GetCell`方法返回一个新**原型单元格**(基于`CellID`上面定义的演示图板中) 的集合视图中的每个项：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

`WillDisplayCell`之前正在显示，因此可以将它配置的单元格调用方法：

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

`DidUpdateFocus`方法向用户提供可视反馈，因为它们突出显示的结果集合视图中的项：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

最后，`ItemSelected`方法处理结果集合视图中选择项目 （单击 Touch 面与 Siri 远程） 的用户：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

如果在搜索字段已显示为模式对话框视图 （通过调用它的视图的顶部），使用`DismissViewController`方法以关闭搜索视图，当用户选择某个项。 对于此示例中，搜索字段将显示作为内容选项卡视图选项卡上，以便它不正在解除此处。

集合视图的详细信息，请参阅我们[使用集合视图](~/ios/tvos/user-interface/collection-views.md)文档。

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>显示搜索字段

有两种主要方法的搜索字段 （和其关联的屏幕键盘和搜索结果） 可以提供对 tvOS 中的用户： 

- **模式对话框视图**的视图和视图为全屏显示模式对话框视图控制器，可以通过当前显示的搜索字段。 这通常是以响应用户单击按钮或其他 UI 元素。 当用户从搜索结果中选择某个项时，关闭对话框。
- **查看内容**-搜索字段是直接给定视图的一部分。 例如，为选项卡视图控制器中的搜索选项卡的内容。

有关图片上面给出的可搜索列表的示例，搜索字段显示为搜索选项卡中查看内容和搜索选项卡视图控制器会查找如下所示：

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

首先，定义一个常量相匹配**情节提要标识符**的已分配到界面设计器中的搜索结果集合视图控制器：

```csharp
public const string SearchResultsID = "SearchResults";
```

下一步，`ShowSearchController`方法创建新的搜索视图集合控制器，并需要它的显示：

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

在上面的方法中，一次`SearchResultsViewController`已从情节提要，实例化一个新`UISearchController`创建显示搜索字段，并向用户屏幕键盘。 搜索结果集合 (由定义`SearchResultsViewController`) 就显示在此键盘下。

下一步，`SearchBar`配置了信息如**占位符**提示。 本文对有关类型的搜索正在执行用户提供信息。

然后搜索字段中有两种向用户显示：

- **模式对话框视图**-`PresentViewController`调用方法来对现有视图，显示搜索全屏幕。
- **查看内容**-`UISearchContainerViewController`创建以包含搜索控制器。 一个`UINavigationController`将创建以包含搜索容器，则导航控制器添加到视图控制器`AddChildViewController (navController)`，并显示的视图`View.Add (navController.View)`。

最后，并再次根据表示类型中，或者`ViewDidLoad`或`ViewDidAppear`方法将调用`ShowSearchController`方法以向用户提供搜索：

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

在运行该应用，并由用户选择搜索选项卡，将向用户显示的项的完整未筛选的列表：

[![](text-fields-and-search-images/intro02.png "默认搜索结果")](text-fields-and-search-images/intro02.png#lightbox)

当用户开始输入搜索词，则将筛选该术语的结果列表中的，并将其自动更新：

[![](text-fields-and-search-images/intro03.png "筛选的搜索结果")](text-fields-and-search-images/intro03.png#lightbox)

在任何时候，用户可以焦点切换到搜索结果中的项目，并单击以选中它的 Siri 远程 Touch 面。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和在 Xamarin.tvOS 应用内处理文本和搜索字段。 它介绍了如何在接口设计器中创建的文本和搜索集合的内容，它介绍了两个搜索字段可以提供对 tvOS 中的用户的各种方法。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)

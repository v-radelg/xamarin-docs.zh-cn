---
title: 使用 tvOS 在 Xamarin 中的集合视图
description: 本文档介绍如何使用在 tvOS 应用程序中使用 Xamarin 生成的集合视图。 它介绍了集合视图布局，创建的单元格和补充视图，响应用户事件和的详细信息。
ms.prod: xamarin
ms.assetid: 5125C4C7-2DDF-4C19-A362-17BB2B079178
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f815afa6b1abb15348019b0c53333b4acb054008
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60933636"
---
# <a name="working-with-tvos-collection-views-in-xamarin"></a>使用 tvOS 在 Xamarin 中的集合视图

集合视图内容的一组允许使用任意布局显示。 使用内置支持，它们允许轻松创建类似于网格的或线性布局，同时还支持自定义布局。

[![](collection-views-images/collection01.png "示例集合视图")](collection-views-images/collection01.png#lightbox)

集合视图会保持使用委托和数据源提供用户交互和集合的内容的项的集合。 由于集合视图基于独立于视图本身的布局子系统，提供不同的布局可以轻松更改集合视图的数据上的动态表示的形式。

<a name="About-Collection-Views" />

## <a name="about-collection-views"></a>有关集合视图

如以上集合视图中所述 (`UICollectionView`) 管理的项的有序的集合并为这些项目提供的自定义布局。 集合视图到表视图相似的方式的效果 (`UITableView`)，但他们可以使用在多个只是单个列中存在的项的布局。

使用集合视图在 tvOS 中，您的应用程序时，负责提供到集合并使用数据源相关联的数据 (`UICollectionViewDataSource`)。 可以根据需要组织集合视图数据，并为其呈现为不同的组 （节）。

集合视图将显示在屏幕上的单元格的各个项 (`UICollectionViewCell`)，它提供表示法的一段给定的集合 （如图像和它的标题） 中的信息。

（可选） 可以将补充视图添加到集合视图的演示文稿，将其用作标头和表尾部分和单元格。 集合视图的布局负责定义各个单元格以及这些视图的位置。

集合视图可以响应用户交互使用委托 (`UICollectionViewDelegate`)。 此委托是还负责确定如果给定的单元格可以突出显示了一个单元格时获得焦点时，或选择一个。 在某些情况下，该委托确定各个单元格的大小。

<a name="Collection-View-Layouts" />

## <a name="collection-view-layouts"></a>集合视图布局

集合视图的一个重要功能是它所呈现的数据和其布局及其分开。 集合视图布局 (`UICollectionViewLayout`) 负责为组织和单元格 （和任何补充视图） 的位置提供，该集合视图的屏幕上的演示文稿中。

每个单元格中创建的集合视图从其连接的数据源是排列并显示给定集合视图布局。

创建集合视图时，通常被提供集合视图布局。 但是，可以在任何时候更改集合视图布局，并自动将使用提供的新布局更新集合视图的数据在屏幕上显示。

集合视图布局提供了一些可用于对两个不同的布局 （默认情况下完成没有动画） 之间的转换进行动画处理的方法。 此外，若要进一步进行用户交互的布局中的更改会导致动画处理的手势识别器可以处理集合视图布局。

<a name="Creating-Cells-and-Supplementary-Views" />

## <a name="creating-cells-and-supplementary-views"></a>创建单元格和补充视图

集合视图的数据源不只负责提供用于显示内容的单元格的项，但是还支持集合的数据。

因为集合视图被设计为处理大量的项，可以取消排队并重复使用，以防止无限大内存限制各个单元格。 有两种不同方法的取消排队视图：

- `DequeueReusableCell` -创建或返回类型 （如应用程序的演示图板中指定） 的给定类型的单元格。
- `DequeueReusableSupplementaryView` -创建或返回给定类型 （如应用程序的演示图板中指定） 的补充视图。

在调用之前其中一种方法，必须注册类，情节提要或`.xib`文件用来创建与集合视图单元格的视图。 例如：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    ...
}
```

其中`typeof(CityCollectionViewCell)`提供支持视图的类和`CityViewDatasource.CardCellId`提供取消排队的单元格 （或视图） 时使用的 ID。

单元格取消排队后，您将其配置与它表示的项的数据，并返回到集合视图，以显示。

<a name="About-Collection-View-Controllers" />

## <a name="about-collection-view-controllers"></a>有关集合视图控制器

集合视图控制器 (`UICollectionViewController`) 是专门的视图控制器 (`UIViewController`)，它提供以下行为：

- 它负责从其情节提要加载集合视图或`.xib`文件和实例化视图。 如果在代码中创建，会自动创建新的、 未配置的集合视图。
- 集合视图加载后，控制器会尝试从情节提要加载其数据源和委托或`.xib`文件。 如果都不可用，它本身设置为这两者的源。
- 确保数据集合视图填充在第一个显示之前, 加载和重新加载并清除每个后续的显示器上选择。

此外，集合视图控制器提供了可用于管理的集合视图生命周期，例如的可重写方法`AwakeFromNib`和`ViewWillDisplay`。

<a name="Collection-Views-and-Storyboards" />

## <a name="collection-views-and-storyboards"></a>集合视图和情节提要

使用在 Xamarin.tvOS 应用中，一个集合视图的最简单方法是添加到其情节提要。 作为一个快速示例，我们要创建的示例应用的呈现图像、 标题和选择按钮。 如果用户单击选择按钮，一个集合视图将显示允许用户选择新图像。 选择映像后，集合视图已关闭，并且将显示新的图像和标题。

让我们执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

    
1. 开始一个新**单个视图 tvOS 应用**在 Visual Studio for mac。
1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，并在 iOS 设计器中打开它。
1. 将图像视图、 标签和一个按钮添加到现有视图，并将其配置为如下所示： 

    [![](collection-views-images/collection02.png "示例布局")](collection-views-images/collection02.png#lightbox)
1. 将分配**名称**为图像视图，并在标签**小组件选项卡**的**属性资源管理器**。 例如： 

    [![](collection-views-images/collection03.png "设置名称")](collection-views-images/collection03.png#lightbox)
1. 接下来，将集合视图控制器拖动到情节提要上： 

    [![](collection-views-images/collection04.png "集合视图控制器")](collection-views-images/collection04.png#lightbox)
1. 控制拖动按钮从到集合视图控制器，然后选择**推送**从弹出窗口： 

    [![](collection-views-images/collection05.png "从弹出选择推送")](collection-views-images/collection05.png#lightbox)
1. 当应用运行时，这将使每当用户单击按钮时要显示的集合视图。
1. 选择集合视图，并输入以下值中的**布局选项卡**的**属性资源管理器**: 

    [![](collection-views-images/collection06.png "在属性资源管理器")](collection-views-images/collection06.png#lightbox)
1. 此参数控制各个单元格和之间的单元格和集合视图的外边缘的边框的大小。
1. 选择集合视图控制器，并将其类设置为`CityCollectionViewController`中**小组件选项卡**: 

    [![](collection-views-images/collection07.png "将类设置为 CityCollectionViewController")](collection-views-images/collection07.png#lightbox)
1. 选择集合视图并将其类设置为`CityCollectionView`中**小组件选项卡**: 

    [![](collection-views-images/collection08.png "将类设置为 CityCollectionView")](collection-views-images/collection08.png#lightbox)
1. 选择集合视图单元格并将其类设置为`CityCollectionViewCell`中**小组件选项卡**: 

    [![](collection-views-images/collection09.png "将类设置为 CityCollectionViewCell")](collection-views-images/collection09.png#lightbox)
1. 在中**小组件选项卡**，请确保**布局**是`Flow`并**滚动方向**是`Vertical`集合视图： 

    [![](collection-views-images/collection10.png "小组件选项卡")](collection-views-images/collection10.png#lightbox)
1. 选择集合视图单元格并设置其**标识**到`CityCell`中**小组件选项卡**: 

    [![](collection-views-images/collection11.png "将标识设置为 CityCell")](collection-views-images/collection11.png#lightbox)
1. 保存更改。
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. 开始一个新**单个视图 tvOS 应用**Visual Studio 中。
1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件，并在 iOS 设计器中打开它。
1. 将图像视图、 标签和一个按钮添加到现有视图，并将其配置为如下所示： 

    [![](collection-views-images/collection02vs.png "配置的布局")](collection-views-images/collection02vs.png#lightbox)
1. 将分配**名称**为图像视图，并在标签**小组件选项卡**的**属性资源管理器**。 例如： 

    [![](collection-views-images/collection03vs.png "在属性资源管理器")](collection-views-images/collection03vs.png#lightbox)
1. 接下来，将集合视图控制器拖动到情节提要上： 

    [![](collection-views-images/collection04vs.png "集合视图控制器")](collection-views-images/collection04vs.png#lightbox)
1. 控制拖动按钮从到集合视图控制器，然后选择**推送**从弹出窗口： 

    [![](collection-views-images/collection05vs.png "从弹出选择推送")](collection-views-images/collection05vs.png#lightbox)
1. 当应用运行时，这将使每当用户单击按钮时要显示的集合视图。
1. 选择集合视图并在**布局选项卡**的**属性资源管理器**输入**宽度**作为_361_和**高度**作为_256_ 
1. 此参数控制各个单元格和之间的单元格和集合视图的外边缘的边框的大小。
1. 选择集合视图控制器，并将其类设置为`CityCollectionViewController`中**小组件选项卡**: 

    [![](collection-views-images/collection07vs.png "将类设置为 CityCollectionViewController")](collection-views-images/collection07vs.png#lightbox)
1. 选择集合视图并将其类设置为`CityCollectionView`中**小组件选项卡**: 

    [![](collection-views-images/collection08vs.png "将类设置为 CityCollectionView")](collection-views-images/collection08vs.png#lightbox)
1. 选择集合视图单元格并将其类设置为`CityCollectionViewCell`中**小组件选项卡**: 

    [![](collection-views-images/collection09vs.png "将类设置为 CityCollectionViewCell")](collection-views-images/collection09vs.png#lightbox)
1. 在中**小组件选项卡**，请确保**布局**是`Flow`并**滚动方向**是`Vertical`集合视图： 

    [![](collection-views-images/collection10vs.png "参阅小组件选项卡")](collection-views-images/collection10vs.png#lightbox)
1. 选择集合视图单元格并设置其**标识**到`CityCell`中**小组件选项卡**: 

    [![](collection-views-images/collection11vs.png "将标识设置为 CityCell")](collection-views-images/collection11vs.png#lightbox)
1. 保存更改。
    

-----

如果选择了我们`Custom`集合视图**布局**，我们可以指定自定义布局。 Apple 提供了内置`UICollectionViewFlowLayout`并`UICollectionViewDelegateFlowLayout`，可以轻松地提供基于网格的布局中的数据 (将使用这些`flow`布局样式)。 

使用情节提要的详细信息，请参阅我们[你好，tvOS 快速入门指南](~/ios/tvos/get-started/hello-tvos.md)。

<a name="Providing-Data-for-the-Collection-View" />

## <a name="providing-data-for-the-collection-view"></a>为集合视图提供数据

现在，我们已添加到自己的情节提要我们集合视图 （和集合视图控制器），我们需要找不到提供的数据。 

<a name="The-Data-Model" />

### <a name="the-data-model"></a>数据模型

首先，我们将创建包含要显示的标题和标志允许城市要选择的图像的文件名我们数据的模型。

创建`CityInfo`类，并使其看起来如下所示：

```csharp
using System;

namespace tvCollection
{
    public class CityInfo
    {
        #region Computed Properties
        public string ImageFilename { get; set; }
        public string Title { get; set; }
        public bool CanSelect{ get; set; }
        #endregion

        #region Constructors
        public CityInfo (string filename, string title, bool canSelect)
        {
            // Initialize
            this.ImageFilename = filename;
            this.Title = title;
            this.CanSelect = canSelect;
        }
        #endregion
    }
}
```

### <a name="the-collection-view-cell"></a>集合视图单元格

现在，我们需要定义数据的每个单元的显示方式。 编辑`CityCollectionViewCell.cs`（自动为你创建从情节提要文件） 的文件，并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using CoreGraphics;

namespace tvCollection
{
    public partial class CityCollectionViewCell : UICollectionViewCell
    {
        #region Private Variables
        private CityInfo _city;
        #endregion

        #region Computed Properties
        public UIImageView CityView { get ; set; }
        public UILabel CityTitle { get; set; }

        public CityInfo City {
            get { return _city; }
            set {
                _city = value;
                CityView.Image = UIImage.FromFile (City.ImageFilename);
                CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
                CityTitle.Text = City.Title;
            }
        }
        #endregion

        #region Constructors
        public CityCollectionViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            CityView = new UIImageView(new CGRect(22, 19, 320, 171));
            CityView.AdjustsImageWhenAncestorFocused = true;
            AddSubview (CityView);

            CityTitle = new UILabel (new CGRect (22, 209, 320, 21)) {
                TextAlignment = UITextAlignment.Center,
                TextColor = UIColor.White,
                Alpha = 0.0f
            };
            AddSubview (CityTitle);
        }
        #endregion
    

    }
}
```

我们将对 tvOS 应用，显示的映像和可选的标题。 如果不能选择给定的城市，我们会变暗图像视图使用以下代码：

```csharp
CityView.Alpha = (City.CanSelect) ? 1.0f : 0.5f;
```

包含图像的单元格使用户的焦点时, 我们想要使用内置视差效果对其进行设置以下属性：

```csharp
CityView.AdjustsImageWhenAncestorFocused = true;
```

有关导航和焦点的详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)并[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。


<a name="The-Collection-View-Data-Provider" />

### <a name="the-collection-view-data-provider"></a>集合视图的数据提供程序

使用我们创建的数据模型和我们定义的单元格布局，让我们为我们集合视图创建数据源。 数据源将负责将显示在屏幕上的各个单元格的单元格不仅提供支持的数据，但还取消排队。

创建`CityViewDatasource`类，并使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using ObjCRuntime;

namespace tvCollection
{
    public class CityViewDatasource : UICollectionViewDataSource
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Static Constants
        public static NSString CardCellId = new NSString ("CityCell");
        #endregion

        #region Computed Properties
        public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
        public CityCollectionView ViewController { get; set; }
        #endregion

        #region Constructors
        public CityViewDatasource (CityCollectionView controller)
        {
            // Initialize
            this.ViewController = controller;
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities() {

            // Clear existing cities
            Cities.Clear();

            // Add new cities
            Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
            Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
            Cities.Add(new CityInfo("City03.jpg", "Skyline at Night", true));
            Cities.Add(new CityInfo("City04.jpg", "Golden Gate Bridge", true));
            Cities.Add(new CityInfo("City05.jpg", "Roads by Night", true));
            Cities.Add(new CityInfo("City06.jpg", "Church Domes", true));
            Cities.Add(new CityInfo("City07.jpg", "Mountain Lights", true));
            Cities.Add(new CityInfo("City08.jpg", "City Scene", false));
            Cities.Add(new CityInfo("City09.jpg", "House in Winter", true));
            Cities.Add(new CityInfo("City10.jpg", "By the Lake", true));
            Cities.Add(new CityInfo("City11.jpg", "At the Dome", true));
            Cities.Add(new CityInfo("City12.jpg", "Cityscape", true));
            Cities.Add(new CityInfo("City13.jpg", "Model City", true));
            Cities.Add(new CityInfo("City14.jpg", "Taxi, Taxi!", true));
            Cities.Add(new CityInfo("City15.jpg", "On the Sidewalk", true));
            Cities.Add(new CityInfo("City16.jpg", "Midnight Walk", true));
            Cities.Add(new CityInfo("City17.jpg", "Lunchtime Cafe", true));
            Cities.Add(new CityInfo("City18.jpg", "Coffee Shop", true));
            Cities.Add(new CityInfo("City19.jpg", "Rustic Tavern", true));
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            return Cities.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
            var city = Cities [indexPath.Row];

            // Initialize city
            cityCell.City = city;

            return cityCell;
        }
        #endregion
    }
}
```

允许查看详细信息中此类。 首先，我们继承`UICollectionViewDataSource`，并向单元格 ID （即我们分配 iOS 设计器中） 提供一种快捷方式：

```csharp
public static NSString CardCellId = new NSString ("CityCell");
```

接下来我们为我们收集的数据提供存储，并提供用于填充数据的类：

```csharp
public List<CityInfo> Cities { get; set; } = new List<CityInfo>();
...

public void PopulateCities() {

    // Clear existing cities
    Cities.Clear();

    // Add new cities
    Cities.Add(new CityInfo("City01.jpg", "Houses by Water", false));
    Cities.Add(new CityInfo("City02.jpg", "Turning Circle", true));
    ...
}
```

然后我们重写`NumberOfSections`方法并返回数字部分 （项的组），查看我们的集合。 在这种情况下，还有一个：

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    return 1;
}
```

接下来，我们使用下面的代码我们集合中返回的项数：

```csharp
public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    return Cities.Count;
}
```

最后，我们取消排队的可重用单元格时集合视图请求与以下代码：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var cityCell = (CityCollectionViewCell)collectionView.DequeueReusableCell (CardCellId, indexPath);
    var city = Cities [indexPath.Row];

    // Initialize city
    cityCell.City = city;

    return cityCell;
}
```

我们获取的集合视图单元格后我们`CityCollectionViewCell`类型，我们填充它与给定的项。

<a name="Responding-to-User-Events" />

## <a name="responding-to-user-events"></a>响应用户事件

因为我们希望用户能够从我们的集合中选择项目，我们需要提供一个集合视图委托来处理这种交互。 而且，我们需要提供方法让我们知道用户的哪些项的调用视图具有处于选中状态。

<a name="The-App-Delegate" />

### <a name="the-app-delegate"></a>应用程序委托

我们需要一种方法与之间建立关联的当前选定的项集合视图返回到调用的视图。 我们将在使用自定义属性我们`AppDelegate`。 编辑`AppDelegate.cs`文件，并添加以下代码：

```csharp
public CityInfo SelectedCity { get; set;} = new CityInfo("City02.jpg", "Turning Circle", true);
```

此定义的属性和设置将最初显示默认市/县。 更高版本，我们将使用此属性，以显示用户的选择，并允许选择要更改。

<a name="The-Collection-View-Delegate" />

### <a name="the-collection-view-delegate"></a>集合视图委托

接下来，添加一个新`CityViewDelegate`类到项目，并使其看起来如下所示：


```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;

namespace tvCollection
{
    public class CityViewDelegate : UICollectionViewDelegateFlowLayout
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public CityViewDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
        {
            return new CGSize (361, 256);
        }

        public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
        {
            if (indexPath == null) {
                return false;
            } else {
                var controller = collectionView as CityCollectionView;
                return controller.Source.Cities[indexPath.Row].CanSelect;
            }
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            var controller = collectionView as CityCollectionView;
            App.SelectedCity = controller.Source.Cities [indexPath.Row];

            // Close Collection
            controller.ParentController.DismissViewController(true,null);
        }
        #endregion
    }
}
```

让我们进一步看一下此类。 首先，我们继承`UICollectionViewDelegateFlowLayout`。 我们从此类继承的原因并不`UICollectionViewDelegate`是，我们将使用内置`UICollectionViewFlowLayout`以显示我们项和而不是自定义布局类型。

接下来，我们返回使用此代码的各个项的大小：

```csharp
public override CGSize GetSizeForItem (UICollectionView collectionView, UICollectionViewLayout layout, NSIndexPath indexPath)
{
    return new CGSize (361, 256);
}
```

然后，我们决定是否给定的单元格可以获得焦点，使用以下代码： 

```csharp
public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
{
    if (indexPath == null) {
        return false;
    } else {
        var controller = collectionView as CityCollectionView;
        return controller.Source.Cities[indexPath.Row].CanSelect;
    }
}
```

查看是否有特定的备份数据，我们检查其`CanSelect`标志设置为`true`并将该值返回。 有关导航和焦点的详细信息，请参阅我们[使用导航和焦点](~/ios/tvos/app-fundamentals/navigation-focus.md)并[Siri 远程和蓝牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文档。

最后，我们响应用户选择某一项使用以下代码：

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    var controller = collectionView as CityCollectionView;
    App.SelectedCity = controller.Source.Cities [indexPath.Row];

    // Close Collection
    controller.ParentController.DismissViewController(true,null);
}
```

在此我们设置`SelectedCity`属性的我们`AppDelegate`，选定的用户，我们关闭集合视图控制器，返回到调用我们的视图的项。 我们尚未定义`ParentController`尚未我们集合视图的属性，我们要做的下一步。

<a name="Configuring-the-Collection-View" />

## <a name="configuring-the-collection-view"></a>配置集合视图

现在，我们需要编辑集合视图，并将我们的数据源和委托分配。 编辑`CityCollectionView.cs`文件 （为我们自动从创建自己的情节提要），并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionView : UICollectionView
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Computed Properties
        public CityViewDatasource Source {
            get { return DataSource as CityViewDatasource;}
        }

        public CityCollectionViewController ParentController { get; set;}
        #endregion

        #region Constructors
        public CityCollectionView (IntPtr handle) : base (handle)
        {
            // Initialize
            RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
            DataSource = new CityViewDatasource (this);
            Delegate = new CityViewDelegate ();
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections ()
        {
            return 1;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
            if (previousItem != null) {
                Animate (0.2, () => {
                    previousItem.CityTitle.Alpha = 0.0f;
                });
            }

            var nextItem = context.NextFocusedView as CityCollectionViewCell;
            if (nextItem != null) {
                Animate (0.2, () => {
                    nextItem.CityTitle.Alpha = 1.0f;
                });
            }
        }
        #endregion
    }
}
```

首先，我们提供了快捷方式来访问我们`AppDelegate`: 

```csharp
public static AppDelegate App {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
```

接下来，我们向该集合视图的数据源和属性来访问集合视图控制器 （由我们上面的委托，当用户进行选择时关闭集合） 提供一种快捷方式：

```csharp
public CityViewDatasource Source {
    get { return DataSource as CityViewDatasource;}
}

public CityCollectionViewController ParentController { get; set;}
```

然后，我们使用下面的代码来初始化集合视图并将我们的单元格类、 数据源和委托分配：

```csharp
public CityCollectionView (IntPtr handle) : base (handle)
{
    // Initialize
    RegisterClassForCell (typeof(CityCollectionViewCell), CityViewDatasource.CardCellId);
    DataSource = new CityViewDatasource (this);
    Delegate = new CityViewDelegate ();
}
```

最后，我们想要仅会显示用户已将其突出显示的图像下方的标题 （焦点）。 我们使用下面的代码执行的操作：

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as CityCollectionViewCell;
    if (previousItem != null) {
        Animate (0.2, () => {
            previousItem.CityTitle.Alpha = 0.0f;
        });
    }

    var nextItem = context.NextFocusedView as CityCollectionViewCell;
    if (nextItem != null) {
        Animate (0.2, () => {
            nextItem.CityTitle.Alpha = 1.0f;
        });
    }
}
```

我们设置的失去焦点为零 (0) 的上一项 transparence、 下一项 transparence 获得焦点设置到 100%。 这些转换也获取经过动画处理。


## <a name="configuring-the-collection-view-controller"></a>配置集合视图控制器

现在，我们需要做我们集合视图上的最终配置和允许设置属性，我们定义，因此用户进行选择之后，可以关闭集合视图控制器。

编辑`CityCollectionViewController.cs`（自动创建的从自己的情节提要） 的文件，并使其如下所示：

```csharp
// This file has been autogenerated from a class added in the UI designer.

using System;

using Foundation;
using UIKit;

namespace tvCollection
{
    public partial class CityCollectionViewController : UICollectionViewController
    {
        #region Computed Properties
        public CityCollectionView Collection {
            get { return CollectionView as CityCollectionView; }
        }
        #endregion

        #region Constructors
        public CityCollectionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Save link to controller
            Collection.ParentController = this;
        }
        #endregion
    }
}

```

## <a name="putting-it-all-together"></a>配合使用 

现在，我们有所有将组合在一起的部分来填充和控制集合视图，我们需要对我们的主视图，以便将所有内容组合在一起进行最终编辑。

编辑`ViewController.cs`（自动创建的从自己的情节提要） 的文件，并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;
using tvCollection;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Application Access
        public static AppDelegate App {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Update image with the currently selected one
            CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
            BackgroundView.Image = CityView.Image;
            CityTitle.Text = App.SelectedCity.Title;
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

下面的代码最初显示从所选的项`SelectedCity`属性的`AppDelegate`和重新显示该用户已从集合视图中进行选择时：

```csharp
public override void ViewWillAppear (bool animated)
{
    base.ViewWillAppear (animated);

    // Update image with the currently selected one
    CityView.Image = UIImage.FromFile(App.SelectedCity.ImageFilename);
    BackgroundView.Image = CityView.Image;
    CityTitle.Text = App.SelectedCity.Title;
}
```

<a name="Testing-the-app" />

## <a name="testing-the-app"></a>测试应用程序

一切就绪后，如果你生成并运行应用程序中，主视图会显示为默认城市：

[![](collection-views-images/run01.png "主屏幕")](collection-views-images/run01.png#lightbox)

如果用户单击**选择一个视图**按钮，集合视图将显示：

[![](collection-views-images/run02.png "集合视图")](collection-views-images/run02.png#lightbox)

具有任何城市及其`CanSelect`属性设置为`false`将显示变暗，用户将不能将焦点设置到它。 当用户将突出显示某个项 (使其处于焦点) 显示标题和他们可以使用 3D 视差效果到微妙的地方倾斜图像。

当用户单击选择映像时，集合视图已关闭，并且使用新映像重新显示主视图：

[![](collection-views-images/run03.png "新的主屏幕上的映像")](collection-views-images/run03.png#lightbox)

<a name="Creating-Custom-Layout-and-Reordering-Items" />

## <a name="creating-custom-layout-and-reordering-items"></a>创建自定义布局和重新排序项

使用集合视图的主要功能之一是能够创建自定义布局。 TvOS 继承自 iOS，因为用于创建自定义布局的过程是相同的。 请参阅我们[集合视图简介](~/ios/user-interface/controls/uicollectionview.md)文档了解详细信息。

最近添加到集合视图适用于 iOS 9 是能够轻松地允许对集合中的项重新排序。 同样，由于 tvOS 9 是 iOS 9 的子集，这是这些相同的方式。 请参阅我们[集合视图更改](~/ios/user-interface/controls/uicollectionview.md)文档的更多详细信息。


<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和在 Xamarin.tvOS 应用内使用集合视图。 首先，它讨论的所有元素组成集合视图。 接下来，它介绍了如何设计和实现使用情节提要的一个集合视图。 最后，是创建自定义布局和重新排序项提供信息的链接。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)

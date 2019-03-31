---
title: 使用 tvOS 在 Xamarin 中的表视图
description: 本文介绍如何设计和使用在 Xamarin.tvOS 应用内的表视图和表视图控制器。
ms.prod: xamarin
ms.assetid: D8F80FA9-6400-4DB7-AFC9-A28A54AD04E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3e7fc3d627b5d7a1dc73caa395a9181efb0b5f08
ms.sourcegitcommit: 946ce514fd6575aa6b93ff24181e02a60b24b106
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58677997"
---
# <a name="working-with-tvos-table-views-in-xamarin"></a>使用 tvOS 在 Xamarin 中的表视图

_本文介绍如何设计和使用在 Xamarin.tvOS 应用内的表视图和表视图控制器。_

TvOS，在表视图，显示为滚动可以根据需要组织到组或节的行的单个列。 当您需要向用户，以明文形式，若要了解的方式有效地显示大量数据时，应使用表视图。

表视图通常显示中的一侧[拆分视图](~/ios/tvos/user-interface/split-views.md)作为导航中的另一侧显示的选定项的详细信息：

[![](table-views-images/intro01.png "示例表视图")](table-views-images/intro01.png#lightbox)

<a name="About-Table-Views" />

## <a name="about-table-views"></a>有关表视图

一个`UITableView`显示为可以根据需要组织到组或节的信息的分层列表的单个列的可滚动的行： 

[![](table-views-images/table01.png "选定的项")](table-views-images/table01.png#lightbox)

Apple 已使用表的以下建议：

- **要注意的宽度**-尝试表宽度中取得适当平衡。 如果太宽表，它可能很难从远处扫描，可能需要从可用的内容区域。 如果表太窄，它可能会导致信息被截断或换行，再次将非常困难，用户可以从在同一房间中读取。
- **显示快速表内容**-有关的数据的大型列表，延迟加载内容并开始显示信息就立即向用户显示表。 如果表花多长时间加载，用户可能会丢失您的应用或认为它已锁定了感兴趣。
- **通知用户的长内容负载**-如果长表加载时间是不可避免的存在[进度栏或活动指示器](~/ios/tvos/user-interface/progress-indicators.md)，以便其了解应用程序没有锁住了。

<a name="Table-Cell-Types" />

## <a name="table-view-cell-types"></a>表视图单元格类型

一个`UITableViewCell`用于表示单个表视图中的数据行。 Apple 已定义的多个默认表单元格类型：

- **默认**-此类型提供一个选项图像左对齐标题在右侧的单元格的左侧。 
- **副标题**-此类型提供的第一行和较小的左对齐标题左对齐副标题下一行。
- **值 1** -此类型会具有更轻便彩色的右边对齐副标题左对齐标题显示在同一行。
- **值 2** -此类型显示在同一行上的一个右对齐标题，其更轻便彩色的左对齐副标题。

所有默认表视图单元格类型还支持图形元素，如泄露指示器或复选标记。 

此外，你可以定义**自定义**表视图单元格类型，并且存在_原型单元格_，您可以创建界面设计器中或通过代码。

Apple 已使用表视图单元格的以下建议：

- **避免文本剪切**-保留单独的行的短文本，使它们最终不会被截断。 用户可以在同一房间从分析难以被截断的词或短语。
- **已设定焦点的行状态，请考虑**-因为行变得更大，使用舍入的详细信息具有焦点的边角，你需要测试所有状态中的单元格的外观。 图像或文本可能会变得剪辑，或在已设定焦点状态中查找不正确。
- **可编辑的表尽可能少地使用**-移动或删除表行是花费更多时间在 tvOS 上 ios。 需要仔细确定，是否此功能将添加或使你的 tvOS 应用变得混乱。
- **创建自定义单元格类型的相应**-虽然内置的表视图单元格类型非常适合用于很多情况下，请考虑创建自定义单元格类型的非标准的信息来提供更好地控制和更好地呈现到的信息用户。

<a name="Working-With-Table-Views" />

## <a name="working-with-table-views"></a>使用表视图

使用 Xamarin.tvOS 应用中的表视图的最简单方法是创建和修改在界面设计器中的显示。

若要开始，请执行以下操作：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
1. 在 Visual Studio for Mac 中，启动一个新的 tvOS 应用程序项目并选择**tvOS** > **应用** > **单一视图应用**单击**下一步**按钮： 

    [![](table-views-images/table02.png "选择单一视图应用")](table-views-images/table02.png#lightbox)
1. 输入**名称**的应用程序并单击**下一步**: 

    [![](table-views-images/table03.png "输入应用程序的名称")](table-views-images/table03.png#lightbox)
1. 可以调整**项目名称**并**解决方案名称**或接受默认值，然后单击**创建**按钮以创建新的解决方案： 

    [![](table-views-images/table04.png "项目名称和解决方案名称")](table-views-images/table04.png#lightbox)
1. 在中**Solution Pad**，双击`Main.storyboard`文件以在 iOS 设计器中打开它： 

    [![](table-views-images/table05.png "Main.storyboard 文件")](table-views-images/table05.png#lightbox)
1. 选择并删除**默认视图控制器**: 

    [![](table-views-images/table06.png "选择并删除默认视图控制器")](table-views-images/table06.png#lightbox)
1. 选择**拆分视图控制器**从**工具箱**并将其拖到设计图面上。
1. 默认情况下，您将获得[拆分视图](~/ios/tvos/user-interface/split-views.md)与**导航视图控制器**和一个**表格视图控制器**中的左侧和**视图控制器**中的右侧。 这是 tvOS 中的表视图的 Apple 的建议的用法： 

    [![](table-views-images/table08.png "添加拆分视图")](table-views-images/table08.png#lightbox)
1. 将需要选择表视图的每个部分并将其分配一个自定义**类名**中**小组件**选项卡**属性资源管理器**，以便可以访问它后面C#代码。 例如，**表格视图控制器**: 

    [![](table-views-images/table09.png "分配的类名称")](table-views-images/table09.png#lightbox)
1. 确保创建的自定义类**表格视图控制器**，则**表视图**和任何**原型的单元格**。 Visual Studio for Mac 将添加到项目树的自定义类，因为它们创建： 

    [![](table-views-images/table10.png "在项目树中的自定义类")](table-views-images/table10.png#lightbox)
1. 接下来，在设计图面中选择表视图，并根据需要调整它的属性。 例如数目**原型的单元格**并**样式**（纯或分组）： 

    [![](table-views-images/table11.png "小组件选项卡")](table-views-images/table11.png#lightbox)
1. 每个**原型单元格**，选择它并分配唯一**标识符**中**小组件**选项卡**属性资源管理器**。 此步骤是_非常重要_因为您稍后将需要此标识符时您填充表。 例如`AttrCell`: 

    [![](table-views-images/table12.png "小组件选项卡")](table-views-images/table12.png#lightbox)
1. 您还可以选择提供作为一个单元格[默认表视图单元格类型](#table-view-cell-types)通过**样式**下拉列表中或将其设置为**自定义**，并使用设计图面上向布局单元格从其他 UI 小组件中通过拖动**工具箱**: 

    [![](table-views-images/table13.png "单元格的布局")](table-views-images/table13.png#lightbox)
1. 分配唯一**名称**中的所有 UI 元素中的单元格原型设计**小组件**选项卡**属性资源管理器**以便您可以访问它们更高版本在C#代码： 

    [![](table-views-images/table14.png "指定一个名称")](table-views-images/table14.png#lightbox)
1. 所有表视图中的原型单元格重复上述步骤。
1. 接下来，将自定义类分配到 UI 设计、 布局的详细信息视图和分配唯一的 rest**名称**于每个 UI 元素在详细信息视图，以便你可以访问它们在C#以及。 例如： 

    [![](table-views-images/table15.png "UI 布局")](table-views-images/table15.png#lightbox)
1. 将所做的更改保存到情节提要。
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. 在 Visual Studio 中，启动一个新的 tvOS 应用程序项目并选择**tvOS** > **单一视图应用**并输入您的应用程序的名称。 单击**好**按钮以创建新的解决方案： 

    [![](table-views-images/table02-vs.png "选择单一视图应用")](table-views-images/table02-vs.png#lightbox)
1. 在中**解决方案资源管理器**，双击`Main.storyboard`文件以在 iOS 设计器中打开它： 

    [![](table-views-images/table05-vs.png "Main.storyboard 文件")](table-views-images/table05-vs.png#lightbox)
1. 选择并删除**默认视图控制器**: 

    [![](table-views-images/table06-vs.png "选择并删除默认视图控制器")](table-views-images/table06-vs.png#lightbox)
1. 选择**拆分视图控制器**从**工具箱**并将其拖到设计图面上： 

    [![](table-views-images/table07-vs.png "拆分视图控制器")](table-views-images/table07-vs.png#lightbox)
1. 默认情况下，您将获得[拆分视图](~/ios/tvos/user-interface/split-views.md)与**导航视图控制器**和一个**表格视图控制器**中的左侧和**视图控制器**中的右侧。 这是 tvOS 中的表视图的 Apple 的建议的用法： 

    [![](table-views-images/table08-vs.png "布局 UI")](table-views-images/table08-vs.png#lightbox)
1. 将需要选择表视图的每个部分并将其分配一个自定义**类名**中**小组件**选项卡**属性资源管理器**，以便可以访问它后面C#代码。 例如，**表格视图控制器**: 

    [![](table-views-images/table09-vs.png "小组件选项卡")](table-views-images/table09-vs.png#lightbox)
1. 确保创建的自定义类**表格视图控制器**，则**表视图**和任何**原型的单元格**。 Visual Studio for Mac 将添加到项目树的自定义类，因为它们创建： 

    [![](table-views-images/table10-vs.png "在项目树中的自定义类")](table-views-images/table10-vs.png#lightbox)
1. 接下来，在设计图面中选择表视图，并根据需要调整它的属性。 例如数目**原型的单元格**并**样式**（纯或分组）： 

    [![](table-views-images/table11-vs.png "小组件选项卡")](table-views-images/table11-vs.png#lightbox)
1. 每个**原型单元格**，选择它并分配唯一**标识符**中**小组件**选项卡**属性资源管理器**。 此步骤是_非常重要_因为您稍后将需要此标识符时您填充表。 例如`AttrCell`: 

    [![](table-views-images/table12-vs.png "分配一个标识符")](table-views-images/table12-vs.png#lightbox)
1. 您还可以选择提供作为一个单元格[默认表视图单元格类型](#table-view-cell-types)通过**样式**下拉列表中或将其设置为**自定义**，并使用设计图面上向布局单元格从其他 UI 小组件中通过拖动**工具箱**: 

    [![](table-views-images/table13-vs.png "样式下拉列表")](table-views-images/table13-vs.png#lightbox)
1. 分配唯一**名称**中的所有 UI 元素中的单元格原型设计**小组件**选项卡**属性资源管理器**以便您可以访问它们更高版本在C#代码： 

    [![](table-views-images/table14-vs.png "小组件选项卡")](table-views-images/table14-vs.png#lightbox)
1. 所有表视图中的原型单元格重复上述步骤。
1. 接下来，将自定义类分配到 UI 设计、 布局的详细信息视图和分配唯一的 rest**名称**于每个 UI 元素在详细信息视图，以便你可以访问它们在C#以及。 例如： 

    [![](table-views-images/table15.png "UI 布局")](table-views-images/table15.png#lightbox)
1. 将所做的更改保存到情节提要。
    
-----

<a name="Designing-a-Data-Model" />

## <a name="designing-a-data-model"></a>设计数据模型

若要使用的表视图将变得更容易显示的信息以及要简化呈现的详细信息 （如用户选择或突出显示表视图中的行），创建自定义类或类，以充当有关的信息的数据模型提供.

包含一系列一个旅行预订应用程序为例**城市**，每个包含的唯一列表**忍耐**，用户可以选择。 用户将能够将标记作为引力*最喜欢*，选择要获取*方向*到吸引程度和*预订航班*为给定的城市。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

若要创建的数据模型**引力**，右键单击项目名称中**Solution Pad** ，然后选择**添加** > **新文件...**.输入`AttractionInformation`有关**名称**然后单击**新建**按钮： 

[![](table-views-images/data01.png "为名称输入 AttractionInformation")](table-views-images/data01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

若要创建的数据模型**引力**，右键单击项目名称中**解决方案资源管理器**，然后选择**添加** > **新项...**.选择**类**并输入`AttractionInformation`有关**名称**然后单击**添加**按钮： 

[![](table-views-images/data01-vs.png "选择类，并输入名称的 AttractionInformation")](table-views-images/data01-vs.png#lightbox)

-----

编辑`AttractionInformation.cs`文件，并使其看起来如下所示：

```csharp
using System;
using Foundation;

namespace tvTable
{
    public class AttractionInformation : NSObject
    {
        #region Computed Properties
        public CityInformation City { get; set;}
        public string Name { get; set;}
        public string Description { get; set;}
        public string ImageName { get; set;}
        public bool IsFavorite { get; set;}
        public bool AddDirections { get; set;}
        #endregion

        #region Constructors
        public AttractionInformation (string name, string description, string imageName)
        {
            // Initialize
            this.Name = name;
            this.Description = description;
            this.ImageName = imageName;
        }
        #endregion
    }
}
```

此类提供用于存储有关信息的属性给定**引力**。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

接下来，右键单击项目名称在**Solution Pad**试，然后选择**添加** > **新文件...**.输入`CityInformation`有关**名称**然后单击**新建**按钮： 

[![](table-views-images/data02.png "为名称输入 CityInformation")](table-views-images/data02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

接下来，右键单击项目名称在**解决方案资源管理器**试，然后选择**添加** > **新项...**.输入`CityInformation`有关**名称**然后单击**添加**按钮： 

[![](table-views-images/data02-vs.png "为名称输入 CityInformation")](table-views-images/data02-vs.png#lightbox)

-----

编辑`CityInformation.cs`文件，并使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using Foundation;

namespace tvTable
{
    public class CityInformation : NSObject
    {
        #region Computed Properties
        public string Name { get; set; }
        public List<AttractionInformation> Attractions { get; set;}
        public bool FlightBooked { get; set;}
        #endregion

        #region Constructors
        public CityInformation (string name)
        {
            // Initialize
            this.Name = name;
            this.Attractions = new List<AttractionInformation> ();
        }
        #endregion

        #region Public Methods
        public void AddAttraction (AttractionInformation attraction)
        {
            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }

        public void AddAttraction (string name, string description, string imageName)
        {
            // Create attraction
            var attraction = new AttractionInformation (name, description, imageName);

            // Mark as belonging to this city
            attraction.City = this;

            // Add to collection
            Attractions.Add (attraction);
        }
        #endregion
    }
}
```

此类可包含有关目标的所有信息**城市**，一系列**忍耐**该城市的并提供了两个帮助器方法 (`AddAttraction`) 以使其更轻松地添加到忍耐城市。

<a name="The-Table-Data-Source" />

## <a name="the-table-view-data-source"></a>表视图的数据源

每个表视图需要的数据源 (`UITableViewDataSource`) 提供的数据的表并生成必要的行所需的表视图。

对于上述示例中，右键单击项目名称在**解决方案资源管理器**，选择**添加** > **新文件...** ，并调用它`AttractionTableDatasource`然后单击**新建**按钮以创建。 接下来，编辑`AttractionTableDatasource.cs`文件，并使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDatasource : UITableViewDataSource
    {
        #region Constants
        const string CellID = "AttrCell";
        #endregion

        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        public List<CityInformation> Cities { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDatasource (AttractionTableViewController controller)
        {
            // Initialize
            this.Controller = controller;
            this.Cities = new List<CityInformation> ();
            PopulateCities ();
        }
        #endregion

        #region Public Methods
        public void PopulateCities ()
        {
            // Clear existing
            Cities.Clear ();

            // Define cities and attractions
            var Paris = new CityInformation ("Paris");
            Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
            Paris.AddAttraction ("Musée du Louvre", "is one of the world's largest museums and a historic monument in Paris, France.", "Louvre");
            Paris.AddAttraction ("Moulin Rouge", "French for 'Red Mill', is a cabaret in Paris, France.", "MoulinRouge");
            Paris.AddAttraction ("La Seine", "Is a 777-kilometre long river and an important commercial waterway within the Paris Basin.", "RiverSeine");
            Cities.Add (Paris);

            var SanFran = new CityInformation ("San Francisco");
            SanFran.AddAttraction ("Alcatraz Island", "Is located in the San Francisco Bay, 1.25 miles (2.01 km) offshore from San Francisco.", "Alcatraz");
            SanFran.AddAttraction ("Golden Gate Bridge", "Is a suspension bridge spanning the Golden Gate strait between San Francisco Bay and the Pacific Ocean", "GoldenGateBridge");
            SanFran.AddAttraction ("San Francisco", "Is the cultural, commercial, and financial center of Northern California.", "SanFrancisco");
            SanFran.AddAttraction ("Telegraph Hill", "Is primarily a residential area, much quieter than adjoining North Beach.", "TelegraphHill");
            Cities.Add (SanFran);

            var Houston = new CityInformation ("Houston");
            Houston.AddAttraction ("City Hall", "It was constructed in 1938-1939, and is located in Downtown Houston.", "CityHall");
            Houston.AddAttraction ("Houston", "Is the most populous city in Texas and the fourth most populous city in the US.", "Houston");
            Houston.AddAttraction ("Texas Longhorn", "Is a breed of cattle known for its characteristic horns, which can extend to over 6 ft.", "LonghornCattle");
            Houston.AddAttraction ("Saturn V Rocket", "was an American human-rated expendable rocket used by NASA between 1966 and 1973.", "Rocket");
            Cities.Add (Houston);
        }
        #endregion

        #region Override Methods
        public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Get cell
            var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

            // Populate cell
            cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

            // Return new cell
            return cell;
        }

        public override nint NumberOfSections (UITableView tableView)
        {
            // Return number of cities
            return Cities.Count;
        }

        public override nint RowsInSection (UITableView tableView, nint section)
        {
            // Return the number of attractions in the given city
            return Cities [(int)section].Attractions.Count;
        }

        public override string TitleForHeader (UITableView tableView, nint section)
        {
            // Get the name of the current city
            return Cities [(int)section].Name;
        }
        #endregion
    }
}
```

让我们看看类的几个部分中详细信息。

首先，我们已定义了一个常量，用于保存原型单元格 （这是上述接口设计器中分配的同一标识符） 的唯一标识符、 快捷方式重新添加到表格视图控制器，为我们的数据创建的存储：

```csharp
const string CellID = "AttrCell";
public AttractionTableViewController Controller { get; set;}
public List<CityInformation> Cities { get; set;}
```

接下来，我们保存表格视图控制器，然后生成并填充我们 （使用上面定义的数据模型） 的数据源时创建的类：

```csharp
public AttractionTableDatasource (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
    this.Cities = new List<CityInformation> ();
    PopulateCities ();
}
```

作为示例，`PopulateCities`方法只需创建数据模型对象在内存中但无法从真正的应用程序中的数据库或 web 服务轻松地被读取这些：

```csharp
public void PopulateCities ()
{
    // Clear existing
    Cities.Clear ();

    // Define cities and attractions
    var Paris = new CityInformation ("Paris");
    Paris.AddAttraction ("Eiffel Tower", "Is a wrought iron lattice tower on the Champ de Mars in Paris, France.", "EiffelTower");
    ...
}
```

`NumberOfSections`方法返回表中的节数：

```csharp
public override nint NumberOfSections (UITableView tableView)
{
    // Return number of cities
    return Cities.Count;
}
```

有关**普通**设置样式表视图，始终返回 1。

`RowsInSection`方法在当前节中返回的行数：

```csharp
public override nint RowsInSection (UITableView tableView, nint section)
{
    // Return the number of attractions in the given city
    return Cities [(int)section].Attractions.Count;
}
```

同样，对于**普通**表视图，在数据源中返回的项总数。

`TitleForHeader`方法返回给定标题部分：

```csharp
public override string TitleForHeader (UITableView tableView, nint section)
{
    // Get the name of the current city
    return Cities [(int)section].Name;
}
```

有关**普通**表视图类型中，将标题留空 (`""`)。

最后，当请求表视图，创建并填充原型单元格使用`GetCell`方法： 

```csharp
public override UITableViewCell GetCell (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Get cell
    var cell = tableView.DequeueReusableCell (CellID) as AttractionTableCell;

    // Populate cell
    cell.Attraction = Cities [indexPath.Section].Attractions [indexPath.Row];

    // Return new cell
    return cell;
}
```

有关使用的详细信息`UITableViewDatasource`，请参阅 Apple [UITableViewDatasource](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDataSource_Protocol/index.html#//apple_ref/doc/uid/TP40006941)文档。

<a name="The-Table-View-Delegate" />

## <a name="the-table-view-delegate"></a>表视图委托

每个表视图需要委托 (`UITableViewDelegate`) 来响应用户交互或表上的其他系统事件。

对于上述示例中，右键单击项目名称在**解决方案资源管理器**，选择**添加** > **新文件...** ，并调用它`AttractionTableDelegate`然后单击**新建**按钮以创建。 接下来，编辑`AttractionTableDelegate.cs`文件，并使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using UIKit;

namespace tvTable
{
    public class AttractionTableDelegate : UITableViewDelegate
    {
        #region Computed Properties
        public AttractionTableViewController Controller { get; set;}
        #endregion

        #region Constructors
        public AttractionTableDelegate (AttractionTableViewController controller)
        {
            // Initializw
            this.Controller = controller;
        }
        #endregion

        #region Override Methods
        public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
            attraction.IsFavorite = (!attraction.IsFavorite);

            // Update UI
            Controller.TableView.ReloadData ();
        }

        public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
        {
            // Inform caller of highlight change
            RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
            return true;
        }
        #endregion

        #region Events
        public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
        public event AttractionHighlightedDelegate AttractionHighlighted;

        internal void RaiseAttractionHighlighted (AttractionInformation attraction)
        {
            // Inform caller
            if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
        }
        #endregion
    }
}
```

让我们看看此类的多个部分中的详细信息。

首先，我们创建表视图控制器的快捷方式时创建的类：

```csharp
public AttractionTableViewController Controller { get; set;}
...

public AttractionTableDelegate (AttractionTableViewController controller)
{
    // Initialize
    this.Controller = controller;
}
```

然后，在选定某一行时 （用户单击的 Apple 远程 Touch 面上） 我们想要将标记**引力**表示为收藏的选定行：

```csharp
public override void RowSelected (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    var attraction = Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row];
    attraction.IsFavorite = (!attraction.IsFavorite);

    // Update UI
    Controller.TableView.ReloadData ();
}
```

接下来，用户会突出显示某行 （通过给予它焦点使用 Apple 远程 Touch 面） 时我们想要显示的详细信息**引力**所对应的行详细信息部分中的拆分视图控制器：

```csharp
public override bool CanFocusRow (UITableView tableView, Foundation.NSIndexPath indexPath)
{
    // Inform caller of highlight change
    RaiseAttractionHighlighted (Controller.Datasource.Cities [indexPath.Section].Attractions [indexPath.Row]);
    return true;
}
...

public delegate void AttractionHighlightedDelegate (AttractionInformation attraction);
public event AttractionHighlightedDelegate AttractionHighlighted;

internal void RaiseAttractionHighlighted (AttractionInformation attraction)
{
    // Inform caller
    if (this.AttractionHighlighted != null) this.AttractionHighlighted (attraction);
}
```

`CanFocusRow`即将获得焦点的表视图中每个行调用方法。 返回`true`如果一行可获得焦点，否则返回`false`。 对于此示例中，我们已创建了一个自定义`AttractionHighlighted`收到焦点时引发的每一行的事件。

有关使用的详细信息`UITableViewDelegate`，请参阅 Apple [UITableViewDelegate](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006942)文档。

<a name="The-Table-View-Cell" />

## <a name="the-table-view-cell"></a>表视图单元格

对于每个原型单元格添加到界面设计器中的表视图，您还创建表视图单元格的自定义实例 (`UITableViewCell`) 以便可以按其创建填充新的单元格 （行）。

对于示例应用程序中，双击`AttractionTableCell.cs`文件打开以进行编辑，并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableCell : UITableViewCell
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public AttractionTableCell (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Trap all errors
            try {
                Title.Text = Attraction.Name;
                Favorite.Hidden = (!Attraction.IsFavorite);
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion
    }
}
```

此类提供了具有数据模型对象存储 (`AttractionInformation`如上文所定义) 显示给定行中：

```csharp
private AttractionInformation _attraction = null;
...

public AttractionInformation Attraction {
    get { return _attraction; }
    set {
        _attraction = value;
        UpdateUI ();
    }
}
```

`UpdateUI`方法填充 UI 小组件 （即已添加到界面设计器中的单元格的原型） 根据需要：

```csharp
private void UpdateUI ()
{
    // Trap all errors
    try {
        Title.Text = Attraction.Name;
        Favorite.Hidden = (!Attraction.IsFavorite);
    } catch {
        // Since the UI might not be fully loaded, ignore
        // all errors at this point
    }
}
```

有关使用的详细信息`UITableViewCell`，请参阅 Apple [UITableViewCell](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewCell_Class/index.html#//apple_ref/doc/uid/TP40006938)文档。

<a name="The-Table-View-Controller" />

## <a name="the-table-view-controller"></a>表格视图控制器

表格视图控制器 (`UITableViewController`) 管理已添加到情节提要界面设计器通过表视图。

对于示例应用程序中，双击`AttractionTableViewController.cs`文件打开以进行编辑，并使其如下所示：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionTableViewController : UITableViewController
    {
        #region Computed Properties
        public AttractionTableDatasource Datasource {
            get { return TableView.DataSource as AttractionTableDatasource; }
        }

        public AttractionTableDelegate TableDelegate {
            get { return TableView.Delegate as AttractionTableDelegate; }
        }
        #endregion

        #region Constructors
        public AttractionTableViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Setup table
            TableView.DataSource = new AttractionTableDatasource (this);
            TableView.Delegate = new AttractionTableDelegate (this);
            TableView.ReloadData ();
        }
        #endregion
    }
}
```

让我们进一步看一下此类。 首先，我们已创建了快捷方式，以使其更易于访问表视图`DataSource`和`TableDelegate`。 我们将使用这些更高版本中左侧和右侧的拆分视图的表视图和右中的详细信息视图之间进行通信。

最后，当表视图加载到内存中，我们创建的实例`AttractionTableDatasource`和`AttractionTableDelegate`（无论是上述步骤中创建） 并将它们附加到表视图。

有关使用的详细信息`UITableViewController`，请参阅 Apple [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)文档。

<a name="Pulling-it-All-Together" />

## <a name="pulling-it-all-together"></a>将所有放在一起

如本文档开头所述，表视图通常显示中的一侧[拆分视图](~/ios/tvos/user-interface/split-views.md)作为导航中的另一侧显示的选定项的详细信息。 例如： 

[![](table-views-images/intro01.png "运行示例应用")](table-views-images/intro01.png#lightbox)

由于这是 tvOS 中的标准模式，让我们看一下将所有内容组合在一起的最后一个步骤并彼此交互的拆分视图左侧和右侧边。

<a name="The-Detail-View" />

### <a name="the-detail-view"></a>详细信息视图

旅游应用的示例介绍上面的自定义类 (`AttractionViewController`) 定义标准中作为详细信息视图拆分视图的右侧显示的视图控制器：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class AttractionViewController : UIViewController
    {
        #region Private Variables
        private AttractionInformation _attraction = null;
        #endregion

        #region Computed Properties
        public AttractionInformation Attraction {
            get { return _attraction; }
            set {
                _attraction = value;
                UpdateUI ();
            }
        }

        public MasterSplitView SplitView { get; set;}
        #endregion

        #region Constructors
        public AttractionViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Public Methods
        public void UpdateUI ()
        {
            // Trap all errors
            try {
                City.Text = Attraction.City.Name;
                Title.Text = Attraction.Name;
                SubTitle.Text = Attraction.Description;

                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
                IsFavorite.Hidden = (!Attraction.IsFavorite);
                IsDirections.Hidden = (!Attraction.AddDirections);
                BackgroundImage.Image = UIImage.FromBundle (Attraction.ImageName);
                AttractionImage.Image = BackgroundImage.Image;
            } catch {
                // Since the UI might not be fully loaded, ignore
                // all errors at this point
            }
        }
        #endregion

        #region Override Methods
        public override void ViewWillAppear (bool animated)
        {
            base.ViewWillAppear (animated);

            // Ensure the UI Updates
            UpdateUI ();
        }
        #endregion

        #region Actions
        partial void BookFlight (NSObject sender)
        {
            // Ask user to book flight
            AlertViewController.PresentOKCancelAlert ("Book Flight",
                                                      string.Format ("Would you like to book a flight to {0}?", Attraction.City.Name),
                                                      this,
                                                      (ok) => {
                Attraction.City.FlightBooked = ok;
                IsFlighBooked.Hidden = (!Attraction.City.FlightBooked);
            });
        }

        partial void GetDirections (NSObject sender)
        {
            // Ask user to add directions
            AlertViewController.PresentOKCancelAlert ("Add Directions",
                                                     string.Format ("Would you like to add directions to {0} to you itinerary?", Attraction.Name),
                                                     this,
                                                     (ok) => {
                                                         Attraction.AddDirections = ok;
                                                         IsDirections.Hidden = (!Attraction.AddDirections);
                                                     });
        }

        partial void MarkFavorite (NSObject sender)
        {
            // Flip favorite state
            Attraction.IsFavorite = (!Attraction.IsFavorite);
            IsFavorite.Hidden = (!Attraction.IsFavorite);

            // Reload table
            SplitView.Master.TableController.TableView.ReloadData ();
        }
        #endregion
    }
}
```

在这里，我们提供了**引力**(`AttractionInformation`) 被作为属性显示并且创建`UpdateUI`填充 UI 小组件的方法添加到界面设计器中的视图。

我们还定义一种快捷方式返回到拆分视图控制器 (`SplitView`)，我们将使用通信将变回表视图 (`AcctractionTableView`)。

最后，自定义操作 （事件） 已添加到这三个`UIButton`接口设计器中创建的实例，允许用户若要将标记作为引力_收藏_，获取_方向_到吸引程度并且_预订航班_为给定的城市。

<a name="The-Navigation-View-Controller" />

### <a name="the-navigation-view-controller"></a>导航视图控制器

导航视图控制器左侧和右侧的拆分视图中的导航视图控制器在嵌套表格视图控制器，因为已分配的自定义类 (`MasterNavigationController`) 接口设计器中，定义如下：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterNavigationController : UINavigationController
    {
        #region Computed Properties
        public MasterSplitView SplitView { get; set;}
        public AttractionTableViewController TableController {
            get { return TopViewController as AttractionTableViewController; }
        }
        #endregion

        #region Constructors
        public MasterNavigationController (IntPtr handle) : base (handle)
        {
        }
        #endregion
    }
}
```

同样，此类只是定义一些快捷方式以便更轻松地跨拆分视图控制器的两个方面进行通信：

* `SplitView` -为拆分视图控制器的链接 (`MainSpiltViewController`) 属于导航视图控制器。
* `TableController` -获取表格视图控制器 (`AttractionTableViewController`) 以导航视图控制器中的顶层视图。

<a name="The-Split-View-Controller" />

### <a name="the-split-view-controller"></a>拆分视图控制器

由于拆分视图控制器是我们的应用程序的基础，因此我们创建的自定义类 (`MasterSplitViewController`) 为其接口设计器中，定义如下：

```csharp
using System;
using Foundation;
using UIKit;

namespace tvTable
{
    public partial class MasterSplitView : UISplitViewController
    {
        #region Computed Properties
        public AttractionViewController Details {
            get { return ViewControllers [1] as AttractionViewController; }
        }

        public MasterNavigationController Master {
            get { return ViewControllers [0] as MasterNavigationController; }
        }
        #endregion

        #region Constructors
        public MasterSplitView (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            Master.SplitView = this;
            Details.SplitView = this;

            // Wire-up events
            Master.TableController.TableDelegate.AttractionHighlighted += (attraction) => {
                // Display new attraction
                Details.Attraction = attraction;
            };
        }
        #endregion
    }
}
```

首先，我们创建的快捷方式**详细信息**侧的拆分视图 (`AttractionViewController`) 和**Master**端 (`MasterNavigationController`)。 同样，这使得它更轻松地实现更高版本的两个方面之间的通信。

接下来，拆分视图加载到内存中，则我们将拆分视图控制器连接到拆分视图的两面并响应突出显示的表视图中具有用户 (`AttractionHighlighted`) 通过显示在新引力**详细信息**端的拆分视图。

请参阅[tvTables](https://developer.xamarin.com/samples/monotouch/tvos/tvTable/)拆分视图内的表视图的完整的实现的示例应用程序。

## <a name="table-views-in-detail"></a>详细信息中的表视图

TvOS 基于 iOS，因为表视图和表视图控制器设计，以类似的方式的行为。 有关更多详细信息使用 Xamarin 应用程序中的表视图，请参阅我们的 iOS[使用表和单元格](~/ios/user-interface/controls/tables/index.md)文档。

<a name="Summary" />

## <a name="summary"></a>总结

本文只讨论了设计和使用在 Xamarin.tvOS 应用内的表视图。 并提供了使用表视图，在拆分视图，这是一个 tvOS 应用程序中的表视图的典型的用法的示例。



## <a name="related-links"></a>相关链接

- [tvOS 示例](https://developer.xamarin.com/samples/tvos/all/)
- [UITableViewController](https://developer.apple.com/library/prerelease/tvos/documentation/UIKit/Reference/UITableViewController_Class/index.html#//apple_ref/doc/uid/TP40007523)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人机接口指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [适用于 tvOS 应用编程指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)

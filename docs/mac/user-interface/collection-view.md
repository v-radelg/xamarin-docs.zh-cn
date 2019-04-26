---
title: 在 Xamarin.Mac 中的集合视图
description: 本文说明如何使用集合视图中的 Xamarin.Mac 应用的。 它介绍如何创建和维护集合视图在 Xcode 和 Interface Builder 中的和以编程方式使用它们。
ms.prod: xamarin
ms.assetid: 6EE32256-5948-4AE4-8133-6D0B3F4173E8
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 05/24/2017
ms.openlocfilehash: 82dd175222289676c3dacd80522c55e71ba12180
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61235935"
---
# <a name="collection-views-in-xamarinmac"></a>在 Xamarin.Mac 中的集合视图

_本文说明如何使用集合视图中的 Xamarin.Mac 应用的。它介绍如何创建和维护集合视图在 Xcode 和 Interface Builder 中的和以编程方式使用它们。_

当使用C#和.NET 中的 Xamarin.Mac 应用中，开发人员有权访问相同的 AppKit 集合视图控件的开发人员*Objective C*和*Xcode* does。 由于 Xamarin.Mac 与 Xcode 直接集成，开发人员可使用 Xcode 的_Interface Builder_来创建和维护集合视图。

一个`NSCollectionView`显示一个网格，组织使用的子视图的`NSCollectionViewLayout`。 在网格中每个子视图由`NSCollectionViewItem`用于管理从视图的内容加载`.xib`文件。

[![运行示例应用](collection-view-images/intro01.png)](collection-view-images/intro01.png#lightbox)

本文介绍如何在 Xamarin.Mac 应用中使用集合视图的基础知识。 强烈建议您明确[Hello，Mac](~/mac/get-started/hello-mac.md)文章第一次，具体而言[Xcode 和 Interface Builder 简介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)并[输出口和操作](~/mac/get-started/hello-mac.md#outlets-and-actions)部分中的，因为它涵盖了关键概念以及本文通篇使用的技术。

可能想要看一看[公开 C# 类 / 方法添加到 Objective C](~/mac/internals/how-it-works.md)一部分[Xamarin.Mac 内部机制](~/mac/internals/how-it-works.md)文档，它还说明了`Register`和`Export`命令用于布置于 C# 类对 OBJECTIVE-C 对象和 UI 元素。

<a name="About_Collection_Views"/>

## <a name="about-collection-views"></a>有关集合视图

集合视图的主要目标 (`NSCollectionView`) 是以可视方式排列的一组以组织方式使用集合视图布局的对象 (`NSCollectionViewLayout`)，其中每个单个对象 (`NSCollectionViewItem`) 更大的集合中获取其自己的视图。 集合视图通过数据绑定和键值编码技术工作，并在这种情况下，应阅读[数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)文档，然后再继续完成这篇文章。

集合视图都有没有标准的内置集合视图项 （类似大纲或表视图执行的操作），因此开发人员负责设计和实现_原型视图_图像字段如使用其他 AppKit 控件文本字段、 标签等。此原型视图将用于显示和使用集合视图由每个项，并且存储在`.xib`文件。

开发人员负责收集视图项的外观和感觉，因为集合视图具有突出显示在网格中选定的项不内置支持。 实现此功能将本文中所述。

<a name="Defining_your_Data_Model"/>

## <a name="defining-the-data-model"></a>定义数据模型

中的数据绑定集合视图 Interface Builder，键值对的编码 (KVC) 之前 / 键值观察 (KVO) 符合类必须定义在 Xamarin.Mac 应用程序充当_数据模型_绑定。 数据模型提供的所有数据，将显示在集合中接收到的数据的用户运行应用程序时在 UI 中进行任何修改。

应用来管理一组员工为例，可以使用以下类定义数据模型：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacDatabinding
{
    [Register("PersonModel")]
    public class PersonModel : NSObject
    {
        #region Private Variables
        private string _name = "";
        private string _occupation = "";
        private bool _isManager = false;
        private NSMutableArray _people = new NSMutableArray();
        #endregion

        #region Computed Properties
        [Export("Name")]
        public string Name {
            get { return _name; }
            set {
                WillChangeValue ("Name");
                _name = value;
                DidChangeValue ("Name");
            }
        }

        [Export("Occupation")]
        public string Occupation {
            get { return _occupation; }
            set {
                WillChangeValue ("Occupation");
                _occupation = value;
                DidChangeValue ("Occupation");
            }
        }

        [Export("isManager")]
        public bool isManager {
            get { return _isManager; }
            set {
                WillChangeValue ("isManager");
                WillChangeValue ("Icon");
                _isManager = value;
                DidChangeValue ("isManager");
                DidChangeValue ("Icon");
            }
        }

        [Export("isEmployee")]
        public bool isEmployee {
            get { return (NumberOfEmployees == 0); }
        }

        [Export("Icon")]
        public NSImage Icon
        {
            get
            {
                if (isManager)
                {
                    return NSImage.ImageNamed("IconGroup");
                }
                else
                {
                    return NSImage.ImageNamed("IconUser");
                }
            }
        }

        [Export("personModelArray")]
        public NSArray People {
            get { return _people; }
        }

        [Export("NumberOfEmployees")]
        public nint NumberOfEmployees {
            get { return (nint)_people.Count; }
        }
        #endregion

        #region Constructors
        public PersonModel ()
        {
        }

        public PersonModel (string name, string occupation)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
        }

        public PersonModel (string name, string occupation, bool manager)
        {
            // Initialize
            this.Name = name;
            this.Occupation = occupation;
            this.isManager = manager;
        }
        #endregion

        #region Array Controller Methods
        [Export("addObject:")]
        public void AddPerson(PersonModel person) {
            WillChangeValue ("personModelArray");
            isManager = true;
            _people.Add (person);
            DidChangeValue ("personModelArray");
        }

        [Export("insertObject:inPersonModelArrayAtIndex:")]
        public void InsertPerson(PersonModel person, nint index) {
            WillChangeValue ("personModelArray");
            _people.Insert (person, index);
            DidChangeValue ("personModelArray");
        }

        [Export("removeObjectFromPersonModelArrayAtIndex:")]
        public void RemovePerson(nint index) {
            WillChangeValue ("personModelArray");
            _people.RemoveObject (index);
            DidChangeValue ("personModelArray");
        }

        [Export("setPersonModelArray:")]
        public void SetPeople(NSMutableArray array) {
            WillChangeValue ("personModelArray");
            _people = array;
            DidChangeValue ("personModelArray");
        }
        #endregion
    }
}
```

`PersonModel`数据模型会在本文的其余部分。

<a name="Working_with_a_Collection_View"/>

## <a name="working-with-a-collection-view"></a>使用集合视图

使用集合视图的数据绑定是使用表视图中，非常类似 like 绑定作为`NSCollectionViewDataSource`用于找不到提供数据。 由于集合视图不包含预设的显示格式，则需要提供用户交互反馈并跟踪用户选择更多工作。

<a name="Creating-the-Cell-Prototype"/>

### <a name="creating-the-cell-prototype"></a>创建单元格原型

由于集合视图不包含默认单元格原型，开发人员将需要添加一个或多个`.xib`文件到 Xamarin.Mac 应用定义的布局和各个单元格的内容。

请执行以下操作：

1. 在中**解决方案资源管理器**，右键单击项目名称并选择**添加** > **新文件...**
2. 选择**Mac** > **视图控制器**，为其提供一个名称 (如`EmployeeItem`在此示例中) 并单击**新建**按钮以创建： 

    ![添加新的视图控制器](collection-view-images/proto01.png)

    这将添加`EmployeeItem.cs`，`EmployeeItemController.cs`和`EmployeeItemController.xib`到项目的解决方案文件。
3. 双击`EmployeeItemController.xib`文件以打开在 Xcode 的 Interface Builder 中进行编辑。
4. 添加`NSBox`，`NSImageView`并将两个`NSLabel`到视图的控件并对其进行安排，如下所示：

    ![设计的单元格原型的布局](collection-view-images/proto02.png)
5. 打开**助手编辑器**并创建**Outlet**为`NSBox`，以便它可以用于指示单元格的选择状态：

    ![公开在输出口 NSBox](collection-view-images/proto03.png)
6. 返回到**标准编辑器**选择的图像视图。
7. 在中**绑定检查器**，选择**绑定到** > **文件的所有者**，然后输入**模型项路径**的`self.Person.Icon`:

    ![绑定图标](collection-view-images/proto04.png)
8. 选择第一个标签并在**绑定检查器**，选择**绑定到** > **文件的所有者**，然后输入**模型项路径**的`self.Person.Name`:

    ![绑定名称](collection-view-images/proto05.png)
9. 选择第二个标签并在**绑定检查器**，选择**绑定到** > **文件的所有者**，然后输入**模型项路径**的`self.Person.Occupation`:

    ![绑定职业](collection-view-images/proto06.png)
10. 保存对更改`.xib`文件，并返回到 Visual Studio 进行同步所做的更改。

编辑`EmployeeItemController.cs`文件，并使其看起来如下所示：

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// The Employee item controller handles the display of the individual items that will
    /// be displayed in the collection view as defined in the associated .XIB file.
    /// </summary>
    public partial class EmployeeItemController : NSCollectionViewItem
    {
        #region Private Variables
        /// <summary>
        /// The person that will be displayed.
        /// </summary>
        private PersonModel _person;
        #endregion

        #region Computed Properties
        // strongly typed view accessor
        public new EmployeeItem View
        {
            get
            {
                return (EmployeeItem)base.View;
            }
        }

        /// <summary>
        /// Gets or sets the person.
        /// </summary>
        /// <value>The person that this item belongs to.</value>
        [Export("Person")]
        public PersonModel Person
        {
            get { return _person; }
            set
            {
                WillChangeValue("Person");
                _person = value;
                DidChangeValue("Person");
            }
        }

        /// <summary>
        /// Gets or sets the color of the background for the item.
        /// </summary>
        /// <value>The color of the background.</value>
        public NSColor BackgroundColor {
            get { return Background.FillColor; }
            set { Background.FillColor = value; }
        }

        /// <summary>
        /// Gets or sets a value indicating whether this <see cref="T:MacCollectionNew.EmployeeItemController"/> is selected.
        /// </summary>
        /// <value><c>true</c> if selected; otherwise, <c>false</c>.</value>
        /// <remarks>This also changes the background color based on the selected state
        /// of the item.</remarks>
        public override bool Selected
        {
            get
            {
                return base.Selected;
            }
            set
            {
                base.Selected = value;

                // Set background color based on the selection state
                if (value) {
                    BackgroundColor = NSColor.DarkGray;
                } else {
                    BackgroundColor = NSColor.LightGray;
                }
            }
        }
        #endregion

        #region Constructors
        // Called when created from unmanaged code
        public EmployeeItemController(IntPtr handle) : base(handle)
        {
            Initialize();
        }

        // Called when created directly from a XIB file
        [Export("initWithCoder:")]
        public EmployeeItemController(NSCoder coder) : base(coder)
        {
            Initialize();
        }

        // Call to load from the XIB/NIB file
        public EmployeeItemController() : base("EmployeeItem", NSBundle.MainBundle)
        {
            Initialize();
        }

        // Added to support loading from XIB/NIB
        public EmployeeItemController(string nibName, NSBundle nibBundle) : base(nibName, nibBundle) {

            Initialize();
        }

        // Shared initialization code
        void Initialize()
        {
        }
        #endregion
    }
}
```

查看详细信息中此代码，该类继承`NSCollectionViewItem`以便它可以用作集合视图单元格的原型。 `Person`属性公开了用于将数据绑定到的图像视图和标签在 Xcode 中的类。 这是实例`PersonModel`上述步骤中创建。

`BackgroundColor`属性是快捷方式`NSBox`控件的`FillColor`将用于显示单元格的选择状态。 通过重写`Selected`属性的`NSCollectionViewItem`，下面的代码设置或清除此选择状态：

```csharp
public override bool Selected
{
    get
    {
        return base.Selected;
    }
    set
    {
        base.Selected = value;

        // Set background color based on the selection state
        if (value) {
            BackgroundColor = NSColor.DarkGray;
        } else {
            BackgroundColor = NSColor.LightGray;
        }
    }
}
```

<a name="Creating-the-Collection-View-Data-Source"/>

### <a name="creating-the-collection-view-data-source"></a>创建集合视图的数据源

集合视图的数据源 (`NSCollectionViewDataSource`) 提供的所有数据集合视图以及创建并填充集合视图单元格 (使用`.xib`原型) 根据需要为每个项集合中。

添加一个新类项目中，调用它`CollectionViewDataSource`并使其如下所示：

```csharp
using System;
using System.Collections.Generic;
using AppKit;
using Foundation;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view data source provides the data for the collection view.
    /// </summary>
    public class CollectionViewDataSource : NSCollectionViewDataSource
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent collection view.
        /// </summary>
        /// <value>The parent collection view.</value>
        public NSCollectionView ParentCollectionView { get; set; }

        /// <summary>
        /// Gets or sets the data that will be displayed in the collection.
        /// </summary>
        /// <value>A collection of PersonModel objects.</value>
        public List<PersonModel> Data { get; set; } = new List<PersonModel>();
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDataSource"/> class.
        /// </summary>
        /// <param name="parent">The parent collection that this datasource will provide data for.</param>
        public CollectionViewDataSource(NSCollectionView parent)
        {
            // Initialize
            ParentCollectionView = parent;

            // Attach to collection view
            parent.DataSource = this;

        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Gets the number of sections.
        /// </summary>
        /// <returns>The number of sections.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        public override nint GetNumberOfSections(NSCollectionView collectionView)
        {
            // There is only one section in this view
            return 1;
        }

        /// <summary>
        /// Gets the number of items in the given section.
        /// </summary>
        /// <returns>The number of items.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="section">The Section number to count items for.</param>
        public override nint GetNumberofItems(NSCollectionView collectionView, nint section)
        {
            // Return the number of items
            return Data.Count;
        }

        /// <summary>
        /// Gets the item for the give section and item index.
        /// </summary>
        /// <returns>The item.</returns>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPath">Index path specifying the section and index.</param>
        public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
        {
            var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
            item.Person = Data[(int)indexPath.Item];

            return item;
        }
        #endregion
    }
}
```

查看详细信息中此代码，该类继承自`NSCollectionViewDataSource`，并公开一系列`PersonModel`实例通过其`Data`属性。

由于此集合仅具有一个部分，该代码重写`GetNumberOfSections`方法，始终返回`1`。 此外，`GetNumberofItems`方法被重写在它返回的项数在`Data`属性列表。

`GetItem`只要新的单元格是必需的并且看起来如下所示调用方法：

```csharp
public override NSCollectionViewItem GetItem(NSCollectionView collectionView, NSIndexPath indexPath)
{
    var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
    item.Person = Data[(int)indexPath.Item];

    return item;
}
```

`MakeItem`调用的集合视图的方法来创建或返回的可重用实例`EmployeeItemController`并将其`Person`属性设置为项请求单元中显示。 

`EmployeeItemController`必须注册到集合视图控制器事先会使用以下代码：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
``` 

**标识符**(`EmployeeCell`) 中使用`MakeItem`调用_必须_与已注册到集合视图的视图控制器的名称匹配。 将下面将详细介绍此步骤。

<a name="Handling-Item-Selection"/>

### <a name="handling-item-selection"></a>处理项选择

若要处理的选择和取消选择的项在集合中，`NSCollectionViewDelegate`都会要求提供。 由于此示例将为使用内置`NSCollectionViewFlowLayout`布局类型`NSCollectionViewDelegateFlowLayout`将需要特定版本的委托。

将新类添加到项目中，调用它`CollectionViewDelegate`并使其如下所示：

```csharp
using System;
using Foundation;
using AppKit;

namespace MacCollectionNew
{
    /// <summary>
    /// Collection view delegate handles user interaction with the elements of the 
    /// collection view for the Flow-Based layout type.
    /// </summary>
    public class CollectionViewDelegate : NSCollectionViewDelegateFlowLayout
    {
        #region Computed Properties
        /// <summary>
        /// Gets or sets the parent view controller.
        /// </summary>
        /// <value>The parent view controller.</value>
        public ViewController ParentViewController { get; set; }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.CollectionViewDelegate"/> class.
        /// </summary>
        /// <param name="parentViewController">Parent view controller.</param>
        public CollectionViewDelegate(ViewController parentViewController)
        {
            // Initialize
            ParentViewController = parentViewController;
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Handles one or more items being selected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being selected.</param>
        public override void ItemsSelected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = (int)paths[0].Item;

            // Save the selected item
            ParentViewController.PersonSelected = ParentViewController.Datasource.Data[index];

        }

        /// <summary>
        /// Handles one or more items being deselected.
        /// </summary>
        /// <param name="collectionView">The parent Collection view.</param>
        /// <param name="indexPaths">The Index paths of the items being deselected.</param>
        public override void ItemsDeselected(NSCollectionView collectionView, NSSet indexPaths)
        {
            // Dereference path
            var paths = indexPaths.ToArray<NSIndexPath>();
            var index = paths[0].Item;

            // Clear selection
            ParentViewController.PersonSelected = null;
        }
        #endregion
    }
}
``` 

`ItemsSelected`并`ItemsDeselected`方法的重写，使用设置或清除`PersonSelected`时用户选择或取消选择某个项处理集合视图的视图控制器的属性。 这将显示在下方提供详细信息。

<a name="Creating-the-Collection-View-in-Interface-Builder"/>

### <a name="creating-the-collection-view-in-interface-builder"></a>在接口生成器中创建集合视图

所有所需的支持部分都准备就绪，可以编辑主要情节提要和集合视图添加到它。

请执行以下操作：

1. 双击`Main.Storyboard`文件中**解决方案资源管理器**打开进行编辑在 Xcode 的 Interface Builder。
2. 将集合视图拖放到主视图，并调整其大小以填充视图：

    ![将集合视图添加到布局](collection-view-images/collection01.png)
3. 与所选的集合视图，使用约束编辑器中将其固定到视图时，它调整：

    ![将约束添加](collection-view-images/collection02.png)
4. 确保集合视图中选择**设计图面**(而非**界定滚动视图**或**剪辑视图**包含它)，切换到**助手编辑器**并创建**插座**集合视图：

    ![将约束添加](collection-view-images/collection03.png)
5. 保存所做的更改并返回到 Visual Studio 进行同步。

<a name="Bringing-it-all-Together"/>

## <a name="bringing-it-all-together"></a>将其所有的结合

所有支持部分现在已放入到位，使其作为数据模型的类 (`PersonModel`)、 一个`NSCollectionViewDataSource`提供的数据，已添加`NSCollectionViewDelegateFlowLayout`旨在处理项选择和`NSCollectionView`已添加到 Main Storyboard公开为输出口 (`EmployeeCollection`)。

最后一步是编辑包含集合视图的视图控制器，并将它们合在一起，填充的集合并处理项选择。

编辑`ViewController.cs`文件，并使其看起来如下所示：

```csharp
using System;
using AppKit;
using Foundation;
using CoreGraphics;

namespace MacCollectionNew
{
    /// <summary>
    /// The View controller controls the main view that houses the Collection View.
    /// </summary>
    public partial class ViewController : NSViewController
    {
        #region Private Variables
        private PersonModel _personSelected;
        private bool shouldEdit = true;
        #endregion

        #region Computed Properties
        /// <summary>
        /// Gets or sets the datasource that provides the data to display in the 
        /// Collection View.
        /// </summary>
        /// <value>The datasource.</value>
        public CollectionViewDataSource Datasource { get; set; }

        /// <summary>
        /// Gets or sets the person currently selected in the collection view.
        /// </summary>
        /// <value>The person selected or <c>null</c> if no person is selected.</value>
        [Export("PersonSelected")]
        public PersonModel PersonSelected
        {
            get { return _personSelected; }
            set
            {
                WillChangeValue("PersonSelected");
                _personSelected = value;
                DidChangeValue("PersonSelected");
                RaiseSelectionChanged();
            }
        }
        #endregion

        #region Constructors
        /// <summary>
        /// Initializes a new instance of the <see cref="T:MacCollectionNew.ViewController"/> class.
        /// </summary>
        /// <param name="handle">Handle.</param>
        public ViewController(IntPtr handle) : base(handle)
        {
        }
        #endregion

        #region Override Methods
        /// <summary>
        /// Called after the view has finished loading from the Storyboard to allow it to
        /// be configured before displaying to the user.
        /// </summary>
        public override void ViewDidLoad()
        {
            base.ViewDidLoad();

            // Initialize Collection View
            ConfigureCollectionView();
            PopulateWithData();
        }
        #endregion

        #region Private Methods
        /// <summary>
        /// Configures the collection view.
        /// </summary>
        private void ConfigureCollectionView()
        {
            EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");

            // Create a flow layout
            var flowLayout = new NSCollectionViewFlowLayout()
            {
                ItemSize = new CGSize(150, 150),
                SectionInset = new NSEdgeInsets(10, 10, 10, 20),
                MinimumInteritemSpacing = 10,
                MinimumLineSpacing = 10
            };
            EmployeeCollection.WantsLayer = true;

            // Setup collection view
            EmployeeCollection.CollectionViewLayout = flowLayout;
            EmployeeCollection.Delegate = new CollectionViewDelegate(this);

        }

        /// <summary>
        /// Populates the Datasource with data and attaches it to the collection view.
        /// </summary>
        private void PopulateWithData()
        {
            // Make datasource
            Datasource = new CollectionViewDataSource(EmployeeCollection);

            // Build list of employees
            Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
            Datasource.Data.Add(new PersonModel("Amy Burns", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Joel Martinez", "Web & Infrastructure"));
            Datasource.Data.Add(new PersonModel("Kevin Mullins", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Mark McLemore", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Tom Opgenorth", "Technical Writer"));
            Datasource.Data.Add(new PersonModel("Larry O'Brien", "API Docs Manager", true));
            Datasource.Data.Add(new PersonModel("Mike Norman", "API Documentor"));

            // Populate collection view
            EmployeeCollection.ReloadData();
        }
        #endregion

        #region Events
        /// <summary>
        /// Selection changed delegate.
        /// </summary>
        public delegate void SelectionChangedDelegate();

        /// <summary>
        /// Occurs when selection changed.
        /// </summary>
        public event SelectionChangedDelegate SelectionChanged;

        /// <summary>
        /// Raises the selection changed event.
        /// </summary>
        internal void RaiseSelectionChanged() {
            // Inform caller
            if (this.SelectionChanged != null) SelectionChanged();
        }
        #endregion
    }
}
```

在详细信息，看一看此代码`Datasource`属性定义为托管实例的`CollectionViewDataSource`，将为集合视图提供数据。 一个`PersonSelected`属性定义用于存储`PersonModel`表示集合视图中当前所选的项。 此属性也会引发`SelectionChanged`时所选内容更改事件。

`ConfigureCollectionView`类用于注册充当与使用以下行集合视图单元格原型视图控制器：

```csharp
EmployeeCollection.RegisterClassForItem(typeof(EmployeeItemController), "EmployeeCell");
```

请注意，**标识符**(`EmployeeCell`) 用来注册一个调用中的原型匹配`GetItem`方法`CollectionViewDataSource`上面定义：

```csharp
var item = collectionView.MakeItem("EmployeeCell", indexPath) as EmployeeItemController;
...
```

此外，视图控制器的类型**必须**的名称匹配`.xib`文件，用于定义该原型**完全**。 此示例中，对于`EmployeeItemController`和`EmployeeItemController.xib`。

集合视图中的项的实际布局由集合视图布局类控制，可以动态更改在运行时通过将分配到的新实例`CollectionViewLayout`属性。 更改此属性更新不会对更改进行动画处理的集合视图的外观。

Apple 提供两种内置布局类型与集合视图将处理最常见用法：`NSCollectionViewFlowLayout`和`NSCollectionViewGridLayout`。 如果开发人员所需的自定义格式，如对一个圆圈中的项进行布局，则可以创建的自定义实例`NSCollectionViewLayout`并重写必需的方法来实现所需的效果。

此示例使用默认流布局，因此它将创建的实例`NSCollectionViewFlowLayout`类，并将其配置，如下所示：

```csharp
var flowLayout = new NSCollectionViewFlowLayout()
{
    ItemSize = new CGSize(150, 150),
    SectionInset = new NSEdgeInsets(10, 10, 10, 20),
    MinimumInteritemSpacing = 10,
    MinimumLineSpacing = 10
};
```

`ItemSize`属性定义该集合中的每个单独的单元格的大小。 `SectionInset`属性定义从单元格将布局中的集合的边缘的嵌入。 `MinimumInteritemSpacing` 定义项之间的最小间距和`MinimumLineSpacing`定义集合中的线之间的最小间距。

布局分配给集合视图和实例`CollectionViewDelegate`附加到处理项选择：

```csharp
// Setup collection view
EmployeeCollection.CollectionViewLayout = flowLayout;
EmployeeCollection.Delegate = new CollectionViewDelegate(this);
```

`PopulateWithData`方法创建的新实例`CollectionViewDataSource`，其中填充数据，将其附加到该集合视图并调用`ReloadData`方法显示的项：

```csharp
private void PopulateWithData()
{
    // Make datasource
    Datasource = new CollectionViewDataSource(EmployeeCollection);

    // Build list of employees
    Datasource.Data.Add(new PersonModel("Craig Dunn", "Documentation Manager", true));
    ...

    // Populate collection view
    EmployeeCollection.ReloadData();
}
```

`ViewDidLoad`方法被重写并调用`ConfigureCollectionView`和`PopulateWithData`方法来向用户显示最终的集合视图：

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();

    // Initialize Collection View
    ConfigureCollectionView();
    PopulateWithData();
}
```

<a name="Summary"/>

## <a name="summary"></a>总结

本文已在 Xamarin.Mac 应用程序中使用集合视图的详细的信息。 首先，它介绍了通过使用键-值编码 (KVC) 和密钥值观察 (KVO) 公开到 Objective C 的 C# 类。 接下来，它介绍了如何使用 KVO 符合类和数据将其绑定到 Xcode 的 Interface Builder 中的集合视图。 最后，它介绍了如何在 C# 代码中使用集合视图交互。

## <a name="related-links"></a>相关链接

- [MacCollectionNew （示例）](https://developer.xamarin.com/samples/mac/MacCollectionNew/)
- [了解 Mac](~/mac/get-started/hello-mac.md)
- [数据绑定和键值编码](~/mac/app-fundamentals/databinding.md)
- [NSCollectionView](https://developer.apple.com/reference/appkit/nscollectionview)
- [OS X 人机界面指南](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)

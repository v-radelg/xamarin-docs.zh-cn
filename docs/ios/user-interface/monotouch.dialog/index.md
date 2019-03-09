---
title: 适用于 Xamarin.iOS 的 MonoTouch.Dialog 简介
description: 本文档介绍了 MonoTouch.Dialog （mt。D)，面向与 Xamarin.iOS 的快速、 声明性 UI 开发的框架。 它讨论了如何使用 MonoTouch.Dialog Api 在代码或 JSON 中创建一个接口并使用下拉刷新、 搜索、 背景图像加载和的详细信息等功能。
ms.prod: xamarin
ms.assetid: 52A35B24-C23B-8461-A8FF-5928A2128FB0
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
---

# <a name="introduction-to-monotouchdialog-for-xamarinios"></a>适用于 Xamarin.iOS 的 MonoTouch.Dialog 简介

MonoTouch.Dialog，称为 mt。D 简称，是一个快速的 UI 开发工具包，允许开发人员能够构建应用程序屏幕和导航使用信息，而不是创建视图控制器、 表等的内容的麻烦。在这种情况下，它提供 UI 开发代码与代码减少显著的简化。 例如，考虑下面的屏幕截图：

 [![](images/image1.png "例如，考虑此屏幕截图")](images/image1.png#lightbox)

下面的代码用于定义此整个屏幕：

```csharp
public enum Category
{
    Travel,
    Lodging,
    Books
}
        
public class Expense
{
    [Section("Expense Entry")]

    [Entry("Enter expense name")]
    public string Name;
    [Section("Expense Details")]
  
    [Caption("Description")]
    [Entry]
    public string Details;
        
    [Checkbox]
    public bool IsApproved = true;
    [Caption("Category")]
    public Category ExpenseCategory;
}
```

当使用 iOS 中的表，是通常大量重复代码。
例如，每次需要一个表时，数据源需要来填充该表。 在具有通过导航控制器连接的两个表基于屏幕的应用程序，每个屏幕共享许多相同的代码。

MT。D，通过将所有相关代码封装到用于创建表的通用 API，简化。 然后，它提供了一种基于声明性绑定语法可简化对象，该 API 抽象。 在这种情况下，两个 Api 中提供了 mt。D:

-   **低级别元素 API** –*元素 API*基于创建表示屏幕和其组件的元素的分层树。 元素 API 为开发人员提供最大的灵活性和控制中创建 Ui。 此外，元素 API 通过 JSON，这允许非常快地声明，以及从服务器动态 UI 生成具有高级支持声明性定义。 
-   **高级反射 API** – 也称为*绑定**API*中的类进行批注与用户界面提示，然后 mt。D 自动创建基于对象的屏幕，并提供和内容之间的绑定是显示 （以及根据需要编辑） 在屏幕上，备份的基础对象。 上面的示例所示反射 API 的用法。 此 API 不提供细化的控件元素 API 执行，但会减少由自动构建基于类的属性的元素层次结构的复杂性更进一步。 


MT。D 中有大量的内置 UI 元素的屏幕创建过程中，但它还认识到需要自定义的元素和高级的屏幕布局。 在这种情况下，可扩展性是一流特色烤到 API。 开发人员可以扩展现有元素或创建新的和无缝集成。

此外，mt。D 具有大量的内置的如"下拉刷新"支持异步图像加载，并搜索支持的常见 iOS 用户体验功能。

本文将全面介绍了使用 mt。D，包括：

-   **MT。D 组件**– 这将重点介绍了解类构成 mt。若要启用快速获取掌握的 D。 
-   **元素引用**– mt。 内置元素的完整列表D. 
-   **高级用法**– 这包括高级的功能，例如下拉刷新、 搜索、 背景图像加载、 使用 LINQ 来构建元素层次结构和创建自定义元素，单元格，并为控制器中使用 mt。D. 

## <a name="setting-up-mtd"></a>设置 mt。D

MT。通过 Xamarin.iOS 分发 D。 若要使用它，右键单击**引用**节点的 Xamarin.iOS 项目在 Visual Studio 2017 或 Visual Studio for Mac，并添加对引用**MonoTouch.Dialog 1**程序集。 然后，添加`using MonoTouch.Dialog`根据代码源中的语句。

## <a name="understanding-the-pieces-of-mtd"></a>了解的组成部分 mt。D

即使使用反射 API，mt。D 创建的元素层次结构实质上，就像已通过元素 API 直接创建。 此外，在上一部分中所述的 JSON 支持以及创建元素。 出于此原因，务必要基本了解的构成片段减少的 mt。D.

MT。D 构建屏幕，即使用以下四个部分：

-  **DialogViewController**
-  **RootElement**
-  **节**
-  **元素**


### <a name="dialogviewcontroller"></a>DialogViewController

一个*DialogViewController*，或*DVC*简称为继承`UITableViewController`，因此代表一个包含表的屏幕。 DVCs 可以推送到导航控制器，就像常规 UITableViewController 上。

### <a name="rootelement"></a>RootElement

一个*RootElement*是进入 DVC 的项的顶级容器。 它包含部分，然后可以包含的元素。 不呈现 RootElements;相反，它们只需容器的内容实际获取呈现。 RootElement 分配给 DVC，然后 DVC 呈现及其子级。

### <a name="section"></a>节

部分是一组表中的单元格。 如与普通表部分中，它可根据需要使用页眉和页脚可以是文本或甚至是自定义视图，如以下屏幕截图中所示：

 [![](images/image2.png "如与普通表部分中，它可根据需要使用页眉和页脚可以是文本或甚至是自定义视图，如以下屏幕截图中所示")](images/image2.png#lightbox)

### <a name="element"></a>元素

元素表示表中的实际单元格。 MT。D 中有各种元素表示不同的数据类型或不同的输入。 例如，下面的屏幕截图演示了几个可用元素：

 [![](images/image3.png "例如，此屏幕快照说明了几个可用元素")](images/image3.png#lightbox)

## <a name="more-on-sections-and-rootelements"></a>在部分和 RootElements

现在让我们讨论 RootElements 和部分中更详细地介绍。

### <a name="rootelements"></a>RootElements

启动 MonoTouch.Dialog 过程需要至少一个 RootElement。

如果使用部分/元素值初始化 RootElement 然后此值用于查找子元素将提供的配置，在右侧显示呈现的摘要。 例如，下面的屏幕截图显示一个表在左侧使用包含的详细信息屏幕，在右侧的"餐后甜点"，以及所选沙漠值标题的单元格。

 [![](images/image4.png "此屏幕截图显示了一个表在左侧使用包含的详细信息屏幕，在右侧，餐后甜点，以及所选沙漠值标题的单元格")](images/image4.png#lightbox) [![](images/image5.png "这下面的屏幕截图与包含的详细信息屏幕，在右侧，餐后甜点，以及所选沙漠值标题的单元格上左侧显示一个表")](images/image5.png#lightbox)

根元素还可在部分内触发加载新的嵌套的配置页上，如上所示。 在此模式下使用时提供的标题在节内呈现时使用，并还用作标题大忙。 例如：

```csharp
var root = new RootElement ("Meals") {
    new Section ("Dinner"){
            new RootElement ("Dessert", new RadioGroup ("dessert", 2)) {
                new Section () {
                    new RadioElement ("Ice Cream", "dessert"),
                    new RadioElement ("Milkshake", "dessert"),
                    new RadioElement ("Chocolate Cake", "dessert")
                }
            }
        }
    }
```

在上述示例中，在用户点击"餐后甜点"时 MonoTouch.Dialog 将创建新的页面并导航到该根被"餐后甜点"并具有三个值与单选按钮组。

在此特定示例中，单选按钮组将选择"餐后甜点"部分中的"巧克力蛋糕"，因为我们传递给 RadioGroup"2"的值。 这意味着选取列表 （零索引） 上的第三项。

调用 Add 方法或使用 C# 4 初始值设定项语法添加部分。
提供了插入方法来插入包含动画的部分。

如果使用的组实例 （而不是 RadioGroup) 创建 RootElement RootElement 时显示的部分中的汇总值将的累计计数的所有 BooleanElements 和 CheckboxElements 具有 Group.Key 值与相同的密钥。

### <a name="sections"></a>部分

节是用于在屏幕中的元素进行分组，他们会 RootElement 的唯一有效的直接子级。 部分可以包含任何标准的元素，其中包括新 RootElements。

RootElements 嵌入在部分中用于导航到新的更深级别。

以字符串形式或作为 UIViews，节可包含页眉和页脚。
通常只需将使用字符串，但若要创建自定义 Ui 可以使用任何 UIView 作为页眉或页脚。 一个字符串，既可用于创建它们像这样：

```csharp
var section = new Section ("Header", "Footer")
```

若要使用视图，只需传递给构造函数的视图：

```csharp
var header = new UIImageView (Image.FromFile ("sample.png"));
var section = new Section (header)
```

### <a name="getting-notified"></a>获取通知

#### <a name="handling-nsaction"></a>处理 NSAction

MT。D 图面`NSAction`作为用于处理回调的委托。
例如，假设你想要处理由 mt。 创建一个表格单元格的触摸事件D. 使用 mt。 创建元素时D，只需提供一个回调函数，如下所示：

```csharp
new Section () {
        new StringElement ("Demo Callback", 
                delegate { Console.WriteLine ("Handled"); })
}
```

#### <a name="retrieving-element-value"></a>检索元素值

结合`Element.Value`属性，该回调可以检索其他元素中设置的值。 例如，考虑以下代码：

```csharp
var element = new EntryElement (task.Name, "Enter task description",
        task.Description);
                
var taskElement = new RootElement (task.Name){
        new Section () { element },
        new Section () { 
                new DateElement ("Due Date", task.DueDate)
        },
        new Section ("Demo Retrieving Element Value") {
                new StringElement ("Output Task Description", 
                        delegate { Console.WriteLine (element.Value); })
        }
};
```

此代码将创建一个 UI，如下所示。 此示例的完整演练，请参阅[元素 API 演练](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)教程。

 [![](images/image6.png "与 Element.Value 属性结合使用，该回调可以检索其他元素中设置的值")](images/image6.png#lightbox)

当用户按下的底部表单元时，匿名函数中的代码执行时，从值写入`element`实例向**应用程序输出**中 Visual Studio for mac。

## <a name="built-in-elements"></a>内置元素

MT。D 附带多个内置的表格单元格项名为元素。
这些元素用于在如字符串、 浮点数、 日期和甚至映像命名只需少量的表单元中显示各种不同类型。 每个元素负责正确显示的数据类型。 例如，布尔值的元素将显示一个开关，用于切换其值。 同样，float 元素将显示一个滑块以更改的浮点值。

没有要支持更丰富的数据类型，如图像和 html 的变得更加复杂元素。 例如，html 元素，这会打开 UIWebView 加载 web 页选择时，在表单元中显示的标题。

### <a name="working-with-element-values"></a>处理元素值

用于捕获用户输入的元素公开公共`Value`属性，该元素的当前值保存在任何时间。 它会自动更新的因为用户使用应用程序。

这是所有属于 MonoTouch.Dialog，元素的行为，但这不是必需的用户创建元素。

### <a name="string-element"></a>字符串元素

一个`StringElement`表格单元格和单元格右侧的字符串值的左侧显示标题。

 [![](images/image7.png "StringElement 表格单元格和单元格右侧的字符串值的左侧显示标题")](images/image7.png#lightbox)

若要使用`StringElement`一个按钮，以提供委托。

```csharp
new StringElement (
        "Click me",
        () => { new UIAlertView("Tapped", "String Element Tapped"
, null, "ok", null).Show(); })
```

 [![](images/image8.png "若要使用 StringElement 以按钮形式，提供委托")](images/image8.png#lightbox)

### <a name="styled-string-element"></a>带样式的字符串元素

一个`StyledStringElement`允许字符串使用任一内置表的单元格样式呈现或使用自定义格式设置。

 [![](images/image9.png "StyledStringElement 允许字符串使用任一内置表的单元格样式呈现或使用自定义格式设置")](images/image9.png#lightbox)

`StyledStringElement`类派生自`StringElement`，但可让开发人员自定义少量的属性，如字体的文本颜色、 背景单元颜色、 行换行模式、 要显示的行数以及是否应显示附件。

### <a name="multiline-element"></a>多行元素

 [![](images/image10.png "多行元素")](images/image10.png#lightbox)

### <a name="entry-element"></a>Entry 元素

`EntryElement`，顾名思义，用于获取用户输入。 它支持常规字符串或其中隐藏字符的密码。

 [![](images/image11.png "EntryElement 用于获取用户输入")](images/image11.png#lightbox)

它是使用三个值进行初始化：

-  将向用户显示的项的标题。
-  （这是为用户提供提示的灰显文本） 的占位符文本。 
-  文本的值。


Placeholder 属性和值可以为 null。 但是，标题是必需的。

在任何时刻，访问其 Value 属性可以检索的值`EntryElement`。

此外`KeyboardType`属性可以设置在创建时为所需的数据输入的键盘类型样式。 这可以用于配置使用的值的键盘`UIKeyboardType`如下所示：

-  Numeric
-  电话
-  URL
-  电子邮件


### <a name="boolean-element"></a>布尔值元素

 [![](images/image12.png "布尔值元素")](images/image12.png#lightbox)

### <a name="checkbox-element"></a>复选框元素

 [![](images/image13.png "复选框元素")](images/image13.png#lightbox)

### <a name="radio-element"></a>单选元素

一个`RadioElement`需要`RadioGroup`中指定`RootElement`。

```csharp
mtRoot = new RootElement ("Demos", new RadioGroup("MyGroup", 0))
```

 [![](images/image14.png "RadioElement 需要 RadioGroup RootElement 中指定")](images/image14.png#lightbox)

 `RootElements` 此外用于协调单选元素。 `RadioElement`成员可以跨多个部分 （例如若要实现类似于环音选择器和单独的自定义铃声从系统铃声的内容）。 摘要视图将显示当前选定的单选元素。 为此，创建`RootElement`与组构造函数，如下：

```csharp
var root = new RootElement ("Meals", new RadioGroup ("myGroup", 0))
```

中的组的名称`RadioGroup`用于显示包含页 （如果有） 中的所选的值和值，该值在这种情况下为零，是第一个选定项的索引。

### <a name="badge-element"></a>徽章元素

 [![](images/image15.png "徽章元素")](images/image15.png#lightbox)

### <a name="float-element"></a>Float 元素

 [![](images/image16.png "Float 元素")](images/image16.png#lightbox)

### <a name="activity-element"></a>Activity 元素

 [![](images/image17.png "Activity 元素")](images/image17.png#lightbox)

### <a name="date-element"></a>日期元素

 ![](images/image18.png "日期元素")

选中对应 DateElement 的单元格后，会看到日期选取器，如下所示：

 [![](images/image19.png "所示选中 DateElement 所对应的单元格后，显示日期选取器")](images/image19.png#lightbox)

### <a name="time-element"></a>Time 元素

 [![](images/image20.png "Time 元素")](images/image20.png#lightbox)

选中对应 TimeElement 的单元格后，会看到时间选取器，如下所示：

 [![](images/image21.png "所示选中 TimeElement 所对应的单元格后，显示时间选取器")](images/image21.png#lightbox)

### <a name="datetime-element"></a>日期时间元素

 [![](images/image22.png "日期时间元素")](images/image22.png#lightbox)

选择对应 DateTimeElement 的单元格时，日期时间选取器会看到如下所示：

 [![](images/image23.png "所示选中 DateTimeElement 所对应的单元格后，显示的日期时间选取器")](images/image23.png#lightbox)

### <a name="html-element"></a>HTML 元素

 [![](images/image24.png "HTML 元素")](images/image24.png#lightbox)

`HTMLElement`的值将显示其`Caption`表单元中的属性。 选择，即可`Url`分配给该元素会加载中`UIWebView`控制，如下所示：

 [![](images/image25.png "选择即可，如下所示，分配给该元素的 Url 加载 UIWebView 控件中")](images/image25.png#lightbox)

### <a name="message-element"></a>消息元素

 [![](images/image26.png "消息元素")](images/image26.png#lightbox)

### <a name="load-more-element"></a>加载更多元素

使用此元素可允许用户加载更多项列表中。 您可以自定义普通和加载隐藏式字幕，以及字体和文本颜色。
`UIActivity`指示器将启动对进行动画处理，并在用户点击该单元格时显示加载标题，然后`NSAction`传递到构造函数执行。 一次中的代码`NSAction`完成之后，`UIActivity`指示器停止进行动画处理，并再次显示正常的标题。

### <a name="uiview-element"></a>UIView 元素

此外，任何自定义`UIView`可以使用显示`UIViewElement`。

### <a name="owner-drawn-element"></a>所有者描述的元素

此元素必须子类化，因为它是一个抽象类。 应重写`Height(RectangleF bounds)`方法应在其中返回的元素的高度，以及`Draw(RectangleF bounds, CGContext context, UIView view)`中这应执行给定的边界内所有自定义的绘制使用上下文和视图的参数。 此元素担负了繁重的子类化`UIView`，并将其放置在单元格中才能返回，让你仅需要实现两个简单的替代。 您所见的示例应用程序中更好地实现示例`DemoOwnerDrawnElement.cs`文件。

下面是一个非常简单的实现类示例：

```csharp
public class SampleOwnerDrawnElement : OwnerDrawnElement
 {
    public SampleOwnerDrawnElement (string text) : base(UITableViewCellStyle.Default, "sampleOwnerDrawnElement")
    {
        this.Text = text;
    }

    public string Text
    {
        get;set;    
    }

    public override void Draw (RectangleF bounds, CGContext context, UIView view)
    {
        UIColor.White.SetFill();
        context.FillRect(bounds);

        UIColor.Black.SetColor();   
        view.DrawString(this.Text, new RectangleF(10, 15, bounds.Width - 20, bounds.Height - 30), UIFont.BoldSystemFontOfSize(14.0f), UILineBreakMode.TailTruncation);
    }

    public override float Height (RectangleF bounds)
    {
        return 44.0f;
    }
 }
```

### <a name="json-element"></a>JSON 元素

`JsonElement`是一个的子类`RootElement`该项`RootElement`要能够从本地或远程 url 加载的嵌套子内容。

`JsonElement`是`RootElement`，可以在两个窗体中实例化。 创建一个版本`RootElement`，将加载按需内容。 这些通过使用创建`JsonElement`在结束时，要加载的内容的 url 需要额外的参数的构造函数：

```csharp
var je = new JsonElement ("Dynamic Data", "https://tirania.org/tmp/demo.json");
```

另一种形式创建将数据从本地文件或将现有`System.Json.JsonObject`具有已对其进行分析：

```csharp
var je = JsonElement.FromFile ("json.sample");
using (var reader = File.OpenRead ("json.sample"))
    return JsonElement.FromJson (JsonObject.Load (reader) as JsonObject, arg);
```

有关详细信息中使用 JSON 的 mt。D，请参阅[JSON 元素演练](http://docs.xamarin.com/guides/ios/user_interface/monotouch.dialog/json_element_walkthrough)教程。

## <a name="other-features"></a>其他功能

### <a name="pull-to-refresh-support"></a>请求刷新支持

 *提取-到-* *刷新*视觉效果最初位于*Tweetie2*应用，后来成为了许多应用程序之间的常见效果。

若要添加到对话框自动下拉刷新支持，只需做两件事： 挂接事件处理程序在用户提取数据时得到通知，并通知`DialogViewController`何时已加载数据返回到其默认状态。

挂接通知很简单;只需连接到`RefreshRequested`上的事件`DialogViewController`，如下所示：

```csharp
dvc.RefreshRequested += OnUserRequestedRefresh;
```

然后在你的方法`OnUserRequestedRefresh`，将队列某些数据加载、 net，从请求某些数据或启动一个线程来计算数据。 将数据加载后，必须通知`DialogViewController`的新数据中，并为其默认状态还原为视图，应执行此操作通过调用`ReloadComplete`:

```csharp
dvc.ReloadComplete ();
```

### <a name="search-support"></a>搜索支持

若要支持搜索，`EnableSearch`上的属性在`DialogViewController`。 您还可以设置`SearchPlaceholder`属性，以使用搜索栏中的水印文本。

搜索将在用户键入时更改视图的内容。 它搜索在可见字段，并显示给用户。 `DialogViewController`公开三个方法以编程方式启动、 终止或触发新的筛选器操作的结果。 下面列出了这些方法：

-  `StartSearch`
-  `FinishSearch`
-  `PerformFilter`


系统是可扩展的因此可更改此行为，如果你想。

### <a name="background-image-loading"></a>背景图像加载

MonoTouch.Dialog 合并[TweetStation](https://github.com/migueldeicaza/TweetStation)应用程序的图像加载程序。 此图像加载程序可以用来加载在后台，支持缓存的图像，并且可以在加载图像时通知你的代码。

它将限制传出网络连接数。

图像加载程序中实现`ImageLoader`类，您需要做的就是调用`DefaultRequestImage`方法，您将需要提供你想要加载的映像，以及实例的 Uri`IImageUpdated`接口将该接口时调用图像 ha已加载的 s。

例如下面的代码从到的 Url 加载图像时`BadgeElement`:

```csharp
string uriString = "http://some-server.com/some image url";

var rootElement = new RootElement("Image Loader") {
        new Section(){
                new BadgeElement( ImageLoader.DefaultRequestImage( new Uri(uriString), this), "Xamarin")
        }
};
```

ImageLoader 类提供可在你想要释放所有当前缓存在内存中的映像时调用的清除方法。 当前代码有 50 个映像的缓存。 如果想要使用不同的缓存大小 （例如，如果希望为太大，50 个映像会过多的映像），可以只创建 ImageLoader 的实例并传递你想要保留在缓存中的映像数量。

## <a name="using-linq-to-create-element-hierarchy"></a>使用 LINQ 来创建元素层次结构

通过明智地利用 LINQ 和 C# 初始化语法，LINQ 可以用于创建元素层次结构。 例如，下面的代码从某些字符串数组创建屏幕和句柄单元格选择一个匿名函数，传递到每个通过`StringElement`:

```csharp
var rootElement = new RootElement ("LINQ root element") {
from x in new string [] { "one", "two", "three" }
select new Section (x) {
from y in "Hello:World".Split (':')
select (Element) new StringElement (y,
delegate { Debug.WriteLine("cell tapped"); })
}
};
```

这可以轻松地结合使用 XML 数据存储或数据库几乎完全从数据创建复杂的应用程序中的数据。

## <a name="extending-mtd"></a>扩展 mt。D

### <a name="creating-custom-elements"></a>创建自定义元素

通过继承现有元素或派生自根类元素，可以创建您自己的元素。

若要创建您自己的元素，您将想要重写以下方法：

```csharp
// To release any heavy resources that you might have
    void Dispose (bool disposing);

    // To retrieve the UITableViewCell for your element
    // you would need to prepare the cell to be reused, in the
    // same way that UITableView expects reusable cells to work
    UITableViewCell GetCell (UITableView tv)

    // To retrieve a "summary" that can be used with
    // a root element to render a summary one level up.  
    string Summary ()
    // To detect when the user has tapped on the cell
    void Selected (DialogViewController dvc, UITableView tableView, NSIndexPath path)
    // If you support search, to probe if the cell matches the user input
    bool Matches (string text)
```

如果您的元素可以具有可变大小，您需要实现`IElementSizing`接口，其中包含一种方法：

```csharp
// Returns the height for the cell at indexPath.Section, indexPath.Row
    float GetHeight (UITableView tableView, NSIndexPath indexPath);
```

如果您打算实现你`GetCell`方法通过调用`base.GetCell(tv)`和自定义返回的单元格，则需要还重写`CellKey`属性以返回一个键，它将是唯一的您的元素，如下所示：

```csharp
static NSString MyKey = new NSString ("MyKey");
    protected override NSString CellKey {
        get {
            return MyKey;
        }
    }
```

对于大多数元素，而不是这样`StringElement`和`StyledStringElement`那些需要对各种呈现方案使用其自己的密钥集。 必须复制这些类中的代码。

### <a name="dialogviewcontrollers-dvcs"></a>DialogViewControllers (DVCs)

反射和元素 API 都使用相同`DialogViewController`。 有时会想要自定义视图的外观或你可能想要使用的一些功能`UITableViewController`限于基本 Ui 的创建。

`DialogViewController`是只是一个的子类`UITableViewController`您可以将自定义的相同方式在自定义和`UITableViewController`。

例如，如果你想要更改列表样式，为`Grouped`或`Plain`，可以通过更改属性时创建的控制器，此类设置此值：

```csharp
var myController = new DialogViewController (root, true){
        Style = UITableViewStyle.Grouped;
    }
```

获取更多高级自定义项的`DialogViewController`，例如，设置其背景，您将子类它并重写了正确的方法，如下面的示例中所示：

```csharp
class SpiffyDialogViewController : DialogViewController {
    UIImage image;

    public SpiffyDialogViewController (RootElement root, bool pushing, UIImage image) 
        : base (root, pushing) 
    {
        this.image = image;
    }

    public override LoadView ()
    {
        base.LoadView ();
        var color = UIColor.FromPatternImage(image);
        TableView.BackgroundColor = UIColor.Clear;
        ParentViewController.View.BackgroundColor = color;
    }
}
```

自定义的另一点是中的以下虚拟方法`DialogViewController`:

```csharp
public override Source CreateSizingSource (bool unevenRows)
```

此方法应返回的子类`DialogViewController.Source`的情况下，在单元格大小保持均等或其子`DialogViewController.SizingSource`如果你的单元格以不相等。

您可以使用此替代捕获的任何`UITableViewSource`方法。 例如， [TweetStation](https://github.com/migueldeicaza/TweetStation)使用它来跟踪用户已滚动到顶部，并相应地更新未读的推文的数目。

## <a name="validation"></a>验证

元素不提供验证本身也适用于网页的模型作为和桌面应用程序执行不直接映射到 iPhone 交互模型。

如果你想要执行数据验证，应执行此操作，当用户输入的数据与触发操作。 例如<span class="ui">完成</span>或<span class="ui">下一步</span>顶部工具栏中，还是某些按钮`StringElement`用作按钮转到下一个阶段。

这是将执行基本的输入的验证和可能是更复杂的验证，例如，查看用户/密码组合与服务器的有效性的位置。

如何通知用户错误是特定于应用程序。 您可以弹出`UIAlertView`或显示一个提示。

## <a name="summary"></a>总结

本文介绍了大量有关 MonoTouch.Dialog 的信息。 它介绍了如何的基础知识 mt。D 工作原理以及涵盖的各种组件构成 mt。D. 它还介绍了各种元素和表的自定义项受 mt。D 和讨论了如何 mt。可以使用自定义元素扩展 D。 此外，它介绍了 mt。 中的 JSON 支持允许从 JSON 动态创建元素的 D。


## <a name="related-links"></a>相关链接

- [截屏视频-Miguel de Icaza 使用 MonoTouch.Dialog 创建 iOS 登录屏幕](http://youtu.be/3butqB1EG0c)
- [截屏视频-轻松创建 iOS 用户界面与 MonoTouch.Dialog](http://youtu.be/j7OC5r8ZkYg)
- [演练：使用元素 API 创建应用程序](~/ios/user-interface/monotouch.dialog/elements-api-walkthrough.md)
- [演练：使用反射 API 创建应用程序](~/ios/user-interface/monotouch.dialog/reflection-api-walkthrough.md)
- [演练：使用 JSON 元素来创建用户界面](~/ios/user-interface/monotouch.dialog/json-element-walkthrough.md)
- [MonoTouch.Dialog JSON 标记](~/ios/user-interface/monotouch.dialog/monotouch.dialog-json-markup.md)
- [Github 上的 MonoTouch 对话框](https://github.com/migueldeicaza/MonoTouch.Dialog)
- [UITableViewController 类引用](https://developer.apple.com/library/ios/#DOCUMENTATION/UIKit/Reference/UITableViewController_Class/Reference/Reference.html)
- [UINavigationController 类引用](https://developer.apple.com/library/ios/#documentation/UIKit/Reference/UINavigationController_Class/Reference/Reference.html)

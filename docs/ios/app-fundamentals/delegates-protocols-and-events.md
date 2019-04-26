---
title: 事件、 协议和 Xamarin.iOS 中的委托
description: 本文档介绍如何使用事件，协议，并在 Xamarin.iOS 中委托。 这些基本概念都普遍 Xamarin.iOS 开发中。
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: 5ccefdb5e527e67338714896905734c74278d00a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392418"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>事件、 协议和 Xamarin.iOS 中的委托

Xamarin.iOS 使用控件公开大多数用户交互的事件。
Xamarin.iOS 应用程序中大致相同的方式使用这些事件，因为执行传统.NET 应用程序。 例如，Xamarin.iOS UIButton 类具有名为 TouchUpInside 的事件，并使用此事件，就好像此类和事件是在.NET 应用中。

除了此.NET 方法时，Xamarin.iOS 公开可用于更复杂的交互和数据绑定的另一个模型。 此方法将使用 Apple 调用委托和协议。 委托是在概念上类似于中的委托C#，但无需定义和调用单个方法，Objective C 中的委托是一种协议符合为整个类。 协议是类似于一个接口在C#，但其方法可以是可选。 因此，例如，若要填充数据 UITableView，会创建实现 UITableView 将调用来填充自己的 UITableViewDataSource 协议中定义的方法的委托类。

在本文中你将了解有关这些主题，为你提供了坚实的基础来处理在 Xamarin.iOS 中，回叫方案包括：

- **事件**– UIKit 控件与使用.NET 事件。
- **协议**– 学习什么协议是否以及如何使用它们，并创建一个示例为地图批注提供数据。
- **委托**– 学习有关通过扩展以 map 为例来处理用户交互，包括批注，然后学习强和弱委托以及何时使用每种之间的差异的 OBJECTIVE-C 的委托。

为了说明协议和委托，我们要生成一个简单的地图应用程序，将批注添加到地图，如下所示：

[![](delegates-protocols-and-events-images/01-map-sml.png "将批注添加到映射的简单的地图应用程序的示例")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "示例批注添加到图")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

之前解决此应用，让我们开始吧通过查看下 UIKit.NET 事件。

## <a name="net-events-with-uikit"></a>使用 UIKit.NET 事件

Xamarin.iOS 公开 UIKit 控件的.NET 事件。 例如，UIButton 具有 TouchUpInside 事件，它使用的以下代码中所示处理像通常那样在.NET 中， C# lambda 表达式：

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

您还可以执行此操作与C#2.0 样式匿名方法如下：

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

前面的代码已建立连接`ViewDidLoad`UIViewController 方法。 `aButton`变量引用一个按钮，可以添加 iOS 设计器中或使用代码。 下图显示了已在 iOS 设计器中添加一个按钮：

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "IOS 设计器中添加了一个按钮")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin.iOS 还支持连接到发生与控件交互的代码的目标操作样式。 若要创建的目标操作**Hello**按钮，请双击 iOS 设计器中单击它。 将显示 UIViewController 的代码隐藏文件和开发人员将需要选择一个位置来插入连接的方法：

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "UIViewControllers 代码隐藏文件")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

选择位置后，新的方法是创建和有线向上取到控件。 在以下示例中，将是一条消息写入到控制台时单击该按钮：

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "单击按钮时，将向控制台写入一条消息")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

有关 iOS 目标操作模式的更多详细信息，请参阅目标操作一部分[适用于 iOS 的核心应用程序能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)Apple 的 iOS 开发人员库中。

有关如何通过 Xamarin.iOS 使用 iOS 设计器的详细信息，请参阅[iOS 设计器概述](~/ios/user-interface/designer/index.md)文档。

## <a name="events"></a>事件

如果你想要截获从 UIControl 事件，有一系列选项： 从使用C#lambda 表达式和委托到使用低级别的 Objective C Api 函数。

以下部分介绍如何将捕获上一个按钮，具体取决于所需的控制程度的 TouchDown 事件。

## <a name="c-style"></a>C#样式

使用委托语法：

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

如果您喜欢的是 lambda:

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

如果你想要具有多个按钮使用相同的处理程序共享相同的代码：

```csharp
void handler (object sender, EventArgs args)
{
   if (sender == button1)
      Console.WriteLine ("button1");
   else
      Console.WriteLine ("some other button");
}

button1.TouchDown += handler;
button2.TouchDown += handler;
```

## <a name="monitoring-more-than-one-kind-of-event"></a>监视多个类型的事件

C# UIControlEvent 标志事件具有一对一映射到各个标志。 如果想要具有相同的代码片段处理两个或多个事件，请使用`UIControl.AddTarget`方法：

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

使用 lambda 语法：

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

如果您需要使用 OBJECTIVE-C、 挂接到特定对象实例和调用特定的选择器等的低级功能：

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

请注意，如果在继承的基类中实现的实例方法，它必须是公共方法。

## <a name="protocols"></a>协议

协议是一项 Objective C 语言功能，提供了一系列方法声明。 它有相似的目标中的接口C#中，主要区别就是协议可以有可选的方法。 如果采用一种协议的类不实现它们，不会调用可选方法。 此外，Objective C 中的单个类可以实现多个协议，就像C#类可以实现多个接口。

Apple 使用整个 iOS 协议来定义约定的类采用时抽象化从调用方，因此操作就像在实现类C#接口。 使用协议中非委托方案 (如与`MKAnnotation`示例如下所示)，并使用委托 （如稍后在本文档中，在委托部分中显示）。

### <a name="protocols-with-xamarinios"></a>使用 Xamarin.ios 的协议

让我们看看使用 Xamarin.iOS 中的 Objective C 协议的一个示例。 对于此示例中，我们将使用`MKAnnotation`协议，它是一部分的`MapKit`框架。 `MKAnnotation` 是一种协议，允许采用它来提供有关可以添加到映射的批注信息的任何对象。 例如，一个对象，实现`MKAnnotation`提供批注和与之相关联的标题的位置。

这样一来，`MKAnnotation`协议用于提供随附批注的相关数据。 从采用对象中的数据生成自身的批注的实际视图`MKAnnotation`协议。 例如，在用户点击批注 （如下面的屏幕截图中所示） 时，将显示标注文本来自`Title`实现协议的类中的属性：

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "当用户点击批注的标注的示例文本")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

下一节中所述[协议的深入探讨](#protocols-deep-dive)，Xamarin.iOS 将协议绑定到抽象类。 有关`MKAnnotation`协议、 绑定C#类命名为`MKAnnotation`若要模拟的名称以及该协议，它是一个的子类`NSObject`，产品 CocoaTouch 根基类。 协议需要 getter 和 setter 来实现的坐标;但是，标题和副标题是可选的。 因此，在`MKAnnotation`类，`Coordinate`属性是*抽象*，这样就必须实现和`Title`并`Subtitle`属性标记*虚拟*使其成为可选的如下所示：

```csharp
[Register ("MKAnnotation"), Model ]
public abstract class MKAnnotation : NSObject
{
    public abstract CLLocationCoordinate2D Coordinate
    {
        [Export ("coordinate")]
        get;
        [Export ("setCoordinate:")]
        set;
    }

    public virtual string Title
    {
        [Export ("title")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }

    public virtual string Subtitle
    {
        [Export ("subtitle")]
        get
        {
            throw new ModelNotImplementedException ();
        }
    }
...
}
```

任何类可提供批注数据通过只派生自`MKAnnotation`只要至少`Coordinate`实现属性。 例如，下面是在构造函数采用坐标，并返回标题字符串的示例类：

```csharp
/// <summary>
/// Annotation class that subclasses MKAnnotation abstract class
/// MKAnnotation is bound by Xamarin.iOS to the MKAnnotation protocol
/// </summary>
public class SampleMapAnnotation : MKAnnotation
{
    string title;

    public SampleMapAnnotation (CLLocationCoordinate2D coordinate)
    {
        Coordinate = coordinate;
        title = "Sample";
    }

    public override CLLocationCoordinate2D Coordinate { get; set; }

    public override string Title {
        get {
            return title;
        }
    }
}
```

通过绑定到协议，任何类的子类`MKAnnotation`可以提供创建批注的视图时，映射将使用的相关数据。 若要向地图添加批注，只需调用`AddAnnotation`方法的`MKMapView`实例，如以下代码所示：

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

下面的映射变量是实例`MKMapView`，这是表示映射本身的类。 `MKMapView`将使用`Coordinate`数据派生自`SampleMapAnnotation`实例来定位在地图上的批注视图。

`MKAnnotation`协议提供了一组已知的功能实现它的任何对象，而无需使用者 （在此情况下图） 无需了解有关实现的详细信息。 这简化了向地图添加各种可能的批注。

### <a name="protocols-deep-dive"></a>协议的深入探讨

由于C#接口不支持可选方法，Xamarin.iOS 将协议映射到抽象类。 因此，采用 Objective C 中的协议被通过在 Xamarin.iOS 中从绑定到协议的抽象类派生并实现所需的方法。 这些方法将公开为类中的抽象方法。 从协议的可选方法将绑定到的虚拟方法C#类。

例如，下面是部分`UITableViewDataSource`协议绑定中的 Xamarin.iOS 作为：

```csharp
public abstract class UITableViewDataSource : NSObject
{
    [Export ("tableView:cellForRowAtIndexPath:")]
    public abstract UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath);
    [Export ("numberOfSectionsInTableView:")]
    public virtual int NumberOfSections (UITableView tableView){...}
...
}
```

请注意，此类为抽象。 Xamarin.iOS 使类抽象以支持协议中的可选/所需的方法。
但是，与 OBJECTIVE-C 的协议不同 (或C#接口)，C#类不支持多重继承。 这会影响设计的C#代码，它使用的协议，并通常会导致嵌套类。 更多有关此问题中介绍了更高版本中的委派部分此文档。

 `GetCell(…)` 是一个抽象方法，绑定到 OBJECTIVE-C*选择器*， `tableView:cellForRowAtIndexPath:`，这是所需的方法的`UITableViewDataSource`协议。 选择器是方法名称的 OBJECTIVE-C 的术语。 若要强制实施所需的方法，Xamarin.iOS 将它声明为抽象。 另一种方法， `NumberOfSections(…)`，绑定到`numberOfSectionsInTableview:`。 此方法是在协议中，可选的因此 Xamarin.iOS 声明为虚拟的它成为可选中重写C#。

Xamarin.iOS 负责为您的所有 iOS 绑定。 但是，如果需要手动将一种协议绑定 Objective C 中，就可以做到修饰类与`ExportAttribute`。 这是 Xamarin.iOS 本身使用的相同方法。

有关如何在 Xamarin.iOS 中绑定 Objective C 类型的详细信息，请参阅文章[绑定 Objective C 类型](~/ios/platform/binding-objective-c/index.md)。

我们还不是通过使用协议，不过。 它们是还用于在 iOS 中的基础 Objective C 的代理，这是下一节的主题。

## <a name="delegates"></a>委托

iOS 使用 OBJECTIVE-C 的委托来实现委派模式，在其中一个对象将传递工作到另一个。 执行工作的对象是第一个对象的委托。 对象告知其委托通过发送该消息后发生某些事件中开展工作。 Objective C 中发送类似这样的消息是功能上等效于调用方法C#。 委托实现这些调用的响应中的方法，并因此提供给应用程序的功能。

委托使您能够扩展类的行为，而无需创建子类。 在 iOS 中的应用程序通常使用委托，当一项重要操作发生后，一个类回调到另一个。 例如，`MKMapView`类返回到其委托的调用，当用户点击在地图上的批注让委托类的作者有机会响应应用程序中。 您可以通过这种类型的委托在本文中，示例中已使用更高版本的使用情况的一个示例处理一个委托，其 Xamarin.iOS。

此时，你可能想知道类如何确定何种方法对其委托调用。 这是使用协议的另一个位置。 通常情况下，可用于委托的方法来自它们采用的协议。

### <a name="how-protocols-are-used-with-delegates"></a>如何与委托一起使用的协议

我们可以看到之前如何使用协议来支持在向地图添加批注。
协议还用于提供一组已知的类来调用某些事件发生时，此类用户两次点击在地图上的批注之后或在表中选择一个单元格后的方法。 实现这些方法的类称为对其进行调用的类的委托。

类支持委派此公开委托属性，向其分配的类实现的委托。 为委托实现的方法将取决于特定的委托采用的协议。 有关`UITableView`方法，实现`UITableViewDelegate`协议，为`UIAccelerometer`方法，你会实施`UIAccelerometerDelegate`，依次类推对于整个 iOS 为其，可能需要的任何其他类公开的委托。

`MKMapView`我们在前面的示例中看到的类还具有一个名为委托，它将调用属性的各种事件发生之后。 委托`MKMapView`属于类型`MKMapViewDelegate`。
你将使用此示例后选择它，则对注释做出响应，但第一个中很快就让我们来讨论强和弱委托之间的差异。

### <a name="strong-delegates-vs-weak-delegates"></a>强委托 vs。弱委托

到目前为止我们已经在委托非常强的委托，这意味着它们都强类型。 Xamarin.iOS 绑定会随附在 iOS 中每个委托协议的强类型类。 但是，iOS 还具有弱委派的概念。 而不是设置为特定的委托绑定到 OBJECTIVE-C 的协议的类的子类，iOS 您还可以选择如何在您喜欢从 NSObject，修饰方法使用与 ExportAttribute，派生的任何类中自行绑定协议方法，然后提供相应的选择器。
时采用此方法，您将您的类的实例分配给 WeakDelegate 属性而不是委托属性。 弱委派提供了灵活地采用委托类按不同的继承层次结构。 让我们看一下使用强和弱委托的 Xamarin.iOS 示例。

### <a name="example-using-a-delegate-with-xamarinios"></a>委托使用 Xamarin.iOS 示例

若要执行代码以响应用户点击批注在本示例中，我们可以子类`MKMapViewDelegate`，并将分配到实例`MKMapView`的`Delegate`属性。 `MKMapViewDelegate`协议只包含可选方法。
因此，所有方法都是虚拟的绑定到此协议在 Xamarin.iOS`MKMapViewDelegate`类。 当用户选择批注，`MKMapView`实例将发送`mapView:didSelectAnnotationView:`其委托到的消息。 若要在 Xamarin.iOS 中处理此问题，我们需要重写`DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)`如下 MKMapViewDelegate 子类中的方法：

```csharp
public class SampleMapDelegate : MKMapViewDelegate
{
    public override void DidSelectAnnotationView (
        MKMapView mapView, MKAnnotationView annotationView)
    {
        var sampleAnnotation =
            annotationView.Annotation as SampleMapAnnotation;

        if (sampleAnnotation != null) {

            //demo accessing the coordinate of the selected annotation to
            //zoom in on it
            mapView.Region = MKCoordinateRegion.FromDistance(
                sampleAnnotation.Coordinate, 500, 500);

            //demo accessing the title of the selected annotation
            Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
        }
    }
}
```

作为包含的控制器中的嵌套类实现上面所示的 SampleMapDelegate 类`MKMapView`实例。 Objective C 中，你通常将看到采用直接在类中的多个协议的控制器。 但是，由于协议绑定到在 Xamarin.iOS 中的类，实现强类型的委托的类是作为嵌套的类通常包含在内。

使用委托类实施完成后，您只需实例化控制器中的委托的实例，并将其分配给`MKMapView`的`Delegate`属性如下所示：

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    SampleMapDelegate _mapDelegate;
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        //set the map's delegate
        _mapDelegate = new SampleMapDelegate ();
        map.Delegate = _mapDelegate;
        ...
    }
    class SampleMapDelegate : MKMapViewDelegate
    {
        ...
    }
}
```

若要使用弱委派来完成相同的操作，需要自行 bind 方法从派生的任何类中`NSObject`并将其分配给`WeakDelegate`属性的`MKMapView`。 由于`UIViewController`类最终都派生`NSObject`（如每个 Objective C 中的类产品 CocoaTouch），我们只是可以实现绑定到的方法`mapView:didSelectAnnotationView:`控制器中直接分配到的控制器和`MKMapView`的`WeakDelegate`，避免所需的额外嵌套类。 下面的代码演示了这种方法：

```csharp
public partial class Protocols_Delegates_EventsViewController : UIViewController
{
    ...
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();
        //assign the controller directly to the weak delegate
        map.WeakDelegate = this;
    }
    //bind to the Objective-C selector mapView:didSelectAnnotationView:
    [Export("mapView:didSelectAnnotationView:")]
    public void DidSelectAnnotationView (MKMapView mapView,
        MKAnnotationView annotationView)
    {
        ...
    }
}
```

运行此代码时，应用程序的行为时运行的强类型的委托版本完全相同。 此代码的好处是类的弱委派不需要的额外时我们使用强类型的委托创建创建。 但是，这会降低类型安全。 如果你打算在传递给的选择器`ExportAttribute`，直到运行时不会找出。

### <a name="events-and-delegates"></a>事件和委托

委托用于在 iOS 中类似于.NET 使用事件的方法的回调。 使 iOS Api 和它们使用 OBJECTIVE-C 的委托的方法看起来更像.NET，Xamarin.iOS 公开在 iOS 中使用委托的位置的多个位置中的.NET 事件。

例如，早期实现其中`MKMapViewDelegate`响应所选的批注也可以实现在 Xamarin.iOS 中使用.NET 事件。 在这种情况下，会在定义事件`MKMapView`并调用`DidSelectAnnotationView`。 它必须`EventArgs`类型的子类`MKMapViewAnnotationEventsArgs`。 `View`属性的`MKMapViewAnnotationEventsArgs`将为您提供对批注视图的引用，无法继续进行相同的实现其从您必须更早版本，如所示此处：

```csharp
map.DidSelectAnnotationView += (s,e) => {
    var sampleAnnotation = e.View.Annotation as SampleMapAnnotation;
    if (sampleAnnotation != null) {
        //demo accessing the coordinate of the selected annotation to
        //zoom in on it
        mapView.Region = MKCoordinateRegion.FromDistance (
            sampleAnnotation.Coordinate, 500, 500);

        //demo accessing the title of the selected annotation
        Console.WriteLine ("{0} was tapped", sampleAnnotation.Title);
    }
};
```

## <a name="summary"></a>总结

本文介绍如何在 Xamarin.iOS 中使用的事件、 协议和委托。 我们已了解如何 Xamarin.iOS 公开控件的正常.NET 样式事件。
接下来我们将了解 OBJECTIVE-C 的协议，包括如何，它们不同于C#接口和 Xamarin.iOS 如何使用它们。 最后，我们探讨了从 Xamarin.iOS 角度来看的 OBJECTIVE-C 的委托。 我们已了解如何 Xamarin.iOS 同时支持强和弱类型化的委托，以及如何绑定.NET 事件委托的方法。

## <a name="related-links"></a>相关链接

- [协议、 委托和事件 （示例）](https://developer.xamarin.com/samples/Protocols_Delegates_Events/)
- [Hello，iOS](~/ios/get-started/hello-ios/index.md)
- [绑定 OBJECTIVE-C 的类型](~/ios/platform/binding-objective-c/index.md)
- [Objective C 编程语言](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [设计用户界面中 Xcode 4](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [适用于 iOS 的核心应用程序能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)

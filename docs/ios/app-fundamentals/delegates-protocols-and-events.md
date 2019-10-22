---
title: Xamarin 中的事件、协议和委托
description: 本文档介绍如何在 Xamarin 中使用事件、协议和委托。 这些基本概念在 Xamarin iOS 开发中无处不在。
ms.prod: xamarin
ms.assetid: 7C07F0B7-9000-C540-0FC3-631C29610447
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/17/2017
ms.openlocfilehash: d42263733c7fa793713738be4b389eaa4850f38b
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68649362"
---
# <a name="events-protocols-and-delegates-in-xamarinios"></a>Xamarin 中的事件、协议和委托

Xamarin 使用控件来公开大多数用户交互事件。
Xamarin iOS 应用程序使用这些事件的方式与使用传统 .NET 应用程序的方式几乎相同。 例如，Xamarin UIButton 类有一个名为 TouchUpInside 的事件，并使用此事件，就像此类和事件位于 .NET 应用中一样。

除了 .NET 方法之外，Xamarin 还公开了另一个可用于更复杂的交互和数据绑定的模型。 此方法使用 Apple 调用委托和协议。 委托在概念上类似于中C#的委托，但并不是定义和调用单个方法，而是在目标-C 中的委托是符合协议的整个类。 协议类似于中C#的接口，只不过其方法可以是可选的。 例如，若要在 UITableView 中填充数据，则需要创建一个委托类，该类实现在 UITableViewDataSource 协议中定义的方法，UITableView 将调用该方法来填充自身。

在本文中，你将了解所有这些主题，为你提供在 Xamarin 中处理回调方案的坚实基础，其中包括：

- **事件**–使用带有 UIKit 控件的 .net 事件。
- **协议**–了解哪些协议是什么以及如何使用它们，以及如何创建一个为地图批注提供数据的示例。
- **委托**-通过扩展映射示例来了解目标-C 委托，以处理包含批注的用户交互，然后了解强和弱委托之间的差异，以及何时使用这些委托。

为了阐明协议和委托，我们将构建一个简单的地图应用程序，用于将批注添加到地图中，如下所示：

[![](delegates-protocols-and-events-images/01-map-sml.png "An example of a simple map application that adds an annotation to a map")](delegates-protocols-and-events-images/01-map.png#lightbox)
[![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "An example annotation added to a map")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

在处理此应用之前，让我们先了解一下 UIKit 下的 .NET 事件。

## <a name="net-events-with-uikit"></a>具有 UIKit 的 .NET 事件

Xamarin 在 UIKit 控件上公开 .NET 事件。 例如，UIButton 有一个 TouchUpInside 事件，该事件在 .NET 中照常处理，如以下使用C# lambda 表达式的代码所示：

```csharp
aButton.TouchUpInside += (o,s) => {
    Console.WriteLine("button touched");
};
```

你还可以使用如下所示C#的2.0 样式匿名方法实现此操作：

```csharp
aButton.TouchUpInside += delegate {
    Console.WriteLine ("button touched");
};
```

前面的代码在 UIViewController 的 `ViewDidLoad` 方法中连接。 @No__t_0 变量引用一个按钮，你可以在 iOS 设计器中或使用代码添加该按钮。 下图显示了已在 iOS 设计器中添加的按钮：

[![](delegates-protocols-and-events-images/02-interface-builder-outlet-sml.png "A button added in iOS Designer")](delegates-protocols-and-events-images/02-interface-builder-outlet.png#lightbox)

Xamarin 还支持将代码连接到与控件发生的交互的目标操作样式。 若要为 " **Hello** " 按钮创建目标操作，请在 IOS 设计器中双击它。 将显示 UIViewController 的代码隐藏文件，并要求开发人员选择要插入连接方法的位置：

[![](delegates-protocols-and-events-images/03-interface-builder-action-sml.png "The UIViewControllers code-behind file")](delegates-protocols-and-events-images/03-interface-builder-action.png#lightbox)

选择某个位置后，将创建一个新方法，并将其连接到该控件。 在下面的示例中，单击按钮时，会将一条消息写入控制台：

[![](delegates-protocols-and-events-images/05-interface-builder-action-sml.png "A message will be written to the console when the button is clicked")](delegates-protocols-and-events-images/05-interface-builder-action.png#lightbox)

有关 iOS 目标-操作模式的详细信息，请参阅 Apple 的 iOS 开发人员库中的[适用于 ios 的核心应用程序能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)的目标操作部分。

有关如何将 iOS 设计器与 Xamarin 一起使用的详细信息，请参阅[Ios 设计器概述](~/ios/user-interface/designer/index.md)文档。

## <a name="events"></a>事件

如果要从 UIControl 截获事件，可以使用一系列选项：从使用C# lambda 和委托函数到使用低级别目标-C api。

以下部分说明如何捕获按钮上的 System.windows.uielement.touchdown> 事件，具体取决于所需的控制程度。

## <a name="c-style"></a>C#方式

使用委托语法：

```csharp
UIButton button = MakeTheButton ();
button.TouchDown += delegate {
    Console.WriteLine ("Touched");
};
```

如果你想要改用 lambda：

```csharp
button.TouchDown += () => {
   Console.WriteLine ("Touched");
};
```

如果希望有多个按钮，请使用同一处理程序来共享同一代码：

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

## <a name="monitoring-more-than-one-kind-of-event"></a>监视多种类型的事件

UIControlEvent C#标志的事件对于单个标志具有一对一的映射。 如果要让相同的代码段处理两个或更多事件，请使用 `UIControl.AddTarget` 方法：

```csharp
button.AddTarget (handler, UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

使用 lambda 语法：

```csharp
button.AddTarget ((sender, event)=> Console.WriteLine ("An event happened"), UIControlEvent.TouchDown | UIControlEvent.TouchCancel);
```

如果需要使用目标 C 的低级别功能，如挂钩到特定对象实例和调用特定的选择器：

```csharp
[Export ("MySelector")]
void MyObjectiveCHandler ()
{
    Console.WriteLine ("Hello!");
}

// In some other place:

button.AddTarget (this, new Selector ("MySelector"), UIControlEvent.TouchDown);
```

请注意，如果在继承的基类中实现实例方法，则该方法必须是公共方法。

## <a name="protocols"></a>协议

协议是一种提供方法声明列表的目标 C 语言功能。 它的作用类似于中C#的接口，主要区别在于协议可以具有可选方法。 如果采用协议的类未实现可选方法，则不会调用这些方法。 此外，目标中的单个类可以实现多个协议，就像C#类可以实现多个接口一样。

Apple 在 iOS 中使用协议来定义类要采用的协定，同时从调用方抽象出实现类，从而像C#接口一样操作。 协议既可用于非委托方案（如下面所示的 `MKAnnotation` 示例），也可用于委托（如本文档后面的 "委托" 部分中所述）。

### <a name="protocols-with-xamarinios"></a>采用 Xamarin 的协议

我们来看一个示例，其中使用了 Xamarin 中的目标-C 协议。 在此示例中，我们将使用 `MKAnnotation` 协议，该协议是 `MapKit` 框架的一部分。 `MKAnnotation` 是一种协议，该协议允许使用它来提供有关可添加到地图的批注的信息。 例如，实现 `MKAnnotation` 的对象提供批注的位置以及与其关联的标题。

这样，就可以使用 `MKAnnotation` 协议来提供批注随附的相关数据。 批注本身的实际视图是从采用 `MKAnnotation` 协议的对象中的数据生成的。 例如，当用户点击批注时显示的标注文本（如下面的屏幕截图所示）来自实现协议的类中的 `Title` 属性：

 [![](delegates-protocols-and-events-images/04-annotation-with-callout-sml.png "Example text for the callout when the user taps on the annotation")](delegates-protocols-and-events-images/04-annotation-with-callout.png#lightbox)

如以下部分中所述，[协议深入探讨](#protocols-deep-dive)，Xamarin 将协议绑定到抽象类。 对于 `MKAnnotation` 协议，绑定C#类命名为 `MKAnnotation` 以模仿协议名称，并且它是 `NSObject` 的子类（CocoaTouch 的根基类）。 协议需要为该坐标实现 getter 和 setter;但标题和副标题是可选的。 因此，在 `MKAnnotation` 类中，`Coordinate` 属性是*抽象*的，需要实现它，`Title` 和 `Subtitle` 属性被标记为 "*虚拟*"，使其成为可选属性，如下所示：

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

只要至少实现了 `Coordinate` 属性，任何类都可以通过仅从 `MKAnnotation` 派生来提供批注数据。 例如，下面是一个在构造函数中采用坐标并返回标题字符串的示例类：

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

通过它绑定到的协议，子类 `MKAnnotation` 的任何类都可以提供将在创建批注视图时由地图使用的相关数据。 若要将批注添加到地图，只需调用 `MKMapView` 实例的 `AddAnnotation` 方法，如以下代码所示：

```csharp
//an arbitrary coordinate used for demonstration here
var sampleCoordinate =
    new CLLocationCoordinate2D (42.3467512, -71.0969456); // Boston

//create an annotation and add it to the map
map.AddAnnotation (new SampleMapAnnotation (sampleCoordinate));
```

此处的地图变量是 `MKMapView` 的实例，它是表示地图本身的类。 @No__t_0 将使用从 `SampleMapAnnotation` 实例派生的 `Coordinate` 数据将批注视图定位到地图上。

@No__t_0 协议跨实现该协议的任何对象提供一组已知功能，无需使用者（在本例中为 map）需要了解有关实现的详细信息。 这简化了将各种可能的批注添加到地图中的工作。

### <a name="protocols-deep-dive"></a>深层协议

由于C#接口不支持可选方法，因此，Xamarin 会将协议映射到抽象类。 因此，在 Ansi-c 中采用的协议是在 Xamarin 中通过从绑定到协议和实现所需方法的抽象类派生而来的。 这些方法将公开为类中的抽象方法。 协议中的可选方法将绑定到C#类的虚方法。

例如，下面是 Xamarin 中的 `UITableViewDataSource` 协议的一部分：

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

请注意，类是抽象类。 Xamarin 使类成为抽象类，以便支持协议中的可选方法/所需的方法。
但是，与目标 C 协议（或C#接口）不同， C#类不支持多重继承。 这会影响使用协议C#的代码设计，并且通常会导致嵌套类。 有关此问题的详细信息将在本文档后面的 "委托" 部分中介绍。

 `GetCell(…)` 是一种抽象方法，该方法绑定到目标 C*选择器*，`tableView:cellForRowAtIndexPath:`，这是 `UITableViewDataSource` 协议所需的方法。 选择器是方法名称的目标-C 术语。 若要根据需要强制执行方法，Xamarin 会将它声明为 abstract。 另一种方法 `NumberOfSections(…)` 绑定到 `numberOfSectionsInTableview:`。 此方法在协议中是可选的，因此，Xamarin 会将它声明为虚拟的，使其在C#中是可选的。

Xamarin 会为你处理所有 iOS 绑定。 但是，如果您需要从目标 C 手动绑定协议，则可以通过使用 `ExportAttribute` 修饰类来实现此目的。 此方法与 Xamarin 本身使用的方法相同。

有关如何在 Xamarin 中绑定目标 C 类型的详细信息，请参阅[绑定目标 c 类型一](~/ios/platform/binding-objective-c/index.md)文。

不过，我们还不会使用协议。 它们还在 iOS 中用作目标-C 委托的基础，这是下一节的主题。

## <a name="delegates"></a>委托

iOS 使用目标-C 委托实现委托模式，在该模式中，一个对象将工作传递到另一个对象。 执行工作的对象是第一个对象的委托。 对象通过在发生某些事情后发送消息来通知其委托执行工作。 在 Ansi-c 中发送类似于此的消息在功能上等同于调用中C#的方法。 委托实现方法以响应这些调用，因此提供应用程序的功能。

委托使你可以扩展类的行为，而无需创建子类。 如果某个类在发生重要操作后返回到另一个类，则 iOS 中的应用程序通常会使用委托。 例如，当用户在地图上点击批注时，`MKMapView` 类向其委托返回，使委托类的作者有机会在应用程序中进行响应。 可以在本文后面的部分中完成此类委托用法的示例，例如，将委托与 Xamarin 配合使用。

此时，你可能想知道类如何确定在其委托上调用的方法。 这是使用协议的另一个位置。 通常，适用于委托的方法来自它们采用的协议。

### <a name="how-protocols-are-used-with-delegates"></a>如何将协议与委托一起使用

我们先前看到了如何使用协议来支持将批注添加到地图中。
协议还用于为类提供一组已知的方法，以便在发生特定事件之后调用，如用户在地图上点击批注或选择表中的单元格之后。 实现这些方法的类称为调用它们的类的委托。

支持委托的类通过公开委托属性来实现此目的，实现委托的类将分配给该属性。 为委托实现的方法将取决于特定委托采用的协议。 对于 `UITableView` 方法，你可以实现 `UITableViewDelegate` 协议，对于 `UIAccelerometer` 方法，你应为要公开委托的所有 iOS 中的任何其他类实现 `UIAccelerometerDelegate`。

我们在前面的示例中看到的 `MKMapView` 类还具有一个名为 Delegate 的属性，该属性将在各种事件发生后调用。 @No__t_0 的委托的类型为 `MKMapViewDelegate`。
你将在示例中稍后使用此方法来响应选中的批注，但首先我们讨论强委托和弱委托之间的区别。

### <a name="strong-delegates-vs-weak-delegates"></a>强委托与弱委托

到目前为止，我们查看的委托是强委托，这意味着它们是强类型的。 对于 iOS 中的每个委托协议，Xamarin 绑定都附带一个强类型类。 但是，iOS 还具有弱委托的概念。 IOS 还可让你选择在任何从 NSObject 派生的类中自行绑定协议方法，并使用 ExportAttribute 修饰方法，然后，而不是将绑定到特定委托的目标为 C 协议的类。提供适当的选择器。
采用此方法时，请将类的实例分配给 WeakDelegate 属性，而不是委托属性。 弱委托使你可以灵活地将委托类向下移动到不同的继承层次结构。 让我们看看使用强委托和弱委托的 Xamarin iOS 示例。

### <a name="example-using-a-delegate-with-xamarinios"></a>将委托与 Xamarin 一起使用的示例

若要在我们的示例中执行代码以响应用户点击批注，可以 `MKMapViewDelegate` 子类，并将实例分配给 `MKMapView` 的 `Delegate` 属性。 @No__t_0 协议仅包含可选方法。
因此，所有方法都是在 Xamarin `MKMapViewDelegate` 类中绑定到此协议的虚拟。 当用户选择某一批注时，`MKMapView` 实例将向其委托发送 `mapView:didSelectAnnotationView:` 消息。 若要在 Xamarin 中处理此情况，需要重写 MKMapViewDelegate 子类中的 `DidSelectAnnotationView (MKMapView mapView, MKAnnotationView annotationView)` 方法，如下所示：

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

上面所示的 SampleMapDelegate 类在包含 `MKMapView` 实例的控制器中作为嵌套类实现。 在目标-C 中，通常会看到控制器直接采用类中的多个协议。 不过，由于协议绑定到 Xamarin 中的类，实现强类型委托的类通常作为嵌套类包括在内。

委托类实现就绪后，只需在控制器中实例化委托实例，并将其分配给 `MKMapView` 的 `Delegate` 属性，如下所示：

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

若要使用弱委托来完成相同的操作，需要在任何派生自 `NSObject` 的类中自行绑定方法，并将其分配给 `MKMapView` 的 `WeakDelegate` 属性。 由于 `UIViewController` 类最终派生自 `NSObject` （类似于 CocoaTouch 中的每个目标-C 类），因此，只需实现直接在控制器中的一个绑定到 `mapView:didSelectAnnotationView:` 的方法，并将控制器分配给 `MKMapView` 的 `WeakDelegate`，这就无需额外的嵌套类。 下面的代码演示了这种方法：

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

运行此代码时，应用程序的行为与运行强类型委托版本时完全相同。 此代码的优点在于弱委托不需要创建在使用强类型委托时创建的额外类。 但是，这种情况下会产生安全类型。 如果在传递给 `ExportAttribute` 的选择器中犯了错误，则在运行时之前将不会发现。

### <a name="events-and-delegates"></a>事件和委托

委托用于 iOS 中的回调，与 .NET 使用事件的方式类似。 为了使 iOS Api 和它们使用目标-C 委托看起来更像 .NET，Xamarin，在 iOS 中使用委托的许多地方都公开了 .NET 事件。

例如，在早期的实现中，还可以使用 .NET 事件在 Xamarin 中实现对选定批注的 `MKMapViewDelegate` 响应。 在这种情况下，将在 `MKMapView` 中定义事件，并将其称为 `DidSelectAnnotationView`。 它将具有类型 `MKMapViewAnnotationEventsArgs` 的 `EventArgs` 子类。 @No__t_1 的 `View` 属性会为你提供一个对批注视图的引用，你可以在该视图中继续使用之前介绍的相同实现，如下所示：

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

本文介绍了如何在 Xamarin 中使用事件、协议和委托。 我们了解到，iOS 如何公开控件的普通 .NET 样式事件。
接下来，我们了解了客观-C 协议，其中包括它们与C#接口以及 Xamarin 如何使用它们的不同之处。 最后，我们从 Xamarin 的一个角度检查了目标-C 委托。 我们了解了 Xamarin iOS 如何支持强类型和弱类型委托，以及如何将 .NET 事件绑定到委托方法。

## <a name="related-links"></a>相关链接

- [协议、委托和事件（示例）](https://docs.microsoft.com/samples/xamarin/ios-samples/protocols-delegates-events)
- [了解 iOS](~/ios/get-started/hello-ios/index.md)
- [绑定目标-C 类型](~/ios/platform/binding-objective-c/index.md)
- [目标 C 编程语言](https://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
- [在 Xcode 4 中设计用户界面](https://developer.apple.com/library/ios/#documentation/IDEs/Conceptual/Xcode4TransitionGuide/InterfaceBuilder/InterfaceBuilder.html)
- [适用于 iOS 的核心应用程序能力](https://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/Devpedia-CocoaApp/TargetAction.html)

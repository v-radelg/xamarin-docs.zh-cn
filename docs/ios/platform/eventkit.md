---
title: 在 Xamarin.iOS EventKit
description: 本文档介绍 EventKit 以及如何在 Xamarin.iOS 中使用它。 它讨论了日历、 日历事件和提醒，查看使用 EventKit，和的详细信息进行编程时常用的类。
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: ea03c8b382e2de29bd20ab1d696d7abb7733e182
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61369221"
---
# <a name="eventkit-in-xamarinios"></a>在 Xamarin.iOS EventKit

iOS 应用有两个日历相关内置： 日历应用程序，并提醒应用程序。 它非常明了，若要了解如何日历应用程序管理日历数据，但提醒应用程序是不太明显。 提醒可能会遇到这样时它们的原因，当它们在完成后，使用它们的相关联的日期等。在这种情况下，iOS 日历将所有数据都存储，无论是日历事件或提醒，在一个位置，调用*日历数据库*。

EventKit framework 提供了一种访问方法*日历*，*日历事件*，并*提醒*日历数据库存储的数据。 访问对日历和日历事件以来提供 iOS 4，但访问提醒 iOS 6 中的新增功能。

在本指南中我们将介绍：

-   **EventKit 基础知识**– 这会引入 EventKit 通过主要类的基本组成部分，并且提供了其使用情况的了解。 需要处理文档的下一步的一部分之前阅读此部分。 
-   **常见任务**– 常见的任务部分用于将如何执行常见如下所述的快速参考; 枚举日历、 创建、 保存和检索日历事件和提醒，以及使用的内置控制器创建和修改日历事件。 本部分中需要读取从前到后，按其目的是为特定任务的引用。 


配套示例应用程序中提供了本指南中的所有任务：

 [![](eventkit-images/01.png "随附示例应用程序屏幕")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>要求

IOS 4.0 中引入了 EventKit 但 iOS 6.0 中引入了对提醒数据的访问。 在这种情况下，为常规 EventKit 开发，都需要至少面向版本 4.0 和 6.0 的提醒。

此外，提醒应用程序不在模拟器中，这意味着，提醒数据将还不可用，除非首先添加上可用。 此外，访问请求才会显示到实际设备上的用户。 在这种情况下，EventKit 开发最适合在设备上进行测试。

## <a name="event-kit-basics"></a>事件工具包基础知识

在使用 EventKit，务必掌握的公共类，其使用情况。 所有这些类可在`EventKit`并`EventKitUI`(对于`EKEventEditController`)。

### <a name="eventstore"></a>EventStore

*EventStore*类是在 EventKit 中最重要的类，因为它所需 EventKit 中执行任何操作。 它可以认为的永久性存储或数据库引擎，为所有 EventKit 数据。 从`EventStore`有权访问的日历和日历应用程序中的日历事件以及提醒应用程序中的提醒。

因为`EventStore`是如数据库引擎，它应该是生存期较长，这意味着，它应创建并在应用程序实例的生存期内销毁降至最低。 事实上，我们建议创建的一个实例后`EventStore`在应用程序，则使围绕该引用的整个生存期内的应用程序，除非您确信不会再次需要。 此外，所有调用应都转到单个`EventStore`实例。 出于此原因，单一实例模式被建议用于保持可用的单一实例。

#### <a name="creating-an-event-store"></a>创建事件存储

以下代码演示了创建的单个实例的有效方法`EventStore`类，并使其以静态方式从应用程序中可用：

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

上面的代码使用单一实例模式来实例化的实例`EventStore`应用程序加载时。 `EventStore`可以访问全局范围内从应用程序中，如下所示：

```csharp
App.Current.EventStore;
```

请注意，此处中的所有示例都使用此模式中，因此它们引用`EventStore`通过`App.Current.EventStore`。

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>请求访问日历和提醒数据

然后才允许访问通过 EventStore 任何数据，应用程序必须首先请求访问日历的事件数据或提醒数据，具体取决于哪一种需要。 以帮助实现这一点，`EventStore`公开一个名为方法`RequestAccess`的 — 调用时，将向用户告知应用程序正在请求访问的日历数据或提醒数据，具体取决于哪个显示警报视图`EKEntityType`传递给它。 因为它会发出警报的视图，该调用是异步的并且将调用完成处理程序作为传递`NSAction`（或 Lambda） 向其以接收两个参数; 一个布尔值，该值指示是否已授予访问权限的和`NSError`，而后者如果 not null 将包含在请求中的任何错误信息。 例如，以下编码将请求访问日历事件数据并显示警报查看如果未授予的请求。

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

后已授予的请求，它将记住，只要该应用程序在设备上安装，不会弹出警报给用户。
但是，访问仅提供给资源，日历事件或提醒授予的类型。 如果应用程序需要访问权限，则应请求两者。

记住权限，因为它是相对比较便宜，因此，最好始终在执行操作之前请求访问每次发出请求。

此外，由于完成处理程序在单独的 (非 UI) 线程上调用的因此完成处理程序中的用户界面的任何更新应调用通过`InvokeOnMainThread`，否则将引发异常，并且未被捕获，如果应用程序将崩溃。

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` 是一个枚举，描述类型的`EventKit`项或数据。 它有两个值：`Event`和提醒。 使用多种方法，包括`EventStore.RequestAccess`告诉`EventKit`要有权访问或检索的数据类型。

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar*表示一种日历，其中包含的一组日历事件。 日历可以存储在多个不同位置，如在本地， *iCloud*中的第三方提供程序位置，如*Exchange Server*或*Google*，等等。很多时候`EKCalendar`用于告知`EventKit`何处寻找的事件或将其保存的位置。

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController*可在`EventKitUI`命名空间，并且是可用于编辑或创建日历事件的内置控制器。 非常像内置照相机控制器`EKEventEditController`会为您显示 UI 和处理保存中完成繁重的任务。

### <a name="ekevent"></a>EKEvent

 *EKEvent*表示日历事件。 这两`EKEvent`并`EKReminder`继承`EKCalendarItem`和具有字段，例如`Title`， `Notes`，依次类推。

### <a name="ekreminder"></a>EKReminder

 *EKReminder*表示提醒项。

### <a name="ekspan"></a>EKSpan

*EKSpan*是一个枚举，用于描述事件时修改的事件可以重复发生，并且有两个值的范围：*ThisEvent*并*FutureEvents*。 `ThisEvent` 表示的任何更改将只发生到正在引用的序列中的特定事件而`FutureEvents`会影响该事件和所有将来的重复周期。

## <a name="tasks"></a>任务

为了方便使用，EventKit 使用情况已被拆分成以下各节中所述的常见任务。

### <a name="enumerate-calendars"></a>枚举的日历

若要枚举已在设备配置了用户的日历，请调用`GetCalendars`上`EventStore`并传递你希望接收的日历 （提醒或事件） 的类型：

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>添加或修改使用内置的控制器的事件

*EKEventEditViewController*为您做了大量繁重的任务，如果你想要创建或编辑具有相同的 UI，使用日历应用程序时，向用户显示的事件：

 [![](eventkit-images/02.png "使用日历应用程序时，向用户显示 UI")](eventkit-images/02.png#lightbox)

若要使用它，您需要将其声明为类级别变量，以便它不会作为垃圾回收时获得方法中声明：

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

然后，若要启动它： 它进行实例化、 为其提供的引用`EventStore`，接通*EKEventEditViewDelegate*委托给它，并显示其使用`PresentViewController`:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

（可选） 如果你想要预填充该事件，您可以创建一个新事件 （如下所示），也可以检索已保存的事件：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

如果想要预填充 UI，请确保在控制器上设置的事件属性：

```csharp
eventController.Event = newEvent;
```

若要使用的现有事件，请参阅*检索事件 ID 通过*部分更高版本上。

委托应重写`Completed`方法，该用户已完成与对话框时，控制器会调用方法：

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

（可选） 在委托中，可以检查*操作*中`Completed`方法可以修改事件并将其重新保存，或执行其他操作，如果取消，等：

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>以编程方式创建一个事件

若要在代码中创建的事件，请使用*FromStore*上的工厂方法`EKEvent`类，并对其设置的任何数据：

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

必须设置要在中，保存该事件的日历，但如果您有没有首选项，则可以使用默认值：

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

若要保存该事件，请调用*SaveEvent*方法`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

保存后， *EventIdentifier*属性将更新为可用于更高版本中检索事件的唯一标识符：

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` 是一个字符串格式的 GUID。

### <a name="create-a-reminder-programmatically"></a>以编程方式创建提醒

在代码中创建提醒是类似于创建日历事件：

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

若要保存，请调用*SaveReminder*方法`EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>检索按 ID 的事件

若要通过它来检索事件 ID，请使用*EventFromIdentifier*方法`EventStore`并将其传递`EventIdentifier`从事件提取：

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

对于事件，还有其他两个标识符属性，但`EventIdentifier`是仅适用于此的一种。

### <a name="retrieving-a-reminder-by-id"></a>检索按 ID 提醒

若要检索提醒，请使用*GetCalendarItem*方法`EventStore`并将其传递*CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

因为`GetCalendarItem`将返回`EKCalendarItem`，必须强制转换为`EKReminder`您是否需要访问提醒数据或使用作为实例`EKReminder`更高版本。

不要使用`GetCalendarItem`对于日历事件，因为在撰写本文之际，它不起作用。

### <a name="deleting-an-event"></a>删除事件

若要删除的日历事件，调用*RemoveEvent*上你`EventStore`，并将传递到事件，然后使用适当的引用`EKSpan`:

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

请注意但是，事件已被删除后，事件引用将被`null`。

### <a name="deleting-a-reminder"></a>删除提醒

若要删除提醒，调用*RemoveReminder*上`EventStore`，并将传递到提醒的引用：

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

请注意，在上面的代码没有强制转换为`EKReminder`，这是因为`GetCalendarItem`用于对其进行检索

### <a name="searching-for-events"></a>搜索事件

若要搜索的日历事件，必须创建*NSPredicate*通过对象*PredicateForEvents*方法`EventStore`。 `NSPredicate`是一种查询的数据对象的 iOS 使用查找匹配项：

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

创建后`NSPredicate`，使用*EventsMatching*方法`EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

请注意查询是同步 （阻止），可能需要较长的时间，具体取决于查询，因此你可能想要启动新线程或任务以执行此操作。

### <a name="searching-for-reminders"></a>搜索提醒

搜索提醒的是类似于事件;它需要一个谓词，但调用已是异步的因此无需担心如何阻止线程：

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>总结

本文档提供的 EventKit 框架中，这两个重要组件和许多最常见任务的概述。 但是，EventKit 框架是非常大而且功能强大，，包括尚未引入了此处，如的功能： 批量更新，配置警报，针对事件，注册并侦听更改日历数据库上配置重复周期设置地域隔离区和的详细信息。  有关详细信息，请参阅 Apple[日历和提醒编程指南](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)。


## <a name="related-links"></a>相关链接

- [日历 （示例）](https://developer.xamarin.com/samples/monotouch/Calendars/)
- [iOS 6 简介](~/ios/platform/introduction-to-ios6/index.md)
- [日历和提醒简介](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)

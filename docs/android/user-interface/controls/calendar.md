---
title: Xamarin Android 日历
ms.prod: xamarin
ms.assetid: 78666541-CA14-4CD8-A94A-A9621C57813E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 610f9be346b761c4d20e4bbf97ecfcda74c0bee0
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523060"
---
# <a name="xamarinandroid-calendar"></a>Xamarin Android 日历


## <a name="calendar-api"></a>日历 API

Android 4 中引入的一组新日历 Api 支持设计为向日历提供程序读取数据或向其写入数据的应用程序。 这些 Api 支持丰富的日历数据交互选项, 包括读取和写入事件、与会者和提醒的能力。 通过在你的应用程序中使用日历提供程序, 你通过 API 添加的数据将出现在 Android 4 随附的内置日历应用中。


## <a name="adding-permissions"></a>添加权限

在您的应用程序中使用新的日历 Api 时, 您需要做的第一件事就是向 Android 清单添加适当的权限。 需要添加的权限为`android.permisson.READ_CALENDAR`和`android.permission.WRITE_CALENDAR`, 具体取决于是否要读取和/或写入日历数据。


## <a name="using-the-calendar-contract"></a>使用日历约定

设置权限后, 可以使用`CalendarContract`类与日历数据进行交互。 此类提供了一个数据模型, 应用程序在与日历提供程序交互时可以使用该模型。 `CalendarContract`允许应用程序将 uri 解析为日历实体, 如日历和事件。 它还提供了一种方法, 用于与每个实体中的各个字段 (例如日历的名称和 ID) 或事件的开始和结束日期交互。

让我们看看使用日历 API 的示例。 在此示例中, 我们将检查如何枚举日历及其事件, 以及如何将新事件添加到日历。


## <a name="listing-calendars"></a>列出日历

首先, 让我们看一下如何枚举已在日历应用中注册的日历。 为此, 我们可以实例化`CursorLoader`。 在 Android 3.0 (API 11) 中引入`CursorLoader` , 是首选的`ContentProvider`使用方法。 至少需要为日历和要返回的列指定内容 Uri;此列规范称为_投影_。

通过调用`CursorLoader.LoadInBackground`方法, 我们可以查询数据的内容提供程序, 如日历提供程序。
`LoadInBackground`执行实际的加载操作并返回`Cursor`包含查询结果的。

有助于我们同时指定内容`Uri`和投影。 `CalendarContract` 若要获取用于`Uri`查询日历的内容, 只需使用如下`CalendarContract.Calendars.ContentUri`所示的属性:

```csharp
var calendarsUri = CalendarContract.Calendars.ContentUri;
```

`CalendarContract`使用指定所需的日历列同样简单。 只需将类中的`CalendarContract.Calendars.InterfaceConsts`字段添加到数组中。 例如, 以下代码包括日历的 ID、显示名称和帐户名:

```csharp
string[] calendarsProjection = {
    CalendarContract.Calendars.InterfaceConsts.Id,
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName
};
```

`Id`如果你`SimpleCursorAdapter`使用将数据绑定到 UI, 则必须包括, 这一点很重要, 因为我们很快就会看到。 使用内容 Uri 和投影后, 我们将实例化`CursorLoader`并`CursorLoader.LoadInBackground`调用方法, 以返回带有日历数据的光标, 如下所示:

```csharp
var loader = new CursorLoader(this, calendarsUri, calendarsProjection, null, null, null);
var cursor = (ICursor)loader.LoadInBackground();

```

此示例的 UI 包含`ListView`, 列表中的每一项都表示一个日历。 下面的 XML 显示了包含的`ListView`标记:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="fill_parent">
  <ListView
    android:id="@android:id/android:list"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" />
</LinearLayout>
```

此外, 我们还需要为每个列表项指定 UI, 并将其放在单独的 XML 文件中, 如下所示:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="fill_parent"
android:layout_height="wrap_content">
  <TextView android:id="@+id/calDisplayName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="16dip" />
  <TextView android:id="@+id/calAccountName"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:textSize="12dip" />
</LinearLayout>
```

从此时开始, 它只是将数据从游标绑定到 UI 的普通 Android 代码。 我们将使用`SimpleCursorAdapter` , 如下所示:

```csharp
string[] sourceColumns = {
    CalendarContract.Calendars.InterfaceConsts.CalendarDisplayName,
    CalendarContract.Calendars.InterfaceConsts.AccountName };

int[] targetResources = {
    Resource.Id.calDisplayName, Resource.Id.calAccountName };      

SimpleCursorAdapter adapter = new SimpleCursorAdapter (this,
    Resource.Layout.CalListItem, cursor, sourceColumns, targetResources);

ListAdapter = adapter;
```

在上面的代码中, 适配器使用`sourceColumns`数组中指定的列, 并将其写入到游标中每个日历条目的`targetResources`数组中的用户界面元素。 此处使用的活动是的子类`ListActivity`; 它`ListAdapter`包含我们将适配器设置到的属性。

下面是显示最终结果的屏幕截图, 其中显示`ListView`了日历信息:

[![CalendarDemo 在模拟器中运行, 显示两个日历条目](calendar-images/11-calendar.png)](calendar-images/11-calendar.png#lightbox)



## <a name="listing-calendar-events"></a>列出日历事件

接下来, 让我们看看如何枚举给定日历的事件。
根据上面的示例, 我们将在用户选择一个日历时显示事件列表。 因此, 我们需要处理前面代码中的项选择:

```csharp
ListView.ItemClick += (sender, e) => {
    int i = (e as ItemEventArgs).Position;

    cursor.MoveToPosition(i);
    int calId =
        cursor.GetInt (cursor.GetColumnIndex (calendarsProjection [0]));

    var showEvents = new Intent(this, typeof(EventListActivity));
    showEvents.PutExtra("calId", calId);
    StartActivity(showEvents);
};
```

在此代码中, 我们将创建一个意图, 以打开类型`EventListActivity`为的活动, 并在意图传递日历的 ID。 我们将需要 ID 来了解要查询事件的日历。 在的方法中, 可以从`Intent`检索 ID, 如下所示: `OnCreate` `EventListActivity`

```csharp
_calId = Intent.GetIntExtra ("calId", -1);
```

现在, 让我们查询此日历 ID 的事件。 用于查询事件的过程与我们之前为日历列表查询的方式类似, 只是我们要使用`CalendarContract.Events`类。 下面的代码创建查询以检索事件:

```csharp
var eventsUri = CalendarContract.Events.ContentUri;

string[] eventsProjection = {
    CalendarContract.Events.InterfaceConsts.Id,
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart
};

var loader = new CursorLoader(this, eventsUri, eventsProjection,
                   String.Format ("calendar_id={0}", _calId), null, "dtstart ASC");
var cursor = (ICursor)loader.LoadInBackground();
```

在此代码中, 我们首先`Uri` `CalendarContract.Events.ContentUri`从属性获取事件的内容。 然后, 在 eventsProjection 数组中指定要检索的事件列。
最后, `CursorLoader`使用此信息来实例化, 并调用加载程序`LoadInBackground`的方法, 以`Cursor`返回包含事件数据的。

若要在 UI 中显示事件数据, 可以使用标记和代码, 就像在显示日历列表之前所做的那样。 同样, 我们使用`SimpleCursorAdapter`将数据绑定`ListView`到, 如以下代码所示:

```csharp
string[] sourceColumns = {
    CalendarContract.Events.InterfaceConsts.Title,
    CalendarContract.Events.InterfaceConsts.Dtstart };

int[] targetResources = {
    Resource.Id.eventTitle,
    Resource.Id.eventStartDate };

var adapter = new SimpleCursorAdapter (this, Resource.Layout.EventListItem,
    cursor, sourceColumns, targetResources);

adapter.ViewBinder = new ViewBinder ();       
ListAdapter = adapter;
```

此代码与之前用于显示日历列表的代码之间的主要区别在于`ViewBinder`, 使用在行上设置的:

```csharp
adapter.ViewBinder = new ViewBinder ();
```

`ViewBinder`类允许我们进一步控制如何将值绑定到视图。 在这种情况下, 我们使用它将事件开始时间从毫秒转换为日期字符串, 如以下实现所示:

```csharp
class ViewBinder : Java.Lang.Object, SimpleCursorAdapter.IViewBinder
{    
    public bool SetViewValue (View view, Android.Database.ICursor cursor,
        int columnIndex)
    {
        if (columnIndex == 2) {
            long ms = cursor.GetLong (columnIndex);

            DateTime date = new DateTime (1970, 1, 1, 0, 0, 0,
                DateTimeKind.Utc).AddMilliseconds (ms).ToLocalTime ();

            TextView textView = (TextView)view;
            textView.Text = date.ToLongDateString ();

            return true;
        }
        return false;
    }    
}
```

这会显示事件列表, 如下所示:

[![显示三个日历事件的示例应用屏幕截图](calendar-images/12-events.png)](calendar-images/12-events.png#lightbox)



## <a name="adding-a-calendar-event"></a>添加日历事件

我们已了解如何读取日历数据。 现在, 让我们看看如何向日历添加事件。 为此, 请确保包括前面提到的`android.permission.WRITE_CALENDAR`权限。 若要将事件添加到日历, 我们将:

1. `ContentValues`创建实例。
1. 使用类中的`CalendarContract.Events.InterfaceConsts`键来`ContentValues`填充实例。
1. 设置事件开始和结束时间的时区。
1. `ContentResolver`使用将事件数据插入日历中。


下面的代码演示了这些步骤:

```csharp
ContentValues eventValues = new ContentValues ();

eventValues.Put (CalendarContract.Events.InterfaceConsts.CalendarId,
    _calId);
eventValues.Put (CalendarContract.Events.InterfaceConsts.Title,
    "Test Event from M4A");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Description,
    "This is an event created from Xamarin.Android");
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtstart,
    GetDateTimeMS (2011, 12, 15, 10, 0));
eventValues.Put (CalendarContract.Events.InterfaceConsts.Dtend,
    GetDateTimeMS (2011, 12, 15, 11, 0));

eventValues.Put(CalendarContract.Events.InterfaceConsts.EventTimezone,
    "UTC");
eventValues.Put(CalendarContract.Events.InterfaceConsts.EventEndTimezone,
    "UTC");

var uri = ContentResolver.Insert (CalendarContract.Events.ContentUri,
    eventValues);
```

请注意, 如果未设置时区, 则会引发类型`Java.Lang.IllegalArgumentException`为的异常。 由于从 epoch 开始, 事件时间值必须以毫秒为单位表示, `GetDateTimeMS`因此我们创建`EventListActivity`了一个方法 (在中为), 以将我们的日期规范转换为毫秒格式:

```csharp
long GetDateTimeMS (int yr, int month, int day, int hr, int min)
{
    Calendar c = Calendar.GetInstance (Java.Util.TimeZone.Default);

    c.Set (Java.Util.CalendarField.DayOfMonth, 15);
    c.Set (Java.Util.CalendarField.HourOfDay, hr);
    c.Set (Java.Util.CalendarField.Minute, min);
    c.Set (Java.Util.CalendarField.Month, Calendar.December);
    c.Set (Java.Util.CalendarField.Year, 2011);

    return c.TimeInMillis;
}
```

如果我们将一个按钮添加到事件列表 UI, 并在按钮的单击事件处理程序中运行上述代码, 则会将该事件添加到日历并在列表中更新, 如下所示:

[![带有日历事件的示例应用屏幕截图, 后跟 "添加示例事件" 按钮](calendar-images/13.png)](calendar-images/13.png#lightbox)

如果我们打开日历应用程序, 我们就会看到该事件也会写入其中:

[![显示选定日历事件的日历应用程序的屏幕截图](calendar-images/14.png)](calendar-images/14.png#lightbox)

正如您所看到的那样, Android 允许使用功能强大且易于访问的来检索和保存日历数据, 从而使应用程序无缝集成日历功能。


## <a name="related-links"></a>相关链接

- [日历演示 (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/calendardemo)
- [冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](https://developer.android.com/sdk/android-4.0.html)

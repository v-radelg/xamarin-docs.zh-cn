---
title: 使用 CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/25/2017
ms.openlocfilehash: 42b9bd528459d8ee941cc293372bf5662a493342
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827612"
---
# <a name="using-cursoradapters"></a>使用 CursorAdapters


## <a name="overview"></a>概述

Android 提供了适配器类，特别是要显示 SQLite 数据库查询中的数据：

 **SimpleCursorAdapter** – 类似到`ArrayAdapter`因为不子类化的情况下可以使用它。 只需在构造函数中提供所需的参数 （如游标和布局信息中），然后将分配给`ListView`。

 **CursorAdapter** – 基类继承时需要更好地控制通过数据绑定的值复制到布局控件 （例如，显示/隐藏控件或更改其属性）。

游标适配器提供高性能的方式来滚动查看存储在 SQLite 中的数据的长列表。 使用代码必须定义中的 SQL 查询`Cursor`对象，然后介绍如何创建和填充的视图的每个行。


## <a name="creating-an-sqlite-database"></a>创建一个 SQLite 数据库

若要演示游标适配器需要简单的 SQLite 数据库实现。 中的代码**SimpleCursorTableAdapter/VegetableDatabase.cs**包含代码和 SQL 来创建一个表并填充一些数据。
完整`VegetableDatabase`类如下所示：

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
   public static readonly string create_table_sql =
       "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
   public static readonly string DatabaseName = "vegetables.db";
   public static readonly int DatabaseVersion = 1;
   public VegetableDatabase(Context context) : base(context, DatabaseName, null, DatabaseVersion) { }
   public override void OnCreate(SQLiteDatabase db)
   {
       db.ExecSQL(create_table_sql);
       // seed with data
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Vegetables')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Fruits')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Flower Buds')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Legumes')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Bulbs')");
       db.ExecSQL("INSERT INTO vegetables (name) VALUES ('Tubers')");
   }
   public override void OnUpgrade(SQLiteDatabase db, int oldVersion, int newVersion)
   {   // not required until second version :)
       throw new NotImplementedException();
   }
}
```

`VegetableDatabase`中进行实例化类`OnCreate`方法的`HomeScreen`活动。 `SQLiteOpenHelper`基类管理数据库文件的安装程序并确保在 SQL 其`OnCreate`方法只运行一次。 在以下两个示例中为使用此类`SimpleCursorAdapter`和`CursorAdapter`。

游标查询*必须*有一个整数列`_id`为`CursorAdapter`工作。 如果基础表不具有名为的整数列`_id`然后使用列别名中的另一个唯一整数`RawQuery`构成光标。 请参阅[Android 文档](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)有关进一步信息。


### <a name="creating-the-cursor"></a>创建游标

这些示例使用`RawQuery`若要打开 SQL 查询到`Cursor`对象。 返回从游标的列列表定义可用于在光标适配器中显示的数据列。 创建的数据库中的代码**SimpleCursorTableAdapter/HomeScreen.cs** `OnCreate`方法如下所示：

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

调用的任何代码`StartManagingCursor`还应调用`StopManagingCursor`。 这些示例使用`OnCreate`若要开始，和`OnDestroy`以关闭游标。 `OnDestroy`方法包含此代码：

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

一旦应用程序都有一个 SQLite 数据库可用并已创建的光标对象，因为所示，它可以利用`SimpleCursorAdapter`或其子`CusorAdapter`以显示行`ListView`。


## <a name="using-simplecursoradapter"></a>使用 SimpleCursorAdapter

`SimpleCursorAdapter` 类似于`ArrayAdapter`，但专用于与 SQLite 一起使用。 其不需要子类化 – 只需创建对象时将设置一些简单的参数，然后将其分配给`ListView`的`Adapter`属性。

SimpleCursorAdapter 构造函数的参数包括：

 **上下文**– 对包含的活动的引用。

 **布局**– 要使用行视图的资源 ID。

 **要求 ICursor** – 其中包含要显示的数据的 SQLite 查询的游标。

 **从**字符串数组-数组与游标中的列的名称相对应的字符串。

 **到**整数数组-数组的对应行布局中控件的布局 Id。 中指定的列的值`from`数组将绑定到相同的索引处的此数组中指定了 ControlID。

`from`和`to`数组必须具有相同的条目数，因为其形成从数据源向布局控件的映射视图中。

**SimpleCursorTableAdapter/HomeScreen.cs**向上示例代码电线`SimpleCursorAdapter`如下所示：

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` 是快速简单地显示在 SQLite 数据`ListView`。 主要限制是，它只能绑定列的值以显示控件，它不允许你更改 （例如，显示/隐藏控件或更改属性） 的行布局的其他方面。


## <a name="subclassing-cursoradapter"></a>子类化 CursorAdapter

一个`CursorAdapter`子类具有相同的性能优点为`SimpleCursorAdapter`显示数据从 SQLite，但它还提供完全控制创建和每个行视图的布局。 `CursorAdapter`实现有很大差异子类化`BaseAdapter`因为它不重写`GetView`， `GetItemId`，`Count`或`this[]`索引器。

提供一个有效的 SQLite 数据库，您只需重写两个方法来创建`CursorAdapter`子类：

- **BindView** – 给定一个视图，将其更新为显示提供的游标中的数据。

- **NewView** – 时调用`ListView`需要新的视图来显示。 `CursorAdapter`将会负责的回收视图 (不同于`GetView`常规适配器上的方法)。

前面的示例中的适配器子类具有方法返回的行数以及如何检索当前项 –`CursorAdapter`不需要这些方法，因为该信息可以从此类本身的光标。 通过将拆分的创建和填充到这两种方法，每个视图的`CursorAdapter`强制执行视图重新使用。 这是与此相反到常规适配器，则可以忽略`convertView`参数的`BaseAdapter.GetView`方法。


### <a name="implementing-the-cursoradapter"></a>实现 CursorAdapter

中的代码**CursorTableAdapter/HomeScreenCursorAdapter.cs**包含`CursorAdapter`子类。 它存储传入构造函数，以便它可以访问的上下文引用`LayoutInflater`在`NewView`方法。 完整的类如下所示：

```csharp
public class HomeScreenCursorAdapter : CursorAdapter {
   Activity context;
   public HomeScreenCursorAdapter(Activity context, ICursor c)
       : base(context, c)
   {
       this.context = context;
   }
   public override void BindView(View view, Context context, ICursor cursor)
   {
       var textView = view.FindViewById<TextView>(Android.Resource.Id.Text1);
       textView.Text = cursor.GetString(1); // 'name' is column 1 in the cursor query
   }
   public override View NewView(Context context, ICursor cursor, ViewGroup parent)
   {
       return this.context.LayoutInflater.Inflate(Android.Resource.Layout.SimpleListItem1, parent, false);
   }
}
```


### <a name="assigning-the-cursoradapter"></a>分配 CursorAdapter

在中`Activity`将显示`ListView`，创建该游标和`CursorAdapter`然后将其分配到列表视图。

执行此操作中的代码**CursorTableAdapter/HomeScreen.cs** `OnCreate`方法如下所示：

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy`方法包含`StopManagingCursor`前面所述的方法调用。



## <a name="related-links"></a>相关链接

- [SimpleCursorTableAdapter (sample)](https://developer.xamarin.com/samples/monodroid/SimpleCursorTableAdapter/)
- [CursorTableAdapter （示例）](https://developer.xamarin.com/samples/monodroid/CursorTableAdapter/)

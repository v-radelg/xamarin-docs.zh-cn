---
title: 使用 CursorAdapters
ms.prod: xamarin
ms.assetid: 60DE467E-A5DA-4420-52E5-D86AD1678FE6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/25/2017
ms.openlocfilehash: d0b5845036ab2981a4aa06d2a01ed6b13d094bef
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028915"
---
# <a name="using-cursoradapters-with-xamarinandroid"></a>将 CursorAdapters 与 Xamarin 配合使用

Android 提供专用于显示 SQLite 数据库查询数据的适配器类：

 **SimpleCursorAdapter** –与 `ArrayAdapter` 类似，因为它可以在没有子类的情况下使用。 只需在构造函数中提供所需的参数（如游标和布局信息），然后将分配给 `ListView`。

 **CursorAdapter** –当需要更好地控制将数据值绑定到布局控件（例如，隐藏/显示控件或更改其属性）时，可以从中继承的基类。

游标适配器提供一种高性能的方法来滚动查看 SQLite 中存储的长数据列表。 使用代码必须在 `Cursor` 对象中定义一个 SQL 查询，然后说明如何创建和填充每个行的视图。

## <a name="creating-an-sqlite-database"></a>创建 SQLite 数据库

为了演示游标适配器，需要简单的 SQLite 数据库实现。 **SimpleCursorTableAdapter/VegetableDatabase**中的代码包含用于创建表的代码和 SQL，并为其填充一些数据。
下面显示了完整的 `VegetableDatabase` 类：

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

`VegetableDatabase` 类将在 `HomeScreen` 活动的 `OnCreate` 方法中实例化。 `SQLiteOpenHelper` 基类管理数据库文件的设置，并确保其 `OnCreate` 方法中的 SQL 只运行一次。 此类用于 `SimpleCursorAdapter` 和 `CursorAdapter`的以下两个示例。

若要运行 `CursorAdapter`，游标查询*必须*`_id` 整数列。 如果基础表不具有名为 `_id` 的整数列，则使用构成游标的 `RawQuery` 中的另一个唯一整数的列别名。 有关详细信息，请参阅[Android 文档](xref:Android.Widget.CursorAdapter)。

### <a name="creating-the-cursor"></a>创建游标

这些示例使用 `RawQuery` 将 SQL 查询转换为 `Cursor` 的对象。 从游标返回的列列表定义了可在游标适配器中显示的数据列。 在**SimpleCursorTableAdapter/HomeScreen** `OnCreate` 方法中创建数据库的代码如下所示：

```csharp
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null); // cursor query
StartManagingCursor(cursor);
// use either SimpleCursorAdapter or CursorAdapter subclass here!
```

调用 `StartManagingCursor` 的任何代码还应调用 `StopManagingCursor`。 这些示例使用 `OnCreate` 开始，并 `OnDestroy` 关闭游标。 `OnDestroy` 方法包含以下代码：

```csharp
StopManagingCursor(cursor);
cursor.Close();
```

当应用程序可用并创建了一个 cursor 对象（如所示）后，它可以利用 `CusorAdapter` 的 `SimpleCursorAdapter` 或子类来显示 `ListView`中的行。

## <a name="using-simplecursoradapter"></a>使用 SimpleCursorAdapter

`SimpleCursorAdapter` 类似于 `ArrayAdapter`，但专用于 SQLite。 它不需要创建子类–只需在创建对象时设置一些简单参数，然后将其分配给 `ListView`的 `Adapter` 属性。

SimpleCursorAdapter 构造函数的参数是：

 **Context** –对包含活动的引用。

 **Layout** -要使用的行视图的资源 ID。

 **ICursor** –包含要显示的数据的 SQLite 查询的游标。

 **From** string array-与游标中列的名称相对应的字符串数组。

 **到**整数数组–与行布局中的控件相对应的布局 id 的数组。 在 `from` 数组中指定的列的值将绑定到在同一索引处的此数组中指定的 ControlID。

`from` 和 `to` 数组必须具有相同的项数，因为它们构成了从数据源到视图中布局控件的映射。

**SimpleCursorTableAdapter/HomeScreen**示例代码将如下所示的 `SimpleCursorAdapter`：

```csharp
// which columns map to which layout controls
string[] fromColumns = new string[] {"name"};
int[] toControlIDs = new int[] {Android.Resource.Id.Text1};
// use a SimpleCursorAdapter
listView.Adapter = new SimpleCursorAdapter (this, Android.Resource.Layout.SimpleListItem1, cursor,
       fromColumns,
       toControlIDs);
```

`SimpleCursorAdapter` 是一种在 `ListView`中快速显示 SQLite 数据的简单方法。 主要的限制是，它只能将列值绑定到显示控件，它不允许您更改行布局的其他方面（例如，显示/隐藏控件或更改属性）。

## <a name="subclassing-cursoradapter"></a>子类 CursorAdapter

`CursorAdapter` 子类与从 SQLite 显示数据的 `SimpleCursorAdapter` 具有相同的性能优势，但它还可让你完全控制每个行视图的创建和布局。 `CursorAdapter` 实现与子类 `BaseAdapter` 的实现非常不同，因为它不重写 `GetView`、`GetItemId`、`Count` 或 `this[]` 索引器。

给定工作的 SQLite 数据库后，只需重写两种方法即可创建 `CursorAdapter` 子类：

- **BindView** –如果提供了一个视图，请更新它以显示所提供的游标中的数据。

- **NewView** –在 `ListView` 需要显示新视图时调用。 `CursorAdapter` 将负责回收视图（不同于常规适配器上的 `GetView` 方法）。

前面的示例中的适配器子类具有返回行数和检索当前项的方法– `CursorAdapter` 不需要这些方法，因为该信息可从游标本身搜集。 通过将每个视图的创建和填充拆分为这两种方法，`CursorAdapter` 强制使用视图。 这与常规适配器相反，在这种情况下，可以忽略 `BaseAdapter.GetView` 方法的 `convertView` 参数。

### <a name="implementing-the-cursoradapter"></a>实现 CursorAdapter

**CursorTableAdapter/HomeScreenCursorAdapter**中的代码包含 `CursorAdapter` 子类。 它存储传入构造函数的上下文引用，以便它可以访问 `NewView` 方法中的 `LayoutInflater`。 完整的类如下所示：

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

在将显示 `ListView`的 `Activity` 中，创建光标 `CursorAdapter`，然后将其分配到列表视图。

在**CursorTableAdapter/HomeScreen** `OnCreate` 方法中执行此操作的代码如下所示：

```csharp
// create the cursor
vdb = new VegetableDatabase(this);
cursor = vdb.ReadableDatabase.RawQuery("SELECT * FROM vegetables", null);
StartManagingCursor(cursor);

// create the CursorAdapter
listView.Adapter = (IListAdapter)new HomeScreenCursorAdapter(this, cursor, false);
```

`OnDestroy` 方法包含前面所述的 `StopManagingCursor` 方法调用。

## <a name="related-links"></a>相关链接

- [SimpleCursorTableAdapter （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/simplecursortableadapter)
- [CursorTableAdapter （示例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/cursortableadapter)

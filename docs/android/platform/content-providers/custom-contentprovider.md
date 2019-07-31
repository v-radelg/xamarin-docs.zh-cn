---
title: 创建自定义 ContentProvider
description: 上一节演示了如何使用内置 ContentProvider 实现中的数据。 本部分将介绍如何生成自定义 ContentProvider 并使用其数据。
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: e16aa1b96749047554b4f8e6887791d8ed4ff63b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643696"
---
# <a name="creating-a-custom-contentprovider"></a>创建自定义 ContentProvider

_上一节演示了如何使用内置 ContentProvider 实现中的数据。本部分将介绍如何生成自定义 ContentProvider 并使用其数据。_

## <a name="about-contentproviders"></a>关于 Contentprovider

内容提供程序类必须继承自`ContentProvider`。 它应包含用于响应查询的内部数据存储区, 它应将 Uri 和 MIME 类型作为常量公开, 以帮助使用代码对数据进行有效的请求。

### <a name="uri-authority"></a>URI (颁发机构)

`ContentProviders`在 Android 中使用 Uri 进行访问。 公开的`ContentProvider`应用程序将在其**androidmanifest.xml**文件中将响应的 uri 设置为。 安装应用程序时, 将注册这些 Uri, 以便其他应用程序可以访问它们。

在 Mono for Android 中, 内容提供程序类应有一个`[ContentProvider]`特性来指定应添加到**androidmanifest.xml**中的 uri。


### <a name="mime-type"></a>Mime 类型

MIME 类型的典型格式由两部分组成。 Android `ContentProviders`通常将以下两个字符串用于 MIME 类型的第一部分:

1. `vnd.android.cursor.item`若要表示单个行, 请在`ContentResolver.CursorItemBaseType`代码中使用常量。 &ndash;

1. `vnd.android.cursor.dir`对于多行, 请在`ContentResolver.CursorDirBaseType`代码中使用常量。 &ndash;

MIME 类型的第二部分是特定于你的应用程序的, 并且应使用带有`vnd.`前缀的反向 DNS 标准。 示例代码使用`vnd.com.xamarin.sample.Vegetables`。


### <a name="data-model-metadata"></a>数据模型元数据

使用应用程序需要构造 Uri 查询才能访问不同类型的数据。 可以扩展基 Uri 以引用特定的数据表, 还可以包含用于筛选结果的参数。 还必须声明用于显示数据的结果游标所使用的列和子句。

为了确保仅构造有效的 Uri 查询, 可以将有效的字符串作为常量值提供。 这样就可以更轻松地访问`ContentProvider` , 因为它使值可以通过代码完成来发现, 并防止字符串中出现键入错误。

在上面的示例中`android.provider.ContactsContract` , 类公开了联系人数据的元数据。 对于我们的`ContentProvider`自定义, 我们将只公开类本身的常量。


## <a name="implementation"></a>实现

创建和使用自定义`ContentProvider`的步骤有三个:

1. **创建数据库类**&ndash;实现。`SQLiteOpenHelper`

2. **使用数据库实例、公开为常量值的元数据和用于访问数据的方法创建类实现。`ContentProvider`**  &ndash; `ContentProvider`

3. **通过`ContentProvider`其 uri** &ndash;使用来`CursorAdapter` 填充,通过其`ContentProvider`uri 进行访问。

如前文所述, `ContentProviders`除了定义外, 还可以从应用程序使用。 在此示例中, 数据在同一应用程序中使用, 但请记住, 其他应用程序也可以访问它, 只要它们知道有关架构的 Uri 和信息 (通常作为常量值公开)。


## <a name="create-a-database"></a>创建数据库

大多数`ContentProvider`实现将基于`SQLite`数据库。 **SimpleContentProvider/VegetableDatabase**中的示例数据库代码创建一个非常简单的两列数据库, 如下所示:

```csharp
class VegetableDatabase  : SQLiteOpenHelper {
  const string create_table_sql =
    "CREATE TABLE [vegetables] ([_id] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL UNIQUE, [name] TEXT NOT NULL UNIQUE)";
  const string DatabaseName = "vegetables.db";
  const int DatabaseVersion = 1;

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
  {
    throw new NotImplementedException();
  }
}
```

数据库实现`ContentProvider`本身不需要使用公开任何特殊注意事项, 但是, 如果你想要将`ContentProvider's`数据绑定到`ListView`控件, 则名为`_id`的唯一整数列必须属于结果集。 有关使用`ListView`控件的详细信息, 请参阅[listview 和适配器](~/android/user-interface/layouts/list-view/index.md)文档。


## <a name="create-the-contentprovider"></a>创建 ContentProvider

本部分的其余部分提供了有关如何生成**SimpleContentProvider/VegetableProvider**示例类的分步说明。


### <a name="initialize-the-database"></a>初始化数据库

第一步是为子类`ContentProvider`添加并添加它将使用的数据库。

```csharp
public class VegetableProvider : ContentProvider 
{
    VegetableDatabase vegeDB;
    public override bool OnCreate()
    {
       vegeDB = new VegetableDatabase(Context);
        return true;
    }
}
```

其余代码将形成实际的内容提供程序实现, 该实现允许发现和查询数据。



## <a name="add-metadata-for-consumers"></a>添加使用者的元数据

有四种不同类型的元数据将在`ContentProvider`类上公开。 只有权限是必需的, 其余部分则按约定完成。

- **颁发机构**必须将  属性添加到类, 以便在安装应用程序时将其注册到 Android。 `ContentProvider` &ndash;

- **Uri**&ndash; 作为常数公开,以便在`CONTENT_URI`代码中轻松使用。 它应与颁发机构匹配, 但包含方案和基路径。

- **MIME 类型**&ndash;结果列表和单个结果被视为不同的内容类型, 因此我们定义了两种 MIME 类型来表示它们。

- **InterfaceConsts**&ndash;为每个数据列名称提供一个常量值, 以便使用代码可以轻松发现和引用这些值, 而不会导致录入错误。

此代码演示如何实现上述每个项, 并从上一步添加到数据库定义:

```csharp
[ContentProvider(new string[] { CursorTableAdapter.VegetableProvider.AUTHORITY })]
public class VegetableProvider : ContentProvider 
{
   public const string AUTHORITY = "com.xamarin.sample.VegetableProvider";
   static string BASE_PATH = "vegetables";
   public static readonly Android.Net.Uri CONTENT_URI = Android.Net.Uri.Parse("content://" + AUTHORITY + "/" + BASE_PATH);
   // MIME types used for getting a list, or a single vegetable
   public const string VEGETABLES_MIME_TYPE = ContentResolver.CursorDirBaseType + "/vnd.com.xamarin.sample.Vegetables";
   public const string VEGETABLE_MIME_TYPE = ContentResolver.CursorItemBaseType + "/vnd.com.xamarin.sample.Vegetables";
   // Column names
   public static class InterfaceConsts {
       public const string Id = "_id";
       public const string Name = "name";
   }
   VegetableDatabase vegeDB;
   public override bool OnCreate()
   {
       vegeDB = new VegetableDatabase(Context);
       return true;
   }
}
```


## <a name="implement-the-uri-parsing-helper"></a>实现 URI 分析帮助器

由于使用的代码使用 uri 来发出的请求`ContentProvider`, 因此我们需要能够分析这些请求, 以确定要返回的数据。 类可以在用`ContentProvider`支持的 uri 模式初始化后, 帮助分析 uri。 `UriMatcher`

示例`UriMatcher`中的将用两个 uri 初始化:

1. *"VegetableProvider/蔬菜"* &ndash;请求返回最完整的蔬菜列表。

2. *"VegetableProvider\#/蔬菜/"* &ndash; , 其中\# `_id`是数字参数 (数据库中的行的) 的占位符。 星号占位符 ("\*") 也可用于匹配文本参数。

在代码中, 我们使用常量来引用元数据值, 如颁发机构和\_基路径。 返回代码将在进行 Uri 分析的方法中使用, 以确定要返回的数据。

```csharp
const int GET_ALL = 0; // return code when list of Vegetables requested
const int GET_ONE = 1; // return code when a single Vegetable is requested by ID
static UriMatcher uriMatcher = BuildUriMatcher();
static UriMatcher BuildUriMatcher()
{
  var matcher = new UriMatcher(UriMatcher.NoMatch);
  // Uris to match, and the code to return when matched
  matcher.AddURI(AUTHORITY, BASE_PATH, GET_ALL); // all vegetables
  matcher.AddURI(AUTHORITY, BASE_PATH + "/#", GET_ONE); // specific vegetable by numeric ID
  return matcher;
}
```

此代码全都是类的`ContentProvider`私有代码。 有关详细信息, 请参阅[Google 的 UriMatcher 文档](xref:Android.Content.UriMatcher)。


## <a name="implement-the-querymethod"></a>实现 QueryMethod

最简单`ContentProvider`的实现方法`Query`是方法。 以下实现使用`UriMatcher` `uri`分析参数并调用正确的数据库方法。 如果包含 ID 参数, 则该整数将在数据库查询中进行`LastPathSegment`分析 (使用) 并使用。 `uri`

```csharp
public override Android.Database.ICursor Query(Android.Net.Uri uri, string[] projection, string selection, string[] selectionArgs, string sortOrder)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return GetFromDatabase();
  case GET_ONE:
    var id = uri.LastPathSegment;
    return GetFromDatabase(id); // the ID is the last part of the Uri
  default:
    throw new Java.Lang.IllegalArgumentException("Unknown Uri: " + uri);
  }
}
Android.Database.ICursor GetFromDatabase()
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables", null);
}
Android.Database.ICursor GetFromDatabase(string id)
{
  return vegeDB.ReadableDatabase.RawQuery("SELECT _id, name FROM vegetables WHERE _id = " + id, null);
}
```

还必须重写方法。`GetType` 可以调用此方法来确定将为给定 Uri 返回的内容类型。
这可能会告诉使用方应用程序如何处理该数据。

```csharp
public override String GetType(Android.Net.Uri uri)
{
  switch (uriMatcher.Match(uri)) {
  case GET_ALL:
    return VEGETABLES_MIME_TYPE; // list
  case GET_ONE:
    return VEGETABLE_MIME_TYPE; // single item
  default:
    throw new Java.Lang.IllegalArgumentExceptoin ("Unknown Uri: " + uri);
   }
}
```


## <a name="implement-the-other-overrides"></a>实现其他替代

简单示例不允许对数据进行编辑或删除操作, 但必须实现插入、更新和删除方法, 以便在不使用实现的情况下添加它们:

```csharp
public override int Delete(Android.Net.Uri uri, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override Android.Net.Uri Insert(Android.Net.Uri uri, ContentValues values)
{
  throw new Java.Lang.UnsupportedOperationException();
}
public override int Update(Android.Net.Uri uri, ContentValues values, string selection, string[] selectionArgs)
{
  throw new Java.Lang.UnsupportedOperationException();
}
```

这就完成了`ContentProvider`基本实现。 安装应用程序后, 该应用程序公开的数据将可用于应用程序, 但也可用于知道 Uri 引用该应用程序的任何其他应用程序。


## <a name="access-the-contentprovider"></a>访问 ContentProvider

`VegetableProvider`实现后, 对其进行访问的方式与本文档开始时的联系人提供程序相同: 使用指定的 Uri 获取游标, 然后使用适配器访问数据。


## <a name="bind-a-listview-to-a-contentprovider"></a>将 ListView 绑定到 ContentProvider

若要`ListView`用数据填充, 我们使用与不筛选的蔬菜列表相对应的 Uri。 在代码中, 我们使用我们知道`VegetableProvider.CONTENT_URI`解析为`com.xamarin.sample.vegetableprovider/vegetables`的常量值。 我们`VegetableProvider.Query`的实现将返回一个可以绑定到的`ListView`游标。

中`SimpleContentProvider/HomeScreen.cs`的代码演示了如何轻松地`ContentProvider`从中显示数据:

```csharp
listView = FindViewById<ListView>(Resource.Id.List);
string[] projection = new string[] { VegetableProvider.InterfaceConsts.Id, VegetableProvider.InterfaceConsts.Name} ;
string[] fromColumns = new string[] { VegetableProvider.InterfaceConsts.Name };
int[] toControlIds = new int[] { Android.Resource.Id.Text1 };

// CursorLoader introduced in Honeycomb (3.0, API_11)
var loader = new CursorLoader(this,
   VegetableProvider.CONTENT_URI, projection, null, null, null);
cursor = (ICursor)loader.LoadInBackground();

// Create a SimpleCursorAdapter
adapter = new SimpleCursorAdapter(this, Android.Resource.Layout.SimpleListItem1, cursor, fromColumns, toControlIds);
listView.Adapter = adapter;
```

产生的应用程序如下所示:

[![列出蔬菜、水果、花卉 Buds、Legumes、电灯泡、Tubers 的应用屏幕截图](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>从 ContentProvider 检索单个项

使用的应用程序可能还需要访问单个数据行, 这可以通过构造引用特定行的不同 Uri 来完成 (例如)。

通过`ContentResolver`构建具有所需`Id`的 Uri, 直接使用来访问单个项。

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

完整的方法如下所示:

```csharp
protected void OnListItemClick(object sender, AdapterView.ItemClickEventArgs e)
{
  var id = e.Id;
  string[] projection = new string[] { "name" };
  var uri = Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
  ICursor vegeCursor = ContentResolver.Query(uri, projection, null, new string[] { id.ToString() }, null);
  string text = "";
  if (vegeCursor.MoveToFirst()) {
    text = vegeCursor.GetInt(0) + " " + vegeCursor.GetString(1);
    Android.Widget.Toast.MakeText(this, text, Android.Widget.ToastLength.Short).Show();
  }
  vegeCursor.Close();
}
```


## <a name="related-links"></a>相关链接

- [SimpleContentProvider (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)

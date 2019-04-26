---
title: 创建自定义 ContentProvider
description: 上一节演示了如何使用内置的 ContentProvider 实现中的数据。 本部分将介绍如何生成自定义 ContentProvider，然后使用其数据。
ms.prod: xamarin
ms.assetid: 36742B59-607E-070E-5D0E-B9C18917D3F4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/07/2018
ms.openlocfilehash: da8aacac1f282fefb6b8d0e84cae168cf3a7148b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953356"
---
# <a name="creating-a-custom-contentprovider"></a>创建自定义 ContentProvider

_上一节演示了如何使用内置的 ContentProvider 实现中的数据。本部分将介绍如何生成自定义 ContentProvider，然后使用其数据。_

## <a name="about-contentproviders"></a>有关 Contentprovider

内容提供程序类必须继承`ContentProvider`。 它应包含用于对查询进行响应的内部数据存储和常量以帮助使用代码进行的数据的有效请求，它应公开的 Uri 和 MIME 类型。

### <a name="uri-authority"></a>URI （颁发机构）

`ContentProviders` 使用 Uri 在 Android 中访问的。 公开的应用程序`ContentProvider`设置它将对在做出响应的 Uri 及其**AndroidManifest.xml**文件。 安装应用程序时，这些 Uri 注册，以便其他应用程序可以访问它们。

适用于 Android 的 Mono 中的内容提供程序类应有`[ContentProvider]`属性指定的 Uri （或 Uri），应将其添加到**AndroidManifest.xml**。


### <a name="mime-type"></a>Mime 类型

MIME 类型的典型格式由两部分组成。 Android`ContentProviders`通常使用这两个字符串的 MIME 类型的第一部分：

1. `vnd.android.cursor.item` &ndash; 若要表示的单个行，使用`ContentResolver.CursorItemBaseType`常量在代码中。

1. `vnd.android.cursor.dir` &ndash; 对于多个行，使用`ContentResolver.CursorDirBaseType`常量在代码中。

MIME 类型的第二部分是特定于应用程序，并应使用反向 DNS 标准的与`vnd.`前缀。 示例代码使用`vnd.com.xamarin.sample.Vegetables`。


### <a name="data-model-metadata"></a>数据模型元数据

使用应用程序需要构造 Uri 查询访问不同类型的数据。 基本 Uri 可以扩展来引用特定表的数据，并且还包括参数以筛选结果。 此外必须声明的列和子句与生成的游标用于显示数据。

若要确保仅有效的 Uri 查询构造，它是惯用提供作为常数值的有效字符串。 这使得能够更轻松地访问`ContentProvider`因为它会使值可通过代码完成功能发现并防止在字符串中的拼写错误。

在前面的示例`android.provider.ContactsContract`类公开的元数据的联系人数据。 对于我们自定义`ContentProvider`我们只需将公开为类本身的常量。


## <a name="implementation"></a>实现

有三个步骤创建和使用自定义到`ContentProvider`:

1. **创建一个数据库类**&ndash;实现`SQLiteOpenHelper`。

2. **创建`ContentProvider`类**&ndash;实现`ContentProvider`与数据库的实例，元数据公开为常量值和方法来访问数据。

3. **访问`ContentProvider`通过其 Uri** &ndash;填充`CursorAdapter`使用`ContentProvider`，可通过其 Uri。

前面所述，`ContentProviders`可以从应用程序而非定义位置使用。 在此示例中使用的数据在同一应用程序，但请记住，其他应用程序还可以访问它，只要他们知道的 Uri 和架构 （这通常会公开为常量值） 的信息。


## <a name="create-a-database"></a>创建数据库

大多数`ContentProvider`实现将基于`SQLite`数据库。 数据库中的代码示例**SimpleContentProvider/VegetableDatabase.cs**创建一个非常简单的两个列数据库，如所示：

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

数据库实现本身不需要任何特殊的注意事项，以通过公开`ContentProvider`，但是如果你想要将绑定`ContentProvider's`数据到`ListView`然后控件名为的唯一整数列`_id`必须属于结果集。 请参阅[Listview 和适配器](~/android/user-interface/layouts/list-view/index.md)使用的更多详细信息的文档`ListView`控件。


## <a name="create-the-contentprovider"></a>创建 ContentProvider

本部分的其余部分介绍有关如何分步说明**SimpleContentProvider/VegetableProvider.cs**生成的示例类。


### <a name="initialize-the-database"></a>初始化数据库

第一步是为子类`ContentProvider`并添加它将使用的数据库。

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

代码的其余部分将窗体可用于发现和查询数据的实际内容的提供程序实现。



## <a name="add-metadata-for-consumers"></a>添加元数据的使用者

有四种不同类型的元数据，我们将在公开`ContentProvider`类。 颁发机构是必需的仅由约定完成其余部分。

- **颁发机构** &ndash; `ContentProvider`特性*必须*添加到类，以便它是否已注册的 Android 安装应用程序。

- **Uri** &ndash; `CONTENT_URI` ，这样就可以方便使用在代码中公开为常量。 它应匹配颁发机构，但包含的方案和基路径。

- **MIME 类型**&ndash;结果和单个结果的列表被视为不同的内容类型，因此我们定义两个 MIME 类型来表示它们。

- **InterfaceConsts** &ndash;为每个数据列名称，提供常量值，以便使用代码可以轻松地发现并不会有键入错误的风险的情况下引用它们。

此代码显示了其中每一项的实现方式，将从上一步添加到数据库定义：

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


## <a name="implement-the-uri-parsing-helper"></a>实现 URI 分析帮助程序

因为使用的代码使用 Uri 发出请求的`ContentProvider`，我们需要能够分析这些请求以确定要返回的数据。 `UriMatcher`类可帮助分析 Uri 后已初始化，, Uri 模式的`ContentProvider`支持。

`UriMatcher`在示例中用于初始化两个 Uri:

1. *"com.xamarin.sample.VegetableProvider/vegetables"* &ndash;请求，以便返回蔬菜的完整列表。

2. *"com.xamarin.sample.VegetableProvider/vegetables/\#"* &ndash;其中\#是数值参数的占位符 (`_id`的数据库中的行)。 星号占位符 ("\*") 也可用于匹配一个文本参数。

在代码中我们使用常量来指代的元数据值，如颁发机构和基本\_路径。 将执行分析，以确定要返回的数据的 Uri 的方法中使用返回代码。

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

此代码是所有私有的`ContentProvider`类。 请参阅[Google UriMatcher 文档](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)有关进一步信息。


## <a name="implement-the-querymethod"></a>实现 QueryMethod

最简单`ContentProvider`实现的方法是`Query`方法。 使用下面的实现`UriMatcher`分析`uri`参数并调用正确的数据库的方法。 如果`uri`包含一个 ID 参数，则将整数解析出 (使用`LastPathSegment`) 和数据库查询中使用。

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

`GetType`必须也重写方法。 可能会调用此方法，以确定将为给定的 Uri 返回的内容类型。
这可能会告知使用方应用程序如何处理该数据。

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

我们的简单示例不允许进行编辑或删除数据，但必须实现的 Insert、 Update 和 Delete 方法因此将它们添加不包含实现：

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

完成基本`ContentProvider`实现。 一旦已安装应用程序，它公开的数据可同时在应用程序内，而且还知道要对其进行引用的 Uri 的任何其他应用程序。


## <a name="access-the-contentprovider"></a>访问 ContentProvider

一次`VegetableProvider`已实现，对其进行访问可以通过相同方法在本文档开头的联系人提供程序为： 获取使用指定的 Uri 游标，然后使用适配器访问的数据。


## <a name="bind-a-listview-to-a-contentprovider"></a>将 ListView 绑定到 ContentProvider

若要填充`ListView`数据中，我们将使用未筛选的蔬菜列表相对应的 Uri。 我们在代码中使用的常量值`VegetableProvider.CONTENT_URI`，我们知道它解析为`com.xamarin.sample.vegetableprovider/vegetables`。 我们`VegetableProvider.Query`实现将返回一个游标，然后绑定到`ListView`。

中的代码`SimpleContentProvider/HomeScreen.cs`显示了以显示来自数据是简单`ContentProvider`:

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

生成的应用程序如下所示：

[![列出蔬菜、 水果、 花卉 Buds、 Legumes、 Bulbs、 Tubers 应用的屏幕截图](custom-contentprovider-images/api11-contentprovider2.png)](custom-contentprovider-images/api11-contentprovider2.png#lightbox)



## <a name="retrieve-a-single-item-from-a-contentprovider"></a>检索一个项从 ContentProvider

使用方应用程序可能还想要访问的数据，这可以通过构造一个不同的 Uri （例如） 是指特定行的单个行。

使用`ContentResolver`直接以访问单个项，通过使用所需的 Uri 构建`Id`。

```csharp
Uri.WithAppendedPath(VegetableProvider.CONTENT_URI, id.ToString());
```

完整的方法如下所示：

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

- [SimpleContentProvider （示例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)

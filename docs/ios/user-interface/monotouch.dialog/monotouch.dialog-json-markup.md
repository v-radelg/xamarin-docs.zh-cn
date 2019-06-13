---
title: MonoTouch.Dialog Json 标记
description: 本文档介绍可用于生成 Xamarin.iOS 用户界面使用 MonoTouch.Dialog JSON 语法。
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: lobrien
ms.author: laobri
ms.openlocfilehash: bc6842871a2f59c9851e90adbc6609707a7ecd1f
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039654"
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json 标记

此页介绍了接受的 MonoTouch.Dialog Json 标记[JsonElement](xref:MonoTouch.Dialog.JsonElement)

让我们从一个示例开始。 下面是一个完整的 Json 文件，可以将它传递到 JsonElement。

```json
{     
    "title": "Json Sample",
    "sections": [ 
        {
            "header": "Booleans",
            "footer": "Slider or image-based",
            "id": "first-section",
            "elements": [
                { 
                    "type": "boolean",
                    "caption": "Demo of a Boolean",
                    "value": true
                }, {
                    "type": "boolean",
                    "caption": "Boolean using images",
                    "value": false,
                    "on": "favorite.png",
                    "off": "~/favorited.png"
                }, {
                    "type": "root",
                    "title": "Tap for nested controller",
                    "sections": [
                        {
                            "header": "Nested view!",
                            "elements": [
                                {
                                    "type": "boolean",
                                    "caption": "Just a boolean",
                                    "id": "the-boolean",
                                    "value": false
                                }, {
                                    "type": "string",
                                    "caption": "Welcome to the nested controller"
                                }
                            ]
                        }
                    ]
                }
            ]
        }, {
            "header": "Entries",
            "elements" : [
                {
                    "type": "entry",
                    "caption": "Username",
                    "value": "",
                    "placeholder": "Your account username"
                }
            ]
        }
    ]
}
```

上述标记将生成以下 UI:

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "由给定标记创建的用户界面")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

在树中的每个元素可以包含属性`"id"`。 就可以在运行时来引用各个部分或使用 JsonElement 索引器元素。 如：

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax" />


## <a name="root-element-syntax"></a>根元素语法

根元素包含以下值：

-  `title`
-  `sections` （可选）


根元素可以在节内显示为元素创建嵌套的控制器。 在这种情况下，额外属性`"type"`必须设置为 `"root"`

 <a name="url" />


### <a name="url"></a>URL

如果`"url"`设置属性，如果在用户点击此 RootElement，代码将从指定的 url 请求文件，并将使内容显示的新信息。 可以使用此创建从服务器基于用户点击来扩展用户界面。

 <a name="group" />


### <a name="group"></a>组

如果设置，此设置的根元素的组名。 组名用于选取一个摘要，显示为从一个元素中的嵌套元素的根元素的值。 这是一个复选框的值或单选按钮的值。

 <a name="radioselected" />


### <a name="radioselected"></a>radioselected

标识在嵌套元素中选择的单选项

 <a name="title" />


### <a name="title"></a>标题

如果存在，它将是用于 RootElement 标题

 <a name="type" />


### <a name="type"></a>类型

必须设置为`"root"`时将显示在一个部分 （这用于嵌套控制器）。

 <a name="sections" />


### <a name="sections"></a>节

这是各个部分的 Json 数组

 <a name="Section_Syntax" />


## <a name="section-syntax"></a>部分语法

部分包含：

-  `header` （可选）
-  `footer` （可选）
-  `elements` 数组


 <a name="header" />


### <a name="header"></a>标题

如果存在，标头文本将显示为部分中的标题。

 <a name="footer" />


### <a name="footer"></a>页脚

如果存在，页脚被显示在该部分的底部。

 <a name="elements" />


### <a name="elements"></a>元素

这是一个元素的数组。 每个元素必须包含至少一个键，`"type"`用于标识要创建的元素类型的密钥。
某些元素共享某些公共属性，如`"caption"`和`"value"`。 以下是受支持的元素的列表：

-  `string` 元素 （同时使用和不使用样式）
-  `entry` 行 （常规或密码）
-  `boolean` 值 （使用开关或图像）


字符串元素可用于为按钮通过提供一种方法在用户点击该单元格或附件时调用

 <a name="Rendering_Elements" />


## <a name="rendering-elements"></a>呈现元素

呈现元素基于C#StringElement 和 StyledStringElement 以及它们可能会使以各种方式的信息，并且可以在各种方法中进行呈现。 最简单的元素可以创建如下：

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

这将显示一个简单的字符串与所有默认值： 字体、 背景、 文本颜色和效果。 可以挂接到这些元素的操作，并使它们通过设置的行为类似于按钮`"ontap"`属性或`"onaccessorytap"`属性：

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

上述将调用类"Acme.PhotoLibrary"中的"ShowPhotos"方法。 `"onaccessorytap"`与此类似，但它将只调用如果在用户点击附件而不是该单元格上点击。 若要启用此功能，还必须设置附件：

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

呈现元素可以同时显示两个字符串、 一个标题，另一个值。 如何呈现这些字符串依赖于样式，您可以设置这使用`"style"`属性。 默认值将显示标题左侧和右侧值。 在样式的更多详细信息，请参阅部分。 使用 # 符号后跟十六进制数字表示红色、 绿色、 蓝色和 alpha 可能值的值进行编码的颜色。 可以以缩写形式 （3 个或 4 个十六进制数字） 表示 RGB 或 RGBA 值编码内容。 或长格式 （6 或 8 位数） 表示 RGB 或 RGBA 值。 简短版本是一到两次编写相同的十六进制数字的简写形式。 因此，"#1bc"常量是为红色 = 0x11，绿色 = 0xbb 和蓝色 = 0xcc。 如果不存在的 alpha 值，颜色是不透明。 示例如下：

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />


### <a name="accessory"></a>附件

确定在呈现元素中，可能的值为要显示的附件的类型：

-  `checkmark`
-  `detail-disclosure`
-  `disclosure-indicator`


如果值不存在，会不显示任何附件

 <a name="background" />


### <a name="background"></a>背景

Background 属性设置为该单元格的背景色。 值是图像的 URL （在这种情况下，将调用异步图像下载程序和下载该映像后，将更新在后台），也可以使用颜色语法指定的颜色。

 <a name="caption" />


### <a name="caption"></a>标题

要在呈现元素上显示的主要字符串。 字体和颜色可以自定义设置`"textcolor"`和`"font"`属性。 由确定呈现样式`"style"`属性。

 <a name="color_and_detailcolor" />


### <a name="color-and-detailcolor"></a>颜色和 detailcolor

要使用的主文本或详细的文本的颜色。

 <a name="detailfont_and_font" />


### <a name="detailfont-and-font"></a>detailfont 和字体

要用于标题或详细信息的文本的字体。 字体规范的格式是根据需要跟一个短划线和点大小的字体名称。
以下是有效的字体规范：

-  "Helvetica"
-  "Helvetica 14"


 <a name="linebreak" />


### <a name="linebreak"></a>linebreak

确定行进行分解。 可能的值为：

-  `character-wrap`
-  `clip`
-  `head-truncation`
-  `middle-truncation`
-  `tail-truncation`
-  `word-wrap`


这两`character-wrap`并`word-wrap`一起使用可以使用`"lines"`属性设置为零，以将呈现元素转换为多行元素。

 <a name="ontap_and_onaccessorytap" />


### <a name="ontap-and-onaccessorytap"></a>ontap 和 onaccessorytap

这些属性必须指向采用一个参数作为对象在应用程序中的静态方法名称。 创建层次结构使用 JsonDialog.FromFile 或 JsonDialog.FromJson 方法时可以传递一个可选的对象值。 此对象值，然后传递给您的方法。 您可以用于将一些上下文传递到你的静态方法。 例如：

```csharp
class Foo {
    Foo ()
    {
        root = JsonDialog.FromJson (myJson, this);
    }

    static void Callback (object obj)
    {
        Foo myFoo = (Foo) obj;
        obj.Callback ();
    }
}
```

 <a name="lines" />


### <a name="lines"></a>文本行

如果设置为零，它将使元素自动调整大小具体取决于包含字符串的内容。 为实现此目的，还必须设置`"linebreak"`属性设置为`"character-wrap"`或`"word-wrap"`。

 <a name="style" />


### <a name="style"></a>样式

该样式确定将用于呈现内容的单元格样式的种类和它们对应于 UITableViewCellStyle 枚举值。
可能的值为：

-  `"default"`
-  `"value1"`
-  `"value2"`
-  `"subtitle"` ： 具有副标题的文本。


 <a name="subtitle" />


### <a name="subtitle"></a>副标题

要使用的子标题的值。 这是一种快捷方式将样式设置为`"subtitle"`并设置`"value"`属性设置为一个字符串。
此命令的作用都具有一个条目。

 <a name="textcolor" />


### <a name="textcolor"></a>textcolor

要使用的文本的颜色。

 <a name="value" />


### <a name="value"></a>值

显示在呈现元素上的辅助值。 受此布局`"style"`设置。 字体和颜色可以自定义设置`"detailfont"`和`"detailcolor"`。

 <a name="Boolean_Elements" />


## <a name="boolean-elements"></a>布尔值的元素

布尔值的元素应将类型设置为`"bool"`，可以包含`"caption"`以显示和`"value"`设置为 true 或 false。 如果`"on"`和`"off"`设置属性，它们被假定为图像。 可解析相对于当前工作目录的应用程序中的图像。 如果你想要引用相对于捆绑包的文件，则可以使用`"~"`作为快捷方式来表示应用程序捆绑包目录。 例如`"~/favorite.png"`将绑定文件中包含 favorite.png。 例如：

```json
{ 
    "type": "boolean",
    "caption": "Demo of a Boolean",
    "value": true
},

{
    "type": "boolean",
    "caption": "Boolean using images",
    "value": false,
    "on": "favorite.png",
    "off": "~/favorited.png"
}
```

 <a name="type" />


### <a name="type"></a>类型

可以将类型设置为`"boolean"`或`"checkbox"`。 如果设置为布尔值则会使用 UISlider 或图像 (如果这两个`"on"`和`"off"`设置)。 如果设置为复选框，它将使用一个复选框。 `"group"`属性可用于标记为属于特定组中的布尔元素。 这是很有用，如果包含根也具有`"group"`作为根的属性将汇总属于相同组使用所有布尔值 （或复选框） 的计数结果。

 <a name="Entry_Elements" />


## <a name="entry-elements"></a>条目元素

项元素用于允许用户输入数据。 项元素的类型是`"entry"`或`"password"`。 `"caption"`属性设置为文本，以在右侧，显示和`"value"`设置为初始值以设置的项。 `"placeholder"`用于针对空的条目 （它显示灰色） 向用户显示一个提示。 下面是一些可能的恶意活动：

```json
{
    "type": "entry",
    "caption": "Username",
    "value": "",
    "placeholder": "Your account username"
}, {
    "type": "password",
    "caption": "Password",
    "value": "",
    "placeholder": "You password"
}, {
    "type": "entry",
    "caption": "Zip Code",
    "value": "01010",
    "placeholder": "your zip code",
    "keyboard": "numbers"
}, {
    "type": "entry",
    "return-key": "route",
    "caption": "Entry with 'route'",
    "placeholder": "captialization all + no corrections",
    "capitalization": "all",
    "autocorrect": "no"
}
```

 <a name="autocorrect" />


### <a name="autocorrect"></a>自动更正

确定要使用的项的自动更正样式。 可能的值为 true 或 false (或字符串`"yes"`和`"no"`)。

 <a name="capitalization" />


### <a name="capitalization"></a>大小写

要使用的项的大小写样式。 可能的值为：

-  `all`
-  `none`
-  `sentences`
-  `words`


 <a name="caption" />


### <a name="caption"></a>标题

要使用的项的标题

 <a name="keyboard" />


### <a name="keyboard"></a>键盘

要用于数据输入的键盘类型。 可能的值为：

-  `ascii`
-  `decimal`
-  `default`
-  `email`
-  `name`
-  `numbers`
-  `numbers-and-punctuation`
-  `twitter`
-  `url`


 <a name="placeholder" />


### <a name="placeholder"></a>占位符

该条目包含空值的时显示提示文本。

 <a name="return-key" />


### <a name="return-key"></a>返回键

用于返回密钥的标签。 可能的值为：

-  `default`
-  `done`
-  `emergencycall`
-  `go`
-  `google`
-  `join`
-  `next`
-  `route`
-  `search`
-  `send`
-  `yahoo`


 <a name="value" />


### <a name="value"></a>值

条目的初始值

 <a name="Radio_Elements" />


## <a name="radio-elements"></a>单选元素

单选元素具有类型`"radio"`。 选定项选择`radioselected`其包含的根元素上的属性。
此外，如果设置了值`"group"`属性，此单选按钮所属的组。

 <a name="Date_and_Time_Elements" />


## <a name="date-and-time-elements"></a>日期和时间元素

元素类型`"datetime"`，`"date"`和`"time"`用于呈现时间的日期、 日期或时间。 标题和一个值，这些元素将作为参数。 该值可以写入.NET DateTime.Parse 函数支持的任何格式。 示例:

```json
"header": "Dates and Times",
"elements": [
    {
        "type": "datetime",
        "caption": "Date and Time",
        "value": "Sat, 01 Nov 2008 19:35:00 GMT"
    }, {
        "type": "date",
        "caption": "Date",
        "value": "10/10"
    }, {
        "type": "time",
        "caption": "Time",
        "value": "11:23"
    }                       
]
```

 <a name="Html/Web_Element" />


## <a name="htmlweb-element"></a>Html/Web 元素

您可以创建一个单元格的点击时将嵌入指定的 URL，内容呈现 UIWebView 本地或远程使用`"html"`类型。 此元素的只有两个属性是`"caption"`和`"url"`:

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```

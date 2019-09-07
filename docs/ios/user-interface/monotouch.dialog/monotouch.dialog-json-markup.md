---
title: MonoTouch.Dialog Json 标记
description: 本文档介绍可用于通过 Monotouch.dialog 生成 Xamarin iOS 用户界面的 JSON 语法。
ms.prod: xamarin
ms.assetid: 59F3E18C-3A73-69B8-DA5E-21B19B9DFB98
ms.technology: xamarin-ios
ms.date: 11/25/2015
author: conceptdev
ms.author: crdun
ms.openlocfilehash: d9174e9b2d6c056c94b405033a25eeb787c92f9f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768770"
---
# <a name="monotouchdialog-json-markup"></a>MonoTouch.Dialog Json 标记

本页介绍 Monotouch.dialog 的[JsonElement](xref:MonoTouch.Dialog.JsonElement)所接受的 Json 标记。

首先，让我们看一个示例。 下面是一个可传递到 JsonElement 的完整 Json 文件。

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

上述标记生成以下 UI：

 [![](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png "给定标记创建的 UI")](monotouch.dialog-json-markup-images/screen-shot-2012-03-02-at-11.31.31-am.png#lightbox)

树中的每个元素都可以包含`"id"`属性。 可以在运行时使用 JsonElement 索引器引用单个节或元素。 如：

```csharp
var jsonElement = JsonElement.FromFile ("demo.json");

var firstSection = jsonElement ["first-section"] as Section;

var theBoolean = jsonElement ["the-boolean"] as BooleanElement;
```

 <a name="Root_Element_Syntax" />

## <a name="root-element-syntax"></a>根元素语法

Root 元素包含以下值：

- `title`
- `sections` （可选）

根元素可以作为元素出现在节内，以创建嵌套控制器。 在这种情况下，必须`"type"`将额外的属性设置为`"root"`

 <a name="url" />

### <a name="url"></a>URL

如果设置`"url"`了属性，则如果用户点击此 RootElement，则代码将从指定的 url 请求文件，并将显示新信息的内容。 可以使用此功能，根据用户点击的内容，从服务器扩展用户界面。

 <a name="group" />

### <a name="group"></a>组

如果设置此设置，则将设置根元素的组名。 组名称用于选取一个摘要，该摘要显示为元素中一个嵌套元素的根元素的值。 这是复选框或单选按钮的值。

 <a name="radioselected" />

### <a name="radioselected"></a>radioselected

标识在嵌套元素中选定的单选项

 <a name="title" />

### <a name="title"></a>标题

如果存在，它将是用于 RootElement 的标题。

 <a name="type" />

### <a name="type"></a>type

当此部分出现`"root"`在节（用于嵌套控制器）时，必须设置为。

 <a name="sections" />

### <a name="sections"></a>节

这是包含各个部分的 Json 数组

 <a name="Section_Syntax" />

## <a name="section-syntax"></a>节语法

部分包含：

- `header` （可选）
- `footer` （可选）
- `elements` 数组

 <a name="header" />

### <a name="header"></a>标题

如果存在，则标题文本显示为节的标题。

 <a name="footer" />

### <a name="footer"></a>尾行

如果存在，则页脚显示在部分的底部。

 <a name="elements" />

### <a name="elements"></a>元素

这是元素数组。 每个元素必须至少包含一个键， `"type"`该键用于标识要创建的元素的类型。
某些元素共享某些常见的属性，例如`"caption"`和`"value"`。 下面列出了受支持的元素：

- `string`元素（带有和不带样式）
- `entry`行（常规或密码）
- `boolean`值（使用开关或图像）

字符串元素可用作按钮，方法是提供一个方法，以便在用户点击该单元或附件时进行调用。

 <a name="Rendering_Elements" />

## <a name="rendering-elements"></a>呈现元素

呈现元素基于C# StringElement 和 StyledStringElement，它们可以通过多种方式呈现信息，并且可以通过多种方式呈现。 可以按如下所示创建最简单的元素：

```json
{
    "type": "string",
    "caption": "Json Serializer"
}
```

这会显示一个简单的字符串，其中包含所有默认值：字体、背景、文本颜色和修饰。 通过设置`"ontap"`属性`"onaccessorytap"`或属性，可以将操作挂钩到这些元素，并使它们的行为与按钮类似：

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos"
}
```

以上将调用类 "PhotoLibrary" 中的 "ShowPhotos" 方法。 `"onaccessorytap"`与类似，但仅当用户点击附件而不是点击该单元时，才会调用它。 若要启用此设置，还必须设置附件：

```json
{
    "type": "string",
    "caption": "View Photos",
    "ontap": "Acme.PhotoLibrary.ShowPhotos",
    "accessory": "detail-disclosure",
    "onaccessorytap": "Acme.PhotoLibrary.ShowStats"
}
```

呈现元素可以同时显示两个字符串，一个是标题，另一个是值。 如何呈现这些字符串取决于样式，可使用`"style"`属性对其进行设置。 默认值将在左侧显示标题，在右侧显示值。 有关更多详细信息，请参阅 style 部分。 颜色是使用 "#" 符号后跟十六进制数字（表示红色、绿色、蓝色和 alpha 值的值）进行编码的。 内容可以采用表示 RGB 或 RGBA 值的缩写形式（3或4个十六进制数字）进行编码。 或表示 RGB 或 RGBA 值的长格式（6或8位数字）。 Short 版本是两次写入同一十六进制数字的简写形式。 因此，"#1bc" 常量看作为 red = 0x11，绿 = 0xbb，blue = 0xcc。 如果 alpha 值不存在，则颜色不透明。 示例如下：

```json
"background": "#f00"
"background": "#fa08f880"
```

 <a name="accessory" />

### <a name="accessory"></a>附件

确定要显示在呈现元素中的附件类型，可能的值为：

- `checkmark`
- `detail-disclosure`
- `disclosure-indicator`

如果值不存在，则不显示附件

 <a name="background" />

### <a name="background"></a>背景

背景属性设置单元的背景色。 该值是指向图像的 URL （在这种情况下，将调用异步图像下载程序，并且在下载图像后会更新背景），也可以是使用颜色语法指定的颜色。

 <a name="caption" />

### <a name="caption"></a>caption

要在呈现元素上显示的主字符串。 可以通过设置`"textcolor"`和`"font"`属性来自定义字体和颜色。 呈现样式由`"style"`属性确定。

 <a name="color_and_detailcolor" />

### <a name="color-and-detailcolor"></a>color 和 detailcolor

用于主文本或详细文本的颜色。

 <a name="detailfont_and_font" />

### <a name="detailfont-and-font"></a>detailfont 和字体

用于标题或详细信息文本的字体。 字体规范的格式是字体名称后跟划线和点大小。
下面是有效的字体规格：

- Helvetica
- "Helvetica-14"

 <a name="linebreak" />

### <a name="linebreak"></a>linebreak

确定如何分离行。 可能的值为：

- `character-wrap`
- `clip`
- `head-truncation`
- `middle-truncation`
- `tail-truncation`
- `word-wrap`

和`character-wrap` `"lines"`均可与属性设置为零的属性一起使用，以将渲染元素变为多行元素。 `word-wrap`

 <a name="ontap_and_onaccessorytap" />

### <a name="ontap-and-onaccessorytap"></a>ontap 和 onaccessorytap

这些属性必须指向应用程序中采用对象作为参数的静态方法名称。 使用 FromFile 或 FromJson 方法创建层次结构时，可以传递可选的对象值 JsonDialog。 然后，此对象值将传递给方法。 您可以使用此方法将某个上下文传递给静态方法。 例如:

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

如果此值设置为零，则它会使元素自动调整大小，具体取决于所包含字符串的内容。 为此，您还必须将`"linebreak"`属性设置为`"character-wrap"`或`"word-wrap"`。

 <a name="style" />

### <a name="style"></a>样式

样式确定将用于呈现内容并且与 UITableViewCellStyle 枚举值相对应的单元格样式的类型。
可能的值为：

- `"default"`
- `"value1"`
- `"value2"`
- `"subtitle"`：带有副标题的文本。

 <a name="subtitle" />

### <a name="subtitle"></a>标题

要用于副标题的值。 这是将样式设置为`"subtitle"`并`"value"`将属性设置为字符串的快捷方式。
这会同时使用单个条目。

 <a name="textcolor" />

### <a name="textcolor"></a>textcolor

用于文本的颜色。

 <a name="value" />

### <a name="value"></a>值

要在呈现元素上显示的辅助值。 此`"style"`设置会影响此的布局。 可以通过设置`"detailfont"`和`"detailcolor"`自定义字体和颜色。

 <a name="Boolean_Elements" />

## <a name="boolean-elements"></a>布尔值元素

布尔值元素应将类型设置`"bool"`为，可以包含`"caption"`要显示的， `"value"`并且设置为 true 或 false。 如果设置`"on"`了`"off"`和属性，则假定它们是图像。 相对于应用程序中的当前工作目录解析映像。 如果要引用绑定相关的文件，可以使用`"~"`作为快捷方式来表示应用程序包目录。 例如`"~/favorite.png"` ，将是包含在绑定文件中的最喜爱的 .png。 例如：

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

### <a name="type"></a>type

类型可以设置为`"boolean"`或。 `"checkbox"` 如果设置为布尔值，则它将使用 UISlider 或映像（ `"on"`如果`"off"`同时设置了和）。 如果设置为复选框，则将使用 checkbox。 `"group"`属性可用于标记属于特定组的布尔元素。 如果包含根`"group"`的属性为根，则这非常有用，它将使用属于同一组的所有布尔值（或复选框）的计数来汇总结果。

 <a name="Entry_Elements" />

## <a name="entry-elements"></a>Entry 元素

使用条目元素可允许用户输入数据。 条目元素的类型为`"entry"`或。 `"password"` 将属性设置为要在右侧显示的文本， `"value"`并将设置为初始值以将项设置为。 `"caption"` `"placeholder"`用于向用户显示空条目的提示（显示为灰色）。 下面是一些可能的恶意活动：

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

### <a name="autocorrect"></a>自动

确定要用于该项的自动更正样式。 可能的值为 true 或 false （或字符串`"yes"`和`"no"`）。

 <a name="capitalization" />

### <a name="capitalization"></a>大小写

要用于该项的大小写样式。 可能的值为：

- `all`
- `none`
- `sentences`
- `words`

 <a name="caption" />

### <a name="caption"></a>caption

要用于该项的标题

 <a name="keyboard" />

### <a name="keyboard"></a>键盘

用于数据输入的键盘类型。 可能的值为：

- `ascii`
- `decimal`
- `default`
- `email`
- `name`
- `numbers`
- `numbers-and-punctuation`
- `twitter`
- `url`

 <a name="placeholder" />

### <a name="placeholder"></a>placeholder

当项具有空值时显示的提示文本。

 <a name="return-key" />

### <a name="return-key"></a>返回键

用于返回键的标签。 可能的值为：

- `default`
- `done`
- `emergencycall`
- `go`
- `google`
- `join`
- `next`
- `route`
- `search`
- `send`
- `yahoo`

 <a name="value" />

### <a name="value"></a>值

条目的初始值

 <a name="Radio_Elements" />

## <a name="radio-elements"></a>单选元素

单选元素具有类型`"radio"`。 所选的项由`radioselected`属性在其包含的根元素上选取。
此外，如果为`"group"`属性设置了值，则此单选按钮属于该组。

 <a name="Date_and_Time_Elements" />

## <a name="date-and-time-elements"></a>日期和时间元素

元素类型`"datetime"` `"date"`和用于呈现带有时间、日期或时间的日期。`"time"` 这些元素采用标题和值作为参数。 可以使用 .NET DateTime. Parse 函数支持的任何格式来写入值。 示例：

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

你可以创建一个单元格，当点击它将嵌入使用该`"html"`类型呈现指定 URL （本地或远程）的内容的 UIWebView。 此元素的唯一两个属性是`"caption"`和`"url"`：

```json
{
    "type": "html",
    "caption": "Miguel's blog",
    "url": "https://tirania.org/blog" 
}
```

---
title: 在 Android 上的辅助功能
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: d004b753c89f3995e8dc511877bd115a894396fc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61018615"
---
# <a name="accessibility-on-android"></a>在 Android 上的辅助功能

此页介绍了如何使用 Android 的辅助功能 Api 来构建应用程序根据[可访问性清单](~/cross-platform/app-fundamentals/accessibility.md)。
请参阅[iOS 可访问性](~/ios/app-fundamentals/accessibility.md)并[OS X 可访问性](~/mac/app-fundamentals/accessibility.md)页的其他平台 Api。


## <a name="describing-ui-elements"></a>描述 UI 元素

Android 提供`ContentDescription`屏幕阅读 Api 用于提供控件的用途的辅助性说明的属性。

内容说明可以设置在C#或 AXML 布局文件中。

**C#**

可以为任何字符串 （或字符串资源） 的代码中设置的说明：

```csharp
saveButton.ContentDescription = "Save data";
```

**AXML 布局**

在 XML 中布局使用`android:contentDescription`属性：

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>TextView 的 use Hint

有关`EditText`并`TextView`控制数据输入，使用`Hint`属性来提供预期输入内容的说明 (而不是`ContentDescription`)。
输入一些文本，文本本身，将"读取"而不是提示。

**C#**

设置`Hint`在代码中的属性：

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**AXML 布局**

在 XML 布局文件使用`android:hint`属性：

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor 链接输入带有标签的字段

若要将标签与数据输入控件相关联，请使用`LabelFor`属性

**C#**

在C#，设置`LabelFor`属性设置为可描述此内容的控件的资源 ID （通常此属性设置上一个标签和引用其他输入的控件）：

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**AXML 布局**

在布局 XML 使用`android:labelFor`属性来引用另一个控件的标识符：

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>公布推出适用于可访问性

使用`AnnounceForAccessibility`方法在任何视图控件时进行通信的事件或状态更改为用户启用可访问性。 此方法不是内置旁白，提供足够的反馈，但应额外信息很有帮助的用户使用的大多数操作所必需的。

下面的代码显示了简单的示例调用`AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>更改焦点设置

可访问的导航依赖于控件具有焦点，以帮助用户了解哪些操作。 Android 提供`Focusable`可以标记为专门能够导航期间接收焦点的控件的属性。

**C#**

若要防止控件获得焦点的C#，设置`Focusable`属性设置为`false`:

```csharp
label.Focusable = false;
```

**AXML 布局**

在布局 XML 文件集`android:focusable`属性：

```xml
<android:focusable="false" />
```

您还可以控制与焦点顺序`nextFocusDown`， `nextFocusLeft`， `nextFocusRight`，`nextFocusUp`属性，通常集 AXML 布局中。 使用这些属性来确保用户可以轻松地浏览屏幕上的控件。


## <a name="accessibility-and-localization"></a>可访问性和本地化

是的提示和内容说明上面的示例中直接设置为显示值。 最好使用中的值是**Strings.xml**文件，如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

使用字符串文件中的文本如下所示在C#和 AXML 布局文件：

**C#**

而不是在代码中使用字符串文字，查找已翻译的值从字符串文件`Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

在布局 XML 可访问性属性，如`hint`和`contentDescription`可以设置为字符串标识符：

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

将文本存储在单独的文件的好处是可以在应用中提供的文件的多个语言翻译。 请参阅[Android 本地化指南](~/android/app-fundamentals/localization.md)若要了解如何将已本地化的字符串文件添加到应用程序项目。


## <a name="testing-accessibility"></a>测试辅助功能

请按照[这些步骤](https://developer.android.com/training/accessibility/testing.html#how-to)启用 TalkBack 和资源管理器通过触摸辅助功能在 Android 设备上的进行测试。

可能需要安装[TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)从 Google Play，如果未出现在**设置 > 可访问性**。


## <a name="related-links"></a>相关链接

- [跨平台辅助功能](~/cross-platform/app-fundamentals/accessibility.md)
- [Android 可访问性 Api](https://developer.android.com/guide/topics/ui/accessibility/index.html)

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
# <a name="accessibility-on-android"></a>在 Android 中的辅助功能

此页介绍了如何依照 [辅助功能清单](~/cross-platform/app-fundamentals/accessibility.md) 使用 Android 辅助功能 Api 来构建应用程序。
对于其它平台下的 Api 请参阅 [iOS 辅助功能](~/ios/app-fundamentals/accessibility.md) 和 [OS X 辅助功能](~/mac/app-fundamentals/accessibility.md)。


## <a name="describing-ui-elements"></a>描述 UI 元素

Android 提供 `ContentDescription` 屏幕阅读 Api 用于提供控件用途的辅助性描述。

内容说明可以设置在 C# 或 AXML 布局文件中。

**C#**

可以为代码中任何字符串 （或字符串资源） 设置描述：

```csharp
saveButton.ContentDescription = "Save data";
```

**AXML 布局**

在 XML 中布局使用 `android:contentDescription` 属性：

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>为 TextView 设置提示

对于 `EditText` 和 `TextView` 控件的数据输入，使用`Hint` 属性来提供预期输入内容的说明 (而不是`ContentDescription` )。
当输入文本后，将 "读取" 文本自身而不是提示。

**C#**

设置`Hint`在代码中的属性：

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**AXML 布局**

在 XML 布局文件中使用 `android:hint` 属性：

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor 链接输入带有标签的字段

若要将标签与数据输入控件相关联，请使用 `LabelFor` 属性

**C#**

在C#，设置 `LabelFor` 属性内容为控件资源 ID (通常设置某个 label 控件的此属性，使其引用其他输入控件)：

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**AXML 布局**

在布局 XML 中使用 `android:labelFor` 属性来引用另一个控件的标识符：

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a> 辅助功能播报

在任何视图控件中使用 `AnnounceForAccessibility` 方法，将在辅助功能启动后，通知用户事件或状态变化。 大多数操作不需要此方法，内置叙述提供了充分的反馈，但应使用额外信息帮助用户。

下面的代码显示了简单的示例调用 `AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>更改焦点设置

辅助功能导航依赖于控件的焦点，以告知用户可使用哪些操作。 Android 提供 `Focusable` 标记在导航期间可接收焦点的控件的属性。

**C#**

若要防止控件获得焦点的C#，设置 `Focusable` 属性设置为`false`:

```csharp
label.Focusable = false;
```

**AXML 布局**

在布局 XML 文件中设置 `android:focusable` 属性：

```xml
<android:focusable="false" />
```

您还可以控制 `nextFocusDown` ， `nextFocusLeft`， `nextFocusRight`，`nextFocusUp`属性的焦点顺序，通常在 AXML 布局中。 使用这些属性来确保用户可以轻松地浏览屏幕上的控件。


## <a name="accessibility-and-localization"></a>可访问性和本地化

在上面的示例中直接设置提示和内容说明为显示值。 但是，最好使用 **Strings.xml** 文件中的值，如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

如下所示在C#和 AXML 布局文件中，使用字符串文件中的文本：

**C#**

而不是在代码中使用字符串文字，查找已翻译的值从字符串文件`Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

在布局 XML 中辅助功能属性，如`hint`和`contentDescription`可以设置为字符串标识符：

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

将文本存储在单独的文件，好处是可以在应用中提供多语言翻译文件。 若要了解如何将已本地化的字符串文件添加到应用程序项目，请参阅 [Android 本地化指南](~/android/app-fundamentals/localization.md) 。


## <a name="testing-accessibility"></a>测试辅助功能
 
请按照 [这些步骤](https://developer.android.com/training/accessibility/testing.html#how-to) 在 Android 设备上启用 TalkBack 和触摸导航，测试辅助功能。

如果未在 **设置 > 可访问性** 中出现，可能需要从 Google Play 安装[TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)。


## <a name="related-links"></a>相关链接

- [跨平台辅助功能](~/cross-platform/app-fundamentals/accessibility.md)
- [Android 辅助功能 Api](https://developer.android.com/guide/topics/ui/accessibility/index.html)

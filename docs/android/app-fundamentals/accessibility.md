---
title: Android 中的辅助功能
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: 6e86663be0bb06697fbfe0e8c360d733bca18da0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017023"
---
# <a name="accessibility-on-android"></a>Android 中的辅助功能

本页介绍如何使用 Android 辅助功能 Api 根据[辅助功能清单](~/cross-platform/app-fundamentals/accessibility.md)来构建应用。
有关其他平台 Api，请参阅[iOS 辅助功能](~/ios/app-fundamentals/accessibility.md)和[OS X 辅助功能](~/mac/app-fundamentals/accessibility.md)页面。

## <a name="describing-ui-elements"></a>描述 UI 元素

Android 提供一个 `ContentDescription` 属性，该属性由屏幕读取 Api 用来提供控件用途的辅助性说明。

可以在C#或 main.axml 布局文件中设置内容说明。

**C#**

可以在代码中将说明设置为任何字符串（或字符串资源）：

```csharp
saveButton.ContentDescription = "Save data";
```

**MAIN.AXML 布局**

在 XML 布局中使用 `android:contentDescription` 特性：

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>使用 TextView 的提示

对于用于数据输入的 `EditText` 和 `TextView` 控件，请使用 `Hint` 属性来提供所需输入的说明（而不是 `ContentDescription`）。
输入某些文本后，文本本身将是 "读取"，而不是提示。

**C#**

在代码中设置 `Hint` 属性：

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**MAIN.AXML 布局**

在 XML 布局文件中使用 `android:hint` 特性：

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```

### <a name="labelfor-links-input-fields-with-labels"></a>Html.labelfor 链接带标签的输入字段

若要将标签与数据输入控件关联，请使用 `LabelFor` 属性

**C#**

在C#中，将`LabelFor`属性设置为此内容描述的控件的资源 ID （通常情况下，此属性设置在标签上并引用其他输入控件）：

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**MAIN.AXML 布局**

在布局 XML 中，使用 `android:labelFor` 属性来引用另一个控件的标识符：

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>可访问性公告

对任何视图控件使用 `AnnounceForAccessibility` 方法，以便在启用辅助功能时向用户传达事件或状态更改。 此方法对于大多数操作都是必需的，其中内置旁白提供了足够的反馈，但应在其他信息对用户有用时使用。

下面的代码演示了一个简单的示例调用 `AnnounceForAccessibility`：

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>更改焦点设置

可访问的导航依赖于具有焦点的控件来帮助用户了解可用的操作。 Android 提供了一个 `Focusable` 属性，该属性可以在导航过程中将控件标记为能够接收焦点。

**C#**

若要防止控件获得焦点C#，请将`Focusable`属性设置为`false`：

```csharp
label.Focusable = false;
```

**MAIN.AXML 布局**

在 "布局 XML 文件" 中，设置 `android:focusable` 特性：

```xml
<android:focusable="false" />
```

还可以通过 `nextFocusDown`、`nextFocusLeft`、`nextFocusRight``nextFocusUp` 特性来控制焦点顺序，通常在布局 MAIN.AXML 中设置。 使用这些属性可以确保用户可以通过屏幕上的控件轻松地导航。

## <a name="accessibility-and-localization"></a>辅助功能和本地化

在上面的示例中，提示和内容说明直接设置为显示值。 最好使用**字符串 .xml**文件中的值，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

使用字符串文件中的文本显示在下面的C#和 main.axml 布局文件中：

**C#**

使用 `Resources.GetText`从字符串文件中查找翻译后的值，而不是在代码中使用字符串：

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**MAIN.AXML**

在布局 XML 可访问性属性（如 `hint` 和 `contentDescription`）中，可以将设置为字符串标识符：

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

在单独的文件中存储文本的好处是，可以在应用程序中提供文件的多种语言翻译。 有关如何向应用程序项目添加本地化字符串文件的详细说明，请参阅[Android 本地化指南](~/android/app-fundamentals/localization.md)。

## <a name="testing-accessibility"></a>测试辅助功能

按照[以下步骤](https://developer.android.com/training/accessibility/testing.html#how-to)启用 TalkBack，并通过触摸来浏览 Android 设备上的辅助功能。

如果 [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) 未出现在 "设置" 中，则可能需要从 Google Play 安装 > 可访问性。

## <a name="related-links"></a>相关链接

- [跨平台辅助功能](~/cross-platform/app-fundamentals/accessibility.md)
- [Android 辅助功能 Api](https://developer.android.com/guide/topics/ui/accessibility/index.html)

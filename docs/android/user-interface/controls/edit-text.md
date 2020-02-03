---
title: 编辑文本
description: 如何使用 EditText 小组件来接受用户输入。
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: 6180896002d19c51bce47bf53aaecdc11b0cae6e
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725147"
---
# <a name="xamarinandroid-edit-text"></a>Xamarin Android 编辑文本

在本部分中，你将使用[EditText](xref:Android.Widget.EditText)小组件来创建用户输入的文本字段。 在字段中输入文本后， **Enter**键将显示 toast 消息中的文本。

打开**Resources/layout/activity_main main.axml** ，并将[EditText](xref:Android.Widget.EditText)元素添加到包含的布局。 下面的示例**activity_main** `EditText` 已添加到 `LinearLayout`：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

在此代码示例中，`EditText` 特性 `android:imeOptions` 设置为 `actionGo`。 此设置会将 "已完成[" 的](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO)默认操作更改为 "[执行](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE)" 操作，以便通过点击**Enter**键来触发 `KeyPress` 输入处理程序。
（通常情况下，使用 `actionGo`，以便**Enter**键将用户转到在中键入的 URL 的目标。）

若要处理用户文本输入，请将以下代码添加到**MainActivity.cs**中[OnCreate](xref:Android.App.Activity.OnCreate*)方法的末尾：

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter)
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

此外，将以下 `using` 语句添加到**MainActivity.cs**的顶部（如果它尚不存在）：

```csharp
using Android.Views;
```

此代码示例从布局中增加[EditText](xref:Android.Widget.EditText)元素，并添加了一个[KeyPress](xref:Android.Views.View.KeyPress)处理程序，用于定义在小组件有焦点的情况下按下某个键时要执行的操作。 在这种情况下，方法定义为侦听**Enter**键（点击时），然后使用已输入的文本弹出[Toast](xref:Android.Widget.Toast)消息。 请注意，如果已处理事件，则应始终 `true`[处理](xref:Android.Views.View.KeyEventArgs.Handled)的属性。 这对于防止事件向上冒泡（这会导致文本字段中出现回车符）是必需的。

运行应用程序，并在文本字段中输入一些文本。 按**enter**键时，toast 将显示在右侧：

[![在 EditText 中输入文本的示例](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据*[*创造性 Commons 2.5 归属许可证*](https://creativecommons.org/licenses/by/2.5/)中所述的条款使用 *。本教程基于* [*Android 表单资料教程*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *。*

## <a name="related-links"></a>相关链接

- [EditTextSample](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-edittextsample)

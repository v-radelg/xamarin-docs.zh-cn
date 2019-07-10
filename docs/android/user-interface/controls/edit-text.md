---
title: 编辑文本
description: 如何使用 EditText 小组件可接受用户输入。
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: 518c13aea431a8e973579768cc70b8281a31acac
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674720"
---
# <a name="edit-text"></a>编辑文本

在本部分中，你将使用[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)小组件创建的用户输入的文本字段。 一旦到字段中，输入文本**Enter**密钥将显示文本中的 toast 消息。

打开**Resources/layout/activity_main.axml**并添加[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)到包含布局元素。 下面的示例**activity_main.axml**已`EditText`已添加到`LinearLayout`:

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

在此代码示例中，`EditText`特性`android:imeOptions`设置为`actionGo`。 此设置更改的默认[完成](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE)操作[转](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO)操作，以便点击**Enter**密钥触发器`KeyPress`输入的处理程序。
(通常情况下，`actionGo`使用，以便**Enter**密钥以便用户可以在中键入的 URL 目标。)

若要处理用户的文本输入，请将以下代码添加到末尾[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/)中的方法**MainActivity.cs**:

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

此外，添加以下`using`语句的页首**MainActivity.cs**如果尚不存在：

```csharp
using Android.Views;
```

此代码示例增大[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)元素在布局，并添加[KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)定义小组件获得焦点时按下某个键时进行的操作的处理程序。 在这种情况下，定义方法来侦听**Enter**密钥 （当点击），然后弹出[Toast](https://developer.xamarin.com/api/type/Android.Widget.Toast/)与已输入的文本消息。 请注意， [Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)属性应始终为`true`处理该事件。 这是有必要阻止从浮升事件向上 （这将产生的文本字段中返回一个回车符）。

运行应用程序和文本字段中输入一些文本。 当您按下**Enter**键、 toast 通知将显示在右侧所示：

[![EditText 中输入文本的示例](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*此页的部分是基于创建的工作的修改并* [*由 Android 的开放源项目共享*](http://code.google.com/policies.html) *中所述的条款，并用* [ *2.5 的 creative Commons Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/) *。本教程基于* [ *Android 窗体内容教程*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *。*


## <a name="related-links"></a>相关链接

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)

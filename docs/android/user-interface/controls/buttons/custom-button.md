---
title: 自定义按钮
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: d85c67cf18c61af04cf12bfab58a5b516d380f62
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029358"
---
# <a name="custom-button"></a>自定义按钮

在本部分中，你将使用 " [`Button`](xref:Android.Widget.Button) " 小组件和一个定义三个不同图像（用于不同按钮状态）的 XML 文件创建一个包含自定义图像（而不是文本）的按钮。 按下该按钮时，将显示一条短消息。

右键单击并下载下面的三个图像，然后将其复制到项目的 **Resources/drawable** 目录。 这些图像将用于不同的按钮状态。

 [![适用](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox)于处于焦点状态的黄色 android 图标[![橙色](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox)android 图标![适用于[按下状态的黄色 android](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)图标

在 **Resources/drawable** 目录中创建名为 **android_button.xml** 的新文件。 插入以下 XML：

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

这会定义一个可绘制的资源，该资源将基于按钮的当前状态更改其图像。 第一个 `<item>` 在按下按钮时将**android_pressed**的图像定义为图像（已激活）;第二个 `<item>` 将**android_focused**为焦点时的图像定义为图像（使用轨迹球或方向面板突出显示按钮时）;第三个 `<item>` 将**android_normal .png**定义为图像的正常状态（未按下或未设定焦点时）。 此 XML 文件现在表示一个可绘制的资源，并在被其背景[`Button`](xref:Android.Widget.Button)引用时，显示的图像将基于这三个状态更改。

> [!NOTE]
> `<item>`元素顺序很重要。 当引用此图像时，`<item>`将遍历这些项，以确定哪个项适合当前按钮状态。
> “默认”的图像是最后一个，因此只在 `android:state_pressed` 和 `android:state_focused` 这两个条件都为 false 时才应用它。

打开**Resources/layout/main.axml**文件并添加[`Button`](xref:Android.Widget.Button)元素：

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

`android:background` 特性指定按钮背景使用的可绘制资源（在**资源/可绘制/android .xml**中保存时，将被引用为 `@drawable/android`）。 这会替换整个系统中用于按钮的正常背景图像。 为了使可绘制的基于按钮状态更改其图像，必须将图像应用到背景。

若要使按钮在按下时执行操作，请在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)的末尾添加以下代码
方法：

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

这会从布局捕获[`Button`](xref:Android.Widget.Button) ，然后添加在单击[`Button`](xref:Android.Widget.Button)时要显示的[`Toast`](xref:Android.Widget.Toast)消息。

现在运行该应用程序。

*此页面的某些部分是基于 Android 开源项目创建和共享的工作的修改，并根据*
[*创造性 Commons 2.5 归属许可证*](https://creativecommons.org/licenses/by/2.5/)中所述的术语使用。

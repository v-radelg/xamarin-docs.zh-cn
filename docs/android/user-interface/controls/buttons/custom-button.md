---
title: 自定义按钮
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b5ccefa1eb7e659584c1c82481bbd4473a3a8abc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076230"
---
# <a name="custom-button"></a>自定义按钮

在本部分中，您将创建一个按钮与自定义映像而不是文本，使用[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)小组件和定义三个不同的映像用于不同按钮状态的 XML 文件。 按下按钮时，将显示一条短消息。

右键单击并下载下面的三个图像，然后将其复制到项目的 **Resources/drawable** 目录。 这些图像将用于不同的按钮状态。

 [![绿色 Android 正常状态图标](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [![已设定焦点状态的橙色 Android 图标](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [![按下状态的黄色 Android 图标](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

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

此项定义单个可绘制资源，这将更改按钮的当前状态基于其映像。 第一个`<item>`定义**android_pressed.png**作为在按下按钮的图像 （激活）; 第二个`<item>`定义**android_focused.png**作为映像时已设定焦点的按钮 （当突出显示该按钮时使用轨迹球或方向键）;第三个`<item>`定义**android_normal.png** （时没有按下或已设定焦点） 的正常状态的图像。 此 XML 文件现在表示单个可绘制资源和通过引用时[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)对于其背景显示的图像将更改基于以下三种状态。


> [!NOTE]
> `<item>`元素顺序很重要。 当引用此图像时，`<item>`将遍历这些项，以确定哪个项适合当前按钮状态。
> “默认”的图像是最后一个，因此只在 `android:state_pressed` 和 `android:state_focused` 这两个条件都为 false 时才应用它。

打开**Resources/layout/Main.axml**文件，并添加[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)元素：

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

`android:background`属性指定要用于按钮背景的可绘制资源 (其中，在保存时**Resources/drawable/android.xml**，作为引用`@drawable/android`)。 这会替换整个系统的按钮所使用的普通的背景图像。 为了使可绘制要更改其根据按钮状态的图像，图像必须应用于背景。

若要执行操作时按下按钮，在末尾添加下面的代码 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/)
方法：

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

这会将捕获[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)从布局，然后添加[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)时要显示的消息[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)单击。

现在运行应用程序。


*此页的部分是基于工作创建和共享通过 Android 的开放源项目和使用中所述的条款的修改*
[*Creative Commons 2.5 Attribution 许可证*](http://creativecommons.org/licenses/by/2.5/).

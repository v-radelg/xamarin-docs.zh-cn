---
title: Xamarin Android 开关
description: 如何在 Xamarin Android 应用程序中使用切换小组件
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: 7fe4ffc733a71ed0f372da8288c7f1b63a961442
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642579"
---
# <a name="xamarinandroid-switch"></a>Xamarin Android 开关

`Switch`小组件 (如下所示) 允许用户在两个状态之间切换, 例如打开或关闭。 `Switch`默认值为 OFF。 此小组件的 "开" 和 "关" 状态如下所示:

[![开关小组件在关闭和打开状态的屏幕截图](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>创建开关

若要创建开关, 只需在`Switch` XML 中声明元素, 如下所示:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

这将创建一个基本交换机, 如下所示:

[![显示处于关闭状态的开关的演示应用屏幕截图](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>更改默认值

控件为开启和关闭状态显示的文本和默认值都是可配置的。 例如, 若要使开关默认为 ON, 并读取 NO/YES 而不是 OFF/ON, 可以在下面`checked`的`textOn`XML 中`textOff`设置、和属性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>提供标题

小组件还支持通过`text`设置属性来包含文本标签, 如下所示: `Switch`

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

此标记会在运行时生成以下屏幕截图:

[![在切换小组件之前水平文本的演示应用屏幕截图](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

当的值发生更改时, 它会`CheckedChange`引发事件。 `Switch`
例如, 在下面的代码中, 我们将捕获此事件, `Toast`并显示一个小组件`Switch`, 其中`isChecked`包含基于的值的消息, 该消息作为`CompoundButton.CheckedChangeEventArg`参数的一部分传递给事件处理程序。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>相关链接

- [SwitchDemo (示例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/switchdemo)
- [选项卡布局教程](~/android/user-interface/layouts/tab-layout/index.md)

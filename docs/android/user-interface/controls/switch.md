---
title: 开关
description: 如何在 Xamarin.Android 应用程序中使用交换机小组件
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: ef400aaa31992b577762ad695418b865882e2e2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075745"
---
# <a name="switch"></a>开关

`Switch`小组件 （如下所示） 允许用户以两个状态，例如在之间切换或禁用。 `Switch`默认值为 OFF。 小组件是在其 ON 和 OFF 状态如下所示：

[![关闭和打开状态中的开关小组件的屏幕快照](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>创建交换机

若要创建一个开关，只需声明`Switch`XML 中的元素，如下所示：

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

这会创建一个基本的开关，如下所示：

[![显示一个开关处于 OFF 状态的演示应用的屏幕截图](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>更改默认值

可配置该控件将显示为 ON 和 OFF 状态的文本和默认值。 例如，若要使开关默认为 ON 和读取而不是 OFF/ON 否 / 是，我们可以设置`checked`， `textOn`，和`textOff`以下 XML 中的属性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>提供一个标题

`Switch`小组件还支持通过设置包括的文本标签`text`属性，如下所示：

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

此标记将生成下面的屏幕截图在运行时：

[![使用文本水平前面交换机小组件的演示应用程序的屏幕截图](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

当`Switch`的值发生更改，它会发出`CheckedChange`事件。
例如，下面的代码中我们捕获此事件并提供`Toast`使用一条消息的小组件基于`isChecked`的值`Switch`，它作为的一部分传递给事件处理程序`CompoundButton.CheckedChangeEventArg`参数。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>相关链接

- [SwitchDemo （示例）](https://developer.xamarin.com/samples/monodroid/SwitchDemo/)
- [选项卡布局教程](~/android/user-interface/layouts/tab-layout/index.md)

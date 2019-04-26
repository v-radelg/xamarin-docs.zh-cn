---
title: RatingBar
description: 如何将 RatingBar 小组件添加到 Android 活动。
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 97d2a126be70e210d2e8f4ebf4d7a25ff8777a02
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945434"
---
# <a name="ratingbar"></a>RatingBar

RatingBar 是介于 1 到 5 星的分级将显示的 UI 小组件。 用户可能通过点按的星形本部分中选择某一等级，你将创建一个小组件，允许用户向某一评级，提供[ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)小组件。

![RatingBar 的示例](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>创建 RatingBar

1. 打开**Resource/layout/Main.axml**文件，并添加 [`RatingBar`](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)
   元素 (内[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   `android:numStars`属性定义了多少星的评级栏显示。 `android:stepSize`属性定义每个星型的粒度 (例如，值为`0.5`将允许半颗星评级)。

2. 若要设置新的分级时可以执行一些操作，请将以下代码添加到末尾 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
   方法：

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    这会将捕获[ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)小组件从与布局[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/)并设置事件方法，然后定义用户设置分级时要执行的操作。 在这种情况下，一个简单[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)消息将显示新的分级。

3.  运行该应用程序。


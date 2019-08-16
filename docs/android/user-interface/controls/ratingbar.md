---
title: Xamarin RatingBar
description: 如何将 RatingBar 小组件添加到 Android 活动。
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 729daef1a7a003613bebc4f82067bc267dab8e00
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69522842"
---
# <a name="xamarinandroid-ratingbar"></a>Xamarin RatingBar

RatingBar 是一个 UI 小组件, 用于显示一到五个星的分级。 用户可在本部分中的星形上选择分级轻按, 你将创建一个小组件, 使用户能够使用[`RatingBar`](xref:Android.Widget.RatingBar)小组件提供评级。

![RatingBar 的示例](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>创建 RatingBar

1. 打开**Resource/layout/main.axml**文件并添加[`RatingBar`](xref:Android.Widget.RatingBar)
   元素 (在中[`LinearLayout`](xref:Android.Widget.LinearLayout)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   `android:numStars`特性定义要为分级栏显示多少星。 属性定义每个星号的粒度 (例如, `0.5`值将允许使用半星级别)。 `android:stepSize`

2. 若要在设置新分级时执行某些操作, 请将以下代码添加到[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   付款方式

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    这会从[`RatingBar`](xref:Android.Widget.RatingBar) [`FindViewById`](xref:Android.App.Activity.FindViewById*)布局中捕获小组件, 然后设置事件方法, 并定义在用户设置分级时要执行的操作。 在这种情况下, [`Toast`](xref:Android.Widget.Toast)简单消息会显示新级别。

3. 运行该应用程序。


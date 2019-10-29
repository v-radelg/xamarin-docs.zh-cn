---
title: Xamarin RatingBar
description: 如何将 RatingBar 小组件添加到 Android 活动。
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 529fecb4e24e83ef7b783815843e132347d99262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029144"
---
# <a name="xamarinandroid-ratingbar"></a>Xamarin RatingBar

RatingBar 是一个 UI 小组件，用于显示一到五个星的分级。 用户可在本部分中的星形上选择分级轻按，你将创建一个小组件，以允许用户使用[`RatingBar`](xref:Android.Widget.RatingBar)小组件提供评级。

![RatingBar 的示例](ratingbar-images/01-ratingbar.png)

## <a name="creating-a-ratingbar"></a>创建 RatingBar

1. 打开**Resource/layout/main.axml**文件并添加[`RatingBar`](xref:Android.Widget.RatingBar)
   元素（在[`LinearLayout`](xref:Android.Widget.LinearLayout)内）：

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   `android:numStars` 属性定义要为分级栏显示多少星。 `android:stepSize` 属性定义每个星形的粒度（例如，值 `0.5` 将允许使用半星级别）。

2. 若要在设置新分级时执行某些操作，请将以下代码添加到[`OnCreate()`](xref:Android.App.Activity.OnCreate*)的末尾
   付款方式

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    这会从具有[`FindViewById`](xref:Android.App.Activity.FindViewById*)的布局捕获[`RatingBar`](xref:Android.Widget.RatingBar)小组件，然后设置事件方法，并定义在用户设置分级时要执行的操作。 在这种情况下，简单的[`Toast`](xref:Android.Widget.Toast)消息会显示新级别。

3. 运行该应用程序。

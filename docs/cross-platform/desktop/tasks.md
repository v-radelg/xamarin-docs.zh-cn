---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常见的任务比较
description: 本文档会比较如何在 WPF 与 Xamarin.Forms 上执行各种常见任务。 它将查看按钮、 计时器、 字体大小，打开一个 URI，并显示操作工作表。
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: 2991e74ec59e3c43c665941706c2d9ac94bfb9ad
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269590"
---
# <a name="common-tasks-comparison"></a>常见的任务比较

| 任务 | WPF | Xamarin.Forms |
|--- |--- |--- |
|使用按钮在屏幕上显示一条消息|`MessageBox`|`Page.DisplayAlert`|
|创建计时器|`DispatcherTimer` 类|`Device.StartTimer` 静态方法|
|获取默认字体大小|`SystemFonts` 静态类|`Device.GetNamedSize` 静态方法|
|打开 URI/URL|`Process.Start`|`Device.OpenUri`|
|显示操作工作表 （列表的按钮）|n/a|`Page.DisplayActionSheet`|

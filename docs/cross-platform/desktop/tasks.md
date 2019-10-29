---
ms.assetid: 0B45BF03-145B-43B2-AFD9-5A0EAB1E63A9
title: 常见任务比较
description: 本文档比较了如何在 WPF 和 Xamarin 上执行各种常见任务。 它查看按钮、计时器、字体大小、打开 URI 以及显示操作表。
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: d1f1430d8044f94c17a19d747334b5ed8a1441cf
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016421"
---
# <a name="common-tasks-comparison"></a>常见任务比较

| 任务 | WPF | Xamarin.Forms |
|--- |--- |--- |
|使用按钮在屏幕上显示一条消息|`MessageBox`|`Page.DisplayAlert`|
|创建计时器|`DispatcherTimer` 类|`Device.StartTimer` 静态方法|
|获取默认字体大小|`SystemFonts` 静态类|`Device.GetNamedSize` 静态方法|
|打开 URI/URL|`Process.Start`|`Device.OpenUri`|
|显示操作表（按钮列表）|不可用|`Page.DisplayActionSheet`|

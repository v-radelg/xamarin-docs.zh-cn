---
title: Xamarin.Forms 效果
description: 凭借效果，无需进行自定义呈现器实现，即可自定义每个平台上的本机控件。
ms.prod: xamarin
ms.assetid: 8AF168A7-4CD9-4603-B961-15B8B1543784
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/01/2017
ms.openlocfilehash: 2de1d1dd065a01bb457ebf03acdc0c01529abf7b
ms.sourcegitcommit: 1242d32b7f072c837005cdee174abe6c0d1d0c68
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73083836"
---
# <a name="xamarinforms-effects"></a>Xamarin.Forms 效果

Xamarin.Forms 使用目标平台的本机控件呈现用户界面，从而让 Xamarin.Forms 应用程序为每个平台保留了相应的界面外观。  凭借效果，无需进行自定义呈现器实现，即可自定义每个平台上的本机控件。

## <a name="introduction-to-effectsintroductionmd"></a>[效果简介](introduction.md)

借助效果可对每个平台上的本机控件进行自定义，通常用于细微的样式更改。 本文对效果进行了介绍，概述了效果和自定义呈现器之间的区别，并介绍了 `PlatformEffect` 类。

## <a name="creating-an-effectcreatingmd"></a>[创建效果](creating.md)

效果简化了控件的自定义。 本文演示如何创建效果，在指向 [`Entry`](xref:Xamarin.Forms.Entry) 控件时更改其背景颜色。

## <a name="passing-parameters-to-an-effectpassing-parametersindexmd"></a>[将参数传递给效果](passing-parameters/index.md)

创建效果并通过参数进行配置便可重复使用该效果。 这些文章演示如何使用属性将参数传递给效果，以及如何在运行时更改参数。

## <a name="invoking-events-from-an-effecttouch-trackingmd"></a>[从效果调用事件](touch-tracking.md)

效果可以调用事件。 本文介绍如何创建下述事件，该事件可实现低级别多点触控手指跟踪并能以触控点压、移动和释放的形式向应用程序发出相应信号。

## <a name="reusable-roundeffectreusable-roundeffectmd"></a>[可重用 RoundEffect](reusable-roundeffect.md)

RoundEffect 是一种可重用的效果，可应用于任何从 VisualElement 派生的控件，以将控件呈现为圆形。 此效果可用于创建圆形图像、圆形按钮和其他圆形控件。

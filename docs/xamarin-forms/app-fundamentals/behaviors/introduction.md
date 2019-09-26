---
title: 行为简介
description: 通过行为可将功能添加到用户界面控件，且无需将其子类化。 功能是在行为类中实现的，并附加到控件上，就像它本身就是控件的一部分。 本文介绍行为。
ms.prod: xamarin
ms.assetid: 0DF1EF8C-A212-4142-A3C6-DF760A82A757
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: d62ba6b025b2fe9865df8279a5e98eba254bb5a2
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "70772052"
---
# <a name="introduction-to-behaviors"></a>行为简介

通过行为可将功能添加到用户界面控件，且无需将其子类化。功能是在行为类中实现的，并附加到控件上，就像它本身就是控件的一部分。本文介绍行为。_

行为使开发人员可以实现那些通常必须以代码隐藏形式编写的代码，因为它直接与控件的 API 进行交互，这样便可简洁地将其附加到控件，并打包以便跨多个应用程序重用。 它们可以用于为控件提供一系列完整的功能，例如：

- 向 [`Entry`](xref:Xamarin.Forms.Entry) 添加电子邮件验证程序。
- 使用点击手势识别器创建评分控件。
- 控制动画。
- 向控件添加效果。

行为还启用了更高级的方案。 在命令的上下文中，行为是一种很有用的方法，能将控件与命令连接起来  。 此外，它们还可用于将命令与非旨在与命令交互的控件相关联。 例如，可以使用它们调用命令来响应事件触发。

Xamarin.Forms 支持以下两种不同风格的行为：

- **Xamarin.Forms 行为** - 从 [`Behavior`](xref:Xamarin.Forms.Behavior) 或 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 类派生的类，其中 `T` 是应用该行为的控件的类型。 有关 Xamarin.Forms 行为的更多信息，请参阅 [Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)和[可重用行为](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md)。
- **附加行为** - 具有一个或多个附加属性的 `static` 类。 有关附加行为的详细信息，请参阅[附加行为](~/xamarin-forms/app-fundamentals/behaviors/attached.md)。

本指南重点介绍 Xamarin.Forms 行为，因为它们是行为构造的首选方法。

## <a name="related-links"></a>相关链接

- [行为](xref:Xamarin.Forms.Behavior)
- [行为&lt;&gt;](xref:Xamarin.Forms.Behavior`1)

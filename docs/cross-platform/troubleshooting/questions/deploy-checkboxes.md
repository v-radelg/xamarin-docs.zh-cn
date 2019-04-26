---
title: “部署”复选框在 Configuration Manager 中处于禁用状态
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: asb3993
ms.author: amburns
ms.date: 12/02/2016
ms.openlocfilehash: 35efb00a721062ad3217300f7e3a5430b1bd1560
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357737"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>“部署”复选框在 Configuration Manager 中处于禁用状态

从 Xamarin 3.5 起，无论何时按“开始”工具栏按钮或选择“调试”>“开始调试”菜单项，Xamarin.iOS 项目都会自动进行部署。 您将仍需要设置为所需的 Xamarin.iOS 应用程序项目**启动项目**之前运行的这些命令。

因此，会在 Visual Studio Configuration Manager for Xamarin.iOS 项目中故意禁用“部署”复选框：

![](deploy-checkboxes-images/configuration.png "Visual Studio 配置管理器显示禁用 Xamarin 3.5 中 Xamarin.iOS 项目的部署复选框")

此更改可消除在较旧版本的 Xamarin（版本 3.3 及更早版本）中，当 Xamarin.iOS 应用项目尚未设为待部署时，可能出现的错误：

![](deploy-checkboxes-images/error.png "错误对话框：项目 iphoneapp1，然后需要才能启动部署。验证选择了项目要部署解决方案配置管理器中。")

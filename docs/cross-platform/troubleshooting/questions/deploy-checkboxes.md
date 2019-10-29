---
title: “部署”复选框在 Configuration Manager 中处于禁用状态
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: aaf675cd-d885-4dac-9754-77dbcaea3be9
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: edf471f1d9a2ee4adc11f09e0c7b7ad3cf6f78f1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014244"
---
# <a name="deploy-checkboxes-disabled-in-configuration-manager"></a>“部署”复选框在 Configuration Manager 中处于禁用状态

从 Xamarin 3.5 起，无论何时按“开始”工具栏按钮或选择“调试”>“开始调试”菜单项，Xamarin.iOS 项目都会自动进行部署。 在运行上述任一命令之前，仍需要将所需的 Xamarin 应用项目设置为**启动项目**。

因此，会在 Visual Studio Configuration Manager for Xamarin.iOS 项目中故意禁用“部署”复选框：

![](deploy-checkboxes-images/configuration.png "Visual Studio Configuration Manager showing the 'Deploy' checkbox disabled for a Xamarin.iOS project in Xamarin 3.5")

此更改可消除在较旧版本的 Xamarin（版本 3.3 及更早版本）中，当 Xamarin.iOS 应用项目尚未设为待部署时，可能出现的错误：

![](deploy-checkboxes-images/error.png "Error dialog: The project iPhoneApp1 needs to be deployed before it can be started. Verify the project is selected to be deployed in the Solution Configuration Manager.")

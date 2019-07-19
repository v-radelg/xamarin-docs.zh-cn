---
title: Xamarin 防火墙配置说明
description: 本文档提供了在防火墙中允许 Xamarin 在企业环境中工作的主机列表。
ms.prod: xamarin
ms.assetid: 658f699b-8cca-48f7-ae54-fa956384b6d6
author: asb3993
ms.author: amburns
ms.date: 07/17/2019
ms.openlocfilehash: b2509bb99a5caf5b508d34aafc0f0c4862a7e563
ms.sourcegitcommit: 9a2a21974d35353c3765eb683ef2fd7161c1d94a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68329952"
---
# <a name="xamarin-firewall-configuration-instructions"></a>Xamarin 防火墙配置说明

_需要在防火墙中允许 Xamarin 的平台为你的公司工作的主机列表。_

为了使 Xamarin 产品正常安装和工作，必须有权访问某些终结点，下载软件必需的工具和更新。 如果你或你公司的防火墙设置严苛，则可能遇到安装、授权和组件等方面的问题。 本文档概述了需要在防火墙中允许用于 Xamarin 的一些已知终结点。 本列表不包括下载文件中的任何第三方工具所需的终结点。 如果仔细检查此列表后仍遇到问题，请参阅 Apple 或 Android 安装疑难解答指南。

## <a name="endpoints-to-allow"></a>允许的终结点

### <a name="xamarin-installer"></a>Xamarin 安装程序

需要添加以下已知地址，以便使用 Xamarin 安装程序的最新版本时正常安装软件：

- xamarin.com（安装程序清单）
- dl.xamarin.com（包下载位置）
- dl.google.com（用于下载 Android SDK）
- download.oracle.com (JDK)
- visualstudio.com（安装包下载位置）
- go.microsoft.com（安装程序 URL 解析）
- aka.ms（安装程序 URL 解析）

如果正使用 Mac，并且遇到 Xamarin.Android 安装问题，请确保 macOS 能够下载 Java。

### <a name="nuget-including-xamarinforms"></a>NuGet（包括 Xamarin.Forms）

需要添加以下地址才可访问 NuGet（Xamarin.Forms 打包为 NuGet）：

- www.nuget.org（用于访问 NuGet）
- globalcdn.nuget.org (NuGet 下载)
- dl-ssl.google.com（适用于 Android 和 Xamarin.Forms 的 Google 组件）

### <a name="software-updates"></a>软件更新

需要添加以下地址，确保可正常下载该软件：

- software.xamarin.com（更新程序服务）
- download.visualstudio.microsoft.com
- dl.xamarin.com

## <a name="xamarin-mac-agent"></a>Xamarin Mac 代理

若要使用 Xamarin Mac 代理将 Visual Studio 连接到 Mac 生成主机， 需要打开 SSH 端口。 该端口默认为**端口 22**。

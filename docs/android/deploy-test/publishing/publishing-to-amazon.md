---
title: 发布到 Amazon 应用商店
ms.prod: xamarin
ms.assetid: A3E9EAC7-2968-8891-CDF2-B73FC0013EC9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 818b6afef395b081768f8bdd6538d5a8ecbad178
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753744"
---
# <a name="publishing-to-the-amazon-app-store"></a>发布到 Amazon 应用商店

通过 Amazon 移动应用分发程序，移动应用开发人员可在 Amazon 上发布应用程序。 本部分简要介绍用于 Android 的 Amazon 应用商店。 

[![“Amazon 应用商店”屏幕](publishing-to-amazon-images/amazon-app-store.png)](publishing-to-amazon-images/amazon-app-store.png#lightbox)

Amazon 不限制 APK 的大小。 但是，如果 APK 大于 30 MB，则会使用 FTP 进行分发，而不是 Amazon 移动应用分发门户。

## <a name="submitting-apps-binary-info"></a>提交应用：二进制信息

将应用程序提交到 Amazon 应用商店的过程与将应用程序提交到 Google Play 的过程类似。 通过 Amazon 分发的应用程序需要以下资产： 

- **图标** &ndash; 这是一个带有透明背景的 114 x 114 .png 文件。 这是必需项。
- **缩略图** &ndash; 这是上述图标的更大版本。 其像素为 512 x 512，透明背景。 此图标也是必需项。
- **屏幕截图** &ndash; Amazon 需要至少 3 个、最多 10 个屏幕截图。 屏幕截图必须为 1024w x 600h 像素或 800w x 480h 像素。 支持 .png 和 .jpg 两种格式。
- **促销图像** &ndash; 为在促销位置（如主页）上推出应用程序，可能需要提交促销图像（可选）。 该图像应为横向放置的 .png 或 .jpg 文件，像素为 1024w x 500h。 可能没有任何动画。
- 可能提供 5 个视频的更新内容。

## <a name="approval-process"></a>审批过程

应用程序提交后，需经过审批过程。
Amazon 将审查应用程序，确保其具有产品说明所述的性能，不会将客户数据置于危险之中，且不会影响设备操作。 审批过程完成后，Amazon 将发出通知并分发应用程序。

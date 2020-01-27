---
ms.openlocfilehash: 338b03ae5e52b06c6ddc225b418ee2bc7d5e5ffc
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "71107182"
---
开始演练本教程的前提条件为已成功完成以下教程：

- [生成第一个 Xamarin.Forms 应用](~/get-started/first-app/index.md)快速入门。
- [网格](~/get-started/tutorials/grid/index.yml)教程。
- [标签](~/get-started/tutorials/label/index.yml)教程。
- [按钮](~/get-started/tutorials/button/index.yml)教程。
- [输入](~/get-started/tutorials/entry/index.yml)教程。

在本教程中，你将了解：

> [!div class="checklist"]
>
> - 使用 NuGet 包管理器将 Newtonsoft.Json 添加到 Xamarin.Forms 项目。
> - 创建 Web 服务类。
> - 使用 Web 服务类。

你将使用 Visual Studio 2019 或 Visual Studio for Mac 创建一个简单的应用程序，演示如何从 [OpenWeatherMap](https://openweathermap.org/) Web 服务中检索数据。 以下屏幕截图显示了最终的应用程序：

[![iOS 和 Android 上西雅图天气数据的屏幕截图](../images/consume-web-service.png "西雅图天气数据")](../images/consume-web-service-large.png#lightbox "西雅图天气数据")

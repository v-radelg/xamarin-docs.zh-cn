---
title: Xamarin.Forms DataPages
description: 本文介绍了 Xamarin.Forms DataPages，它提供到 API 快速且轻松地将数据源绑定到预建的视图。
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 2a74b636a41a72b26776157a774f0a33ef45a075
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61407664"
---
# <a name="xamarinforms-datapages"></a>Xamarin.Forms DataPages

![](~/media/shared/preview.png "此 API 目前处于预览状态")

> [!IMPORTANT]
> 需要 DataPages [Xamarin.Forms 主题](~/xamarin-forms/user-interface/themes/index.md)引用来呈现。

Xamarin.Forms DataPages 曾宣布在 Evolve 2016，并可作为客户试用并提供反馈的预览。

DataPages 提供一个 API，用于快速、 轻松地将数据源绑定到预建的视图。 列表项和详细信息页将自动呈现数据，并可以使用主题自定义。

若要查看发展主题演讲演示的工作原理，请查看[入门指南](get-started.md)。

[![](images/demo-sml.png "DataPages 示例应用程序")](images/demo.png#lightbox "DataPages 示例应用程序")

## <a name="introduction"></a>介绍

数据源和关联的数据页允许开发人员快速、 轻松地使用支持的数据源，并使其与主题中使用的内置基架，UI 可以自定义。

通过包括 DataPages 添加到 Xamarin.Forms 应用程序**Xamarin.Forms.Pages** Nuget 包。

### <a name="data-sources"></a>Data Sources

在预览具有可供使用一些预构建的数据源：

* **JsonDataSource**
* **AzureDataSource** （单独的 Nuget）
* **AzureEasyTableDataSource** （单独的 Nuget）

请参阅[入门指南](get-started.md)示例使用`JsonDataSource`。


### <a name="pages--controls"></a>页和控件

以下页面和控件都包括在内，以便允许轻松绑定到提供的数据源：

* **ListDataPage** – 请参阅[入门示例](get-started.md)。
* **DirectoryPage** – 已启用分组列表。
* **PersonDetailPage** – 单个数据项为特定对象类型 （联系人项） 自定义的视图。
* **DataView** – 公开从采用一般形式的源数据的视图。
* **CardView** – 样式视图，它包含图像、 标题文本和说明文本。
* **HeroImage** – 图像呈现视图。
* **ListItem** – 预构建的具有类似于本机 iOS 和 Android 的列表项的布局视图。

请参阅[DataPages 控件参考](controls.md)有关示例。



### <a name="under-the-hood"></a>揭秘

Xamarin.Forms 数据源遵守`IDataSource`接口。

Xamarin.Forms 基础结构进行交互与数据源通过以下属性：

* `Data` – 可以显示的数据项的只读列表。
* `IsLoading` – 一个布尔值，该值指示数据是否已加载并可用于呈现。
* `[key]` – 要从中检索元素的索引器。

有两种方法`MaskKey`和`UnmaskKey`可用来隐藏 （或显示） （即数据项目属性 防止它们呈现）。
键对应于数据项对象上的已命名的属性。

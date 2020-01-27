---
title: Xamarin. Forms DataPages
description: 本文介绍了 Xamarin. Forms DataPages，它提供了一个 API，可用于快速轻松地将数据源绑定到预先生成的视图。
ms.prod: xamarin
ms.assetid: DF16EAEE-DB78-42CA-9C59-51D9D6CB6B95
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 4569240d7419254bad41957d30b4ad652c7a3f4e
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "75727935"
---
# <a name="xamarinforms-datapages"></a>Xamarin. Forms DataPages

![](~/media/shared/preview.png "This API is currently in preview")

> [!IMPORTANT]
> DataPages 要求使用 Xamarin. Forms 主题引用来呈现。 这涉及到将[xamarin. Base](https://www.nuget.org/packages/Xamarin.Forms.Theme.Base/) nuget 包安装到项目中，然后将其后跟[xamarin](https://www.nuget.org/packages/Xamarin.Forms.Theme.Light/)或[xamarin. 暗体](https://www.nuget.org/packages/Xamarin.Forms.Theme.Dark/)nuget 包。

Xamarin DataPages 于2016年推出，并以预览版的形式提供，供客户试用并提供反馈。

DataPages 提供一个 API，用于快速、 轻松地将数据源绑定到预建的视图。 列表项和详细信息页将自动呈现数据，并可使用主题进行自定义。

若要查看演化的主题演示如何工作，请查看[入门指南](get-started.md)。

[![](images/demo-sml.png "DataPages Sample Application")](images/demo.png#lightbox "DataPages Sample Application")

## <a name="introduction"></a>소개

数据源和关联的数据页使开发人员能够快速轻松地使用受支持的数据源，并使用可使用主题自定义的内置 UI 基架对其进行呈现。

通过包括**Xamarin** DataPages NuGet 包，将其添加到 xamarin 应用程序应用程序。

### <a name="data-sources"></a>데이터 원본

预览版有一些可供使用的预生成数据源：

* **JsonDataSource**
* **AzureDataSource** （单独 NuGet）
* **AzureEasyTableDataSource** （单独 NuGet）

有关使用 `JsonDataSource`的示例，请参阅[入门指南](get-started.md)。

### <a name="pages--controls"></a>页面 & 控件

包括以下页面和控件，以便轻松绑定到提供的数据源：

* **ListDataPage** –请参阅[入门示例](get-started.md)。
* **DirectoryPage** –启用了分组的列表。
* **PersonDetailPage** –针对特定对象类型（联系人项）自定义的单个数据项视图。
* **DataView** –用于以一般方式从源公开数据的视图。
* **CardView** –包含图像、标题文本和说明文本的样式视图。
* **HeroImage** -图像呈现视图。
* 列表 **-预**建的视图，其布局类似于本机 IOS 和 Android 列表项。

有关示例，请参阅[DataPages 控件参考](controls.md)。

### <a name="under-the-hood"></a>在后台

Xamarin. Forms 数据源符合 `IDataSource` 接口。

Xamarin 基础结构通过以下属性与数据源进行交互：

* `Data` –可显示的数据项的只读列表。
* `IsLoading` –一个布尔值，该值指示数据是否已加载并可用于呈现。
* `[key]` –用于检索元素的索引器。

可以使用两种方法 `MaskKey` 和 `UnmaskKey` 来隐藏（或显示）数据项属性（即 阻止其呈现）。
键对应于数据项对象的命名属性。

---
title: Xamarin. Forms 和 Azure 认知服务简介
description: 本文介绍了一个示例应用程序，演示如何调用某些 Microsoft 认知服务 Api。
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 52774b387644b14e3d4612dffa6d3c3b28a37f25
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "68652316"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin. Forms 和 Azure 认知服务简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft 认知服务是一组可供开发人员使用的 Api、Sdk 和服务，通过添加面部识别、语音识别和语言理解等功能，使应用程序更智能。本文介绍了演示如何调用某些 Microsoft 认知服务 Api 的示例应用程序。_

## <a name="overview"></a>概述

随附的示例是一个待办事项列表应用程序，它提供以下功能：

- 查看任务的列表。
- 通过软键盘添加和编辑任务，或通过使用 Microsoft Speech API 执行语音识别。 有关执行语音识别的详细信息，请参阅[使用 Microsoft SPEECH API 的语音识别](speech-recognition.md)。
- 使用必应拼写检查 API 对任务进行拼写检查。 有关详细信息，请参阅[使用必应拼写检查 API 的拼写检查](spell-check.md)。
- 使用转换器 API 将任务从英语转换为德语。 有关详细信息，请参阅[使用转换器 API 进行文本翻译](text-translation.md)。
- 删除任务。
- 将任务状态设置为 "已完成"。
- 使用人脸 API，使用情感识别对应用程序进行评级。 有关详细信息，请参阅[使用人脸 API 的情感识别](emotion-recognition.md)。

任务存储在本地 SQLite 数据库中。 有关使用本地 SQLite 数据库的详细信息，请参阅使用[本地数据库](~/xamarin-forms/data-cloud/data/databases.md)。

当应用程序启动时，将显示 `TodoListPage`。 此页显示存储在本地数据库中的所有任务的列表，并允许用户创建新任务或对应用程序进行评分：

![](introduction-images/sample-application-1.png "TodoListPage")

可以通过单击导航到 `TodoItemPage` 的 " *+* " 按钮来创建新项目。 还可以通过选择任务导航到此页面：

![](introduction-images/sample-application-2.png "TodoItemPage")

@No__t_0 允许创建、编辑、拼写检查、转换、保存和删除任务。 语音识别可用于创建或编辑任务。 这是通过以下方式实现的：按下麦克风按钮开始录制，并再次按下相同按钮以停止录制，这会将录制发送到必应语音识别 API。

单击 `TodoListPage` 上的 "smilies" 按钮将导航到 `RateAppPage`，用于在面部表达式的图像上执行情感识别：

![](introduction-images/sample-application-3.png "RateAppPage")

该 `RateAppPage` 允许用户拍摄人脸照片，并将其提交到人脸 API 并显示返回的情感。

## <a name="understand-the-application-anatomy"></a>了解应用程序解析

示例应用程序的共享代码项目包含五个主要文件夹：

|文件夹|目标|
|--- |--- |
|模型|包含应用程序的数据模型类。 这包括 `TodoItem` 类，该类对应用程序使用的单个数据项建模。 此文件夹还包括用于对从不同 Microsoft 认知服务 Api 返回的 JSON 响应建模的类。|
|储存|包含用于执行数据库操作的 `ITodoItemRepository` 接口和 `TodoItemRepository` 类。|
|Services|包含用于访问不同 Microsoft 认知服务 Api 的接口和类，以及 `DependencyService` 类在平台项目中实现接口的类所使用的接口。|
|utils|包含 `Timer` 类，该类由 `AuthenticationService` 类用于每9分钟续订一次 JWT 访问令牌。|
|Views|包含应用程序的页面。|

共享代码项目还包含一些重要文件：

|文件|目标|
|--- |--- |
|Constants.cs|@No__t_0 类，它为调用的 Microsoft 认知服务 Api 指定 API 密钥和终结点。 API 密钥常量需要更新才能访问不同的认知服务 Api。|
|App.xaml.cs|@No__t_0 类负责将应用程序将在每个平台上显示的第一页和用于调用数据库操作的 `TodoManager` 类进行实例化。|

### <a name="nuget-packages"></a>NuGet 包

示例应用程序使用以下 NuGet 包：

- `Newtonsoft.Json` –提供适用于 .NET 的 JSON 框架。
- `PCLStorage` –提供一组跨平台本地文件 IO Api。
- `sqlite-net-pcl` –提供 SQLite 数据库存储。
- `Xam.Plugin.Media` –提供跨平台的照片拍摄和挑选 Api。

此外，这些 NuGet 包还会安装其自己的依赖项。

### <a name="model-the-data"></a>为数据建模

示例应用程序使用 `TodoItem` 类对在本地 SQLite 数据库中显示和存储的数据进行建模。 以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

@No__t_0 属性用于唯一标识每个 `TodoItem` 实例，并使用 SQLite 特性修饰，使该属性成为数据库中自动递增的主键。

### <a name="invoke-database-operations"></a>调用数据库操作

@No__t_0 类实现数据库操作，可以通过 `App.TodoManager` 属性访问类的实例。 @No__t_0 类提供以下方法来调用数据库操作：

- **GetAllItemsAsync** –检索本地 SQLite 数据库中的所有项目。
- **GetItemAsync** –从本地 SQLite 数据库中检索指定的项。
- **SaveItemAsync** –创建或更新本地 SQLite 数据库中的项。
- **DeleteItemAsync** –从本地 SQLite 数据库中删除指定项。

### <a name="platform-project-implementations"></a>平台项目实现

共享代码项目中的 "`Services`" 文件夹包含 `DependencyService` 类用于在平台项目中实现接口的类的 `IFileHelper` 和 `IAudioRecorderService` 接口。

@No__t_0 接口由每个平台项目中的 `FileHelper` 类实现。 此类由单个方法（`GetLocalFilePath`）组成，该方法返回存储 SQLite 数据库的本地文件路径。

@No__t_0 接口由每个平台项目中的 `AudioRecorderService` 类实现。 此类包含 `StartRecording`、`StopRecording` 和支持方法，这些方法使用平台 Api 从设备的麦克风记录音频，并将其存储为 wav 文件。 在 iOS 上，`AudioRecorderService` 使用 `AVFoundation` API 来记录音频。 在 Android 上，`AudioRecordService` 使用 `AudioRecord` API 来记录音频。 在通用 Windows 平台（UWP）上，`AudioRecorderService` 使用 `AudioGraph` API 来记录音频。

### <a name="invoke-cognitive-services"></a>调用认知服务

示例应用程序调用以下 Microsoft 认知服务：

- Microsoft 语音 API。 有关详细信息，请参阅[使用 Microsoft SPEECH API 的语音识别](speech-recognition.md)。
- 必应拼写检查 API。 有关详细信息，请参阅[使用必应拼写检查 API 的拼写检查](spell-check.md)。
- 转换 API。 有关详细信息，请参阅[使用转换器 API 进行文本翻译](text-translation.md)。
- 人脸 API。 有关详细信息，请参阅[使用人脸 API 的情感识别](emotion-recognition.md)。

## <a name="related-links"></a>相关链接

- [Microsoft 认知服务文档](https://www.microsoft.com/cognitive-services/documentation)
- [Todo 认知服务（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

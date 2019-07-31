---
title: Xamarin. Forms 和 Azure 认知服务简介
description: 本文演示如何调用的一些 Microsoft 认知服务 Api 的示例应用程序的简介。
ms.prod: xamarin
ms.assetid: 74121ADB-1322-4C1E-A103-F37257BC7CB0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/08/2017
ms.openlocfilehash: 52774b387644b14e3d4612dffa6d3c3b28a37f25
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652316"
---
# <a name="xamarinforms-and-azure-cognitive-services-introduction"></a>Xamarin. Forms 和 Azure 认知服务简介

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

_Microsoft 认知服务是一组 Api、 Sdk 和适用于开发人员可以通过添加面部识别、 语音识别和语言理解等功能，使其应用程序更智能服务。本文演示如何调用的一些 Microsoft 认知服务 Api 的示例应用程序的简介。_

## <a name="overview"></a>概述

随附的示例是 todo 列表应用程序，提供了以下功能：

- 查看任务的列表。
- 添加和编辑任务通过软键盘，或通过执行语音识别与 Microsoft 语音 API。 有关执行语音识别的详细信息，请参阅[使用 Microsoft 语音 API 的语音识别](speech-recognition.md)。
- 拼写检查任务使用必应拼写检查 API。 有关详细信息，请参阅[拼写检查功能使用必应拼写检查 API](spell-check.md)。
- 转换从英语为德语使用 Translator API 的任务。 有关详细信息，请参阅[使用 Translator API 的文本翻译](text-translation.md)。
- 删除任务。
- 设置任务的状态设置为完成。
- 通过情绪识别，使用人脸 API 应用程序评级。 有关详细信息，请参阅[使用人脸 API 的情感识别](emotion-recognition.md)。

任务存储在本地 SQLite 数据库。 有关使用本地 SQLite 数据库的详细信息，请参阅[使用本地数据库](~/xamarin-forms/data-cloud/data/databases.md)。

`TodoListPage`应用程序启动时显示。 此页面显示本地数据库中存储的任何任务的列表，并使用户可以创建新的任务或应用程序评级：

![](introduction-images/sample-application-1.png "TodoListPage")

可以通过单击创建新项 *+* 按钮，导航到`TodoItemPage`。 此页还可通过选择一项任务定位：

![](introduction-images/sample-application-2.png "TodoItemPage")

`TodoItemPage`转换、 保存，并且删除使得任务可以创建、 编辑、 拼写检查。 语音识别可以用于创建或编辑任务。 这被实现通过按麦克风按钮以开始记录，并通过第二次按同一按钮停止录制，后者将记录发送到必应语音识别 API。

单击 smilies 按钮`TodoListPage`导航到`RateAppPage`，用于执行情感识别面部表情的图像上：

![](introduction-images/sample-application-3.png "RateAppPage")

`RateAppPage`使用户可以拍照其人脸、 与显示返回情感提交人脸 api。

## <a name="understand-the-application-anatomy"></a>了解应用程序解析

示例应用程序的共享代码项目包含五个主要文件夹:

|文件夹|目标|
|--- |--- |
|Models|包含应用程序的数据模型类。 这包括`TodoItem`类，该类使用的应用程序数据的单个项。 该文件夹还包括用于对模型从不同的 Microsoft 认知服务 Api 返回的 JSON 响应的类。|
|存储库|包含`ITodoItemRepository`接口和`TodoItemRepository`类，用于执行数据库操作。|
|Services|包含的接口和用于访问不同 Microsoft 认知服务 Api，并使用的接口的类`DependencyService`类用来定位在平台项目中实现接口的类。|
|Utils|包含`Timer`类，该类用于通过`AuthenticationService`类，以续订时间间隔为 9 分钟的 JWT 访问令牌。|
|Views|包含应用程序页。|

共享代码项目还包含一些重要文件:

|文件|目标|
|--- |--- |
|Constants.cs|`Constants` Microsoft 认知服务 Api 的调用中指定的 API 密钥和终结点的类。 API 密钥常量需要更新访问不同的认知服务 Api。|
|App.xaml.cs|`App`类负责实例化的这两个将显示的每个平台上的应用程序的第一页和`TodoManager`类，用于调用数据库操作。|

### <a name="nuget-packages"></a>NuGet 包

示例应用程序使用以下 NuGet 包：

- `Newtonsoft.Json` – 用于.NET 提供 JSON 框架。
- `PCLStorage` -提供了一套跨平台本地文件 IO Api。
- `sqlite-net-pcl` -提供 SQLite 数据库存储。
- `Xam.Plugin.Media` -提供了跨平台照片拍摄和选择的 Api。

此外，以下 NuGet 包还安装其自己的依赖项。

### <a name="model-the-data"></a>为数据建模

示例应用程序使用`TodoItem`类显示在本地 SQLite 数据库中存储的数据建模。 以下代码示例演示 `TodoItem` 类：

```csharp
public class TodoItem
{
  [PrimaryKey, AutoIncrement]
  public int ID { get; set; }
  public string Name { get; set; }
  public bool Done { get; set; }
}
```

`ID`属性用于唯一地标识每个`TodoItem`实例，并使用在数据库中进行属性自动递增的主键的 SQLite 属性修饰。

### <a name="invoke-database-operations"></a>调用数据库操作

`TodoItemRepository`类实现数据库操作，可通过访问类的实例`App.TodoManager`属性。 `TodoItemRepository`类提供了以下方法来调用数据库操作：

- **GetAllItemsAsync** – 从本地 SQLite 数据库中检索的所有项。
- **GetItemAsync** – 从本地 SQLite 数据库中检索指定的项。
- **SaveItemAsync** – 创建或更新本地 SQLite 数据库中的项。
- **DeleteItemAsync** – 从本地 SQLite 数据库中删除指定的项。

### <a name="platform-project-implementations"></a>平台项目实现

共享`Services`代码项目中的文件夹`IFileHelper`包含`DependencyService`类使用的`IAudioRecorderService`和接口, 这些类用于在平台项目中查找实现接口的类。

`IFileHelper`接口由实现`FileHelper`每个平台项目中的类。 此类包含单个方法`GetLocalFilePath`，这会返回存储的 SQLite 数据库的本地文件路径。

`IAudioRecorderService`接口由实现`AudioRecorderService`每个平台项目中的类。 此类包含`StartRecording`， `StopRecording`，和支持性的方法，它使用来自设备的麦克风录制音频平台 Api 并将其存储为一个 wav 文件。 在 iOS 上，`AudioRecorderService`使用`AVFoundation`录制音频的 API。 在 Android 上，`AudioRecordService`使用`AudioRecord`录制音频的 API。 在通用 Windows 平台 (UWP)，`AudioRecorderService`使用`AudioGraph`录制音频的 API。

### <a name="invoke-cognitive-services"></a>调用认知服务

示例应用程序将调用以下 Microsoft 认知服务：

- Microsoft 语音 API。 有关详细信息，请参阅[使用 Microsoft 语音 API 的语音识别](speech-recognition.md)。
- 必应拼写检查 API。 有关详细信息，请参阅[拼写检查功能使用必应拼写检查 API](spell-check.md)。
- 转换 API。 有关详细信息，请参阅[使用 Translator API 的文本翻译](text-translation.md)。
- 人脸 API。 有关详细信息，请参阅[使用人脸 API 的情感识别](emotion-recognition.md)。

## <a name="related-links"></a>相关链接

- [Microsoft 认知服务文档](https://www.microsoft.com/cognitive-services/documentation)
- [Todo 认知服务 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/webservices-todocognitiveservices)

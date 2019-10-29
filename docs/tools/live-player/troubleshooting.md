---
title: 疑难解答 Xamarin Live Player
description: 本文档介绍 Xamarin Live Player 和潜在修补程序的已知问题。 它讨论了连接问题、配置问题等。
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: d51241bee5f4ddc06032006071fa8296be37f2fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005945"
---
# <a name="troubleshooting-xamarin-live-player"></a>疑难解答 Xamarin Live Player

![预览版功能](~/media/shared/preview.png)

> [!WARNING]
> Xamarin Live Player 预览已结束。 应用不再可用。 以下说明适用于继续使用 Visual Studio 2017 预览的客户。

> [!TIP]
> 你可以在 Visual Studio 2019 或 Visual Studio for Mac 中使用[XAML 预览](~/xamarin-forms/xaml/xaml-previewer/index.md)器来查看你编辑的屏幕设计。

本文介绍了实时播放机的限制，并介绍了解决问题的步骤中的一些常见问题。

## <a name="limitations-of-xamarin-live-player"></a>Xamarin Live Player 的限制

### <a name="ide-requirements"></a>IDE 要求

实时播放机预览仅适用于 Visual Studio 2017。

### <a name="device-requirements"></a>设备要求

Xamarin Live Player 应用支持以下 Android 设备：

- Android 4.2 或更高版本。
- ARM-armeabi-v7a、ARM-arm64-v8a、ARM64-arm64-v8a、x86 或 x86_64 处理器。

### <a name="ios-limitations"></a>iOS 限制

Live Player 不适用于 iOS。

### <a name="xamarinforms-limitations"></a>Xamarin。窗体限制

- 不支持自定义呈现器。
- 不支持效果。
- 不支持具有自定义可绑定属性的自定义控件。
- 不支持嵌入的资源（即，在 PCL 中嵌入图像或其他资源）。
- 不支持第三方 MVVM 框架（即Prism、Mvvm 跨、Mvvm Light 等。

### <a name="other-project-type-limitations"></a>其他项目类型限制

- Live Player 不适用于本机 Android 项目（对于用户界面使用 Android XML）。

### <a name="miscellaneous-limitations"></a>其他限制

- 对反射的有限支持（当前影响一些常用的 Nuget，如 SQLite 和 Json.NET）。 其他 Nuget 可能仍受支持。
- 不能重写某些系统类（例如，无法实现子类）。
- 某些要求预配的平台功能无法在 Xamarin Live Player 应用中工作（但已为照片库访问等常见操作配置了此功能）。
- 忽略自定义目标和生成步骤。 例如，不能将 Fody、重新调整、AutoFac 和 AutoMapper 等工具合并在一起。
- F#项目不受支持
- 可能不支持带有自定义泛型类和接口的高级方案。

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>移动设备在扫描条形码后未连接（或输入代码）

当运行 Xamarin Live Player 的移动设备不在与运行 IDE 的计算机相同的网络上时发生。 查看以下内容：

- 确认设备和计算机都在同一 Wi-fi 网络上。
  - 如果计算机还连接到有线网络，请尝试拔出有线连接。
- 网络可能会受到严格的保护（如某些企业网络），阻止 Xamarin Live Player 所需的端口。
- 关闭 Xamarin Live Player 的应用程序并重新启动它。

## <a name="error-while-trying-to-deploy-message-in-ide"></a>IDE 中的 "尝试部署时出错" 消息

**"IOException：无法从传输连接中读取数据：对非阻止套接字的操作将会阻止"**

当运行 Xamarin Live Player 的移动设备与运行 Visual Studio 的计算机不在同一网络中时，通常会出现此错误。连接到以前已成功配对的设备时通常会发生这种情况。

- 检查设备和计算机是否位于同一 Wi-fi 网络上。
- 网络可能会受到严格的保护（如某些企业网络），阻止 Xamarin Live Player 所需的端口。 Xamarin Live Player 需要以下端口：
  - 37847-内部网络访问 
  - 8090–外部网络访问

## <a name="manually-configure-device"></a>手动配置设备

如果无法通过 Wi-fi 连接到设备，可以尝试通过配置文件手动配置设备，步骤如下：

**步骤1：打开配置文件**

转到你的应用程序数据文件夹：

- Windows： **%userprofile%\AppData\Roaming**
- macOS： **~/Users/$USER/.config**

在此文件夹中，如果**PlayerDeviceList**不存在，则需要创建一个。

**步骤2：获取 IP 地址**

在 Xamarin Live Player 应用中，请参阅**关于 > 连接测试 > 开始连接测试**。

记下 IP 地址，配置设备时需要列出的 IP 地址。

**步骤3：获取配对代码**

在 Xamarin Live Player 依次点击**配对**或**配对**，然后**手动按 enter**。 将显示一个数字代码，你将需要更新配置文件。

**步骤4：生成 GUID**

请参阅： https://www.guidgenerator.com/online-guid-generator.aspx 并生成新的 guid，并确保大写为 on。

**步骤5：配置设备**

在 Visual Studio 或 Visual Studio Code 等编辑器中打开**PlayerDeviceList** 。 你需要在此文件中手动配置你的设备。 默认情况下，该文件应包含以下空的 `Devices` XML 元素：

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**添加 Android 设备：**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**关闭并重新打开 Visual Studio。** 设备应显示在列表中。

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>IDE 中的 "无法找到类型或命名空间" 消息

检查是否选择了与设备类型匹配的**启动项目**（例如 Android）和配置是否匹配该设备类型（例如 适用于 Android 的**调试**）。

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>播放器中的 "找不到" 类型的 "InterpretedXamarin" 消息

不能重写某些系统类，例如：

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs：" 不包含 "Main" 的定义

对于 MAIN.AXML 文件中定义了用户界面的 Android 项目，会发生此错误。
Xamarin Live Player 当前不支持 MAIN.AXML 文件。

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android 工具栏和选项卡使用 Xamarin 正确呈现

Xamarin。窗体 Android 项目必须使用 "main.axml" 和 "选项卡栏" 作为相关布局文件的名称。 默认模板使用这些名称;重命名它们会导致呈现问题。

## <a name="related-links"></a>相关链接

- [安装](~/tools/live-player/install.md)

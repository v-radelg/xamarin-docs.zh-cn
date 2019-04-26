---
title: Xamarin 实时重新加载 （预览版）
description: 请参阅对你的 XAML 的更改反映实时，而无需其他编译和部署。
ms.prod: xamarin
ms.assetid: 4917273d-32f9-401a-a52c-5cfb53a2170d
ms.technology: xamarin-forms
author: pierceboggan
ms.author: piboggan
robots: noindex
ms.date: 10/26/2018
ms.openlocfilehash: 21ff09f2af93ee46578b959111bf744ba05a74d7
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61384906"
---
# <a name="xamarin-live-reload-preview"></a>Xamarin 实时重新加载 （预览版）

> [!NOTE]
> Xamarin Live 重新加载预览已结束，并且我们想要感谢各位的反馈和提出的意见。 请阅读我们[路线图](https://docs.microsoft.com/visualstudio/productinfo/vs-roadmap)的 Visual Studio 2019，若要了解有关我们正在开发适用于 Xamarin.Forms 的新的工作效率功能的详细信息。 此扩展仍然可供 Visual Studio 2017，但不是会收到将来的更新。

Xamarin Live 重新加载使你能够**对你的 XAML 进行更改和查看它们反映实时，而无需其他编译并部署**。 将重新部署到你的 XAML 所做的任何更改在保存和部署目标上反映出来。

## <a name="requirements"></a>要求

* [Visual Studio 2017 版本 15.7 或更高版本](https://visualstudio.microsoft.com/vs/)与**使用.NET 的移动开发**工作负荷。
* [Xamarin.Forms 3.0.0 或更高版本](https://www.nuget.org/packages/Xamarin.Forms/)。

## <a name="getting-started"></a>入门
### <a name="1-install-xamarin-live-reload-from-the-visual-studio-marketplace"></a>1.从 Visual Studio Marketplace 安装 Xamarin 实时重新加载

通过 Visual Studio Marketplace 分发 Xamarin Live 重新加载。 若要安装扩展，请访问[Visual Studio Marketplace 上的 Xamarin Live 重新加载页面](https://marketplace.visualstudio.com/items?itemName=Xamarin.XamarinLiveReload)网站然后单击**下载**。

打开.vsix，下载后，单击**安装**。

![Visual Studio 安装程序 Xamarin Live 重新加载确认](images/LiveReloadVSIXInstall.png)

或者，您可以在其中搜索中为其**联机**选项卡**扩展和更新**在 Visual Studio 中的对话框。

### <a name="2-configure-your-app-to-use-live-reload"></a>2.将应用配置为使用实时重新加载

可以在三个步骤中将实时重新加载添加到现有的移动应用：

1. 确保所有项目都更新为使用[Xamarin.Forms 3.0.0 或更高版本](https://www.nuget.org/packages/Xamarin.Forms/)或更高版本。

2. 添加**Xamarin.LiveReload** NuGet 包：

    a. **.NET standard** – 安装**Xamarin.LiveReload**到.NET Standard 2.0 库的 NuGet。 这不需要安装在平台项目中。 絋粄**包源**设置为**所有**。
    
    b. **共享的项目**– 安装**Xamarin.LiveReload** NuGet 的所有平台项目 （如 Android、 iOS、 UWP、 支持等）。 絋粄**包源**设置为**所有**。

    [![添加 Xamarin 实时重新加载 NuGet 使用 NuGet 程序包管理器](images/addlivereloadnuget.w157-sml.png)](images/addlivereloadnuget.w157.png#lightbox)

3. 添加`LiveReload.Init();`中的构造函数为`Application`类，如以下代码片段中所示：

```csharp
public partial class App : Application
{
    public App ()
    {
        // Initialize Live Reload.
        #if DEBUG
        LiveReload.Init();
        #endif
        
        InitializeComponent();
        MainPage = new MainPage();
    }
}
```

### <a name="3-start-live-reloading"></a>3.启动实时重新加载

编译和部署你的应用程序。 在已部署应用程序后，打开 XAML 文件、 进行一些更改，并保存文件。 所做的更改是重新部署到部署目标。

> [!Video https://www.youtube.com/embed/-5WJZpeXlC8]

实时重新加载适用于任何 XAML 文件的更改。 对 C# 或添加/删除 NuGet 包的更改需要新的生成和部署才会生效。

## <a name="frequently-asked-questions"></a>常见问题 
### <a name="is-xamarin-live-reload-available-on-visual-studio-for-mac"></a>位于 Xamarin Live 重新加载 Visual Studio for Mac？ 

否，才可用于 Visual Studio 2017 预览版本的 Xamarin Live 重新加载。

### <a name="does-this-work-with-all-libraries-such-as-prism"></a>这的工作原理的所有其他库，如 Prism？ 

由于您的应用程序被编译，实时重新加载适用于所有库，如 Prism 和第三方控件库，如 Telerik、 Infragistics、 Syncfusion，ArcGIS，GrapeCity 和其他控件供应商。

### <a name="what-changes-does-live-reload-redeploy"></a>实时重新加载重新部署哪些更改？ 

实时重新加载仅应用到 XAML 或 CSS 所做的更改。 如果更改 C# 文件，将需要重新编译。 

### <a name="what-platforms-are-supported"></a>支持哪些平台？ 

实时重新加载 Xamarin.Forms，包括 Android、 iOS 和 UWP 支持任何平台上工作。

### <a name="does-this-work-on-emulators-simulators-and-physical-devices"></a>这是否适用于仿真程序、 模拟器、 和物理设备？ 

是的实时重新加载适用于所有有效的部署目标，包括 Android 仿真程序、 iOS 模拟器和物理设备。 部署到设备需要设备和计算机处于同一 Wi-fi 网络上。

### <a name="does-this-work-with-corporate-networks"></a>这是否适用于企业的网络？

如果你正在调试的 Android 模拟器或 iOS 模拟器，实时重新加载使用本地主机通信。 如果你想要部署到设备，设备和计算机需要处于同一 Wi-fi 网络上。 在其中无法做到这一点的情况下，你可以[配置实时重新加载服务器](#live-reload-server)，这使您能够通过实时重新加载，而不考虑网络连接设置。

### <a name="does-it-require-debugging-the-app"></a>它是否需要调试此应用？ 

不是。 事实上，甚至可以在任意数量的设备或模拟器/仿真程序上启动所有受支持应用程序所针对 （Android、 iOS 和 UWP） 并将它们全部一次更新。 

## <a name="limitations"></a>限制

* 支持仅重新加载的 XAML。
* 除非使用 MVVM 中的用户界面状态可能不保留之间重新部署。

## <a name="known-issues"></a>已知问题

* 仅 Visual Studio 中受支持。
* 链接必须设置为**不链接**或**仅上链接 Framework Sdk** 
* 重新加载应用程序范围内的资源 (即**App.xaml**或共享资源字典)，重置应用导航。 
* 当前的 ContentView 重新加载需要重新加载包含页。
* 包含 AutomationId 元素可能会导致重新加载失败。
* 调试 UWP 可能会导致运行时故障发生时，请编辑 XAML。 解决方法：使用**启动但不调试 （Ctrl + F5）** 而不是**开始调试 (F5)**。

## <a name="troubleshooting"></a>疑难解答

### <a name="error-codes"></a>错误代码

* **XLR001**:*当前项目引用 Xamarin.LiveReload NuGet 包版本 [VERSION]，但 Xamarin Live 重新加载扩展需要版本 [VERSION]。*

  若要允许快速迭代和实时重新加载功能的演变，nuget 包与 Visual Studio 扩展必须完全匹配。 在 nuget 包更新到相同版本的已安装的扩展。

* **XLR002**:*从命令行生成时，实时重新加载至少需要 MqttHostname 属性。或者，将 EnableLiveReload 设置为 false 以禁用该功能。*

  通过实时重新加载所需的属性时将不可用生成从命令行中 （或在持续集成），并因此必须显式提供。 

* **XLR003**:*实时重新加载 nuget 包，需要安装 Xamarin Live 重新加载 Visual Studio 扩展。*

  尝试生成项目的引用实时重新加载 nuget 包，但未安装 Visual 扩展。  

* *加载程序集时发生异常：System.IO.FileNotFoundException:无法加载程序集 Xamarin.Live.Reload，版本 = 0.3.27.0，区域性 = 中性，PublicKeyToken =。*

  应使用主机项目`PackageReference`而不是 `packages.config`

### <a name="app-doesnt-connect"></a>应用程序不会连接

应用程序生成时中的信息**工具 > 选项 > Xamarin > 实时重新加载**（主机名、 端口和加密密钥） 嵌入在应用中，因此，当`LiveReload.Init()`运行时，任何配对或配置是若要成功执行了连接所必需。

非正常网络问题 （防火墙、 不同网络上的设备），该应用程序可能无法成功连接到 IDE 的主要原因是因为其配置不同于 Visual Studio 中的一个。 如果可能发生这种情况：

* 在另一台计算机上已编译的应用程序。
* 应用程序已编译并部署在不同的 Visual Studio 会话中，并**自动生成加密密钥**是签入 （默认值）**工具 > 选项 > Xamarin > 实时重新加载**。
* Visual Studio 设置已更改 （即主机名、 端口或加密密钥），但未生成并再次部署应用程序。

这种情况下所有通过生成和重新部署该应用解决。

### <a name="uninstalling-preview-1"></a>卸载预览版 1

如果具有较早的预览版，并且必须卸载它的问题，请按照下列步骤：

1. 删除文件夹**C:\Program Files (x86) \Microsoft Visual Studio\Preview\Enterprise\Common7\IDE\Extensions\Xamarin\LiveReload** (注意:"企业"替换为你安装的版本和"预览版"与"2017"如果您安装到稳定 VS）
2. 打开**开发人员命令提示**该 Visual Studio 和运行`devenv /updateconfiguration`。 

## <a name="tips--tricks"></a>提示和技巧

* 只要不会更改实时重新加载设置 (包括加密密钥，例如，如果您关闭**自动生成加密密钥**) 和中的同一台计算机生成，不需要生成和部署后初始应用程序部署，除非您更改代码或依赖项。 您可以只需重新启动之前已部署的应用，且它将到最后一个连接使用的主机。

* 可连接到相同的 Visual Studio 会话的设备数量没有限制。 可以部署和任意多个设备/模拟器中启动应用程序根据需要在同一时间对所有这些查看实时重新加载工作。

* 实时重新加载将只重新加载您的应用程序的用户界面部分，但它*不*重新创建您的页面，则两者不它替换您的视图模型 （或绑定上下文）。 这意味着*整个*应用程序状态始终保留在重新加载，包括注入依赖项。

## <a name="live-reload-server"></a>实时重新加载服务器

在方案中从正在运行的应用连接到你的计算机 (通过使用指示`localhost`或`127.0.0.1`中**工具 > 选项 > Xamarin > 实时重新加载**) 不能 （例如防火墙、 不同的网络）你可以配置远程服务器相反，哪些 IDE 和应用程序将连接到。

实时重新加载使用标准[MQTT 协议](http://mqtt.org/)到交换消息，并因此可以与之通信[第三方服务器](https://github.com/mqtt/mqtt.github.io/wiki/servers)。 甚至还有[公共服务器](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers)(也称为*中转站*) 可用，可以使用。 实时重新加载经过了`broker.hivemq.com`并`iot.eclipse.org`主机名，以及提供的服务[www.cloudmqtt.com](https://www.cloudmqtt.com)并[www.cloudamqp.com](https://www.cloudamqp.com)。 此外可以部署你自己的 MQTT 服务器在云中，如[在 Azure 上的 HiveMQ](https://www.hivemq.com/blog/hivemq-on-windows-azure-mqtt-microsoft-cloud)。

可以配置任何端口，但通常对远程服务器使用默认 1883年端口。 实时重新加载消息使用强的端到端 AES 对称加密，因此，可以安全地连接到远程服务器。 默认情况下，加密密钥和初始化向量 (IV) 重新生成的每个 Visual Studio 会话。

可能的最简单方法是安装[mosquitto](https://mosquitto.org)在 Azure 中的空白 Ubuntu VM 中的服务器：

1. 在 Azure 门户中创建新的 Ubuntu Server VM
2. 为 1883 （默认 MQTT 端口） 在网络选项卡中添加新的入站的端口规则
3. 打开[Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) (bash 模式)
4. 键入`ssh [USERNAME]@[PUBLIC_IP]`使用 1 中选择的用户名) 和你的 VM 概述页中所示的公共 IP
5. 运行`sudo apt-get install mosquitto`，输入 1 中所选的密码)

现在你可以使用该 IP 连接到 MQTT 服务器。

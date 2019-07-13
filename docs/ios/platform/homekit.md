---
title: 在 Xamarin.iOS HomeKit
description: HomeKit 是 Apple 的框架，用于控制家庭自动化设备。 本文介绍了 HomeKit 并介绍了配置测试 accessories HomeKit 附件模拟器并编写一个简单的 Xamarin.iOS 应用程序与这些附件交互。
ms.prod: xamarin
ms.assetid: 90C0C553-916B-46B1-AD52-1E7332792283
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 9daedbe9bba5a2923a247104c4e69ae2e1b635aa
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865773"
---
# <a name="homekit-in-xamarinios"></a>在 Xamarin.iOS HomeKit

_HomeKit 是 Apple 的框架，用于控制家庭自动化设备。本文介绍了 HomeKit 并介绍了配置测试 accessories HomeKit 附件模拟器并编写一个简单的 Xamarin.iOS 应用程序与这些附件交互。_

[![](homekit-images/accessory01.png "示例 HomeKit 启用应用程序")](homekit-images/accessory01.png#lightbox)

Apple 引入 HomeKit iOS 8 中作为一种方法将来自不同供应商的多个家庭自动化设备无缝集成到单个、 一致的单元。 通过将提升用于发现一种常见协议，配置和控制家庭自动化设备 HomeKit 就让设备从非相关供应商进行协作，同时无需协调工作的各个供应商。

使用 HomeKit，可以创建一个 Xamarin.iOS 应用，用于控制任何启用 HomeKit 设备没有使用供应商提供的 Api 或应用程序。 使用 HomeKit，可以执行以下操作：

- 发现新的启用 HomeKit 家庭自动化设备并将其添加到数据库中的所有用户的 iOS 设备将都保留。
- 设置、 配置、 显示和控制任何设备中 HomeKit_主页配置数据库_。
- 与任何预配置的 HomeKit 设备通信和它们来执行单个操作，或运行配合使用，如打开在厨房中使用系统正常运行的所有命令。

为设备提供服务启用 HomeKit 应用到在主页配置数据库中，除了 HomeKit 提供了使用 Siri 语音命令的访问。 给定适当配置的 HomeKit 安装程序，用户可以发出语音命令如"Siri，打开在起居室灯光。"

<a name="Home-Configuration-Database" />

## <a name="the-home-configuration-database"></a>主配置数据库

HomeKit 组织到 Home 集合的给定位置中的所有自动化设备。 此集合提供用户分组到可使用有意义，用户可读的标签以逻辑方式排列单位其家庭自动化设备的方法。

主页集合存储到主页配置数据库中，将自动备份和同步所有用户的 iOS 设备。 HomeKit 提供用于处理与主页配置数据库的以下类：

- `HMHome` -这是在一个物理位置 （例如包含所有信息和配置适用于所有家庭自动化设备的顶级容器。 一个系列居住)。 用户可能具有多个未成年人，例如其主要的家庭和休假房子。 或者它们可能具有不同"房屋"的相同属性，如主房屋和来宾房屋通过车库。 无论哪种方式，至少一个`HMHome`对象_必须_进行安装和存储之前可以输入任何其他 HomeKit 信息。
- `HMRoom` -可选的段`HMRoom`使用户可以定义特定房间内家庭 (`HMHome`) 如：厨房、 洗澡、 车库或客厅。 用户可以分组到其房屋中的特定位置中的家庭自动化设备的所有`HMRoom`并对其执行操作作为一个单元。 例如，要求使用 Siri 关闭车库系统正常运行。
- `HMAccessory` -这表示单个，物理 HomeKit 启用已在用户的居住地 （例如智能调温器） 中安装的自动化设备。 每个`HMAccessory`分配给`HMRoom`。 如果用户尚未配置任何房间，HomeKit 会为特殊默认空间分配附件。
- `HMService` -表示由提供的服务给定`HMAccessory`，例如光或其颜色 （如果支持颜色更改） 的开/关状态。 每个`HMAccessory`可以有多个服务，例如车库门打开工具，它还包括一个指示灯。 此外，给定`HMAccessory`可能具有不受用户控制的服务，例如固件更新。
- `HMZone` -允许用户进行分组的集合`HMRoom`逻辑区域，如楼上、 Downstairs 或地下室对象。 尽管可选的这可以像在问 Siri 交互打开所有光线 downstairs 关闭。

<a name="Provisioning-a-HomeKit-App" />

## <a name="provisioning-a-homekit-app"></a>预配 HomeKit 应用

由于 HomeKit 所规定的安全要求，必须正确配置 Xamarin.iOS 应用程序使用 HomeKit 框架和 Xamarin.iOS 项目文件中 Apple 开发人员门户。

请执行以下操作：

1. 登录到[Apple 开发人员门户](https://developer.apple.com)。
2. 单击**证书、 标识符和配置文件**。
3. 如果尚未这样做，请单击**标识符**并创建您的应用程序的 ID (例如`com.company.appname`)，否则编辑现有的 id。
4. 絋粄**HomeKit**服务具有给定 ID 的复选框： 

    [![](homekit-images/provision01.png "启用 HomeKit 服务为给定的 ID")](homekit-images/provision01.png#lightbox)
5. 保存更改。
6. 单击**预配配置文件** > **开发**并创建新的开发预配配置文件为你的应用： 

    [![](homekit-images/provision02.png "创建新的开发预配配置文件的应用")](homekit-images/provision02.png#lightbox)
7. 请下载并安装新的预配配置文件或使用 Xcode 下载并安装该配置文件。
8. 编辑 Xamarin.iOS 项目选项，并确保使用你刚刚创建的预配配置文件： 

    [![](homekit-images/provision03.png "选择刚创建的预配配置文件")](homekit-images/provision03.png#lightbox)
9. 接下来，编辑你**Info.plist**文件，并确保将用于创建预配配置文件的应用程序 ID: 

    [![](homekit-images/provision04.png "设置应用程序 ID ")](homekit-images/provision04.png#lightbox)
10. 最后，编辑你**Entitlements.plist**文件，并确保**HomeKit**选择了权利： 

    [![](homekit-images/provision05.png "启用 HomeKit 权利")](homekit-images/provision05.png#lightbox)
11. 将所做的更改保存到的所有文件。

在准备好这些设置，该应用程序现在已准备好访问 HomeKit 框架 Api。 有关预配的详细信息，请参阅我们[设备预配](~/ios/get-started/installation/device-provisioning/index.md)并[预配您的应用程序](~/ios/get-started/installation/device-provisioning/index.md)指南。

> [!IMPORTANT]
> 测试 HomeKit 启用应用程序需要已完成正常预配的真实的 iOS 设备进行开发。 HomeKit 无法从 iOS 模拟器进行测试。

## <a name="the-homekit-accessory-simulator"></a>HomeKit 附件模拟器

若要提供一种方法来测试所有可能的家庭自动化设备和服务，而无需物理设备，Apple 创建_HomeKit 附件模拟器_。 使用此模拟器，可以设置和配置 HomeKit 的虚拟设备。

### <a name="installing-the-simulator"></a>安装模拟器

Apple 提供 HomeKit 附件模拟器作为单独的下载从 Xcode，因此将需要在继续之前安装它。

请执行以下操作：

1. 在 web 浏览器中访问[适用于 Apple 开发人员下载](https://developer.apple.com/download/more/?name=for%20Xcode)
2. 下载**Xcode xxx 的其他工具**（其中 xxx 是已安装的 Xcode 版本）： 

    [![](homekit-images/simulator01.png "Xcode 中下载的其他工具")](homekit-images/simulator01.png#lightbox)
3. 打开磁盘映像和安装的工具中您**应用程序**目录。

与 HomeKit 附件模拟器安装，可以为测试创建虚拟附件。

### <a name="creating-virtual-accessories"></a>创建虚拟附件

若要启动 HomeKit 附件模拟器和创建一些虚拟附件，执行以下操作：

1. 从应用程序文件夹中，启动 HomeKit 附件模拟器： 

    [![](homekit-images/simulator02.png "HomeKit 附件模拟器")](homekit-images/simulator02.png#lightbox)
2. 单击 **+** 按钮，然后选择**新附件...** : 

    [![](homekit-images/simulator03.png "添加新附件")](homekit-images/simulator03.png#lightbox)
3. 填写有关新附件的信息并单击**完成**按钮： 

    [![](homekit-images/simulator04.png "填写有关新附件的信息")](homekit-images/simulator04.png#lightbox)
4. 单击**添加服务...** 按钮，然后从下拉列表中选择服务类型： 

    [![](homekit-images/simulator05.png "从下拉列表中选择服务类型")](homekit-images/simulator05.png#lightbox)
5. 提供**名称**作为服务，然后单击**完成**按钮： 

    [![](homekit-images/simulator06.png "输入服务的名称")](homekit-images/simulator06.png#lightbox)
6. 可以为服务提供可选特性，通过单击**添加特征**按钮和配置所需的设置： 

    [![](homekit-images/simulator07.png "配置所需的设置")](homekit-images/simulator07.png#lightbox)
7. 重复上述步骤来创建一个虚拟的家庭自动化设备 HomeKit 支持每种类型。

与某些示例虚拟 HomeKit 附件创建和配置，您可使用，并从 Xamarin.iOS 应用程序控制这些设备。

## <a name="configuring-the-infoplist-file"></a>配置 Info.plist 文件

用于 iOS 10 的新功能 （和更高版本），开发人员将需要添加`NSHomeKitUsageDescription`到应用程序的关键`Info.plist`文件，并提供应用程序需要访问用户的 HomeKit 数据库为何字符串声明。 此字符串将显示在运行应用的第一个用户时间：

[![](homekit-images/info01.png "HomeKit 权限对话框")](homekit-images/info01.png#lightbox)

若要设置此项，请执行以下操作：

1. 双击`Info.plist`文件中**解决方案资源管理器**打开进行编辑。
2. 在屏幕的底部，切换到**源**视图。
3. 添加一个新**条目**到列表。
4. 从下拉列表中，选择**隐私-HomeKit 使用说明**: 

    [![](homekit-images/info02.png "选择隐私-HomeKit 使用说明")](homekit-images/info02.png#lightbox)
5. 输入应用程序需要访问用户的 HomeKit 数据库的原因的说明： 

    [![](homekit-images/info03.png "输入的说明")](homekit-images/info03.png#lightbox)
6. 保存对文件所做的更改。

> [!IMPORTANT]
> 设置失败`NSHomeKitUsageDescription`中的键`Info.plist`文件会导致应用程序_以静默方式失败_（正在关闭系统会在运行时） 而无需在 iOS 10 （或更高版本） 中运行时错误。

## <a name="connecting-to-homekit"></a>连接到 HomeKit

若要与 HomeKit 进行通信，Xamarin.iOS 应用程序需要首先实例化的实例`HMHomeManager`类。 主页管理器是 HomeKit 的中央入口点，并负责提供一系列可用的家庭，更新和维护该列表和返回用户的_主主页_。

`HMHome`对象包含有关为家庭包括任何房间、 组或它可能包含，以及安装了任何家庭自动化附件的区域的所有信息。 在 HomeKit，至少一个可以执行任何操作之前`HMHome`必须创建和分配为主主页。

您的应用程序负责检查是否存在主主页以及创建和分配一个，如果不是。

### <a name="adding-a-home-manager"></a>添加主管理器

若要添加 HomeKit 意识到 Xamarin.iOS 应用程序，请编辑**AppDelegate.cs**文件以对其进行编辑，并使其看起来如下所示：

```csharp
using HomeKit;
...

public HMHomeManager HomeManager { get; set; }
...

public override void FinishedLaunching (UIApplication application)
{
    // Attach to the Home Manager
    HomeManager = new HMHomeManager ();
    Console.WriteLine ("{0} Home(s) defined in the Home Manager", HomeManager.Homes.Count());

    // Wire-up Home Manager Events
    HomeManager.DidAddHome += (sender, e) => {
        Console.WriteLine("Manager Added Home: {0}",e.Home);
    };

    HomeManager.DidRemoveHome += (sender, e) => {
        Console.WriteLine("Manager Removed Home: {0}",e.Home);
    };
    HomeManager.DidUpdateHomes += (sender, e) => {
        Console.WriteLine("Manager Updated Homes");
    };
    HomeManager.DidUpdatePrimaryHome += (sender, e) => {
        Console.WriteLine("Manager Updated Primary Home");
    };
}
```

当首次运行应用程序时，系统将要求用户，他们想要使其能够访问其 HomeKit 信息：

[![](homekit-images/home01.png "将要求用户，他们想要使其能够访问其 HomeKit 信息")](homekit-images/home01.png#lightbox)

如果用户回答**确定**，则该应用程序将能够使用其 HomeKit 附件否则将不会和对 HomeKit 的任何调用将失败并返回错误。

与主页管理器中的位置，接下来应用程序将需要查看是否已配置主主页，以及如果没有，请提供用户创建并分配一个的方式。

### <a name="accessing-the-primary-home"></a>访问主主页

如上面所述，必须创建和配置之前可用 HomeKit，它是应用程序负责提供一种方法为用户创建并分配主家庭，如果一个尚不存在主主页。

当您的应用程序首次启动或从后台返回时，需要监视`DidUpdateHomes`事件的`HMHomeManager`类，以检查是否存在主主页。 如果不存在，它应提供一个接口，使用户可以创建一个。

可以将以下代码添加到视图控制器，以检查主主页：

```csharp
using HomeKit;
...

public AppDelegate ThisApp {
    get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
}
...

// Wireup events
ThisApp.HomeManager.DidUpdateHomes += (sender, e) => {

    // Was a primary home found?
    if (ThisApp.HomeManager.PrimaryHome == null) {
        // Ask user to add a home
        PerformSegue("AddHomeSegue",this);
    }
};
```

主页管理器对 HomeKit，进行连接时`DidUpdateHomes`时将触发事件、 任何现有的家庭会加载到的家庭的管理器的集合，并且将加载主主页，如果可用。

### <a name="adding-a-primary-home"></a>添加主主页

如果`PrimaryHome`的属性`HMHomeManager`是`null`后`DidUpdateHomes`事件，您需要提供某种方式让用户创建并分配主主页，然后再继续。

通常，应用会显示用于用户命名的新的家，然后获取传递到主管理器作为的主数据库的安装程序的窗体。 有关**HomeKitIntro**示例应用程序，模式视图已在 IOS 设计器中创建并由调用`AddHomeSegue`segue 从应用程序的主接口。

它提供了用户输入的名称的新主目录以及用于添加主页的按钮的文本字段。 当用户点击**添加主页**按钮，下面的代码调用主管理器中，以添加主页：

```csharp
// Add new home to HomeKit
ThisApp.HomeManager.AddHome(HomeName.Text,(home,error) =>{
    // Did an error occur
    if (error!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Add Home Error",string.Format("Error adding {0}: {1}",HomeName.Text,error.LocalizedDescription),this);
        return;
    }

    // Make the primary house
    ThisApp.HomeManager.UpdatePrimaryHome(home,(err) => {
        // Error?
        if (err!=null) {
            // Inform user of error
            AlertView.PresentOKAlert("Add Home Error",string.Format("Unable to make this the primary home: {0}",err.LocalizedDescription),this);
            return ;
        }
    });

    // Close the window when the home is created
    DismissViewController(true,null);
});
```

`AddHome`方法将尝试创建新主目录，并将其返回到给定的回调例程。 如果`error`属性不是`null`出现错误，它应显示给用户。 最常见错误是由非唯一的主目录名称或主管理器无法与 HomeKit 通信导致的。

如果已成功创建主页，则需要调用`UpdatePrimaryHome`方法作为主主页设置新的家。 同样，如果`error`属性不是`null`出现错误，它应显示给用户。

您还应监视主页经理`DidAddHome`和`DidRemoveHome`应用的用户界面所需的事件和更新。

> [!IMPORTANT]
> `AlertView.PresentOKAlert`在上面的示例代码中使用的方法是可以轻松处理 iOS 警报更轻松的 HomeKitIntro 应用程序中的帮助器类。


## <a name="finding-new-accessories"></a>查找新的附件

Xamarin.iOS 应用程序后已定义主主页或从主管理器中加载，可以调用`HMAccessoryBrowser`查找任何新的家庭自动化附件，并将其添加到主页。

调用`StartSearchingForNewAccessories`方法开始寻找新的附件和`StopSearchingForNewAccessories`方法完成。

> [!IMPORTANT]
> `StartSearchingForNewAccessories` 应不保留长时间运行，因为它会产生负面影响电池寿命和 iOS 设备的性能。 Apple 建议调用`StopSearchingForNewAccessories`后分钟或仅搜索时向用户显示查找附件 UI。

`DidFindNewAccessory`时发现新的附件，它们将添加到将调用事件`DiscoveredAccessories`附件浏览器中的列表。

`DiscoveredAccessories`列表将包含一系列`HMAccessory`对象以定义为 HomeKit 启用家庭自动化设备和其可用的服务，如灯或车库门控件。

一旦已找到新附件，它应显示给用户并因此他们可以选择它，将其添加到主页。 示例:

[![](homekit-images/accessory01.png "查找新附件")](homekit-images/accessory01.png#lightbox)

调用`AddAccessory`方法将所选的附件添加到主页的集合。 例如:

```csharp
// Add the requested accessory to the home
ThisApp.HomeManager.PrimaryHome.AddAccessory (_controller.AccessoryBrowser.DiscoveredAccessories [indexPath.Row], (err) => {
    // Did an error occur
    if (err !=null) {
        // Inform user of error
        AlertView.PresentOKAlert("Add Accessory Error",err.LocalizedDescription,_controller);
    }
});
```

如果`err`属性不是`null`出现错误，它应显示给用户。 否则，将要求用户输入要添加的设备的安装程序代码：

[![](homekit-images/accessory02.png "输入要添加的设备的安装程序代码")](homekit-images/accessory02.png#lightbox)

HomeKit 附件模拟器中可以下找到此编号**安装程序代码**字段：

[![](homekit-images/accessory03.png "HomeKit 附件模拟器中的设置代码字段")](homekit-images/accessory03.png#lightbox)

有关实际 HomeKit 附件，或者将在设备本身，在产品框中或附件的用户手册中的标签上打印设置代码。

附件的浏览器，则应该监视`DidRemoveNewAccessory`事件以及更新用户界面后用户已将其添加到其主页集合可用列表中删除附件。

## <a name="working-with-accessories"></a>使用附件

一次主主页和已建立和附件已添加到它，你可以让用户使用呈现附件 （和 （可选） 聊天室） 列表。

`HMRoom`对象包含有关针对给定聊天室的所有信息和任何附件属于它。 聊天室 （可选） 可以组织到一个或多个区域中。 一个`HMZone`包含有关给定区域的所有信息和所有属于该聊天室。

我们将会作为此示例中，保持操作，简单且适用于直接，主页的附件而不是将它们组织到聊天室或区域。

`HMHome`对象包含一系列可以提供给用户的分配附件其`Accessories`属性。 例如:

[![](homekit-images/accessory04.png "示例附件")](homekit-images/accessory04.png#lightbox)

此处窗体中，用户可以选择给定的附件，并使用它提供的服务。

## <a name="working-with-services"></a>使用服务

当用户与给定的 HomeKit 已启用家庭自动化设备交互时，通常是通过它提供的服务。 `Services`的属性`HMAccessory`类包含一系列`HMService`对象定义的服务提供了设备。

服务是诸如灯、 恒温器、 车库开门器供电、 交换机或锁。 某些设备 （如车库门打开工具） 将提供多个服务，例如光和打开或关闭门的功能。

每个附件包含给定的附件提供的特定服务，除了`Information Service`，用于定义属性，如其名称、 制造商、 型号和序列号。

### <a name="accessory-service-types"></a>附件的服务类型

下面的服务类型可用于通过`HMServiceType`枚举：

- **AccessoryInformation** -提供有关给定家庭自动化设备 （附件） 的信息。
- **AirQualitySensor** -定义空气质量感应器。
- **电池**-定义附件的电池的状态。
- **CarbonDioxideSensor** -定义碳排放的二氧化碳传感器。
- **CarbonMonoxideSensor** -定义一氧化碳传感器。
- **ContactSensor** -定义了联系人传感器 （例如正在打开或关闭窗口）。
- **门**-定义门状态传感器 （如打开或关闭）。
- **风扇**-定义了远程控制的风扇。
- **GarageDoorOpener** -定义车库门打开程序。
- **HumiditySensor** -定义湿度传感器。
- **LeakSensor** -定义泄漏传感器 （例如，热水器或碗机）。
- **灯泡**-定义独立 light 或是另一个附件 （例如车库门打开工具） 的一部分的光。
- **LightSensor** -定义光线传感器。
- **LockManagement** -定义了用于管理自动化的门锁的服务。
- **LockMechanism** -定义 （如门锁） 的远程控制的锁。
- **MotionSensor** -定义动作传感器。
- **OccupancySensor** -定义占有传感器。
- **输出口**-定义了远程控制墙上插座。
- **安全系统**-定义了一个家庭安全系统。
- **StatefulProgrammableSwitch** -定义仍处于给定状态 （就像一个翻转开关） 一次触发一个可编程开关。
- **StatelessProgrammableSwitch** -定义在触发 （如按钮） 后返回到其初始状态的可编程交换机。
- **SmokeSensor** -定义冒烟传感器。
- **切换**-定义就像一个标准墙开关打开/关闭开关。
- **温度传感器**-定义温度传感器。
- **调温器**-定义用于控制的 HVAC 系统的智能调温器。
- **窗口**-定义杖为远程打开或关闭自动的窗口。
- **WindowCovering** -定义了介绍，如 blinds 可以打开或关闭的远程控制的窗口。

### <a name="displaying-service-information"></a>显示服务信息

加载后`HMAccessory`您可以查询个人`HNService`对象提供，并向用户显示该信息：

[![](homekit-images/accessory05.png "显示服务信息")](homekit-images/accessory05.png#lightbox)

您应始终应检查`Reachable`属性的`HMAccessory`然后再尝试使用它。 无法访问用户不在范围内，或者如果它已被拔出，可以是设备的附件。

一旦选择了一种服务，用户可以查看或修改该服务来监视或控制给定家庭自动化设备的一个或多个特征。

<a name="Working-with-Characteristics" />

## <a name="working-with-characteristics"></a>使用特征

每个`HMService`对象可包含一系列`HMCharacteristic`可以提供的服务 （如门正在打开或关闭） 状态有关的信息或允许用户调整 （如设置光的颜色） 的状态的对象。

`HMCharacteristic` 不仅提供了有关特征和其状态的信息，而且还提供了这些方法用于处理与通过状态_特征元数据_(`HMCharacteristisMetadata`)。 此元数据可以提供当向用户或允许他们修改状态显示信息时非常有用的属性 （如最小和最大值的范围）。

`HMCharacteristicType`枚举提供了一组特征元数据值可以定义或修改，如下所示：

- AdminOnlyAccess
- AirParticulateDensity
- AirParticulateSize
- AirQuality
- AudioFeedback
- BatteryLevel
- 亮度
- CarbonDioxideDetected
- CarbonDioxideLevel
- CarbonDioxidePeakLevel
- CarbonMonoxideDetected
- CarbonMonoxideLevel
- CarbonMonoxidePeakLevel
- ChargingState
- ContactState
- CoolingThreshold
- CurrentDoorState
- CurrentHeatingCooling
- CurrentHorizontalTilt
- CurrentLightLevel
- CurrentLockMechanismState
- CurrentPosition
- CurrentRelativeHumidity
- CurrentSecuritySystemState
- CurrentTemperature
- CurrentVerticalTilt
- FirmwareVersion
- HardwareVersion
- HeatingCoolingStatus
- HeatingThreshold
- HoldPosition
- 色调
- Identify
- InputEvent
- LeakDetected
- LockManagementAutoSecureTimeout
- LockManagementControlPoint
- LockMechanismLastKnownAction
- 日志
- 制造商
- 模型
- MotionDetected
- 名称
- ObstructionDetected
- OccupancyDetected
- OutletInUse
- OutputState
- PositionState
- PowerState
- RotationDirection
- RotationSpeed
- 饱和度
- SerialNumber
- SmokeDetected
- SoftwareVersion
- StatusActive
- StatusFault
- StatusJammed
- StatusLowBattery
- StatusTampered
- TargetDoorState
- TargetHeatingCooling
- TargetHorizontalTilt
- TargetLockMechanismState
- TargetPosition
- TargetRelativeHumidity
- TargetSecuritySystemState
- TargetTemperature
- TargetVerticalTilt
- TemperatureUnits
- Version

### <a name="working-with-a-characteristics-value"></a>使用特性的值

若要确保你应用具有给定特征的最新状态，调用`ReadValue`方法的`HMCharacteristic`类。 如果`err`属性不是`null`，出现错误，它可能会或可能不会呈现给用户。

特征`Value`属性包含为给定的特征的当前状态`NSObject`，且这种情况下不能得到与直接在C#。

若要读取的值，以下帮助器类已添加到**HomeKitIntro**示例应用程序：

```csharp
using System;
using Foundation;
using System.Globalization;
using CoreGraphics;

namespace HomeKitIntro
{
    /// <summary>
    /// NS object converter is a helper class that helps to convert NSObjects into
    /// C# objects
    /// </summary>
    public static class NSObjectConverter
    {
        #region Static Methods
        /// <summary>
        /// Converts to an object.
        /// </summary>
        /// <returns>The object.</returns>
        /// <param name="nsO">Ns o.</param>
        /// <param name="targetType">Target type.</param>
        public static Object ToObject (NSObject nsO, Type targetType)
        {
            if (nsO is NSString) {
                return nsO.ToString ();
            }

            if (nsO is NSDate) {
                var nsDate = (NSDate)nsO;
                return DateTime.SpecifyKind ((DateTime)nsDate, DateTimeKind.Unspecified);
            }

            if (nsO is NSDecimalNumber) {
                return decimal.Parse (nsO.ToString (), CultureInfo.InvariantCulture);
            }

            if (nsO is NSNumber) {
                var x = (NSNumber)nsO;

                switch (Type.GetTypeCode (targetType)) {
                case TypeCode.Boolean:
                    return x.BoolValue;
                case TypeCode.Char:
                    return Convert.ToChar (x.ByteValue);
                case TypeCode.SByte:
                    return x.SByteValue;
                case TypeCode.Byte:
                    return x.ByteValue;
                case TypeCode.Int16:
                    return x.Int16Value;
                case TypeCode.UInt16:
                    return x.UInt16Value;
                case TypeCode.Int32:
                    return x.Int32Value;
                case TypeCode.UInt32:
                    return x.UInt32Value;
                case TypeCode.Int64:
                    return x.Int64Value;
                case TypeCode.UInt64:
                    return x.UInt64Value;
                case TypeCode.Single:
                    return x.FloatValue;
                case TypeCode.Double:
                    return x.DoubleValue;
                }
            }

            if (nsO is NSValue) {
                var v = (NSValue)nsO;

                if (targetType == typeof(IntPtr)) {
                    return v.PointerValue;
                }

                if (targetType == typeof(CGSize)) {
                    return v.SizeFValue;
                }

                if (targetType == typeof(CGRect)) {
                    return v.RectangleFValue;
                }

                if (targetType == typeof(CGPoint)) {
                    return v.PointFValue;
                }
            }

            return nsO;
        }

        /// <summary>
        /// Convert to string
        /// </summary>
        /// <returns>The string.</returns>
        /// <param name="nsO">Ns o.</param>
        public static string ToString(NSObject nsO) {
            return (string)ToObject (nsO, typeof(string));
        }

        /// <summary>
        /// Convert to date time
        /// </summary>
        /// <returns>The date time.</returns>
        /// <param name="nsO">Ns o.</param>
        public static DateTime ToDateTime(NSObject nsO){
            return (DateTime)ToObject (nsO, typeof(DateTime));
        }

        /// <summary>
        /// Convert to decimal number
        /// </summary>
        /// <returns>The decimal.</returns>
        /// <param name="nsO">Ns o.</param>
        public static decimal ToDecimal(NSObject nsO){
            return (decimal)ToObject (nsO, typeof(decimal));
        }

        /// <summary>
        /// Convert to boolean
        /// </summary>
        /// <returns><c>true</c>, if bool was toed, <c>false</c> otherwise.</returns>
        /// <param name="nsO">Ns o.</param>
        public static bool ToBool(NSObject nsO){
            return (bool)ToObject (nsO, typeof(bool));
        }

        /// <summary>
        /// Convert to character
        /// </summary>
        /// <returns>The char.</returns>
        /// <param name="nsO">Ns o.</param>
        public static char ToChar(NSObject nsO){
            return (char)ToObject (nsO, typeof(char));
        }

        /// <summary>
        /// Convert to integer
        /// </summary>
        /// <returns>The int.</returns>
        /// <param name="nsO">Ns o.</param>
        public static int ToInt(NSObject nsO){
            return (int)ToObject (nsO, typeof(int));
        }

        /// <summary>
        /// Convert to float
        /// </summary>
        /// <returns>The float.</returns>
        /// <param name="nsO">Ns o.</param>
        public static float ToFloat(NSObject nsO){
            return (float)ToObject (nsO, typeof(float));
        }

        /// <summary>
        /// Converts to double
        /// </summary>
        /// <returns>The double.</returns>
        /// <param name="nsO">Ns o.</param>
        public static double ToDouble(NSObject nsO){
            return (double)ToObject (nsO, typeof(double));
        }
        #endregion
    }
}
```

`NSObjectConverter`只要该应用程序需要读取的当前状态的一个显著特征就会使用。 例如：

```csharp
var value = NSObjectConverter.ToFloat (characteristic.Value);
```

上面的行转换为值`float`然后可以使用在 Xamarin 中C#代码。

若要修改`HMCharacteristic`，调用其`WriteValue`方法和包装中的新值`NSObject.FromObject`调用。 例如：

```csharp
Characteristic.WriteValue(NSObject.FromObject(value),(err) =>{
    // Was there an error?
    if (err!=null) {
        // Yes, inform user
        AlertView.PresentOKAlert("Update Error",err.LocalizedDescription,Controller);
    }
});
```

如果`err`属性不是`null`，已发生错误，应提供给用户。

### <a name="testing-characteristic-value-changes"></a>测试特性的值更改

使用时`HMCharacteristics`模拟附件、 修改和`Value`属性可以监视在 HomeKit 附件模拟器。

与**HomeKitIntro** HomeKit 附件模拟器中应几乎会立即看到真实的 iOS 设备的硬件，对特性的值的更改上运行的应用。 例如，更改 iOS 应用中的光的状态：

[![](homekit-images/test01.png "更改 iOS 应用中的光的状态")](homekit-images/test01.png#lightbox)

应更改光在 HomeKit 附件模拟器中的状态。 如果值不会更改，编写新的特性值时检查错误消息的状态，并确保附件仍可以访问。

## <a name="advanced-homekit-features"></a>高级的 HomeKit 功能

本文只讨论了用于处理与 HomeKit 附件中的 Xamarin.iOS 应用程序所需的基本功能。 但是，有几个高级的功能 HomeKit，且不在此介绍：

- **聊天室**-启用 HomeKit 附件 （可选） 可以组织到由最终用户的聊天室。 这样便于用户了解和使用的方式存在附件 HomeKit。 有关创建和维护聊天室的详细信息，请参阅 Apple [HMRoom](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMRoom_Class/index.html#//apple_ref/occ/cl/HMRoom)文档。
- **区域**-聊天室可以根据需要组织成区域由最终用户。 区域是指用户可能会将作为单个单元的聊天室的集合。 例如:楼上 Downstairs 或地下室。 同样，这允许 HomeKit 提供和使用附件对最终用户有意义的方式。 有关创建和维护区域的详细信息，请参阅 Apple [HMZone](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMZone_Class/index.html#//apple_ref/occ/cl/HMZone)文档。
- **操作和操作设置**-操作修改附件服务特征和可以分组到集中。 操作集充当控件的附件的组并协调其操作的脚本。 例如，"观看电视节目"脚本可能会关闭 blinds、 dim 系统正常运行，并打开电视和其声音系统。 有关创建和维护操作和操作集的详细信息，请参阅 Apple [HMAction](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMAction_Class/index.html#//apple_ref/occ/cl/HMAction)并[HMActionSet](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMActionSet_Class/index.html#//apple_ref/occ/cl/HMActionSet)文档。
- **触发器**-触发器可以激活一个或多个操作时，设置一组给定的条件已满足。 例如，打开 portch light 并在收到外部深色锁所有外部库门。 有关创建和维护触发器的详细信息，请参阅 Apple [HMTrigger](https://developer.apple.com/library/prerelease/ios/documentation/HomeKit/Reference/HMTrigger_Class/index.html#//apple_ref/occ/cl/HMTrigger)文档。

由于这些功能使用上面介绍的相同技术，它们应该很容易实现由以下 Apple [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)， [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)和[HomeKit 框架引用](https://developer.apple.com/library/ios/home_kit_framework_ref)。

## <a name="homekit-app-review-guidelines"></a>HomeKit 应用审核指南

提交 HomeKit 之前到 iTunes Connect 的 iTunes 应用商店中发布的已启用的 Xamarin.iOS 应用程序确保遵循适用于启用 HomeKit 应用 Apple 准则：

- 应用程序的主要目的_必须_如果使用 HomeKit 框架是家庭自动化。
- 应用程序的市场营销文本必须通知用户正在使用 HomeKit，他们必须提供隐私策略。
- 收集用户信息或用于广告目的使用 HomeKit 是严格禁止的。

对于完整查看准则，请参阅 Apple[应用商店查看准则](https://developer.apple.com/app-store/review/guidelines/)。

## <a name="whats-new-in-ios-9"></a>What's New iOS 9 中

Apple 已发布了以下更改和添加到 HomeKit 适用于 iOS 9:

- **现有对象进行维护**-如果修改现有附件时，主页管理器 (`HMHomeManager`) 将通知你已修改的特定项。
- **永久标识符**-所有相关 HomeKit 类现在包括`UniqueIdentifier`属性来唯一地标识给定的项跨 HomeKit 启用应用程序 （或同一应用程序的实例）。
- **用户管理**-添加用户的主要用户家中有权访问 HomeKit 设备通过提供用户管理的内置视图控制器。
- **用户功能**-HomeKit 用户现在可以控制他们就能够在 HomeKit 中使用哪些功能的权限集和 HomeKit 启用附件。 您的应用程序应仅显示与当前用户的相关功能。 例如，只有管理员应该能够维护其他用户。
- **预定义的场景**-平均 HomeKit 用户发生的四个常见的事件的创建预定义的场景：启动、 保留、 返回，请转到平台。 不能从主页删除这些预定义的场景。
- **场景和 Siri** -Siri 具有更深入的支持，对于 iOS 9，可以在场景识别 HomeKit 中定义任何场景的名称。 用户可以执行场景，只需通过说话 Siri 其名称。
- **附件类别**-已添加到所有附件和可帮助确定要添加到 Home 的附件的类型或从处理中的应用内一组预定义的类别。 这些新类别可在附件安装过程。
- **Apple Watch 支持**-HomeKit 现已可供 watchOS 和 Apple Watch 将能够 HomeKit 而无需 iPhone 附近监视正在启用设备管理。 适用于 watchOS HomeKit 支持以下功能：查看家庭，控制附件和执行的场景。
- **新的事件触发器类型**-除了在 iOS 8 中，iOS 9 现在支持事件触发器基于附件状态 （如传感器数据） 或地理位置中受支持类型触发计时器。 事件触发器使用`NSPredicates`可以设置为其执行的条件。
- **远程访问**-与远程访问，用户现在就能够控制其 HomeKit 启用主页自动化的附件，当他们离开在远程位置的房子。 在 iOS 8 中这已才支持此用户拥有第三代 Apple TV 在家中。 在 iOS 9 中，解除了此限制，并通过 iCloud 和 HomeKit 附件协议 (HAP) 支持远程访问。
- **新蓝牙低功耗 (BLE) 功能允许**-HomeKit 现在支持更多的附件类型可通过蓝牙低功耗 (BLE) 协议进行通信。 使用 HAP 安全隧道，HomeKit 附件可以公开其他蓝牙附件通过 Wi-fi （如果它不在蓝牙范围内）。 在 iOS 9，BLE 附件具有完全支持通知和元数据。
- **新附件类别**-Apple 在 iOS 9 中添加以下新附件类别：窗口 Coverings、 机动的门和 Windows、 警报系统、 传感器和可编程的开关。

有关 iOS 9 中 HomeKit 的新功能的详细信息，请参阅 Apple [HomeKit 索引](https://developer.apple.com/homekit/)并[What's New in HomeKit](https://developer.apple.com/videos/wwdc/2015/?id=210)视频。

## <a name="summary"></a>总结

本文介绍了 Apple HomeKit 家庭自动化框架。 它演示了如何设置和配置测试设备使用 HomeKit 附件模拟器以及如何创建简单的 Xamarin.iOS 应用程序来发现、 与通信和控制使用 HomeKit 的家庭自动化设备。



## <a name="related-links"></a>相关链接

- [iOS 9 示例](https://developer.xamarin.com/samples/ios/iOS9/)
- [面向开发人员的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [What's New iOS 9.0 中](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [HomeKitDeveloper 指南](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/HomeKitDeveloperGuide/Introduction/Introduction.html)
- [HomeKit 用户界面指南](https://developer.apple.com/homekit/ui-guidelines/)
- [HomeKit 框架引用](https://developer.apple.com/library/ios/home_kit_framework_ref)

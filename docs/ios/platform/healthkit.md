---
title: 在 Xamarin.iOS HealthKit
description: 本文档介绍 HealthKit，提供运行状况相关信息的集中式、 协调和安全数据存储的 iOS 8 中引入的一个框架。 它讨论了如何预配 HealthKit 应用以及如何编写使用 HealthKit 框架的代码。
ms.prod: xamarin
ms.assetid: E3927A21-507C-43BA-A2AD-957716BA9B52
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: df0b0e8dd57129917f2d8dab07115551ca675acf
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61163163"
---
# <a name="healthkit-in-xamarinios"></a>在 Xamarin.iOS HealthKit

运行状况工具包提供了有关用户的运行状况相关信息的安全数据存储。 运行状况工具包应用可能，使用用户的显式权限、 读取和写入到此数据存储和添加相关数据时收到通知。 应用可以显示数据，或者用户可以使用 Apple 的提供的运行状况应用以查看其所有数据的仪表板。

因为与运行状况相关的数据是因此敏感和重要，运行状况工具包强类型，使用度量值和与正在记录的信息 （例如，血液血糖级别或心率） 的类型的显式关联的单位。 此外，运行状况工具包应用程序必须使用显式的权利，必须请求访问特定类型的信息，并且用户必须显式授予对这些类型的数据的应用程序访问权限。

本文将介绍：

- 运行状况工具包的安全要求，包括应用程序预配并请求访问的运行状况工具包数据库; 用户权限
- 错误地将应用或解释数据; 的可能性降至最低的运行状况工具包类型系统
- 写入到共享的系统范围内的运行状况工具包数据存储。

本文将介绍更高级的主题，例如查询数据库、 之类的度量单位之间进行转换或接收通知的新数据。

在本文中，我们将创建示例应用程序来记录用户的心率：

[![](healthkit-images/image01.png "示例应用程序来记录用户心率")](healthkit-images/image01.png#lightbox)

## <a name="requirements"></a>要求

以下被所要完成本文中介绍的步骤：

- **Xcode 7 和 iOS 8 （或更高版本）** – Apple 的最新的 Xcode 和 iOS Api 需要安装并配置开发人员的计算机上。
- **Visual Studio for Mac 或 Visual Studio** – 应安装并配置开发人员的计算机上的 Visual Studio for Mac 的最新版本。
- **iOS 8 （或更高版本） 设备**– 8 或更大的测试运行 iOS 的最新版本的 iOS 设备。

> [!IMPORTANT]
> IOS 8 中引入了运行状况工具包。 目前，运行状况工具包不是可在 iOS 模拟器中，并且调试需要连接到物理 iOS 设备。




## <a name="creating-and-provisioning-a-health-kit-app"></a>创建和预配 Health 工具包应用程序
Xamarin iOS 8 应用程序可以使用 HealthKit API 之前，它必须正确配置它们并将其预配中。 本部分将介绍正确设置你的 Xamarin 应用程序所需的步骤。

运行状况工具包应用需要：

- 使用显式**应用程序 ID**。
- 一个**预配配置文件**相关联的显式**应用程序 ID**且**运行状况工具包**权限。
- `Entitlements.plist`与`com.apple.developer.healthkit`类型的属性`Boolean`设置为`Yes`。
- `Info.plist`其`UIRequiredDeviceCapabilities`密钥包含一个具有项`String`值`healthkit`。
- `Info.plist`还必须具有适当的隐私说明条目：`String`密钥说明`NSHealthUpdateUsageDescription`如果应用程序要写入数据和一个`String`密钥说明`NSHealthShareUsageDescription`如果应用程序将读取运行状况工具包数据。

若要了解有关预配 iOS 应用的详细信息[设备预配](~/ios/get-started/installation/device-provisioning/index.md)Xamarin 的中的文章**入门**系列介绍了开发人员证书，应用程序 Id 之间的关系预配配置文件和应用权利。

<a name="explicit-appid" />

### <a name="explicit-app-id-and-provisioning-profile"></a>显式应用 ID 和预配配置文件

创建显式**应用程序 ID**和相应**预配配置文件**Apple 中完成[iOS 开发人员中心](https://developer.apple.com/devcenter/ios/index.action)。 

您的当前**应用 Id**中列出[证书、 标识符和配置文件](https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action)部分开发人员中心。 通常情况下，此列表将显示**ID**的值`*`，以指示的**应用程序 ID** - **名称**可以在任意数量的后缀。 此类*通配符应用 Id*不能用于运行状况工具包。
 
若要创建显式**应用程序 ID**，单击**+** 在右上角以使你转到按钮**注册 iOS 应用程序 ID**页：


[![](healthkit-images/image02.png "注册 Apple 开发人员门户上的应用程序")](healthkit-images/image02.png#lightbox)

创建应用程序说明后上, 图中所示，使用**显式应用 ID**部分，以创建你的应用程序的 ID。 在中**应用服务**部分中，选择**运行状况工具包**中**启用服务**部分。

完成后，按**继续**按钮以注册**应用程序 ID**中你的帐户。 您将得到回复**证书、 标识符和配置文件**页。 单击**预配配置文件**转到你当前的预配配置文件的列表，然后单击**+** 按钮在右上角登录以使你转到**添加 iOS预配配置文件**页。 选择**iOS 应用开发**选项，然后单击**继续**到达**选择应用 ID**页。 在这里，选择的显式**应用程序 ID**之前指定：


[![](healthkit-images/image03.png "选择显式应用 ID")](healthkit-images/image03.png#lightbox)

单击**继续**和工作通过余下的屏幕，其中指定你**开发人员证书**，**设备**，和一个**名称**为此**预配配置文件**:

[![](healthkit-images/image04.png "生成预配配置文件")](healthkit-images/image04.png#lightbox)

单击**生成**和 await 的你的配置文件创建。 下载文件，并双击它，以在 Xcode 中安装。 你可以确认其安装在**Xcode > 首选项 > 帐户 > 查看详细信息...** 应会看到你刚刚安装的配置文件，并应图标的运行状况工具包和中的任何其他特殊服务及其**权利**行：

[![](healthkit-images/image05.png "在 Xcode 中查看配置文件")](healthkit-images/image05.png#lightbox)

<a name="associating-appid" />

### <a name="associating-the-app-id-and-provisioning-profile-with-your-xamarinios-app"></a>将相关联的应用程序 ID 和预配配置文件与 Xamarin.iOS 应用程序

一旦创建并安装相应**预配配置文件**如所述，它通常是在 Visual Studio for Mac 或 Visual Studio 创建解决方案的时间。 运行状况工具包访问可供任何 iOSC#或F#项目。

而不是介绍手动创建 Xamarin iOS 8 项目的过程，打开示例应用程序附加到这篇文章 （其中包括一个预构建的情节提要和代码）。 若要将示例应用程序与你启用的运行状况套件相关联**预配配置文件**，在**Solution Pad**，右键单击项目并打开其**选项**对话框。 切换到**iOS 应用程序**面板并输入的显式**应用程序 ID**之前创建为应用程序的**捆绑包标识符**:

[![](healthkit-images/image06.png "输入的显式应用 ID")](healthkit-images/image06.png#lightbox)

现在，切换到**iOS 捆绑签名**面板。 您最近安装**预配配置文件**，其关联到的显式**应用程序 ID**，将现在可用作**预配配置文件**:

[![](healthkit-images/image07.png "选择预配配置文件")](healthkit-images/image07.png#lightbox)

如果**预配配置文件**不可用，请仔细检查**捆绑包标识符**中**iOS 应用程序**面板中指定的与**iOS开发人员中心**并且**预配配置文件**安装 (**Xcode > 首选项 > 帐户 > 查看详细信息...**).

当启用运行状况工具包的**预配配置文件**为选中状态，单击**确定**以关闭项目选项对话框。

### <a name="entitlementsplist-and-infoplist-values"></a>Entitlements.plist 和 Info.plist 值

示例应用包含`Entitlements.plist`（这是必要的运行状况工具包已启用应用程序） 的文件，并且不包含在每个项目模板。 如果你的项目不包括权利，右键单击项目，选择**文件 > 新建文件...> iOS > Entitlements.plist**手动添加一个。

从根本上讲，您`Entitlements.plist`必须具有以下键和值对：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.HealthKit</key>
    <true/>
</dict>
</plist>

```

同样，`Info.plist`应用程序必须具有值`healthkit`与关联`UIRequiredDeviceCapabilities`密钥：

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
<string>armv7</string>
    <string>healthkit</string>
</array>

```

本文提供的示例应用程序包括一个预配置`Entitlements.plist`，包含所有所需的密钥。

<a name="programming" />

## <a name="programming-health-kit"></a>编程运行状况工具包

运行状况工具包数据存储是在应用之间共享的专用、 特定于用户的数据存储。 由于运行状况信息是非常敏感的用户必须采取积极的措施，以允许数据访问。 此访问权限可能不完整 (写不读，对于某些类型的数据而不是其他，访问等) 并可能会随时吊销。 运行状况工具包应用程序应使用多个用户将会犹豫不决存储其运行状况相关的信息了解采用防御方式进行，编写。

运行状况工具包数据仅限于 Apple 指定类型。 严格地定义这些类型： 一些，例如血液类型仅限于 Apple 提供枚举的特定值，而其他人结合将量值 （例如元语法、 卡路里和升为单位） 的度量单位。 甚至共享兼容度量单位的数据进行区分通过其`HKObjectType`; 例如，类型系统将捕获错误的尝试存储`HKQuantityTypeIdentifier.NumberOfTimesFallen`到字段需要值`HKQuantityTypeIdentifier.FlightsClimbed`即使都使用` HKUnit.Count`度量单位。

可在运行状况工具包数据存储中存储的类型是所有子类的`HKObjectType`。 `HKCharacteristicType` 对象存储生物性别、 血液类型和出生日期。 不过，不是更常见`HKSampleType`，这些对象表示在特定时间或时间段内进行采样的数据。 

[![](healthkit-images/image08.png "HKSampleType 对象图表")](healthkit-images/image08.png#lightbox)

`HKSampleType` 是抽象的有四个具体子类。 目前只有一种`HKCategoryType`数据，这是进入睡眠状态的分析。 运行状况工具包中的数据的大多数属于类型`HKQuantityType`，并将存储在其数据`HKQuantitySample`使用熟悉的工厂设计模式创建的对象：

[![](healthkit-images/image09.png "大多数运行状况工具包中的数据类型 HKQuantityType 并将其数据存储在 HKQuantitySample 对象")](healthkit-images/image09.png#lightbox)

`HKQuantityType` 类型的范围`HKQuantityTypeIdentifier.ActiveEnergyBurned`到`HKQuantityTypeIdentifier.StepCount`。 

<a name="requesting-permission" />

### <a name="requesting-permission-from-the-user"></a>从用户请求权限

最终用户必须采取积极的措施，使应用能够读取或写入运行状况工具包数据。 这是通过在 iOS 8 设备可能已预安装的运行状况应用程序。 首次运行的运行状况工具包应用程序，则用户会看到与系统控制**运行状况访问**对话框：

[![](healthkit-images/image10.png "用户会看到其中一个控制系统的运行状况访问对话框")](healthkit-images/image10.png#lightbox)

更高版本，用户可以更改使用 Health 应用程序的权限**源**对话框：

[![](healthkit-images/image11.png "用户可以更改权限使用运行状况应用源对话框")](healthkit-images/image11.png#lightbox)

由于运行状况信息是非常敏感，因此应用程序开发人员应编写其程序采用防御方式进行的假定条件下将被拒绝，并运行应用时，更改权限。 最常用方法是请求中的权限`UIApplicationDelegate.OnActivated`方法，然后修改相应的用户界面。

### <a name="permissions-walkthrough"></a>权限演练

在运行状况工具包设置项目中，打开`AppDelegate.cs`文件。 请注意，语句使用`HealthKit`; 在该文件的顶部。


下面的代码与运行状况工具包权限：

```csharp
private HKHealthStore healthKitStore = new HKHealthStore ();

public override void OnActivated (UIApplication application)
{
        ValidateAuthorization ();
}

private void ValidateAuthorization ()
{
        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
        var heartRateType = HKObjectType.GetQuantityType (heartRateId);
        var typesToWrite = new NSSet (new [] { heartRateType });
        var typesToRead = new NSSet ();
        healthKitStore.RequestAuthorizationToShare (
                typesToWrite, 
                typesToRead, 
                ReactToHealthCarePermissions);
}

void ReactToHealthCarePermissions (bool success, NSError error)
{
        var access = healthKitStore.GetAuthorizationStatus (HKObjectType.GetQuantityType (HKQuantityTypeIdentifierKey.HeartRate));
        if (access.HasFlag (HKAuthorizationStatus.SharingAuthorized)) {
                HeartRateModel.Instance.Enabled = true;
        } else {
                HeartRateModel.Instance.Enabled = false;
        }
}

```

所有这些方法中的代码可以实现内联`OnActivated`，但示例应用程序使用单独的方法可以使它们更清晰的意图：`ValidateAuthorization()`具有特定正在编写的类型 （并读取，如果所需的应用程序） 请求访问所必需的步骤和`ReactToHealthCarePermissions()`之后用户交互 Health.app 中的权限对话框将激活的回调。

作业`ValidateAuthorization()`是构建的一套`HKObjectTypes`应用将编写并请求授权更新该数据。 在示例应用`HKObjectType`键是`KHQuantityTypeIdentifierKey.HeartRate`。 此类型添加到集中`typesToWrite`，而组`typesToRead`保留为空。 这些设置，并对引用`ReactToHealthCarePermissions()`回叫，传递给`HKHealthStore.RequestAuthorizationToShare()`。

`ReactToHealthCarePermissions()`用户权限对话框中的问题，并传递两条信息后，将调用 callback:`bool`值将成为`true`用户如果具有与权限对话框并交互`NSError`，如果非 null，指示某种显示权限对话框与关联的错误。

> [!IMPORTANT]
> 若要清楚地了解此函数的参数：_成功_和_错误_参数并不表示用户是否已授予权限来访问运行状况工具包数据 ！ 它们仅指示该用户授予了相应的机会，以允许对数据的访问。

若要确认应用是否有权访问数据，`HKHealthStore.GetAuthorizationStatus()`使用时，传入`HKQuantityTypeIdentifierKey.HeartRate`。 根据返回的状态，该应用程序启用或禁用的输入数据的能力。 没有标准的用户体验可用于处理拒绝的访问并且有许多可能的选项。 在示例应用中，将状态设置上`HeartRateModel`单一对象，反过来又会引发相关事件。

## <a name="model-view-and-controller"></a>模型、 视图和控制器

若要查看`HeartRateModel`单一实例对象，打开`HeartRateModel.cs`文件：

```csharp
using System;
using HealthKit;
using Foundation;

namespace HKWork
{
        public class GenericEventArgs<T> : EventArgs
        {
                public T Value { get; protected set; }
                public DateTime Time { get; protected set; }

                public GenericEventArgs (T value)
                {
                        this.Value = value;
                        Time = DateTime.Now;
                }
        }

        public delegate void GenericEventHandler<T> (object sender,GenericEventArgs<T> args);

        public sealed class HeartRateModel : NSObject
        {
                private static volatile HeartRateModel singleton;
                private static object syncRoot = new Object ();

                private HeartRateModel ()
                {
                }

                public static HeartRateModel Instance {
                        get {
                                //Double-check lazy initialization
                                if (singleton == null) {
                                        lock (syncRoot) {
                                                if (singleton == null) {
                                                        singleton = new HeartRateModel ();
                                                }
                                        }
                                }

                                return singleton;
                        }
                }

                private bool enabled = false;

                public event GenericEventHandler<bool> EnabledChanged;
                public event GenericEventHandler<String> ErrorMessageChanged;
                public event GenericEventHandler<Double> HeartRateStored;

                public bool Enabled { 
                        get { return enabled; }
                        set {
                                if (enabled != value) {
                                        enabled = value;
                                        InvokeOnMainThread(() => EnabledChanged (this, new GenericEventArgs<bool>(value)));
                                }
                        }
                }

                public void PermissionsError(string msg)
                {
                        Enabled = false;
                        InvokeOnMainThread(() => ErrorMessageChanged (this, new GenericEventArgs<string>(msg)));
                }

                //Converts its argument into a strongly-typed quantity representing the value in beats-per-minute
                public HKQuantity HeartRateInBeatsPerMinute(ushort beatsPerMinute)
                {
                        var heartRateUnitType = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        var quantity = HKQuantity.FromQuantity (heartRateUnitType, beatsPerMinute);

                        return quantity;
                }
                        
                public void StoreHeartRate(HKQuantity quantity)
                {
                        var bpm = HKUnit.Count.UnitDividedBy (HKUnit.Minute);
                        //Confirm that the value passed in is of a valid type (can be converted to beats-per-minute)
                        if (! quantity.IsCompatible(bpm))
                        {
                                InvokeOnMainThread(() => ErrorMessageChanged(this, new GenericEventArgs<string> ("Units must be compatible with BPM")));
                        }

                        var heartRateId = HKQuantityTypeIdentifierKey.HeartRate;
                        var heartRateQuantityType = HKQuantityType.GetQuantityType (heartRateId);
                        var heartRateSample = HKQuantitySample.FromType (heartRateQuantityType, quantity, new NSDate (), new NSDate (), new HKMetadata());

                        using (var healthKitStore = new HKHealthStore ()) {
                                healthKitStore.SaveObject (heartRateSample, (success, error) => {
                                        InvokeOnMainThread (() => {
                                                if (success) {
                                                        HeartRateStored(this, new GenericEventArgs<Double>(quantity.GetDoubleValue(bpm)));
                                                } else {
                                                        ErrorMessageChanged(this, new GenericEventArgs<string>("Save failed"));
                                                }
                                                if (error != null) {
                                                        //If there's some kind of error, disable 
                                                        Enabled = false;
                                                        ErrorMessageChanged (this, new GenericEventArgs<string>(error.ToString()));
                                                }
                                        });
                                });
                        }
                }
        }
}

```

第一个部分是用于创建泛型事件和处理程序的样板代码。 初始部分`HeartRateModel`类也是用于创建线程安全的单一实例对象的样本。

然后，`HeartRateModel`公开 3 个事件： 

- `EnabledChanged` -指示已启用或禁用心率存储 （请注意存储最初处于禁用状态）。 
- `ErrorMessageChanged` -对于此示例应用，我们有一个非常简单的错误处理模型： 最后一个错误的字符串。 
- `HeartRateStored` -核心率存储在运行状况工具包数据库时引发。

请注意，只要触发这些事件，它都是通过`NSObject.InvokeOnMainThread()`，这允许订阅服务器更新 UI。 或者，可以为在后台线程上引发记录事件并负责确保兼容性可能留到处理程序。 线程注意事项非常重要运行状况工具包应用程序中，因为很多的功能，如权限请求是异步的在非主线程上执行其回调。

中的运行状况工具包特定代码`HeartRateModel`中的两个函数`HeartRateInBeatsPerMinute()`和`StoreHeartRate()`。 

`HeartRateInBeatsPerMinute()` 将参数转换为强类型化的运行状况配套件`HKQuantity`。 由指定数量的类型是`HKQuantityTypeIdentifierKey.HeartRate`数量的单位，并且`HKUnit.Count`除以`HKUnit.Minute`(换而言之，单位是*每分钟节拍数*)。 

`StoreHeartRate()`函数采用`HKQuantity`(在示例应用中，创建一个通过`HeartRateInBeatsPerMinute()`)。 若要验证其数据，它使用`HKQuantity.IsCompatible()`方法，它返回`true`如果对象的单元可以转换为参数中的单位。 如果在创建时数量`HeartRateInBeatsPerMinute()`很显然，这将返回`true`，但它也会返回`true`数量作为创建的例如，如果*被难倒了每个小时*。 更常见的是，`HKQuantity.IsCompatible()`可以用来验证大容量，距离和能源的用户或设备可能会输入或显示在一个系统中的度量值 （如英制单位），但这可能会存储在另一个系统 （如公制单位）。 

一旦经过验证的兼容性的数量，`HKQuantitySample.FromType()`工厂方法用于创建强类型`heartRateSample`对象。 `HKSample` 对象具有开始和结束日期;对于即时读数，这些值应该是相同的因为它们位于该示例。 该示例还不设置任何键-值数据其`HKMetadata`参数，但其中一个可以使用类似下面的代码的代码来指定传感器位置：

```csharp
var hkm = new HKMetadata();
hkm.HeartRateSensorLocation = HKHeartRateSensorLocation.Chest;

```

一次`heartRateSample`已创建，该代码的使用中创建新的连接到数据库块。 在该块中，`HKHealthStore.SaveObject()`方法会尝试异步写入到数据库。 对 lambda 表达式的结果调用触发相关事件，要么`HeartRateStored`或`ErrorMessageChanged`。

现在，已编程模型，就可以看到如何在控制器反映了模型的状态。 打开`HKWorkViewController.cs`文件。 构造函数只需绑定`HeartRateModel`到事件处理方法的单一实例 （同样，这可以通过使用 lambda 表达式以内联方式但单独的方法使意图更明显）：

```csharp
public HKWorkViewController (IntPtr handle) : base (handle)
{
     HeartRateModel.Instance.EnabledChanged += OnEnabledChanged;
     HeartRateModel.Instance.ErrorMessageChanged += OnErrorMessageChanged;
     HeartRateModel.Instance.HeartRateStored += OnHeartBeatStored;
}

```

下面是相关的处理程序：

```csharp
void OnEnabledChanged (object sender, GenericEventArgs<bool> args)
{
        StoreData.Enabled = args.Value;
        PermissionsLabel.Text = args.Value ? "Ready to record" : "Not authorized to store data.";
        PermissionsLabel.SizeToFit ();
}

void OnErrorMessageChanged (object sender, GenericEventArgs<string> args)
{
        PermissionsLabel.Text = args.Value;
}

void OnHeartBeatStored (object sender, GenericEventArgs<double> args)
{
        PermissionsLabel.Text = String.Format ("Stored {0} BPM", args.Value);
}

```

很明显，在具有单个控制器的应用程序，它就可以避免单独的模型对象的创建和使用事件的控制流，但使用的模型对象是更适合于实际的应用程序。

## <a name="running-the-sample-app"></a>运行示例应用

IOS 模拟器不支持运行状况工具包。 必须运行 iOS 8 的物理设备上完成调试。

将正确预配 iOS 8 开发设备附加到您的系统。 选择它作为部署目标在 Visual Studio for Mac 并从菜单中选择**运行 > 调试**。

> [!IMPORTANT]
> 此时将出现与设置相关的错误。 若要解决错误，请查看创建和预配上述 Health 工具包应用程序部分。 组件包括： 
>
> - **iOS 开发人员中心**-显式应用 ID 与运行状况套件启用预配配置文件。 
> - **项目选项**的捆绑标识符 (显式应用 ID) 和预配配置文件。
> - **源代码**-Entitlements.plist & Info.plist

假设预配已正确设置，将启动你的应用程序。 当它到达其`OnActivated`方法，它将请求运行状况工具包授权。 第一次遇到此操作系统，你的用户将看到以下对话框：


[![](healthkit-images/image12.png "用户将会出现此对话框")](healthkit-images/image12.png#lightbox)

使应用程序来更新数据的核心速率，并且您的应用程序将重新出现。 `ReactToHealthCarePermissions`回调将以异步方式激活。 这将导致`HeartRateModel’s``Enabled`属性更改，它将引发`EnabledChanged`事件，这将导致`HKPermissionsViewController.OnEnabledChanged()`事件处理程序运行，它使`StoreData`按钮。 下图显示了序列：


[![](healthkit-images/image13.png "下图显示了事件的顺序")](healthkit-images/image13.png#lightbox)

按**记录**按钮。 这将导致`StoreData_TouchUpInside()`处理程序运行，它将尝试分析的值`heartRate`文本字段中，将转换成`HKQuantity`通过前面所述`HeartRateModel.HeartRateInBeatsPerMinute()`函数并传递到该数量`HeartRateModel.StoreHeartRate()`。 如前文所述，这将尝试将数据存储，并将引发任一`HeartRateStored`或`ErrorMessageChanged`事件。

双击**主页**按钮在设备上，打开运行状况应用程序。 单击**源**选项卡，您将看到列出的示例应用。 选择它并不允许更新心率数据的权限。 双击**主页**按钮并切换回您的应用程序。 再一次`ReactToHealthCarePermissions()`将调用，但这一次，访问被拒绝，因为**StoreData**按钮会被禁用 （请注意，这是以异步方式和用户界面中的更改可能会向最终用户显示）。

## <a name="advanced-topics"></a>高级主题

从运行状况工具包中读取数据数据库是非常类似于写入数据： 一个指定的人访问，请求授权的数据类型，并授予该授权后，数据是否可用，它包含自动转换为兼容的单位度量值。

有多个更复杂查询函数，这允许基于谓词的查询和更新相关数据时执行更新的查询。 

运行状况工具包应用程序的开发人员应查看 Apple 的运行状况工具包部分[应用程序查看准则](https://developer.apple.com/app-store/review/guidelines/#healthkit)。

一旦理解安全和类型系统模型存储和读取共享的运行状况工具包数据库中的数据是非常简单。 运行状况工具包中的函数的许多操作以异步方式和应用程序开发人员必须相应地编写他们的程序。

在撰写本文时，目前尚没有等效于在 Android 或 Windows Phone 中的运行状况工具包。

## <a name="summary"></a>总结

在本文中我们已了解如何运行状况工具包允许应用程序存储，检索和共享运行状况相关的信息，同时还提供一个标准的运行状况应用，它允许用户访问权限和对此数据的控制。 

此外展示了如何隐私、 安全性和数据完整性将重写与运行状况相关的信息的问题，并使用运行状况工具包应用程序必须处理增加的复杂性在应用程序管理方面 （预配） 编码 （运行状况工具包类型系统），和用户体验 （通过系统对话框和 Health 应用程序的权限的用户控件）。 

最后，我们看一看使用检测信号数据写入的运行状况工具包存储并具有异步感知设计的随附的示例应用的运行状况工具包的简单实现。

## <a name="related-links"></a>相关链接

- [HKWork （示例）](https://developer.xamarin.com/samples/monotouch/ios8/IntroToHealthKit/)
- [iOS 8 简介](~/ios/platform/introduction-to-ios8.md)

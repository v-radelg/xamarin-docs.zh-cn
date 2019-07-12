---
title: 更新现有 Xamarin.Forms 应用
description: 本文档介绍了必须遵循更新 Unified API 从经典 API 的 Xamarin.Forms 应用的步骤。
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 36a4c6b66f7f724bfccc3c2a3b81c17f1d34a9c5
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829737"
---
# <a name="updating-existing-xamarinforms-apps"></a>更新现有 Xamarin.Forms 应用

_请按照下列步骤来更新现有的 Xamarin.Forms 应用程序以使用统一 API 和更新到版本 1.3.1_

> [!IMPORTANT]
> 由于 Xamarin.Forms 1.3.1 支持统一 API 的第一个版本，应更新整个解决方案在迁移到统一的 iOS 应用程序在同一时间使用最新版本。 这意味着，除了更新统一支持的 iOS 项目，你将还需要在中编辑代码_所有_解决方案中的项目。

执行更新，则两个步骤：

1. 将 iOS 应用程序迁移到 Unified API 使用 Visual Studio for Mac 的迁移工具中的生成。

    - 使用迁移工具将自动更新项目。

    - 更新 iOS 本机 Api 到说明中所述[更新 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)（特别是在自定义呈现器或依赖关系服务代码）。

2. 更新到 Xamarin.Forms 版本 1.3 整个解决方案。

    1. 安装 Xamarin.Forms 1.3.1 NuGet 包。

    2. 更新`App`中共享代码的类。

    3. 更新`AppDelegate`iOS 项目中。

    4. 更新`MainActivity`Android 项目中。

    5. 更新`MainPage`Windows Phone 项目中。

## <a name="1-ios-app-unified-migration"></a>1.iOS 应用 （统一迁移）

在迁移过程要求升级到支持统一 API 的版本 1.3，Xamarin.Forms。 为了使要创建的正确的程序集引用，我们首先需要更新 iOS 项目以使用统一 API。

### <a name="migration-tool"></a>迁移工具

单击 iOS 项目，以便选择它，然后选择**项目 > 迁移到 Xamarin.iOS Unified API...** 并同意显示的警告消息。

![](updating-xamarin-forms-apps-images/beta-tool1.png "选择项目 > 迁移到 Xamarin.iOS Unified API...并同意显示的警告消息")

这将自动：

- 更改项目类型以支持统一的 64 位 API。
- 更改对 framework 引用**Xamarin.iOS** (替换旧**monotouch**引用)。
- 若要删除的代码中的命名空间引用更改`MonoTouch`前缀。
- 更新**csproj**要统一 API 的使用正确的生成目标文件。

**干净**并**生成**项目以确保没有任何其他错误修复。 应不需要任何进一步的操作。 中更详细地说明了这些步骤[统一的 API 文档](~/cross-platform/macios/unified/updating-ios-apps.md)。

### <a name="update-native-ios-apis-if-required"></a>更新本机 iOS Api （如果需要）

如果你已添加其他 iOS 本机代码 （如自定义呈现器或依赖关系服务） 可能需要执行其他手动代码修补程序。 重新编译您的应用程序，并参考[更新现有 iOS 应用程序说明](~/cross-platform/macios/unified/updating-ios-apps.md)有关更改所需的其他信息。 [这些提示](~/cross-platform/macios/unified/updating-tips.md)还将帮助确定所需更改。

## <a name="2-xamarinforms-131-update"></a>2.Xamarin.Forms 1.3.1 更新

IOS 应用程序已更新为 Unified API 之后, 该解决方案的其余需要对其更新到 Xamarin.Forms 1.3.1 版。 这包括：

- 正在更新每个项目中的 Xamarin.Forms NuGet 包。
- 将代码更改为使用新的 Xamarin.Forms `Application`， `FormsApplicationDelegate` (iOS) `FormsApplicationActivity` (Android) 和`FormsApplicationPage`(Windows Phone) 类。

下面介绍了这些步骤：

### <a name="21-update-nuget-in-all-projects"></a>2.1 更新中的所有项目的 NuGet

更新为 1.3.1 的 Xamarin.Forms NuGet 包管理器使用的解决方案中的所有项目的预发行：PCL （如果存在）、 iOS、 Android 和 Windows Phone。 建议你**删除并重新添加**的 Xamarin.Forms NuGet 包更新到版本 1.3。

> [!NOTE]
> Xamarin.Forms 版本 1.3.1 当前处于*预发行版*。 这意味着您必须选择**预发行版**选项在 NuGet 中通过 （一个刻度线框在 Visual Studio for Mac） 或 Visual Studio 中的下拉列表列表以查看最新的预发布版本。

> [!IMPORTANT]
> 如果使用的 Visual Studio，请确保安装最新版本的 NuGet 包管理器。 较旧版本的 Visual Studio 中的 NuGet 将正确安装 Xamarin.Forms 1.3.1 的统一版本。 转到**工具 > 扩展和更新...** ，然后单击**已安装**列表来检查是否**Visual Studio 的 NuGet 包管理器**至少为版本 2.8.5。 如果是较旧，单击**更新**下载最新版本的列表。

一旦你已更新到 Xamarin.Forms 1.3.1 的 NuGet 包，升级到新的每个项目中进行以下更改`Xamarin.Forms.Application`类。

### <a name="22-portable-class-library-or-shared-project"></a>2.2 可移植类库 （或共享的项目）

更改**App.cs**文件，以便：

- `App`类现在继承自`Application`。
- `MainPage`属性设置为你想要显示的第一个内容页。

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

我们已完全删除`GetMainPage`方法，并改为设置`MainPage`*属性*上`Application`子类。

这一新`Application`基类还支持`OnStart`， `OnSleep`，和`OnResume`重写来帮助你管理应用程序的生命周期。

`App`类然后传递给一个新`LoadApplication`中每个应用程序项目，如下所述的方法：

### <a name="23-ios-app"></a>2.3 iOS 应用

更改**AppDelegate.cs**文件，以便：

- 类继承自`FormsApplicationDelegate`(而不是`UIApplicationDelegate`之前)。
- `LoadApplication` 使用的新实例调用`App`。

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>2.3 android 应用

更改**MainActivity.cs**文件，以便：

- 类继承自`FormsApplicationActivity`(而不是`FormsActivity`之前)。
- `LoadApplication` 使用的新实例的调用 `App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2.4 Windows Phone 应用

我们需要更新**MainPage** -XAML 和代码隐藏文件。

更改**MainPage.xaml**文件，以便：

- 根 XAML 元素应为`winPhone:FormsApplicationPage`。
- `xmlns:phone`属性应为*更改*到 `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

更新的示例，如下所示-只需编辑的事物 （其他属性应保持不变）：

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

更改**MainPage.xaml.cs**文件，以便：

- 类继承自`FormsApplicationPage`(而不是`PhoneApplicationPage`之前)。
- `LoadApplication` 使用 Xamarin.Forms 的新实例调用`App`类。 可能需要完全限定此引用，因为 Windows Phone 有其自己的`App`已定义的类。

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>疑难解答

偶尔也会看到类似于此更新的 Xamarin.Forms NuGet 包后的错误。 NuGet 更新程序不会完全删除对从较旧版本的引用时发生你**csproj**文件。

>你\_PROJECT.csproj:错误：此项目将引用此计算机缺少的 NuGet 包。 启用 NuGet 包还原下载它们。  有关详细信息，请参阅 http://go.microsoft.com/fwlink/?LinkID=322105 。 缺少的文件是.../../packages/Xamarin.Forms.1.2.3.6257/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets。 (你\_项目)

若要修复这些错误，请打开**csproj**文件在文本编辑器中，并查找`<Target`引用较旧版本的 Xamarin.Forms 中，例如，如下所示的元素的元素。 应手动删除此整个元素**csproj**文件，并保存所做的更改。

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

删除这些旧的引用后，该项目应已成功生成。

## <a name="considerations"></a>注意事项

将现有的 Xamarin.Forms 项目从经典 API 转换为新的统一 API，如果该应用程序依赖于一个或多个组件或 NuGet 包时，应考虑以下注意事项。

### <a name="components"></a>组件

你的应用程序中包含的任何组件还需要更新为 Unified API 或尝试进行编译时，您将看到冲突。 对于任何包含的组件，使用支持统一 API 的 Xamarin 组件存储区中的新版本替换当前版本和生成已清理。 任何组件都尚未转换由作者，将显示在组件存储中的唯一警告 32 位。

### <a name="nuget-support"></a>NuGet 支持

虽然我们提供对 NuGet 才能使用统一 API 支持的更改，没有新版本的 NuGet，因此我们评估如何获取 NuGet 能够识别新的 Api。

在此之前，就像这些组件，您将需要切换到支持统一的 Api 版本在项目中包含任何 NuGet 包之后执行干净的生成。

> [!IMPORTANT]
> 如果窗体中有错误 _"错误 3 不能在相同的 Xamarin.iOS 项目中包含 monotouch.dll 和 Xamarin.iOS.dll'-'Xamarin.iOS.dll 显式引用，而 monotouch.dll 引用的 ' xxx，版本 = 0.0.000，区域性 = 中性，PublicKeyToken = null"_ 后转换到统一的 Api 应用程序，它通常是因为尚未更新到 Unified API 的项目中具有的组件或 NuGet 包。 你将需要删除现有的组件/NuGet、 更新为支持统一 Api 的版本和生成已清理。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 64 位版本的 xamarin ios 应用

对于 Xamarin.iOS 移动应用程序已转换为 Unified API，开发人员仍需要启用 64 位计算机从应用程序的选项的应用程序的构建。 请参阅**启用 64 位版本的 Xamarin.iOS 应用程序**的[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#enable-64)启用 64 位的详细说明的文档生成。

## <a name="summary"></a>总结

Xamarin.Forms 应用程序现在应更新到版本 1.3.1 和 iOS 应用程序迁移到 Unified API （它在 iOS 平台上支持 64 位体系结构）。

如上所述，如果您的 Xamarin.Forms 应用程序包括本机代码如自定义呈现器或依赖关系服务，则这些可能还需要更新，以使用新的类型[统一 API 中引入](~/cross-platform/macios/index.md)。

## <a name="related-links"></a>相关链接

- [更新 iOS 应用](~/cross-platform/macios/unified/updating-apps.md)
- [更新 iOS 应用](~/cross-platform/macios/unified/updating-ios-apps.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [正在更新提示](~/cross-platform/macios/unified/updating-tips.md)
- [经典 vs 统一的 API 差异](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)

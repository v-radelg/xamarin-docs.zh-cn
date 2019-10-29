---
title: 更新现有的 Xamarin Forms 应用
description: 本文档介绍将 Xamarin 应用程序从 Classic API 更新到 Unified API 时必须遵循的步骤。
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: dad1b7173e302931455887fdaa4730347f0e5e55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015005"
---
# <a name="updating-existing-xamarinforms-apps"></a>更新现有的 Xamarin Forms 应用

_按照以下步骤更新现有的 Xamarin Forms 应用，以使用 Unified API 并更新到版本1.3。1_

> [!IMPORTANT]
> 因为 Xamarin. Forms 1.3.1 是支持 Unified API 的第一个版本，所以应将整个解决方案更新为使用最新版本，同时将 iOS 应用程序迁移到统一版本。 这意味着除了更新 iOS 项目以获得统一支持外，还需要在解决方案中的_所有_项目中编辑代码。

此更新按两个步骤执行：

1. 使用迁移工具中 Visual Studio for Mac 的生成将 iOS 应用迁移到 Unified API。

    - 使用迁移工具自动更新项目。

    - 如[更新 ios 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)（特别是在自定义呈现器或依赖项服务代码中）中所述，更新 Ios 本机 api。

2. 将整个解决方案更新为 Xamarin. Forms 版本1.3。

    1. 安装 Xamarin. Forms 1.3.1 NuGet 包。

    2. 更新共享代码中的 `App` 类。

    3. 更新 iOS 项目中的 `AppDelegate`。

    4. 更新 Android 项目中的 `MainActivity`。

    5. 更新 Windows Phone 项目中的 `MainPage`。

## <a name="1-ios-app-unified-migration"></a>1 iOS 应用（统一迁移）

迁移的一部分需要将 Xamarin 升级到版本1.3，该版本支持 Unified API。 若要创建正确的程序集引用，首先需要更新 iOS 项目以使用 Unified API。

### <a name="migration-tool"></a>迁移工具

单击 iOS 项目，使其处于选中状态，然后选择 "**项目" "> 迁移到 Xamarin Unified API ...** "，并同意显示的警告消息。

![](updating-xamarin-forms-apps-images/beta-tool1.png "Choose Project > Migrate to Xamarin.iOS Unified API... and agree to the warning message that appears")

这会自动执行以下操作：

- 更改项目类型以支持统一64位 API。
- 更改对**Xamarin**的框架引用（替换旧的**monotouch.dialog**引用）。
- 更改代码中的命名空间引用以删除 `MonoTouch` 前缀。
- 更新 **.csproj**文件，为 Unified API 使用正确的生成目标。

**清理**并**生成**项目，以确保不存在要修复的其他错误。 不需要执行任何其他操作。 [Unified API 文档](~/cross-platform/macios/unified/updating-ios-apps.md)中更详细地介绍了这些步骤。

### <a name="update-native-ios-apis-if-required"></a>更新本机 iOS Api （如果需要）

如果已添加其他 iOS 本机代码（如自定义呈现器或依赖项服务），则可能需要执行其他手动代码修复。 重新编译你的应用，并参阅[更新现有 IOS 应用说明](~/cross-platform/macios/unified/updating-ios-apps.md)，以获取有关可能需要的更改的其他信息。 [这些提示](~/cross-platform/macios/unified/updating-tips.md)还有助于识别所需的更改。

## <a name="2-xamarinforms-131-update"></a>1.3.1 更新

将 iOS 应用更新到 Unified API 后，解决方案的其余部分需要更新为 Xamarin 版本1.3.1。 这包括：

- 在每个项目中更新 Xamarin NuGet 包。
- 更改代码以使用新的 Xamarin. Forms `Application`、`FormsApplicationDelegate` （iOS）、`FormsApplicationActivity` （Android）和 `FormsApplicationPage` （Windows Phone）类。

这些步骤如下所述：

### <a name="21-update-nuget-in-all-projects"></a>2.1 更新所有项目中的 NuGet

使用 NuGet 包管理器将1.3.1 更新为解决方案中所有项目的预发布版本： PCL （如果存在）、iOS、Android 和 Windows Phone。 建议**删除并重新添加**Xamarin. Forms NuGet 包以更新到版本1.3。

> [!NOTE]
> Xamarin 版本1.3.1 当前处于*预发布*版本。 这意味着，你必须选择 NuGet 中的**预发行**选项（通过 Visual Studio for Mac 中的刻度盒或 Visual Studio 中的下拉列表）来查看最新的预发行版本。

> [!IMPORTANT]
> 如果使用的是 Visual Studio，请确保已安装最新版本的 NuGet 包管理器。 Visual Studio 中的 NuGet 的早期版本将无法正确安装 Xamarin 的统一版本。窗体1.3.1。 中转到 "**工具" > "扩展和更新 ...** "，然后单击 "**已安装**" 列表以检查**适用于 Visual Studio 的 NuGet 包管理器**是否至少为版本2.8.5。 如果旧版本，请单击 "**更新**" 列表下载最新版本。

将 NuGet 包更新到 Xamarin 后，请在每个项目中进行以下更改，以升级到新的 `Xamarin.Forms.Application` 类。

### <a name="22-portable-class-library-or-shared-project"></a>2.2 可移植类库（或共享项目）

更改**App.cs**文件，以便：

- `App` 类现在继承自 `Application`。
- `MainPage` 属性设置为要显示的第一个内容页面。

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

已完全删除 `GetMainPage` 方法，而是在 `Application` 子类上设置 `MainPage`*属性*。

这一新的 `Application` 基类还支持 `OnStart`、`OnSleep`和 `OnResume` 替代，以帮助你管理应用程序的生命周期。

然后，`App` 类将传递到每个应用程序项目中的新 `LoadApplication` 方法中，如下所述：

### <a name="23-ios-app"></a>2.3 iOS 应用

更改**AppDelegate.cs**文件，以便：

- 类继承自 `FormsApplicationDelegate` （而不是以前的 `UIApplicationDelegate`）。
- 使用 `App`的新实例调用 `LoadApplication`。

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

### <a name="23-android-app"></a>2.3 Android 应用

更改**MainActivity.cs**文件，以便：

- 类继承自 `FormsApplicationActivity` （而不是以前的 `FormsActivity`）。
- 使用的新实例调用 `LoadApplication` `App`

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

我们需要更新**MainPage** -XAML 和代码隐藏。

更改**MainPage**文件，以便：

- 应 `winPhone:FormsApplicationPage`根 XAML 元素。
- `xmlns:phone` 特性应*更改*为 `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

下面显示了一个已更新的示例，只需编辑这些内容（其余属性应保持不变）：

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

更改**MainPage.xaml.cs**文件，以便：

- 类继承自 `FormsApplicationPage` （而不是以前的 `PhoneApplicationPage`）。
- `App` 类的新实例调用 `LoadApplication`。 可能需要完全限定此引用，因为 Windows Phone 已经定义了自己 `App` 类。

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

偶尔，在更新 Xamarin 后，你将看到类似于下面的错误。 当 NuGet 更新程序未完全从 **.csproj**文件中删除对较旧版本的引用时，会发生这种情况。

>你的\_项目 .csproj：错误：此项目引用了此计算机上缺少的 NuGet 包。 启用 NuGet 程序包还原以进行下载。  有关更多信息，请参见 http://go.microsoft.com/fwlink/?LinkID=322105 。 缺少的文件为../../packages/Xamarin.Forms.1.2.3.6257/build/portable-win + net45 + wp80 + MonoAndroid10 + MonoTouch10/Xamarin。 （您的\_项目）

若要修复这些错误，请在文本编辑器中打开 **.csproj**文件，然后查找引用早期版本 Xamarin 的 `<Target` 元素，如下面所示的元素。 你应从 **.csproj**文件中手动删除此整个元素，并保存所做的更改。

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

删除这些旧引用后，应成功生成项目。

## <a name="considerations"></a>注意事项

如果将现有 Xamarin. Forms 项目从 Classic API 转换为新 Unified API （如果该应用依赖于一个或多个组件或 NuGet 包），则应考虑以下注意事项。

### <a name="components"></a>组件数

还需要将应用程序中包含的任何组件更新为 Unified API，否则在尝试编译时将会出现冲突。 对于包含的任何组件，请将当前版本替换为支持 Unified API 的 Xamarin 组件存储中的新版本，并执行干净的生成。 尚未由作者转换的任何组件将在组件存储区中显示仅限32位的警告。

### <a name="nuget-support"></a>NuGet 支持

虽然我们提供了对 NuGet 的更改来处理 Unified API 支持，但尚未推出新版本的 NuGet，因此我们正在评估如何获取 NuGet 来识别新 Api。

在此之前，你需要将项目中包含的任何 NuGet 包切换到支持统一 Api 的版本，并在以后进行干净的生成。

> [!IMPORTANT]
> 如果您在_同一 Xamarin iOS 项目中出现错误 "错误3不能同时包括 ' monotouch.dialog ' 和 ' xamarin. .dll '"，则会显式引用 "xamarin"，而 "monotouch.dialog" 被 "xxx，Version = 0.0.000，Culture =" 引用非特定类型，PublicKeyToken = null ' "_ 将应用程序转换为统一 api 后，通常是因为项目中的组件或 NuGet 包尚未更新到 Unified API。 需要删除现有的组件/NuGet，更新到支持统一 Api 的版本，并执行干净的生成。

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>启用 Xamarin iOS 应用的64位版本

对于已转换为 Unified API 的 Xamarin iOS 移动应用程序，开发人员仍需从应用的选项中为64位计算机启用应用程序的构建。 有关启用64位生成的详细说明，请参阅启用[32/64 位平台注意事项](~/cross-platform/macios/32-and-64/index.md#enable-64)文档的**Xamarin iOS 应用的64位版本**。

## <a name="summary"></a>总结

现在，Xamarin 应用程序应更新到版本1.3.1，并将 iOS 应用程序迁移到 Unified API （支持 iOS 平台上的64位体系结构）。

如上所述，如果你的 Xamarin. Forms 应用包括自定义呈现器或依赖项服务等本机代码，则这些可能还需要更新才能使用[Unified API 中引入](~/cross-platform/macios/index.md)的新类型。

## <a name="related-links"></a>相关链接

- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-apps.md)
- [更新 iOS 应用程序](~/cross-platform/macios/unified/updating-ios-apps.md)
- [使用跨平台应用中的本机类型](~/cross-platform/macios/native-types-cross-platform.md)
- [更新提示](~/cross-platform/macios/unified/updating-tips.md)
- [经典与 Unified API 差异](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)

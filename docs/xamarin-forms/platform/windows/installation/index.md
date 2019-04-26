---
title: 安装 Windows 项目
description: 较旧的 Xamarin.Forms 解决方案 （或在 macOS 上创建的那些） 未使用通用 Windows 平台项目，因此此文章介绍了如何将新的 UWP 项目添加到现有的 Xamarin.Forms 解决方案。
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: b0f06cf15d3a3ec7eae4742d5d037e233be46d08
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60857573"
---
# <a name="setup-windows-projects"></a>安装 Windows 项目

_将新的 Windows 项目添加到现有的 Xamarin.Forms 解决方案_

较旧的 Xamarin.Forms 解决方案 （或在 macOS 上创建的那些） 将具有通用 Windows 平台 (UWP) 应用程序项目。 因此，您将需要手动添加一个 UWP 项目以构建的 Windows 10 (UWP) 应用。

## <a name="add-a-universal-windows-platform-app"></a>添加通用 Windows 平台应用

**Visual Studio 2019**上**Windows 10**建议生成 UWP 应用。 有关通用 Windows 平台的详细信息，请参阅[通用 Windows 平台简介](/windows/uwp/get-started/universal-application-platform-guide/)。

UWP 是推出 Xamarin.Forms 2.1 及更高版本，且 Xamarin.Forms.Maps 支持 Xamarin.Forms 2.2 及更高版本。

检查<a href="#troubleshooting">故障排除</a>有用提示和技巧的部分。

按照这些说明添加将在 Windows 10 手机、 平板电脑和台式计算机运行的 UWP 应用：

 1 . 右键单击解决方案并选择**添加 > 新建项目...** 并添加**空白应用 (通用 Windows)** 项目：

  ![](universal-images/add-wu.png "添加新项目对话框")

 2 . 在中**新的通用 Windows 平台项目**对话框中，选择应用程序将在运行的 Windows 10 的最小值和目标版本：

  ![](universal-images/target-version.png "新建通用 Windows 平台项目对话框")

 3 . 右键单击 UWP 项目并选择**管理 NuGet 包...** 并添加**Xamarin.Forms**包。 请确保该解决方案中的其他项目也将更新为相同版本的 Xamarin.Forms 包。

 4 . 请确保将中生成新的 UWP 项目**生成 > Configuration Manager**窗口 （发生此情况可能不会是默认情况下）。 刻度线**构建**并**部署**通用项目对应的框：

  [![](universal-images/configuration-sml.png "配置管理器窗口")](universal-images/configuration.png#lightbox "配置管理器窗口")

 5 . 右键单击项目并选择**添加 > 引用**并创建对 Xamarin.Forms 应用程序项目 （.NET Standard 或共享项目） 的引用。

  ![](universal-images/addref-sml.png "引用管理器对话框")

 6 . 在 UWP 项目中，编辑**App.xaml.cs**包括`Init`方法调用置于`OnLaunched`方法在第 52 行：

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7 . 在 UWP 项目中，编辑**MainPage.xaml**通过删除`Grid`中包含`Page`元素。

 8 . 在中**MainPage.xaml**，添加一个新`xmlns`条目`Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9 . 在中**MainPage.xaml**，更改根`<Page`元素`<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10 . 在 UWP 项目中，编辑**MainPage.xaml.cs**若要删除`: Page`继承的类名的说明符 (因为它现在将从继承`WindowsPage`由于上一步中所做的更改):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11 . 在中**MainPage.xaml.cs**，添加`LoadApplication`调用中`MainPage`构造函数，以启动 Xamarin.Forms 应用程序：

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12 . 添加任何本地资源 （例如。 图像文件） 从现有平台项目所需的。

## <a name="troubleshooting"></a>疑难解答

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>"目标调用异常"时使用"使用.NET 本机工具链编译"

如果你的 UWP 应用引用的多个程序集 （例如第三方控件库，或您的应用程序本身拆分成多个库），Xamarin.Forms 可能无法从这些程序集 （如自定义呈现器） 加载对象。

这可能会使用时可能发生**使用.NET Native 工具链汇编**这是一个用于 UWP 应用中的选项**属性 > 生成 > 常规**项目窗口。

可以通过使用的特定于 UWP 的重载来修复此问题`Forms.Init`调用中的**App.xaml.cs**下面的代码中所示 (应替换`ClassInOtherAssembly`实际类与您的代码引用):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

已添加的解决方案资源管理器中，通过直接引用或 NuGet 引用为每个程序集添加一个条目。

#### <a name="dependency-services-and-net-native-compilation"></a>依赖关系服务和.NET Native 编译

使用.NET Native 编译发布版本可能无法解析外部的主应用程序可执行文件 （例如在单独的项目或库） 定义的依赖关系服务。

使用`DependencyService.Register<T>()`方法来手动注册依赖关系服务类。 根据上面的示例中，添加此类的 register 方法：

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```

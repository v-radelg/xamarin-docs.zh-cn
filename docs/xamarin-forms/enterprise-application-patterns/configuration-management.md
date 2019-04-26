---
title: 配置管理
description: 本章介绍 eShopOnContainers 移动应用程序如何实现的配置管理提供应用程序设置和用户设置。
ms.prod: xamarin
ms.assetid: 50d6e780-e768-47f8-9361-3af11e56b87b
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 6f32d8f328232bdfc644da57bdb3201c60010063
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61381891"
---
# <a name="configuration-management"></a>配置管理

设置可用于将配置应用行为的数据与代码相互分离，这允许在不重新构建应用的情况下更改行为。 有两种类型的设置： 应用程序设置和用户设置。

应用设置是应用程序创建和管理的数据。 它可以包括固定的 web 服务终结点、 API 密钥和运行时状态等数据。 应用程序设置绑定到该应用程序存在并且仅对该应用程序有意义。

用户设置是应用程序的可自定义设置，会影响应用程序行为并不需要频繁重新调整。 例如，应用可能会让用户指定从中检索数据，以及如何在屏幕上显示。

Xamarin.Forms 具有可以用于存储设置数据的持久性字典。 可以使用访问此字典[ `Application.Current.Properties` ](xref:Xamarin.Forms.Application.Properties)属性，并放入它的任何数据时，保存应用程序会进入睡眠状态，并应用恢复或重新启动时，会恢复。 此外， [ `Application` ](xref:Xamarin.Forms.Application)类还具有[ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync)允许其设置保存在需要时应用的方法。 有关此字典的详细信息，请参阅[属性字典](~/xamarin-forms/app-fundamentals/application-class.md#Properties_Dictionary)。

使用 Xamarin.Forms 持久字典来存储数据缺点是，它不是轻松地绑定到数据。 因此，eShopOnContainers 移动应用使用 Xam.Plugins.Settings 库，可从[NuGet](https://www.nuget.org/packages/Xam.Plugins.Settings/)。 此库提供了一种一致的、 类型安全、 跨平台的方法，用于保存和检索应用程序和用户设置，同时使用提供的每个平台的本机设置管理。 此外，它是简单地使用数据绑定来访问由库公开的设置数据。

> [!NOTE]
> 尽管 Xam.Plugin.Settings 库可以存储应用程序和用户设置，但会使两者之间没有区别。

## <a name="creating-a-settings-class"></a>创建一个设置类

使用 Xam.Plugins.Settings 库时，单个静态类应创建的将包含所需的应用程序的应用程序和用户设置。 下面的代码示例演示在 eShopOnContainers 的移动应用中设置类：

```csharp
public static class Settings  
{  
    private static ISettings AppSettings  
    {  
        get  
        {  
            return CrossSettings.Current;  
        }  
    }  
    ...  
}
```

可以读取和写入通过设置`ISettings`Xam.Plugins.Settings 库提供的 API。 此库提供了可用于访问 API 的单一实例`CrossSettings.Current`，并应用的设置类应公开此单一实例通过`ISettings`属性。

> [!NOTE]
> 在 Plugin.Settings 和 Plugin.Settings.Abstractions 命名空间的 using 指令应添加到需要 Xam.Plugins.Settings 库类型的访问权限的类。

## <a name="adding-a-setting"></a>添加设置

每个设置组成的键，默认值和属性。 下面的代码示例显示了表示 eShopOnContainers 移动应用连接到联机服务的基 URL 为用户设置的所有三个项：

```csharp
public static class Settings  
{  
    ...  
    private const string IdUrlBase = "url_base";  
    private static readonly string UrlBaseDefault = GlobalSetting.Instance.BaseEndpoint;  
    ...  

    public static string UrlBase  
    {  
        get  
        {  
            return AppSettings.GetValueOrDefault<string>(IdUrlBase, UrlBaseDefault);  
        }  
        set  
        {  
            AppSettings.AddOrUpdateValue<string>(IdUrlBase, value);  
        }  
    }  
}
```

项始终是一个定义密钥名称的常量字符串，该设置的默认值是所需类型的静态只读值。 提供默认值可确保有效的值是检索到取消设置的设置才可用。

`UrlBase`静态属性使用两种方法从`ISettings`API 来读取或写入设置值。 `ISettings.GetValueOrDefault`方法用于检索特定于平台的存储设置的值。 如果设置不定义任何值，而被检索其默认值。 同样，`ISettings.AddOrUpdateValue`方法用于持久保存到特定于平台的存储设置的值。

而是用于定义内的默认值`Settings`类，`UrlBaseDefault`字符串获取其值从`GlobalSetting`类。 下面的代码示例演示`BaseEndpoint`属性和`UpdateEndpoint`中此类的方法：

```csharp
public class GlobalSetting  
{  
    ...  
    public string BaseEndpoint  
    {  
        get { return _baseEndpoint; }  
        set  
        {  
            _baseEndpoint = value;  
            UpdateEndpoint(_baseEndpoint);  
        }  
    }  
    ...  

    private void UpdateEndpoint(string baseEndpoint)  
    {  
        RegisterWebsite = string.Format("{0}:5105/Account/Register", baseEndpoint);  
        CatalogEndpoint = string.Format("{0}:5101", baseEndpoint);  
        OrdersEndpoint = string.Format("{0}:5102", baseEndpoint);  
        BasketEndpoint = string.Format("{0}:5103", baseEndpoint);  
        IdentityEndpoint = string.Format("{0}:5105/connect/authorize", baseEndpoint);  
        UserInfoEndpoint = string.Format("{0}:5105/connect/userinfo", baseEndpoint);  
        TokenEndpoint = string.Format("{0}:5105/connect/token", baseEndpoint);  
        LogoutEndpoint = string.Format("{0}:5105/connect/endsession", baseEndpoint);  
        IdentityCallback = string.Format("{0}:5105/xamarincallback", baseEndpoint);  
        LogoutCallback = string.Format("{0}:5105/Account/Redirecting", baseEndpoint);  
    }  
}
```

每次`BaseEndpoint`属性设置，`UpdateEndpoint`调用方法。 此方法将更新的属性，所有这些基于一系列`UrlBase`提供的用户设置`Settings`表示不同的 eShopOnContainers 移动应用连接到的终结点的类。

## <a name="data-binding-to-user-settings"></a>数据绑定到用户设置

在 eShopOnContainers 移动应用中，`SettingsView`公开两个用户设置。 这些设置允许应用程序是否应检索数据从作为 Docker 容器部署微服务或应用程序是否不需要 internet 连接的模拟服务从应检索数据的配置。 在选择要从容器化微服务中检索数据时，必须指定微服务的终结点基 URL。 图 7-1 显示了`SettingsView`时用户已选择从容器化微服务中检索数据。

![](configuration-management-images/settings-endpoint.png "公开的 eShopOnContainers 移动应用的用户设置")

**图 7-1**:公开的 eShopOnContainers 移动应用的用户设置

数据绑定可用于检索和设置通过公开的设置`Settings`类。 这由视图绑定到反过来访问中的属性的视图模型属性上的控件来实现`Settings`类，并引发属性更改通知，如果设置值已更改。 有关如何在 eShopOnContainers 的移动应用构造视图的信息进行建模，并将它们关联到视图，请参阅[会自动使用视图模型定位符创建视图模型](~/xamarin-forms/enterprise-application-patterns/mvvm.md#automatically_creating_a_view_model_with_a_view_model_locator)。

下面的代码示例演示[ `Entry` ](xref:Xamarin.Forms.Entry)控件从`SettingsView`，允许用户输入容器化微服务的终结点基 URL:

```xaml
<Entry Text="{Binding Endpoint, Mode=TwoWay}" />
```

这[ `Entry` ](xref:Xamarin.Forms.Entry)控件绑定到`Endpoint`属性`SettingsViewModel`类，使用双向绑定。 下面的代码示例显示了终结点属性：

```csharp
public string Endpoint  
{  
    get { return _endpoint; }  
    set  
    {  
        _endpoint = value;  

        if(!string.IsNullOrEmpty(_endpoint))  
        {  
            UpdateEndpoint(_endpoint);  
        }  

        RaisePropertyChanged(() => Endpoint);  
    }  
}
```

当`Endpoint`属性设置`UpdateEndpoint`方法调用，前提是所提供的值是有效的且属性更改引发通知。 下面的代码示例说明 `UpdateEndpoint` 方法：

```csharp
private void UpdateEndpoint(string endpoint)  
{  
    Settings.UrlBase = endpoint;  
}
```

此方法会更新`UrlBase`中的属性`Settings`类与终结点基 URL 值输入该用户后，即可保存到特定于平台的存储器。

当`SettingsView`导航到，`InitializeAsync`中的方法`SettingsViewModel`类执行。 下面的代码示例演示此方法：

```csharp
public override Task InitializeAsync(object navigationData)  
{  
    ...  
    Endpoint = Settings.UrlBase;  
    ...  
}
```

方法设置`Endpoint`属性的值`UrlBase`属性中的`Settings`类。 访问`UrlBase`属性会导致 Xam.Plugins.Settings 库来检索特定于平台的存储的设置值。 有关如何信息`InitializeAsync`调用方法，请参阅[传递参数期间导航](~/xamarin-forms/enterprise-application-patterns/navigation.md#passing_parameters_during_navigation)。

此机制可确保用户导航到 SettingsView，每当用户设置是从特定于平台的存储中检索并显示通过数据绑定。 然后，如果用户更改设置值，可确保数据绑定它们立即保留回特定于平台的存储。

## <a name="summary"></a>总结

设置可用于将配置应用行为的数据与代码相互分离，这允许在不重新构建应用的情况下更改行为。 应用设置是应用创建和管理的数据；用户设置是应用的可自定义设置，可影响应用的行为，但不需要频繁进行重新调整。

Xam.Plugins.Settings 库提供了一致，类型安全、 用于保存和检索应用程序和用户设置和数据绑定的跨平台方法可用于访问与库创建的设置。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)

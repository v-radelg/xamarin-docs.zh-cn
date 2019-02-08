---
title: 在 Xamarin.Forms 中 XAML Namespace 自定义架构
description: 可以使用 XmlnsDefinitionAttribute 类，该类指定自定义 URL 和一个或多个 CLR 命名空间之间的映射定义 XAML 自定义命名空间架构。 然后可以在 XAML 命名空间声明中使用自定义命名空间架构。
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 8167ff00d3e4d7167772f6f5a578da6197c0d72d
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55832198"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>在 Xamarin.Forms 中 XAML Namespace 自定义架构

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)

可以通过使用指定的公共语言运行时 (CLR) 命名空间名称和程序集名称的命名空间声明中声明的 XAML 命名空间的库，在 XAML 中引用库中的类型：

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly="MyCompany.Controls">
    ...
</ContentPage>
```

但是，指定中的 CLR 命名空间和程序集名称`xmlns`定义可以很繁琐且容易出错。 此外，如果库中包含多个命名空间中的类型，需要进行多个 XAML 命名空间声明。

另一种方法是定义自定义命名空间架构，如`http://mycompany.com/schemas/controls`，映射到一个或多个 CLR 命名空间。 这使单个 XAML 命名空间声明来引用程序集中的所有类型，即使它们位于不同的命名空间。 它还使在多个程序集的引用类型到单个 XAML 命名空间声明。

有关 XAML 命名空间的详细信息，请参阅[Xamarin.Forms 中 XAML 命名空间](namespaces.md)。

## <a name="defining-a-custom-namespace-schema"></a>定义自定义命名空间架构

示例应用程序包含一个库，如公开一些简单的控制， `CircleButton`:

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

库中的所有控件都位于`MyCompany.Controls`命名空间。 这些控件可以向自定义命名空间架构通过调用程序集公开。

使用定义的自定义命名空间架构`XmlnsDefinitionAttribute`类，该类指定 XAML 命名空间和一个或多个 CLR 命名空间之间的映射。 `XmlnsDefinitionAttribute`采用两个参数： XAML 命名空间名称和 CLR 命名空间名称。 XAML 命名空间名称存储在`XmlnsDefinitionAttribute.XmlNamespace`属性，并且 CLR 命名空间名称存储在`XmlnsDefinitionAttribute.ClrNamespace`属性。

> [!NOTE]
> `XmlnsDefinitionAttribute`类还具有一个名为属性`AssemblyName`，它可以根据需要设置为程序集的名称。 这只是在从引用的 CLR 命名空间时所需`XmlnsDefinitionAttribute`外部程序集中。

`XmlnsDefinitionAttribute`应在包含将自定义命名空间架构中映射的 CLR 命名空间的项目中的程序集级别定义。 下面的示例演示**AssemblyInfo.cs**示例应用程序中的文件：

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

此代码将创建自定义命名空间架构映射`http://mycompany.com/schemas/controls`URL `MyCompany.Controls` CLR 命名空间。 此外，`Preserve`属性指定程序集，以确保链接器仍保留在程序集中的所有类型。

> [!IMPORTANT]
> `Preserve`属性应应用于类中通过自定义命名空间架构、 映射或应用于整个程序集的程序集。

自定义命名空间架构然后可以用于在 XAML 文件中的类型解析。

## <a name="consuming-a-custom-namespace-schema"></a>使用自定义命名空间架构

若要使用自定义命名空间架构中的类型，XAML 编译器需要使用类型的程序集中的代码引用，对程序集的定义的类型。 这可以通过添加一个类，其中包含实现`Init`定义将通过 XAML 使用的类型的程序集的方法：

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

`Init`然后可以从使用自定义命名空间架构中的类型的程序集调用方法：

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> 不包括此类的代码引用会导致 XAML 编译器在找不到包含自定义命名空间架构类型的程序集。

若要使用`CircleButton`控件，XAML 命名空间声明，使用指定的自定义命名空间架构 URL 的命名空间声明：

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton` 然后可以将实例添加到[ `ContentPage` ](xref:Xamarin.Forms.ContentPage)通过声明它们与`controls`命名空间前缀。

若要查找的自定义的命名空间架构类型，Xamarin.Forms 将搜索引用的程序集的`XmlnsDefinitionAttribute`实例。 如果`xmlns`XAML 文件中的元素的属性与匹配`XmlNamespace`中的属性值`XmlnsDefinitionAttribute`，将尝试使用 Xamarin.Forms`XmlnsDefinitionAttribute.ClrNamespace`进行解析的类型的属性值。 如果类型解析失败，Xamarin.Forms 将继续尝试基于任何其他匹配的类型解析`XmlnsDefinitionAttribute`实例。

结果是两个`CircleButton`实例会显示：

![圆形按钮](custom-namespace-schemas-images/circle-buttons.png "圆形按钮")

## <a name="related-links"></a>相关链接

- [自定义 Namespace 架构 （示例）](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)
- [在 Xamarin.Forms 中 XAML 命名空间](namespaces.md)

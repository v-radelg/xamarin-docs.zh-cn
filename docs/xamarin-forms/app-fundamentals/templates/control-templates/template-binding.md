---
title: 从 Xamarin.Forms ControlTemplate 绑定
description: 模板绑定允许控件模板中的控件将数据绑定到公共属性，从而可以轻松更改控件模板中控件的属性值。 本文演示如何使用模板绑定从控件模板执行数据绑定。
ms.prod: xamarin
ms.assetid: 794A663C-3A8D-438A-BD02-8E97C919B55F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: e4e67ba63d2040bff264498bc9c00124dfd22164
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926338"
---
# <a name="binding-from-a-xamarinforms-controltemplate"></a>从 Xamarin.Forms ControlTemplate 绑定

[![下载示例](~/media/shared/download.png) 下载示例](https://developer.xamarin.com/samples/xamarin-forms/Templates/ControlTemplates/SimpleThemeWithTemplateBinding/)

模板绑定允许控件模板中的控件将数据绑定到公共属性，从而可以轻松更改控件模板中控件的属性值。本文演示如何使用模板绑定从控件模板执行数据绑定_。

[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 用于将控件模板中的控件属性绑定到拥有控件模板的目标视图的父级上的可绑定属性。 例如，可以使用模板绑定将 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 属性绑定到定义要显示的文本的可绑定属性，而不是定义通过 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 中的 [`Label`](xref:Xamarin.Forms.Label) 实例显示的文字。

[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 类似于现有的 [`Binding`](xref:Xamarin.Forms.Binding)，区别在于 `TemplateBinding` 的源始终自动设置为拥有控件模板的目标视图的父级。 但请注意，不支持在 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 之外使用 `TemplateBinding`。

## <a name="creating-a-templatebinding-in-xaml"></a>在 XAML 中创建 TemplateBinding

在 XAML 中，使用 [`TemplateBinding`](xref:Xamarin.Forms.Xaml.TemplateBindingExtension) 标记扩展创建 [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding)，如下面代码示例所示：

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

属性可以使用模板绑定来绑定到拥有 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 的目标视图的父级上的可绑定属性，而不是将 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 属性设置为静态文本。 但请注意，模板绑定绑定到 `Parent.HeaderText` 和 `Parent.FooterText`，而不是 `HeaderText` 和 `FooterText`。 这是因为在此示例中，可绑定属性是在目标视图的祖父节点上定义的，而不是父节点，如以下代码示例所示：

```xaml
<ContentPage ...>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
          ...
    </ContentView>
</ContentPage>
```

模板绑定的源始终自动设置为拥有控件模板的目标视图的父级，此处为 [`ContentView`](xref:Xamarin.Forms.ContentView) 实例。 模板绑定使用 [`Parent`](xref:Xamarin.Forms.Element.Parent) 属性返回 `ContentView` 实例的父元素，即 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 实例。 因此，使用 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 中的 [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 绑定到 `Parent.HeaderText` 和 `Parent.FooterText` 可查找 `ContentPage` 上定义的可绑定属性，如以下代码示例：

```csharp
public static readonly BindableProperty HeaderTextProperty =
  BindableProperty.Create ("HeaderText", typeof(string), typeof(HomePage), "Control Template Demo App");
public static readonly BindableProperty FooterTextProperty =
  BindableProperty.Create ("FooterText", typeof(string), typeof(HomePage), "(c) Xamarin 2016");

public string HeaderText {
  get { return (string)GetValue (HeaderTextProperty); }
}

public string FooterText {
  get { return (string)GetValue (FooterTextProperty); }
}
```

这会导致如以下屏幕截图中所示的外观：

![](template-binding-images/teal-theme.png "使用模板绑定的 Teal 控件模板")

## <a name="creating-a-templatebinding-in-c35"></a>在 C&#35 中创建模板绑定；

在 C# 中，使用 `TemplateBinding` 构造函数创建 [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding)，如下面代码示例所示：

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var topLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    topLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.HeaderText"));
    ...
    var bottomLabel = new Label { TextColor = Color.White, VerticalOptions = LayoutOptions.Center };
    bottomLabel.SetBinding (Label.TextProperty, new TemplateBinding ("Parent.FooterText"));
    ...
  }
}
```

属性可以使用模板绑定来绑定到拥有 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 的目标视图的父级上的可绑定属性，而不是将 [`Label.Text`](xref:Xamarin.Forms.Label.Text) 属性设置为静态文本。 使用 [`SetBinding`](xref:Xamarin.Forms.BindableObject.SetBinding(Xamarin.Forms.BindableProperty,Xamarin.Forms.BindingBase)) 方法创建模板绑定，并指定 [`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 实例作为第二个参数。 请注意，模板绑定会绑定到 `Parent.HeaderText` 和 `Parent.FooterText`，因为可绑定属性是在目标视图的祖父节点而不是父节点上定义的，如以下代码示例所示：

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    Content = new ContentView {
      ControlTemplate = tealTemplate,
      Content = new StackLayout {
        ...
      },
      ...
    };
    ...
  }
}
```

如前面所述，在 `ContentPage` 上定义可绑定属性。

### <a name="binding-a-bindableproperty-to-a-viewmodel-property"></a>将 BindableProperty 绑定到 ViewModel 属性

如前面所述，[`TemplateBinding`](xref:Xamarin.Forms.TemplateBinding) 将控件模板中的控件属性绑定到拥有控件模板的目标视图的父级上的可绑定属性。 反之，这些可绑定属性可绑定到 Viewmodel 中的属性。

下面的代码示例在 ViewModel 上定义两个属性：

```csharp
public class HomePageViewModel
{
  public string HeaderText { get { return "Control Template Demo App"; } }
  public string FooterText { get { return "(c) Xamarin 2016"; } }
}
```

可绑定 `HeaderText` 和 `FooterText` ViewModel 属性，如以下 XAML 代码示例所示：

```xaml
<ContentPage xmlns:local="clr-namespace:SimpleTheme;assembly=SimpleTheme"
             HeaderText="{Binding HeaderText}" FooterText="{Binding FooterText}" ...>
    <ContentPage.BindingContext>
        <local:HomePageViewModel />
    </ContentPage.BindingContext>
    <ContentView ControlTemplate="{StaticResource TealTemplate}" ...>
    ...
    </ContentView>
</ContentPage>
```

由于将 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为 `HomePageViewModel` 类的实例，因此可将`HeaderText` 和 `FooterText` 可绑定属性绑定到 `HomePageViewModel.HeaderText` 和 `HomePageViewModel.FooterText` 属性。 总体而言，这使得 [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) 中的控件属性绑定到 [`ContentPage`](xref:Xamarin.Forms.ContentPage) 上的 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 实例，而这些实例又绑定到 ViewModel 属性。

以下代码示例显示相应的 C# 代码：

```csharp
public class HomePageCS : ContentPage
{
  ...
  public HomePageCS ()
  {
    BindingContext = new HomePageViewModel ();
    this.SetBinding (HeaderTextProperty, "HeaderText");
    this.SetBinding (FooterTextProperty, "FooterText");
    ...
  }
}
```

还可以直接绑定到视图模型属性，因此无需通过将控件模板绑定到 Parent.BindingContext 来为 `ContentPage` 上的 `HeaderText` 和 `FooterText` 声明 `BindableProperty`。例如 PropertyName：

```xaml
<ControlTemplate x:Key="TealTemplate">
  <Grid>
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.HeaderText}" ... />
    ...
    <Label Text="{TemplateBinding Parent.BindingContext.FooterText}" ... />
  </Grid>
</ControlTemplate>
```

有关将数据绑定到 Viewmodel 的详细信息，请参阅[从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)。

## <a name="summary"></a>总结

本文演示了如何使用模板绑定从控件模板执行数据绑定。 模板绑定允许控件模板中的控件将数据绑定到公共属性，从而可以轻松更改控件模板中控件的属性值。

## <a name="related-links"></a>相关链接

- [数据绑定基础知识](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [从数据绑定到 MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)
- [带有模板绑定的简单主题（示例）](https://developer.xamarin.com/samples/xamarin-forms/Templates/ControlTemplates/SimpleThemeWithTemplateBinding/)
- [带有模板绑定和 ViewModel 的简单主题（示例）](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simplethemewithtemplatebindingandviewmodel/)
- [TemplateBinding](xref:Xamarin.Forms.TemplateBinding)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentView](xref:Xamarin.Forms.ContentView)

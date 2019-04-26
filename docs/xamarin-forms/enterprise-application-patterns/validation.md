---
title: 企业应用程序中的验证
description: 本章介绍 eShopOnContainers 移动应用程序如何执行验证的用户输入。 这包括指定验证规则、 触发验证，以及显示验证错误。
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: 22b5fe703486f0ded3a5b91241e3fe5ce41bbc98
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61298910"
---
# <a name="validation-in-enterprise-apps"></a>企业应用程序中的验证

任何接受用户输入的应用都应确保输入是有效的。 输入包含在特定范围内的唯一字符，具有特定长度，或与特定的格式，例如，检查应用。 如果未进行验证，用户提供的数据可能导致应用故障。 验证可强制实施业务规则，并防止攻击者注入恶意数据。

在上下文的模型-视图-视图模型 (MVVM) 模式，视图模型或模型通常需要执行数据验证和信号到视图的任何验证错误，以便用户可以更正它们。 EShopOnContainers 移动应用执行的视图模型属性的同步客户端验证，并通过突出显示该控件包含无效数据，以及显示错误消息，告知用户通知任何验证错误的用户数据无效的原因。 图 6-1 显示 eShopOnContainers 移动应用中执行验证所涉及的类。

[![](validation-images/validation.png "在 eShopOnContainers 的移动应用中的验证类")](validation-images/validation-large.png#lightbox "eShopOnContainers 移动应用中的验证类")

**图 6-1**:在 eShopOnContainers 的移动应用中的验证类

要求进行验证的视图模型属性属于类型`ValidatableObject<T>`，和每个`ValidatableObject<T>`实例具有的验证规则添加到其`Validations`属性。 通过调用从视图模型调用验证`Validate`方法`ValidatableObject<T>`实例，检索验证规则并执行针对这些`ValidatableObject<T>``Value`属性。 任何验证错误放入`Errors`的属性`ValidatableObject<T>`实例，并`IsValid`属性的`ValidatableObject<T>`实例将更新以指示验证是成功还是失败。

提供属性更改通知`ExtendedBindableObject`类，因此[ `Entry` ](xref:Xamarin.Forms.Entry)控件可以绑定到`IsValid`属性`ValidatableObject<T>`视图模型类，即是否通知中的实例输入的数据有效。

## <a name="specifying-validation-rules"></a>指定验证规则

通过创建派生的类指定验证规则`IValidationRule<T>`接口，在下面的代码示例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

此接口指定验证规则类必须提供`boolean``Check`用于执行所需的验证方法和一个`ValidationMessage`其值是如果，则将显示的验证错误消息的属性验证将失败。

下面的代码示例演示`IsNotNullOrEmptyRule<T>`验证规则，用于执行验证的用户名和密码用户输入的`LoginView`eShopOnContainers 移动应用中使用的模拟服务时：

```csharp
public class IsNotNullOrEmptyRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        return !string.IsNullOrWhiteSpace(str);  
    }  
}
```

`Check`方法将返回`boolean`，该值指示是否为值参数`null`、 空还是仅包含空白字符。

尽管不使用 eShopOnContainers 移动应用，但下面的代码示例显示了用于验证电子邮件地址的验证规则：

```csharp
public class EmailRule<T> : IValidationRule<T>  
{  
    public string ValidationMessage { get; set; }  

    public bool Check(T value)  
    {  
        if (value == null)  
        {  
            return false;  
        }  

        var str = value as string;  
        Regex regex = new Regex(@"^([\w\.\-]+)@([\w\-]+)((\.(\w){2,3})+)$");  
        Match match = regex.Match(str);  

        return match.Success;  
    }  
}
```

`Check`方法将返回`boolean`，该值指示值自变量是否有效的电子邮件地址。 这通过搜索中指定的正则表达式模式的第一个匹配项的值参数来实现`Regex`构造函数。 可以通过检查的值来确定正则表达式模式是否已在输入字符串中找到`Match`对象的`Success`属性。

> [!NOTE]
> 属性验证有时可能涉及到依赖属性。 有关属性的有效值集取决于已设置属性 B.中的特定值时的依赖属性示例若要检查的属性的值是一个允许的值将涉及检索 B.属性的值此外，当属性 B 的值更改时，属性 A 将需要重新验证。

## <a name="adding-validation-rules-to-a-property"></a>将验证规则添加到属性

在 eShopOnContainers 移动应用中，要求进行验证的视图模型属性声明为类型`ValidatableObject<T>`，其中`T`是要验证的数据类型。 下面的代码示例显示了两个此类属性的示例：

```csharp
public ValidatableObject<string> UserName  
{  
    get  
    {  
        return _userName;  
    }  
    set  
    {  
        _userName = value;  
        RaisePropertyChanged(() => UserName);  
    }  
}  

public ValidatableObject<string> Password  
{  
    get  
    {  
        return _password;  
    }  
    set  
    {  
        _password = value;  
        RaisePropertyChanged(() => Password);  
    }  
}
```

要进行的验证，必须将验证规则添加到`Validations`的每个集合`ValidatableObject<T>`实例，如下面的代码示例中所示：

```csharp
private void AddValidations()  
{  
    _userName.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A username is required."   
    });  
    _password.Validations.Add(new IsNotNullOrEmptyRule<string>   
    {   
        ValidationMessage = "A password is required."   
    });  
}
```

此方法将添加`IsNotNullOrEmptyRule<T>`验证规则应用于`Validations`的每个集合`ValidatableObject<T>`实例，指定的验证规则的值`ValidationMessage`属性，用于指定如果，则将显示的验证错误消息验证将失败。

## <a name="triggering-validation"></a>触发验证

EShopOnContainers 移动应用中使用的验证方法可以手动触发的一个属性，验证和自动触发器验证属性更改时。

### <a name="triggering-validation-manually"></a>手动触发验证

视图模型属性，可以手动触发验证。 例如，此错误出现在 eShopOnContainers 的移动应用程序时在用户点击**登录名**按钮`LoginView`，当使用的模拟服务。 命令委托调用`MockSignInAsync`中的方法`LoginViewModel`，这样就可通过执行调用验证`Validate`方法，在下面的代码示例所示：

```csharp
private bool Validate()  
{  
    bool isValidUser = ValidateUserName();  
    bool isValidPassword = ValidatePassword();  
    return isValidUser && isValidPassword;  
}  

private bool ValidateUserName()  
{  
    return _userName.Validate();  
}  

private bool ValidatePassword()  
{  
    return _password.Validate();  
}
```

`Validate`方法执行验证的用户名和密码输入的用户名`LoginView`，通过调用每个的 Validate 方法`ValidatableObject<T>`实例。 下面的代码示例显示了从 Validate 方法`ValidatableObject<T>`类：

```csharp
public bool Validate()  
{  
    Errors.Clear();  

    IEnumerable<string> errors = _validations  
        .Where(v => !v.Check(Value))  
        .Select(v => v.ValidationMessage);  

    Errors = errors.ToList();  
    IsValid = !Errors.Any();  

    return this.IsValid;  
}
```

此方法将清除`Errors`集合，然后检索任何验证规则，已添加到对象的`Validations`集合。 `Check`执行每个检索到的验证规则的方法时，将`ValidationMessage`无法验证数据的任何验证规则的属性值添加到`Errors`的集合`ValidatableObject<T>`实例。 最后，`IsValid`设置属性，并且其值返回到调用方法，该值指示验证是成功还是失败。

### <a name="triggering-validation-when-properties-change"></a>当属性更改时触发验证

绑定的属性发生更改时，也可以触发验证。 例如，当在一个双向绑定`LoginView`设置`UserName`或`Password`触发验证属性。 下面的代码示例演示了如何发生这种情况：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    <Entry.Behaviors>  
        <behaviors:EventToCommandBehavior  
            EventName="TextChanged"  
            Command="{Binding ValidateUserNameCommand}" />  
    </Entry.Behaviors>  
    ...  
</Entry>
```

[ `Entry` ](xref:Xamarin.Forms.Entry)控件绑定到`UserName.Value`属性`ValidatableObject<T>`实例，并且该控件的`Behaviors`集合具有`EventToCommandBehavior`实例添加到它。 此行为执行`ValidateUserNameCommand`响应 [`TextChanged`] 事件触发`Entry`，它时引发中的文本`Entry`更改。 依次`ValidateUserNameCommand`执行委托`ValidateUserName`方法，执行`Validate`方法`ValidatableObject<T>`实例。 因此，每次用户输入中的字符`Entry`作为用户名，输入数据的验证控件执行。

有关行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>显示验证错误

EShopOnContainers 移动应用通知的任何验证错误的用户通过突出显示该控件包含无效的数据与一条红线，并通过显示错误消息，为什么要将通知用户的数据无效下面包含的控件无效的数据。 更正无效的数据，行更改为黑色，并删除错误消息。 存在验证错误时，图 6-2 eShopOnContainers 移动应用中显示登录视图。

![](validation-images/validation-login.png "在登录期间显示验证错误")

**图 6-2:** 在登录期间显示验证错误

### <a name="highlighting-a-control-that-contains-invalid-data"></a>突出显示控件包含无效数据

`LineColorBehavior`附加的行为用于突出显示[ `Entry` ](xref:Xamarin.Forms.Entry)发生了验证错误的控件。 下面的代码示例演示如何`LineColorBehavior`附加的行为附加到`Entry`控件：

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">
    <Entry.Style>
        <OnPlatform x:TypeArguments="Style">
            <On Platform="iOS, Android" Value="{StaticResource EntryStyle}" />
            <On Platform="UWP" Value="{StaticResource UwpEntryStyle}" />
        </OnPlatform>
    </Entry.Style>
    ...
</Entry>
```

[ `Entry` ](xref:Xamarin.Forms.Entry)控件使用显式样式，下面的代码示例中所示：

```xaml
<Style x:Key="EntryStyle"  
       TargetType="{x:Type Entry}">  
    ...  
    <Setter Property="behaviors:LineColorBehavior.ApplyLineColor"  
            Value="True" />  
    <Setter Property="behaviors:LineColorBehavior.LineColor"  
            Value="{StaticResource BlackColor}" />  
    ...  
</Style>
```

此样式设置`ApplyLineColor`并`LineColor`附加的属性`LineColorBehavior`上附加行为[ `Entry` ](xref:Xamarin.Forms.Entry)控件。 有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

时的值`ApplyLineColor`附加的属性是集或更改`LineColorBehavior`附加的行为执行`OnApplyLineColorChanged`方法，在下面的代码示例所示：

```csharp
public static class LineColorBehavior  
{  
    ...  
    private static void OnApplyLineColorChanged(  
                BindableObject bindable, object oldValue, object newValue)  
    {  
        var view = bindable as View;  
        if (view == null)  
        {  
            return;  
        }  

        bool hasLine = (bool)newValue;  
        if (hasLine)  
        {  
            view.Effects.Add(new EntryLineColorEffect());  
        }  
        else  
        {  
            var entryLineColorEffectToRemove =   
                    view.Effects.FirstOrDefault(e => e is EntryLineColorEffect);  
            if (entryLineColorEffectToRemove != null)  
            {  
                view.Effects.Remove(entryLineColorEffectToRemove);  
            }  
        }  
    }  
}
```

此方法的参数提供的控件的行为附加到，实例和的新旧值`ApplyLineColor`附加属性。 `EntryLineColorEffect`类添加到控件的[ `Effects` ](xref:Xamarin.Forms.Element.Effects)集合如果`ApplyLineColor`附加的属性是`true`，否则删除从控件的`Effects`集合。 有关行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

`EntryLineColorEffect`子类[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)类，并在下面的代码示例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect)类表示一种独立于平台的效果，包装是特定于平台的内部效果。 这简化了效果删除过程，因为对于特定于平台的效果，没有对类型信息的编译时访问。 `EntryLineColorEffect`调用基类构造函数，传入参数包含解析组名称，并在每个特定于平台的效果类指定的唯一 ID 的串联。

下面的代码示例演示`eShopOnContainers.EntryLineColorEffect`适用于 iOS 的实现：

```csharp
[assembly: ResolutionGroupName("eShopOnContainers")]  
[assembly: ExportEffect(typeof(EntryLineColorEffect), "EntryLineColorEffect")]  
namespace eShopOnContainers.iOS.Effects  
{  
    public class EntryLineColorEffect : PlatformEffect  
    {  
        UITextField control;  

        protected override void OnAttached()  
        {  
            try  
            {  
                control = Control as UITextField;  
                UpdateLineColor();  
            }  
            catch (Exception ex)  
            {  
                Console.WriteLine("Can't set property on attached control. Error: ", ex.Message);  
            }  
        }  

        protected override void OnDetached()  
        {  
            control = null;  
        }  

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)  
        {  
            base.OnElementPropertyChanged(args);  

            if (args.PropertyName == LineColorBehavior.LineColorProperty.PropertyName ||  
                args.PropertyName == "Height")  
            {  
                Initialize();  
                UpdateLineColor();  
            }  
        }  

        private void Initialize()  
        {  
            var entry = Element as Entry;  
            if (entry != null)  
            {  
                Control.Bounds = new CGRect(0, 0, entry.Width, entry.Height);  
            }  
        }  

        private void UpdateLineColor()  
        {  
            BorderLineLayer lineLayer = control.Layer.Sublayers.OfType<BorderLineLayer>()  
                                                             .FirstOrDefault();  

            if (lineLayer == null)  
            {  
                lineLayer = new BorderLineLayer();  
                lineLayer.MasksToBounds = true;  
                lineLayer.BorderWidth = 1.0f;  
                control.Layer.AddSublayer(lineLayer);  
                control.BorderStyle = UITextBorderStyle.None;  
            }  

            lineLayer.Frame = new CGRect(0f, Control.Frame.Height-1f, Control.Bounds.Width, 1f);  
            lineLayer.BorderColor = LineColorBehavior.GetLineColor(Element).ToCGColor();  
            control.TintColor = control.TextColor;  
        }  

        private class BorderLineLayer : CALayer  
        {  
        }  
    }  
}
```

`OnAttached`方法检索的本机控件适用于 Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry)控件，并通过调用来更新的线条颜色`UpdateLineColor`方法。 `OnElementPropertyChanged`重写上可绑定属性更改响应`Entry`通过如果更新的线条颜色的控件附加`LineColor`属性更改或[ `Height` ](xref:Xamarin.Forms.VisualElement.Height)属性`Entry`更改。 有关效果的更多信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

当在输入有效的数据时[ `Entry` ](xref:Xamarin.Forms.Entry)控件，将其应用一条黑线控件，以指示不不存在任何验证错误的底部。 图 6-3 显示了此示例。

![](validation-images/validation-blackline.png "黑色线条，该值指示任何验证错误")

**图 6-3**:黑色线条，该值指示任何验证错误

[ `Entry` ](xref:Xamarin.Forms.Entry)控件还具有[ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger)添加到其[ `Triggers` ](xref:Xamarin.Forms.VisualElement.Triggers)集合。 下面的代码示例演示`DataTrigger`:

```xaml
<Entry Text="{Binding UserName.Value, Mode=TwoWay}">  
    ...  
    <Entry.Triggers>  
        <DataTrigger   
            TargetType="Entry"  
            Binding="{Binding UserName.IsValid}"  
            Value="False">  
            <Setter Property="behaviors:LineColorBehavior.LineColor"   
                    Value="{StaticResource ErrorColor}" />  
        </DataTrigger>  
    </Entry.Triggers>  
</Entry>
```

这[ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger)监视器`UserName.IsValid`属性，而如果它是值将变成`false`，它将执行[ `Setter` ](xref:Xamarin.Forms.Setter)，哪些更改`LineColor`附加属性的`LineColorBehavior`附加到红色的行为。 图 6-4 显示了此示例。

![](validation-images/validation-redline.png "红线表示验证错误的错误")

**图 6-4**:红线表示验证错误的错误

中的行[ `Entry` ](xref:Xamarin.Forms.Entry)控件将保持红色，而是无效的输入的数据，否则它将更改为黑色以指示输入的数据是否有效。

有关触发器的详细信息，请参阅[触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

### <a name="displaying-error-messages"></a>显示的错误消息

UI 在其数据未能通过验证每个控件的下方的标签控件中显示验证错误消息。 下面的代码示例演示[ `Label` ](xref:Xamarin.Forms.Label)如果用户未输入有效的用户名显示一条验证错误消息：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每个[ `Label` ](xref:Xamarin.Forms.Label)绑定到`Errors`所验证的视图模型对象的属性。 `Errors`提供的属性`ValidatableObject<T>`类，并且类型`List<string>`。 因为`Errors`属性可以包含多个验证错误，`FirstValidationErrorConverter`实例用于显示集合中检索的第一个错误。

## <a name="summary"></a>总结

EShopOnContainers 移动应用执行的视图模型属性的同步客户端验证，并通过突出显示该控件包含无效数据，以及显示错误消息，告知用户通知任何验证错误的用户为什么的数据无效。

要求进行验证的视图模型属性属于类型`ValidatableObject<T>`，和每个`ValidatableObject<T>`实例具有的验证规则添加到其`Validations`属性。 通过调用从视图模型调用验证`Validate`方法`ValidatableObject<T>`实例，检索验证规则并执行针对这些`ValidatableObject<T>``Value`属性。 任何验证错误放入`Errors`的属性`ValidatableObject<T>`实例，并`IsValid`属性的`ValidatableObject<T>`实例将更新以指示验证是成功还是失败。


## <a name="related-links"></a>相关链接

- [下载电子书 (2 Mb PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) （示例）](https://github.com/dotnet-architecture/eShopOnContainers)

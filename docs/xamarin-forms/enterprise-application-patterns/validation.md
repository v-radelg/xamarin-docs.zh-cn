---
title: 企业应用程序中的验证
description: 本章介绍 eShopOnContainers mobile 应用如何执行用户输入验证。 这包括指定验证规则、触发验证和显示验证错误。
ms.prod: xamarin
ms.assetid: 56e4f0fc-48d9-4033-91ec-173bb46a5e4d
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: de5728710a408b8e0c7c68dc89c7e6484cbcc3ce
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760168"
---
# <a name="validation-in-enterprise-apps"></a>企业应用程序中的验证

任何接受用户输入的应用都应确保输入是有效的。 例如，应用程序可以检查输入中是否仅包含特定范围内的字符、是否为特定长度，或是否匹配特定格式。 如果未进行验证，用户提供的数据可能导致应用故障。 验证可强制实施业务规则，并防止攻击者注入恶意数据。

在 ViewModel （MVVM）模式的上下文中，视图模型或模型通常需要执行数据验证并向视图发出任何验证错误信号，以便用户可以更正这些错误。 EShopOnContainers 移动应用执行视图模型属性的同步客户端验证，通过突出显示包含无效数据的控件并显示通知用户的错误消息，通知用户任何验证错误。数据无效的原因。 图6-1 显示了在 eShopOnContainers 移动应用程序中执行验证所涉及的类。

[EShopOnContainers 移动应用中的验证类![(validation-images/validation.png " ")]](validation-images/validation-large.png#lightbox "EShopOnContainers 移动应用中的验证类")

**图 6-1**：EShopOnContainers 移动应用中的验证类

需要验证的视图模型属性的类型`ValidatableObject<T>`为，并且每个`ValidatableObject<T>`实例都已将验证规则`Validations`添加到其属性中。 `Validate`验证是通过调用`ValidatableObject<T>`实例的方法从视图模型调用的，该方法检索验证规则`ValidatableObject<T>` `Value`并对属性执行这些验证规则。 任何验证错误都将放入`Errors` `ValidatableObject<T>`实例的属性中， `ValidatableObject<T>`并更新`IsValid`实例的属性以指示验证是成功还是失败。

`ExtendedBindableObject`属性更改通知由类提供， [`Entry`](xref:Xamarin.Forms.Entry)因此控件可以`ValidatableObject<T>`绑定到视图模型类中的实例`IsValid`的属性，以通知所输入的数据是否有效。

## <a name="specifying-validation-rules"></a>指定验证规则

验证规则是通过创建从`IValidationRule<T>`接口派生的类来指定的，如下面的代码示例所示：

```csharp
public interface IValidationRule<T>  
{  
    string ValidationMessage { get; set; }  
    bool Check(T value);  
}
```

此接口指定验证规则类必须提供`boolean` `Check`用于执行所需验证的方法，并指定一个`ValidationMessage`属性，其值为验证错误消息（如果验证失败。

下面的代码示例演示`IsNotNullOrEmptyRule<T>`验证规则，该规则用于在 eShopOnContainers 移动应用中使用模拟服务`LoginView`时对用户在上输入的用户名和密码进行验证：

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

方法返回一个`boolean` ，它指示值参数是`null`、空还是仅由空白字符组成。 `Check`

下面的代码示例演示了用于验证电子邮件地址的验证规则：

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

方法返回一个`boolean` ，它指示值参数是否为有效的电子邮件地址。 `Check` 为此，可在`Regex`构造函数中搜索指定的正则表达式模式的第一个匹配项的值参数。 是否可以通过检查`Match`对象的`Success`属性的值来确定是否在输入字符串中找到了正则表达式模式。

> [!NOTE]
> 属性验证有时可以涉及依赖属性。 依赖属性的一个示例是，属性 A 的有效值集取决于在属性 B 中设置的特定值。若要检查属性 A 的值是否为允许的值之一，将需要检索属性 B 的值。此外，当属性 B 的值更改时，需要重新验证属性 A。

## <a name="adding-validation-rules-to-a-property"></a>将验证规则添加到属性

在 eShopOnContainers 移动应用中，查看需要验证的模型属性的类型`ValidatableObject<T>`为，其中`T`是要验证的数据的类型。 下面的代码示例显示了两个这样的属性的示例：

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

若要进行验证，必须将验证规则添加到`Validations`每个`ValidatableObject<T>`实例的集合中，如下面的代码示例所示：

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

此`IsNotNullOrEmptyRule<T>`方法将验证规则添加到每`Validations`个`ValidatableObject<T>`实例的集合`ValidationMessage` ，并指定验证规则属性的值，该属性指定在以下情况下将显示的验证错误消息：验证失败。

## <a name="triggering-validation"></a>触发验证

EShopOnContainers 移动应用中使用的验证方法可以手动触发属性验证，并在属性发生更改时自动触发验证。

### <a name="triggering-validation-manually"></a>手动触发验证

可以为视图模型属性手动触发验证。 例如，当用户点击上`LoginView`的 "**登录**" 按钮时，将在 eShopOnContainers 移动应用中出现这种情况。 命令委托调用`MockSignInAsync` `LoginViewModel`中的方法，该方法通过执行`Validate`方法来调用验证，如以下代码示例所示：

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

方法通过对每个`ValidatableObject<T>`实例调用 Validate 方法`LoginView`，对上用户输入的用户名和密码进行验证。 `Validate` 下面的代码示例演示`ValidatableObject<T>`类中的 Validate 方法：

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

此方法清除`Errors`集合，然后检索添加到对象的`Validations`集合中的任何验证规则。 将`Check`执行每个检索到的验证规则的方法， `ValidationMessage`并且将无法验证`Errors`数据的任何验证规则的属性值添加`ValidatableObject<T>`到实例的集合中。 最后，设置`IsValid`该属性，并将其值返回给调用方法，指示验证是成功还是失败。

### <a name="triggering-validation-when-properties-change"></a>属性更改时触发验证

只要绑定属性发生更改，也可以触发验证。 例如，当中`LoginView`的双向绑定`UserName`设置或`Password`属性时，将触发验证。 下面的代码示例演示了这种情况的发生方式：

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

`Behaviors` `UserName.Value` `ValidatableObject<T>`控件将绑定到该实例的属性，并向该控件的集合添加一个`EventToCommandBehavior`实例。 [`Entry`](xref:Xamarin.Forms.Entry) 此行为`ValidateUserNameCommand`在响应中的 [`TextChanged` `Entry`] 事件触发时执行，在中的文本`Entry`发生更改时引发。 反过来， `ValidateUserNameCommand` `ValidatableObject<T>`委托`ValidateUserName` 执行`Validate`方法，该方法对实例执行方法。 因此，每次用户在`Entry`控件中为用户名输入一个字符时，都会对输入的数据进行验证。

有关行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

<a name="displaying_validation_errors" />

## <a name="displaying-validation-errors"></a>显示验证错误

EShopOnContainers 移动应用通过以下方式向用户通知任何验证错误：突出显示包含无效数据的控件，并显示一条错误消息，告知用户数据在包含数据无效。 当更正无效数据时，该行将变为黑色，并删除错误消息。 图6-2 显示验证错误时 eShopOnContainers 移动应用中的登录视图。

![](validation-images/validation-login.png "在登录过程中显示验证错误")

**图6-2：** 在登录过程中显示验证错误

### <a name="highlighting-a-control-that-contains-invalid-data"></a>突出显示包含无效数据的控件

附加`LineColorBehavior`的行为用于突出显示[`Entry`](xref:Xamarin.Forms.Entry)发生验证错误的控件。 下面的代码示例演示如何`LineColorBehavior`将附加行为附加`Entry`到控件：

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

[`Entry`](xref:Xamarin.Forms.Entry)控件使用显式样式，如以下代码示例所示：

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

此`ApplyLineColor`样式设置`LineColor` [控件`Entry`](xref:Xamarin.Forms.Entry)上`LineColorBehavior`附加行为的和附加属性。 有关样式的详细信息，请参阅[样式](~/xamarin-forms/user-interface/styles/index.md)。

当设置`ApplyLineColor`附加属性的值或更改时`LineColorBehavior` ，附加行为将执行`OnApplyLineColorChanged`方法，如以下代码示例所示：

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

此方法的参数提供行为所附加到的控件的实例，以及`ApplyLineColor`附加属性的新旧值。 [`Effects`](xref:Xamarin.Forms.Element.Effects) `Effects`如果`EntryLineColorEffect` 附加属性`true`为，则将类添加到控件的集合，否则将从控件的集合中删除该类。 `ApplyLineColor` 有关行为的详细信息，请参阅[实现行为](~/xamarin-forms/enterprise-application-patterns/mvvm.md#implementing_behaviors)。

`EntryLineColorEffect` [子类为类，如下面的`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)代码示例所示：

```csharp
public class EntryLineColorEffect : RoutingEffect  
{  
    public EntryLineColorEffect() : base("eShopOnContainers.EntryLineColorEffect")  
    {  
    }  
}
```

[`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect)类表示一个独立于平台的效果，该效果包装了平台特定的内部效果。 这简化了效果删除过程，因为对于特定于平台的效果，没有对类型信息的编译时访问。 `EntryLineColorEffect`调用基类构造函数，传入一个参数，该参数由解析组名称的连接和在每个特定于平台的效果类上指定的唯一 ID 组成。

下面的代码示例演示`eShopOnContainers.EntryLineColorEffect` iOS 实现：

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

方法检索 Xamarin [`Entry`](xref:Xamarin.Forms.Entry)控件的本机控件，并通过调用`UpdateLineColor`方法更新线条颜色。 `OnAttached` `LineColor` [`Height`](xref:Xamarin.Forms.VisualElement.Height) 重写`Entry`通过在附加属性更改或更改`Entry`的属性时更新线条颜色来响应控件上的可绑定属性更改。 `OnElementPropertyChanged` 有关效果的更多信息，请参阅[效果](~/xamarin-forms/app-fundamentals/effects/index.md)。

当在[`Entry`](xref:Xamarin.Forms.Entry)控件中输入有效数据时，它将在控件的底部应用一条黑线，以指示不存在验证错误。 图6-3 显示了一个示例。

![](validation-images/validation-blackline.png "指示无验证错误的黑色线条")

**图 6-3**：指示无验证错误的黑色线条

控件还将添加到其[`Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)集合中。 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) [`Entry`](xref:Xamarin.Forms.Entry) 下面的代码示例演示`DataTrigger`：

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

这[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)会[`Setter`](xref:Xamarin.Forms.Setter) `false` `LineColor` `LineColorBehavior`监视属性，如果其值为，它将执行，这会将附加行为的附加属性更改为红色。 `UserName.IsValid` 图6-4 显示了一个示例。

![](validation-images/validation-redline.png "指示验证错误的红线")

**图 6-4**：指示验证错误的红线

当输入的数据[`Entry`](xref:Xamarin.Forms.Entry)无效时，控件中的行会保持为红色; 否则，它将更改为黑色以指示输入的数据有效。

有关触发器的详细信息，请参阅[触发器](~/xamarin-forms/app-fundamentals/triggers.md)。

### <a name="displaying-error-messages"></a>显示错误消息

UI 在其数据验证失败的每个控件下的标签控件中显示验证错误消息。 下面的代码示例演示了[`Label`](xref:Xamarin.Forms.Label)在用户未输入有效用户名的情况下显示验证错误消息的：

```xaml
<Label Text="{Binding UserName.Errors, Converter={StaticResource FirstValidationErrorConverter}}"  
       Style="{StaticResource ValidationErrorLabelStyle}" />
```

每[`Label`](xref:Xamarin.Forms.Label)个绑定到`Errors`要验证的视图模型对象的属性。 属性由类提供，且的类型`List<string>`为。 `ValidatableObject<T>` `Errors` 由于属性可能包含多个验证错误`FirstValidationErrorConverter` ，因此将使用实例来检索要显示的集合中的第一个错误。 `Errors`

## <a name="summary"></a>总结

EShopOnContainers 移动应用执行视图模型属性的同步客户端验证，通过突出显示包含无效数据的控件并显示通知用户的错误消息，通知用户任何验证错误。数据无效的原因。

需要验证的视图模型属性的类型`ValidatableObject<T>`为，并且每个`ValidatableObject<T>`实例都已将验证规则`Validations`添加到其属性中。 `Validate`验证是通过调用`ValidatableObject<T>`实例的方法从视图模型调用的，该方法检索验证规则`ValidatableObject<T>` `Value`并对属性执行这些验证规则。 任何验证错误都将放入`Errors` `ValidatableObject<T>`实例的属性中， `ValidatableObject<T>`并更新`IsValid`实例的属性以指示验证是成功还是失败。

## <a name="related-links"></a>相关链接

- [下载电子书（2Mb）](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers （GitHub）（示例）](https://github.com/dotnet-architecture/eShopOnContainers)

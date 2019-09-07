---
title: 第 23 章的摘要。 触发器和行为
description: 用 Xamarin 创建移动应用程序：第 23 章的摘要。 触发器和行为
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760594"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>第 23 章的摘要。 触发器和行为

[![下载示例](~/media/shared/download.png)下载示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

触发器和行为的类似，在于它们都旨在简化元素交互，而不仅仅使用的数据绑定并扩展功能的 XAML 元素在 XAML 文件中使用。 使用可视用户界面对象几乎始终使用触发器和行为。

若要支持的触发器和行为，同时`VisualElement`和`Style`支持两个集合属性：

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) 并[ `Style.Triggers` ](xref:Xamarin.Forms.Style.Triggers)的类型 `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 并[ `Style.Behaviors` ](xref:Xamarin.Forms.Style.Behaviors)的类型 `IList<Behavior>`

## <a name="triggers"></a>Triggers

触发器是导致出现响应 （其他属性更改或运行一些代码） 的条件 （更改属性或事件触发）。 `Triggers`的属性`VisualElement`并`Style`的类型`IList<TriggersBase>`。 [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) 是以下四个密封的类派生一个抽象类：

- [`Trigger`](xref:Xamarin.Forms.Trigger) 为响应基于属性的更改
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) 基于事件触发的响应
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) 为响应基于数据绑定
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) 为响应基于多个触发器

触发器始终设置其属性正在更改的触发器在元素上。

### <a name="the-simplest-trigger"></a>最简单的触发器

[ `Trigger` ](xref:Xamarin.Forms.Trigger)类检查属性值的更改，并响应通过同一元素的另一个属性设置。

`Trigger` 定义三个属性：

- [`Property`](xref:Xamarin.Forms.Trigger.Property) 类型 `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) 类型 `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) 类型的`IList<SetterBase>`的 content 属性 `Trigger`

此外，`Trigger`需要以下属性继承自`TriggerBase`设置：

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) 若要指示在其上元素的类型`Trigger`附加

`Property`并`Value`构成条件和`Setters`集合是响应。 当所指示`Property`具有所指示的值`Value`，然后`Setter`中的对象`Setters`应用集合。 当`Property`具有不同的值，删除资源库。 `Setter` 定义的前两个属性相同的两个属性`Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) 类型 `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) 类型 `Object`

[ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)示例演示如何`Trigger`应用于`Entry`可以增加的大小`Entry`通过`Scale`属性时`IsFocused`的属性`Entry`是`true`。

虽然不常见，但`Trigger`可以在代码中，设置为[ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)示例演示。

[ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)示例演示如何`Trigger`可以中设置`Style`要应用到多个`Entry`元素。

### <a name="trigger-actions-and-animations"></a>触发器操作和动画

还有可能要运行基于触发器的一小段代码。 此代码可以是以属性为目标的动画。 一种常用方式是使用[ `EventTrigger` ](xref:Xamarin.Forms.EventTrigger)，用于定义两个属性：

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) 类型的`string`，事件的名称
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) 类型的`IList<TriggerAction>`，要在响应中运行的操作列表。

若要使用此开关，需要编写的类，派生自[ `TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1)中，通常`TriggerAction<VisualElement>`。 此类中，可以定义属性。 这些是普通 CLR 属性，而不是可绑定属性因为`TriggerAction`也不是派生`BindableObject`。 必须重写[ `Invoke` ](xref:Xamarin.Forms.TriggerAction`1.Invoke*)当调用操作时调用的方法。 该参数是目标元素。

[ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库是一个示例。 它将调用`ScaleTo`属性进行动画处理`Scale`元素的属性。 因为其属性之一的类型`Easing`，则[ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs)类使您可以使用标准`Easing`XAML 中的静态字段。

[ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)示例演示如何调用`ScaleAction`从`EventTrigger`对象的监视器`Focused`和`Unfocused`事件。

[ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)示例演示如何定义自定义缓动函数`ScaleAction`代码隐藏文件中。

此外可以调用使用的操作`Trigger`(distinguished 从`EventTrigger`)。 这需要了解的`TriggerBase`定义两个集合：

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) 类型 `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) 类型 `IList<TriggerAction>`

[ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)示例演示如何使用这些集合。

### <a name="more-event-triggers"></a>更多的事件触发器

[ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库调用`ScaleTo`两次以扩展和缩减。 [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)中某一样式的示例使用此`EventTrigger`来提供视觉反馈时`Button`按下。 此双重动画，还可以使用两个操作类型的集合中 [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs)类**Xamarin.FormsBook.Toolkit**库定义的可自定义不操作。 [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)示例说明了这一点。

[ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs)类**Xamarin.FormsBook.Toolkit**库仅限于`Entry`元素和集`TextColor`属性设置为红色如果`Text`属性不是`double`。 [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)示例说明了这一点。

### <a name="data-triggers"></a>数据触发器

[ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger)类似于`Trigger`除外，而不是监视值发生更改的属性，它将监视数据绑定。 这样会影响另一个元素中的属性的一个元素中的一个属性。

`DataTrigger` 定义三个属性：

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) 类型 `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) 类型 `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) 类型 `IList<SetterBase>`

[ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)示例要求[ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)库和集的名称为蓝色的学生的颜色或粉红色基于`Sex`属性：

[![性别颜色的三个屏幕截图](images/ch23fg04-small.png "性别颜色")](images/ch23fg04-large.png#lightbox "性别颜色")

[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)示例集`IsEnabled`属性的`Entry`到`False`如果`Length`属性`Text`属性`Entry`等于 0。 请注意，`Text`属性初始化为空字符串; 默认情况下它是`null`，和`DataTrigger`无法正常工作。

### <a name="combining-conditions-in-the-multitrigger"></a>在 MultiTrigger 组合条件

[ `MultiTrigger` ](xref:Xamarin.Forms.MultiTrigger)是条件的集合。 如果它们没有所有`true`，然后应用资源库。 该类定义了两个属性：

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) 类型 `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) 类型 `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) 是一个抽象类，具有两个子代类：

- [`PropertyCondition`](xref:Xamarin.Forms.Condition)其中包含[ `Property` ](xref:Xamarin.Forms.PropertyCondition.Property)并[ `Value` ](xref:Xamarin.Forms.PropertyCondition.Value)之类的属性 `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition)其中包含[ `Binding` ](xref:Xamarin.Forms.BindingCondition.Binding)并[ `Value` ](xref:Xamarin.Forms.BindingCondition.Value)之类的属性 `DataTrigger`

在中[ **AndConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions)示例中，`BoxView`仅当四个着色`Switch`元素所有打开的。

[ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)示例演示如何使`BoxView`一种颜色时*任何*四个`Switch`元素处于打开状态。 这要求 De Morgan 法律和反转的所有逻辑的应用程序。

组合 AND 和或逻辑并不简单，而且通常不需要不可见`Switch`中间结果的元素。 [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)示例演示如何`Button`如果可以启用两个`Entry`元素具有在中，键入一些文本，但如果二者都提供中键入一些文本。

## <a name="behaviors"></a>Behaviors

可以使用触发器执行的任何内容，还可以执行行为，但行为始终需要派生的类[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)并重写以下两个方法：

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

该参数是行为附加到的元素。 通常情况下，`OnAttachedTo`方法将附加一些事件处理程序和`OnDetachingFrom`分离它们。 因为此类通常会保存一些状态，它通常不能共享中`Style`。

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)示例是类似于**TriggerEntryValidation**只不过它使用一种行为&mdash; [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

### <a name="behaviors-with-properties"></a>属性行为

`Behavior<T>` 派生自`Behavior`，它派生`BindableObject`，因此可以对行为定义可绑定属性。 这些属性可以是数据绑定中处于活动状态。

了这一点[ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程序，用于使利用[ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs)类[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。 `ValidEmailBehavior` 具有只读的可绑定属性，并且用作数据绑定中的源。

[ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)示例使用此相同的行为来显示另一种类型的标记，以通知的电子邮件地址是否有效。

[ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)示例是上一示例的变体。 ButtonGlide 使用`DataTrigger`结合该行为。

### <a name="toggles-and-check-boxes"></a>切换和复选框

可以封装在类中的切换按钮的行为，例如[ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库，然后定义了所有完全在 XAML 中切换视觉对象。

[ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)的示例使用`ToggleBehavior`与`DataTrigger`使用`Label`使用切换的两个文本字符串。

[ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)示例通过两个之间切换来扩展此概念`FormattedString`对象。

[ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs)类**Xamarin.FormsBook.Toolkit**库派生`ContentView`，定义`IsToggled`属性，并结合`ToggleBehavior`切换逻辑。 这使得它更轻松地切换按钮定义中 XAML，如所示[ **TraditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)示例。

[ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包括[ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs)派生的类`ToggleBase`，并使用[ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)类来构造一个切换按钮，类似于 Xamarin.Forms `Switch`。

一个[ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs)中**Xamarin.FormsBook.Toolkit**提供了用于使动画的级别中的动画[ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)示例。

### <a name="responding-to-taps"></a>响应点击

一个缺点`EventTrigger`是，您不能将其附加到`TapGestureRecognizer`响应分流点。 获取绕开此问题的用途是[ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)的示例使用`TapBehavior`若要使用早期版本`ShiverAction`为点击`BoxView`元素。

[ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)示例演示如何通过封装标记减少`ShiverView`类。

### <a name="radio-buttons"></a>单选按钮

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库还具有[ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs)生成单选按钮，按分组类`string`组名称。

[ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程序将使用其单选按钮的文本字符串。 [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)的示例使用`Style`外观 checked 和 unchecked 按钮之间的差异。 [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)示例使用装箱的映像对于其单选按钮：

[![单选映像的三个屏幕截图](images/ch23fg17-small.png "单选按钮图像")](images/ch23fg17-large.png#lightbox "单选按钮图像")

[ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)示例在一个圆圈内绘制带有圆点的传统显示单选按钮。

### <a name="fades-and-orientation"></a>淡出和方向

最后一个示例中， [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders)允许您使用单选按钮的三个不同的颜色选择视图之间切换。 三个视图淡入淡出使用[ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)库。

该程序也响应方向纵向和横向使用之间变化[ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs)中**Xamarin.FormsBook.Toolkit**库。

## <a name="related-links"></a>相关链接

- [第 23 章全文 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [第 23 章示例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用触发器](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 行为](~/xamarin-forms/app-fundamentals/behaviors/creating.md)

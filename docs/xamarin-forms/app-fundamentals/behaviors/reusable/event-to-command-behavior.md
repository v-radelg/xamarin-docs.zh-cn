---
title: 可重用 EventToCommandBehavior
description: 行为可用于将命令与非旨在与命令交互的控件相关联。 本文演示了如何创建 Xamarin.Forms 行为并在事件触发后使用它来调用命令。
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 292a6aaaea4fb0f84138e04c88f001c72ddd096d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68650903"
---
# <a name="reusable-eventtocommandbehavior"></a>可重用 EventToCommandBehavior

[![下载示例](~/media/shared/download.png) 下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

行为可用于将命令与非旨在与命令交互的控件相关联。本文演示了如何创建 Xamarin.Forms 行为并在事件触发后使用它来调用命令。_

## <a name="overview"></a>概述

`EventToCommandBehavior` 类是可重用的 Xamarin.Forms 自定义行为，它执行命令以响应任何  事件触发。 默认情况下，事件的事件参数被传递给命令，并且可以通过 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 实现进行选择性转换。

必须设置以下行为属性才能使用该行为：

- **EventName** - 行为侦听的事件的名称。
- **Command** - 要执行的 `ICommand`。 该行为期望在附加控件的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 上找到 `ICommand` 实例，该实例可以从父元素继承。

还可以设置以下可选行为属性：

- **CommandParameter** - 要传递给命令的 `object`。
- **Converter** - 一种 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 实现，当绑定引擎在源和目标之间传递事件参数数据时，该实现将改变该数据的格式   。

> [!NOTE]
> `EventToCommandBehavior` 是一个自定义类，它可以位于 [EventToCommand Behavior 示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)中，但并不属于 Xamarin.Forms。

## <a name="creating-the-behavior"></a>创建行为

`EventToCommandBehavior` 类从 `BehaviorBase<T>` 派生类，而后者又从 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 类派生。 `BehaviorBase<T>` 类的目的是为任何需要将行为的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 设置为附加控件的 Xamarin.Forms 行为提供基类。 这可确保在使用行为时，行为可以绑定到并执行 `Command` 属性指定的 `ICommand`。

`BehaviorBase<T>` 类提供一个可替代的 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法（用于设置行为的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)）以及一个可替代的 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法（用于清理 `BindingContext`）。 此外，该类还在 `AssociatedObject` 属性中存储对附加控件的引用。

### <a name="implementing-bindable-properties"></a>实现可绑定属性

`EventToCommandBehavior` 类定义四个 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 实例，它们在事件触发时执行用户定义的命令。 以下代码示例中显示了这些属性：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

使用 `EventToCommandBehavior` 类时，`Command` 属性应该是绑定到 `ICommand` 的数据，以响应 `EventName` 属性中定义的事件触发。 该行为期望在附加控件的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 上找到 `ICommand`。

默认情况下，事件的事件参数被传递给命令。 当绑定引擎在源和目标之间传递该数据时，可以通过指定 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 实现作为 `Converter` 属性值来选择性地转换该数据   。 或者，可以通过指定 `CommandParameter` 属性值将参数传递给命令。

### <a name="implementing-the-overrides"></a>实现替代

`EventToCommandBehavior` 类替代 `BehaviorBase<T>` 类的 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 和 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法，如以下代码示例所示：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法通过调用 `RegisterEvent` 方法执行设置，将 `EventName` 属性的值作为参数传递。 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法通过调用 `DeregisterEvent` 方法执行清理，将 `EventName` 属性的值作为参数传递。

### <a name="implementing-the-behavior-functionality"></a>实现行为功能

该行为的目的是执行由 `Command` 属性定义的命令，以响应由 `EventName` 属性定义的事件触发。 核心行为功能如以下代码示例所示：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

执行 `RegisterEvent` 方法以响应要附加到控件的 `EventToCommandBehavior`，并且该方法接收 `EventName` 属性的值作为参数。 然后，该方法尝试在附加控件上查找 `EventName` 属性中定义的事件。 如果可以找到该事件，则会将 `OnEvent` 方法注册为事件的处理程序方法。

执行 `OnEvent` 方法以响应在 `EventName` 属性中定义的事件触发。 如果 `Command` 属性引用有效的 `ICommand`，则该方法尝试检索要传递给 `ICommand` 的参数，如下所示：

- 如果 `CommandParameter` 属性定义参数，则检索它。
- 除此以外，如果 `Converter` 属性定义 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 实现，则会执行转换器，并且当绑定引擎在源和目标之间传递事件参数数据时，转换器会转换该数据   。
- 否则，事件实参被认定为形参。

然后，执行绑定 `ICommand` 的数据，将参数传递给命令，前提是 [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) 方法返回 `true`。

虽然此处未显示，但 `EventToCommandBehavior` 还包含由 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法执行的 `DeregisterEvent` 方法。 `DeregisterEvent` 方法用于查找和注销 `EventName` 属性中定义的事件，以清除任何潜在的内存泄漏。

## <a name="consuming-the-behavior"></a>使用行为

`EventToCommandBehavior` 类可以附加到控件的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，如以下 XAML 代码示例所示：

```xaml
<ListView ItemsSource="{Binding People}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Name}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.Behaviors>
        <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}" Converter="{StaticResource SelectedItemConverter}" />
    </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

以下代码示例显示相应的 C# 代码：

```csharp
var listView = new ListView();
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "People");
listView.ItemTemplate = new DataTemplate(() =>
{
    var textCell = new TextCell();
    textCell.SetBinding(TextCell.TextProperty, "Name");
    return textCell;
});
listView.Behaviors.Add(new EventToCommandBehavior
{
    EventName = "ItemSelected",
    Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
    Converter = new SelectedItemEventArgsToSelectedItemConverter()
});

var selectedItemLabel = new Label();
selectedItemLabel.SetBinding(Label.TextProperty, "SelectedItemText");
```

该行为的 `Command` 属性是绑定到关联 ViewModel 的 `OutputAgeCommand` 属性的数据，而 `Converter` 属性被设置为 `SelectedItemConverter` 实例，该实例从 [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 返回 [`ListView`](xref:Xamarin.Forms.ListView) 的 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)。

在运行时，该行为将响应与控件的交互。 在 [`ListView`](xref:Xamarin.Forms.ListView) 中选定一个项目时，将触发 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件，该事件将在 ViewModel 中执行 `OutputAgeCommand`。 反过来，这又会更新 [`Label`](xref:Xamarin.Forms.Label) 绑定到的 ViewModel `SelectedItemText` 属性，如以下屏幕截图所示：

[![](event-to-command-behavior-images/screenshots-sml.png "使用 EventToCommandBehavior 的示例应用程序")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

在事件触发时使用此行为执行命令的优点是，命令可与非旨在与命令交互的控件相关联。 此外，还可从代码隐藏文件中删除冗余重复事件处理代码。

## <a name="summary"></a>总结

本文演示了如何使用 Xamarin.Forms 行为在事件触发后调用命令。 行为可用于将命令与非旨在与命令交互的控件相关联。

## <a name="related-links"></a>相关链接

- [EventToCommand 行为（示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)
- [行为](xref:Xamarin.Forms.Behavior)
- [行为&lt;&gt;](xref:Xamarin.Forms.Behavior`1)

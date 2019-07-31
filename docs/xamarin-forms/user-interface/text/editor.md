---
title: Xamarin.Forms 编辑器
description: 本文介绍如何使用 Xamarin.Forms 编辑器控件接受的应用程序中的多行文本输入。
ms.prod: xamarin
ms.assetid: 7074DB3A-30D2-4A6B-9A89-B029EEF20B07
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: 1f9cd0acd201f124f4a4577fec23dce52aee627d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657510"
---
# <a name="xamarinforms-editor"></a>Xamarin.Forms 编辑器

[![下载示例](~/media/shared/download.png)下载示例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)

_多行文本输入_

[ `Editor` ](xref:Xamarin.Forms.Editor)控件用来接受多行输入。 本文包含以下内容：

- **[自定义](#customization)** &ndash;键盘和颜色选项。
- **[交互性](#interactivity)** &ndash;可以将侦听的事件，以提供交互性。

## <a name="customization"></a>自定义

### <a name="setting-and-reading-text"></a>设置和读取文本

[ `Editor` ](xref:Xamarin.Forms.Editor)，如其他文本呈现的视图，公开`Text`属性。 此属性可以用于设置和读取通过显示的文本`Editor`。 下面的示例演示了如何设置`Text`在 XAML 中的属性：

```xaml
<Editor Text="I am an Editor" />
```

在 C# 中：

```csharp
var MyEditor = new Editor { Text = "I am an Editor" };
```

若要读取的文本，访问`Text`C# 中的属性：

```csharp
var text = MyEditor.Text;
```

### <a name="setting-placeholder-text"></a>设置占位符文本

[ `Editor` ](xref:Xamarin.Forms.Editor)可以设置为显示占位符文本，它不存储用户输入时。 这可以通过设置[ `Placeholder` ](xref:Xamarin.Forms.Editor.Placeholder)属性设置为`string`，并通常用于指示的是适用于的内容类型`Editor`。 此外，通过设置控制的占位符文本颜色[ `PlaceholderColor` ](xref:Xamarin.Forms.Editor.PlaceholderColor)属性设置为[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Editor Placeholder="Enter text here" PlaceholderColor="Olive" />
```

```csharp
var editor = new Editor { Placeholder = "Enter text here", PlaceholderColor = Color.Olive };
```

### <a name="preventing-text-entry"></a>防止文本输入

[`Editor`](xref:Xamarin.Forms.Editor)通过`false` `true`将属性 (其默认值) 设置为, 可以防止用户修改中的文本: `IsReadOnly`

```xaml
<Editor Text="This is a read-only Editor"
        IsReadOnly="true" />
```

```csharp
var editor = new Editor { Text = "This is a read-only Editor", IsReadOnly = true });
```

> [!NOTE]
> 属性不会改变的视觉外观[`Editor`](xref:Xamarin.Forms.Editor), 这与还会将`IsEnabled`的`Editor`视觉外观改为灰色的属性不同。 `IsReadonly`

### <a name="limiting-input-length"></a>输入的长度限制

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制所允许的范围的输入的长度[ `Editor` ](xref:Xamarin.Forms.Editor)。 此属性应设置为一个正整数：

```xaml
<Editor ... MaxLength="10" />
```

```csharp
var editor = new Editor { ... MaxLength = 10 };
```

一个[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性值为 0 指示将允许任何输入，并将值`int.MaxValue`，它是默认值， [ `Editor` ](xref:Xamarin.Forms.Editor)，指示是否有任何可能输入的字符数的有效限制。

### <a name="auto-sizing-an-editor"></a>自动调整大小编辑器

[ `Editor` ](xref:Xamarin.Forms.Editor)可以对通过设置其内容自动调整大小[ `Editor.AutoSize` ](xref:Xamarin.Forms.Editor.AutoSize)属性设置为[ `TextChanges` ](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges)，它是一个值[ `EditoAutoSizeOption` ](xref:Xamarin.Forms.EditorAutoSizeOption)枚举。 此枚举有两个值：

- [`Disabled`](xref:Xamarin.Forms.EditorAutoSizeOption.Disabled) 指示自动调整大小处于禁用状态，并且是默认值。
- [`TextChanges`](xref:Xamarin.Forms.EditorAutoSizeOption.TextChanges) 指示启用自动调整大小。

这可以在代码中完成，如下所示：

```xaml
<Editor Text="Enter text here" AutoSize="TextChanges" />
```

```csharp
var editor = new Editor { Text = "Enter text here", AutoSize = EditorAutoSizeOption.TextChanges };
```

当启用自动调整大小、 的高度[ `Editor` ](xref:Xamarin.Forms.Editor)用户将其填充文本，并且高度会降低用户删除文本时，将提高。

> [!NOTE]
> [ `Editor` ](xref:Xamarin.Forms.Editor)将不自动调整大小 if [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest)设置属性。

### <a name="customizing-the-keyboard"></a>自定义键盘

当与用户交互时显示键盘[ `Editor` ](xref:Xamarin.Forms.Editor)可以通过编程方式设置[ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard)属性，因为的以下属性之一[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)类：

- [`Chat`](xref:Xamarin.Forms.Keyboard.Chat) – 用于短和表情符号是有用的地方。
- [`Default`](xref:Xamarin.Forms.Keyboard.Default) – 默认键盘。
- [`Email`](xref:Xamarin.Forms.Keyboard.Email) – 输入电子邮件地址时使用。
- [`Numeric`](xref:Xamarin.Forms.Keyboard.Numeric) – 输入数字时使用。
- [`Plain`](xref:Xamarin.Forms.Keyboard.Plain) – 输入文本时使用，而无需指定任何 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags)。
- [`Telephone`](xref:Xamarin.Forms.Keyboard.Telephone) – 输入电话号码时使用。
- [`Text`](xref:Xamarin.Forms.Keyboard.Text) – 输入文本时使用。
- [`Url`](xref:Xamarin.Forms.Keyboard.Url) – 用于输入文件路径和 Web 地址。

这可以实现在 XAML 中，如下所示：

```xaml
<Editor Keyboard="Chat" />
```

等效的 C# 代码是：

```csharp
var editor = new Editor { Keyboard = Keyboard.Chat };
```

可以在中找到的每个键盘示例我们[配方](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/Controls/choose-keyboard-for-entry)存储库。

[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)类还具有[ `Create` ](xref:Xamarin.Forms.Keyboard.Create*)工厂方法，可以用来通过指定的大小写、 拼写检查，并建议行为自定义键盘。 [`KeyboardFlags`](xref:Xamarin.Forms.KeyboardFlags) 枚举值指定为方法的参数，并返回自定义的 `Keyboard`。 `KeyboardFlags` 枚举包含以下值：

- [`None`](xref:Xamarin.Forms.KeyboardFlags.None) – 没有功能添加到键盘。
- [`CapitalizeSentence`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeSentence) – 指示输入的每句话的第一个词的首字母将自动大写。
- [`Spellcheck`](xref:Xamarin.Forms.KeyboardFlags.Spellcheck) – 指示将对输入的文本执行拼写检查。
- [`Suggestions`](xref:Xamarin.Forms.KeyboardFlags.Suggestions) – 指示将对输入的文本执行单词自动完成。
- [`CapitalizeWord`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeWord) – 指示每个词的首字母将自动大写。
- [`CapitalizeCharacter`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeCharacter) – 指示每个字符将自动大写。
- [`CapitalizeNone`](xref:Xamarin.Forms.KeyboardFlags.CapitalizeNone) – 指示不会发生自动大写。
- [`All`](xref:Xamarin.Forms.KeyboardFlags.All) – 指示将对输入的文本执行拼写检查、单词自动完成和句子首字母大写。

以下 XAML 代码示例演示如何自定义默认 [`Keyboard`](xref:Xamarin.Forms.Keyboard) 来执行单词自动完成并将输入的每个字符的首字母大写：

```xaml
<Editor>
    <Editor.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Editor.Keyboard>
</Editor>
```

等效的 C# 代码是：

```csharp
var editor = new Editor();
editor.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

### <a name="enabling-and-disabling-spell-checking"></a>启用和禁用拼写检查

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否拼写检查已启用。 默认情况下，该属性设置为`true`。 当用户输入文本，指示拼写错误。

但是，对于某些文本项方案中，输入用户名，如拼写检查功能提供了负的体验，因此应禁用通过设置[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`:

```xaml
<Editor ... IsSpellCheckEnabled="false" />
```

```csharp
var editor = new Editor { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 当[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`，并自定义键盘未被使用，将禁用本机拼写检查器。 但是，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已设置，以禁用拼写检查，如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，则`IsSpellCheckEnabled`属性将被忽略。 因此，该属性不能用于启用拼写检查`Keyboard`的显式禁用它。

### <a name="enabling-and-disabling-text-prediction"></a>启用和禁用文本预测

`IsTextPredictionEnabled`属性控制是否启用文本预测和自动文本更正。 默认情况下，该属性设置为`true`。 当用户输入文本时，会显示 word 预测。

但是, 对于某些文本输入方案, 例如输入用户名, 文本预测和自动文本更正将提供消极的体验, 并应通过将`IsTextPredictionEnabled`属性设置为来`false`禁用它:

```xaml
<Editor ... IsTextPredictionEnabled="false" />
```

```csharp
var editor = new Editor { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> `false`如果将`IsTextPredictionEnabled`属性设置为, 并且未使用自定义键盘, 则会禁用文本预测和自动文本更正。 但是，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)已设置该禁用文本预测`IsTextPredictionEnabled`属性将被忽略。 因此，该属性不能用于启用文本预测`Keyboard`的显式禁用它。

### <a name="colors"></a>颜色

`Editor` 可以设置为使用自定义背景色通过`BackgroundColor`属性。 特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的文本颜色的默认值，可能需要设置每个平台的自定义背景颜色。 请参阅[使用平台调整](~/xamarin-forms/platform/device.md)有关优化每个平台的 UI 的详细信息。

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        //dark blue on UWP & Android, light blue on iOS
        var editor = new Editor { BackgroundColor = Device.RuntimePlatform == Device.iOS ? Color.FromHex("#A4EAFF") : Color.FromHex("#2c3e50") };
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="TextSample.EditorPage"
    Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor>
                <Editor.BackgroundColor>
                    <OnPlatform x:TypeArguments="x:Color">
                        <On Platform="iOS" Value="#a4eaff" />
                        <On Platform="Android, UWP" Value="#2c3e50" />
                    </OnPlatform>
                </Editor.BackgroundColor>
            </Editor>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

![](editor-images/textbackgroundcolor.png "使用 BackgroundColor 示例编辑器")

请确保你选择的背景和文本颜色为每个平台上可用，且变得模糊的任何占位符文本。

## <a name="interactivity"></a>交互性

`Editor` 公开两个事件：

- [TextChanged](xref:Xamarin.Forms.Editor.TextChanged) &ndash;文本在编辑器中更改时引发。 更改之前和之后提供的文本。
- [已完成](xref:Xamarin.Forms.Editor.Completed)&ndash;用户已结束输入通过键盘上按 return 键时引发。

> [!NOTE]
> 从其[`Focused`](xref:Xamarin.Forms.VisualElement.Focused) [`Unfocused`](xref:Xamarin.Forms.VisualElement.Unfocused)继承的[`VisualElement`类](xref:Xamarin.Forms.VisualElement) [`Entry`](xref:Xamarin.Forms.Entry)也具有和事件。

### <a name="completed"></a>已完成

`Completed`事件用来与的交互完成做出反应`Editor`。 `Completed`如果用户通过输入键盘上的 return 键 (或按 UWP 上的 Tab 键) 来结束输入, 则会引发。 该事件的处理程序是一个泛型事件处理程序，使发件人和`EventArgs`:

```csharp
void EditorCompleted (object sender, EventArgs e)
{
    var text = ((Editor)sender).Text; // sender is cast to an Editor to enable reading the `Text` property of the view.
}
```

可以在代码和 XAML 中订阅的已完成的事件：

在 C# 中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.Completed += EditorCompleted;
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor Completed="EditorCompleted" />
        </StackLayout>
    </ContentPage.Content>
</Contentpage>
```

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用于对字段的内容中更改做出反应。

`TextChanged` 时引发`Text`的`Editor`更改。 为事件处理程序将执行的实例`TextChangedEventArgs`。 `TextChangedEventArgs` 提供对旧的和新值的访问`Editor``Text`通过`OldTextValue`和`NewTextValue`属性：

```csharp
void EditorTextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

可以在代码和 XAML 中订阅的已完成的事件：

在代码中：

```csharp
public partial class EditorPage : ContentPage
{
    public EditorPage ()
    {
        InitializeComponent ();
        var layout = new StackLayout { Padding = new Thickness(5,10) };
        this.Content = layout;
        var editor = new Editor ();
        editor.TextChanged += EditorTextChanged;
        layout.Children.Add(editor);
    }
}
```

在 XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TextSample.EditorPage"
Title="Editor Demo">
    <ContentPage.Content>
        <StackLayout Padding="5,10">
            <Editor TextChanged="EditorTextChanged" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```


## <a name="related-links"></a>相关链接

- [文本 （示例）](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-text)
- [编辑器 API](xref:Xamarin.Forms.Editor)

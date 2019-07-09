---
title: Xamarin.Forms 条目
description: 此文章介绍了如何使用 Xamarin.Forms 入口类接受的单行文本或应用程序中的密码输入。
ms.prod: xamarin
ms.assetid: 9923C541-3C10-4D14-BAB5-C4D6C514FB1E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2018
ms.openlocfilehash: 238ba1853df523ee275de628ebdbd138a6d1d1bf
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649603"
---
# <a name="xamarinforms-entry"></a>Xamarin.Forms 条目

[![下载示例](~/media/shared/download.png)下载示例](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)

_单行文本或输入的密码_

Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry)用于单行文本输入。 `Entry`，例如[ `Editor` ](xref:Xamarin.Forms.Editor)视图中，支持多个键盘类型。 此外，`Entry`可以用作密码字段。

## <a name="display-customization"></a>显示自定义

### <a name="setting-and-reading-text"></a>设置和读取文本

`Entry`，如其他文本呈现的视图，公开[ `Text` ](xref:Xamarin.Forms.Entry.Text)属性。 此属性可以用于设置和读取通过显示的文本`Entry`。 下面的示例演示了如何设置`Text`在 XAML 中的属性：

```xaml
<Entry Text="I am an Entry" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { Text = "I am an Entry" };
```

若要读取的文本，访问`Text`C# 中的属性：

```csharp
var text = MyEntry.Text;
```

### <a name="setting-placeholder-text"></a>设置占位符文本

[ `Entry` ](xref:Xamarin.Forms.Entry)可以设置为显示占位符文本，它不存储用户输入时。 这可以通过设置[ `Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder)属性设置为`string`，并通常用于指示的是适用于的内容类型`Entry`。 此外，通过设置控制的占位符文本颜色[ `PlaceholderColor` ](xref:Xamarin.Forms.Entry.PlaceholderColor)属性设置为[ `Color` ](xref:Xamarin.Forms.Color):

```xaml
<Entry Placeholder="Username" PlaceholderColor="Olive" />
```

```csharp
var entry = new Entry { Placeholder = "Username", PlaceholderColor = Color.Olive };
```

> [!NOTE]
> 宽度`Entry`可以通过设置定义其`WidthRequest`属性。 不依赖于的宽度`Entry`所定义的值基于其`Text`属性。

### <a name="preventing-text-entry"></a>阻止文本输入

可以阻止用户修改中的文本[ `Entry` ](xref:Xamarin.Forms.Entry)通过设置`IsReadOnly`属性，它具有默认值的`false`到`true`:

```xaml
<Entry Text="This is a read-only Entry"
       IsReadOnly="true" />
```

```csharp
var entry = new Entry { Text = "This is a read-only Entry", IsReadOnly = true });
```

> [!NOTE]
> `IsReadonly`属性不会更改的可视外观[ `Entry` ](xref:Xamarin.Forms.Entry)，但不同于`IsEnabled`也会更改的可视外观的属性`Entry`为灰色。

### <a name="limiting-input-length"></a>输入的长度限制

[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性可用于限制所允许的范围的输入的长度[ `Entry` ](xref:Xamarin.Forms.Entry)。 此属性应设置为一个正整数：

```xaml
<Entry ... MaxLength="10" />
```

```csharp
var entry = new Entry { ... MaxLength = 10 };
```

一个[ `MaxLength` ](xref:Xamarin.Forms.InputView.MaxLength)属性值为 0 指示将允许任何输入，并将值`int.MaxValue`，它是默认值， [ `Entry` ](xref:Xamarin.Forms.Entry)，指示是否有任何可能输入的字符数的有效限制。

### <a name="password-fields"></a>密码字段

`Entry` 提供了`IsPassword`属性。 当`IsPassword`是`true`，字段的内容将显示为黑色圆圈：

在 XAML:

```xaml
<Entry IsPassword="true" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true };
```

![](entry-images/password.png "条目 IsPassword 示例")

占位符可能使用的实例，并用`Entry`的配置为密码字段：

在 XAML:

```xaml
<Entry IsPassword="true" Placeholder="Password" />
```

在 C# 中：

```csharp
var MyEntry = new Entry { IsPassword = true, Placeholder = "Password" };
```

![](entry-images/passwordplaceholder.png "条目 IsPassword 和占位符的示例")

### <a name="setting-the-cursor-position-and-text-selection-length"></a>设置光标位置和文本所选内容长度

[ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition)属性可以用于返回或设置下一个字符将插入中存储的字符串的位置[ `Text` ](xref:Xamarin.Forms.Entry.Text)属性：

```xaml
<Entry Text="Cursor position set" CursorPosition="5" />
```

```csharp
var entry = new Entry { Text = "Cursor position set", CursorPosition = 5 };
```

默认值[ `CursorPosition` ](xref:Xamarin.Forms.Entry.CursorPosition)属性为 0，表示将在开头插入文本`Entry`。

此外， [ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength)属性可以用于返回或设置选定文本的长度`Entry`:

```xaml
<Entry Text="Cursor position and selection length set" CursorPosition="2" SelectionLength="10" />
```

```csharp
var entry = new Entry { Text = "Cursor position and selection length set", CursorPosition = 2, SelectionLength = 10 };
```

默认值[ `SelectionLength` ](xref:Xamarin.Forms.Entry.SelectionLength)属性为 0，指示未选定任何文本。

### <a name="customizing-the-keyboard"></a>自定义键盘

当与用户交互时显示键盘[ `Entry` ](xref:Xamarin.Forms.Entry)可以通过编程方式设置[ `Keyboard` ](xref:Xamarin.Forms.InputView.Keyboard)属性，因为的以下属性之一[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)类：

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
<Entry Keyboard="Chat" />
```

等效的 C# 代码是：

```csharp
var entry = new Entry { Keyboard = Keyboard.Chat };
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
<Entry Placeholder="Enter text here">
    <Entry.Keyboard>
        <Keyboard x:FactoryMethod="Create">
            <x:Arguments>
                <KeyboardFlags>Suggestions,CapitalizeCharacter</KeyboardFlags>
            </x:Arguments>
        </Keyboard>
    </Entry.Keyboard>
</Entry>
```

等效的 C# 代码是：

```csharp
var entry = new Entry { Placeholder = "Enter text here" };
entry.Keyboard = Keyboard.Create(KeyboardFlags.Suggestions | KeyboardFlags.CapitalizeCharacter);
```

#### <a name="customizing-the-return-key"></a>自定义 Return 键

Return 键是软键盘上的外观显示何时[ `Entry` ](xref:Xamarin.Forms.Entry)具有焦点，可以通过设置自定义[ `ReturnType` ](xref:Xamarin.Forms.Entry.ReturnType)属性的值[ `ReturnType` ](xref:Xamarin.Forms.ReturnType)枚举：

- [`Default`](xref:Xamarin.Forms.ReturnType.Default) – 指示是否需要任何特定的 return 键，并且将使用平台默认值。
- [`Done`](xref:Xamarin.Forms.ReturnType.Done) – 指示"Done"的 return 键。
- [`Go`](xref:Xamarin.Forms.ReturnType.Go) – 表示"Go"return 键。
- [`Next`](xref:Xamarin.Forms.ReturnType.Next) – 表示"下一步"返回密钥。
- [`Search`](xref:Xamarin.Forms.ReturnType.Search) – 表示"搜索"返回密钥。
- [`Send`](xref:Xamarin.Forms.ReturnType.Send) – 指示"发送"return 键。

下面的 XAML 示例演示如何设置返回的项：

```xaml
<Entry ReturnType="Send" />
```

等效的 C# 代码是：

```csharp
var entry = new Entry { ReturnType = ReturnType.Send };
```

> [!NOTE]
> 返回键的确切外观是依赖于平台。 在 iOS 上，返回的密钥是基于文本的按钮。 但是，在 Android 和通用 Windows 平台中，return 键是基于图标的按钮。

按下 return 键时， [ `Completed` ](xref:Xamarin.Forms.Entry.Completed)事件就会激发，并且任何`ICommand`指定的[ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand)执行属性。 此外，任何`object`通过指定[ `ReturnCommandParameter` ](xref:Xamarin.Forms.Entry.ReturnCommandParameter)属性将传递给`ICommand`作为参数。 有关命令的详细信息，请参阅[命令界面](~/xamarin-forms/app-fundamentals/data-binding/commanding.md)。

### <a name="enabling-and-disabling-spell-checking"></a>启用和禁用拼写检查

[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性控制是否拼写检查已启用。 默认情况下，该属性设置为`true`。 当用户输入文本，指示拼写错误。

但是，对于某些文本项方案，例如输入用户名，拼写检查功能提供负体验，并应通过设置禁用[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`:

```xaml
<Entry ... IsSpellCheckEnabled="false" />
```

```csharp
var entry = new Entry { ... IsSpellCheckEnabled = false };
```

> [!NOTE]
> 当[ `IsSpellCheckEnabled` ](xref:Xamarin.Forms.InputView.IsSpellCheckEnabled)属性设置为`false`，并自定义键盘未被使用，将禁用本机拼写检查器。 但是，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)具有已设置，以禁用拼写检查，如[ `Keyboard.Chat` ](xref:Xamarin.Forms.Keyboard.Chat)，则`IsSpellCheckEnabled`属性将被忽略。 因此，该属性不能用于启用拼写检查`Keyboard`的显式禁用它。

### <a name="enabling-and-disabling-text-prediction"></a>启用和禁用文本预测

[ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)属性控制是否文本预测和自动启用文本校正。 默认情况下，该属性设置为`true`。 当用户输入文本时，会显示 word 预测。

但是，对于某些文本项方案，例如输入用户名、 文本预测和文本自动更正提供负体验，并应通过设置禁用[ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)属性设置为`false`:

```xaml
<Entry ... IsTextPredictionEnabled="false" />
```

```csharp
var entry = new Entry { ... IsTextPredictionEnabled = false };
```

> [!NOTE]
> 当[ `IsTextPredictionEnabled` ](xref:Xamarin.Forms.Entry.IsTextPredictionEnabled)属性设置为`false`，和自定义键盘不被使用，文本预测和自动禁用文本更正。 但是，如果[ `Keyboard` ](xref:Xamarin.Forms.Keyboard)已设置该禁用文本预测`IsTextPredictionEnabled`属性将被忽略。 因此，该属性不能用于启用文本预测`Keyboard`的显式禁用它。

### <a name="colors"></a>颜色

若要使用的自定义背景和文本颜色通过以下可绑定属性，可以设置项：

- **TextColor** &ndash;设置文本的颜色。
- **BackgroundColor** &ndash;设置显示文本的背景的颜色。

特别注意有必要确保将每个平台上可用的颜色。 因为每个平台都有不同的默认值的文本和背景颜色，通常需要同时设置，如果你设置一个。

使用以下代码设置的项的文本颜色：

在 XAML:

```xaml
<Entry TextColor="Green" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.TextColor = Color.Green;
```

![](entry-images/textcolor.png "条目 TextColor 示例")

请注意，占位符不是受影响的指定`TextColor`。

若要在 XAML 中设置的背景色：

```xaml
<Entry BackgroundColor="#2c3e50" />
```

在 C# 中：

```csharp
var entry = new Entry();
entry.BackgroundColor = Color.FromHex("#2c3e50");
```

![](entry-images/textbackgroundcolor.png "项的 BackgroundColor 示例")

请务必确保您选择的背景和文本颜色每个平台上可用，并且变得模糊的任何占位符文本。

## <a name="events-and-interactivity"></a>事件和交互性

项公开两个事件：

- [`TextChanged`](xref:Xamarin.Forms.Entry.TextChanged) &ndash; 文本更改的项中时引发。 更改之前和之后提供的文本。
- [`Completed`](xref:Xamarin.Forms.Entry.Completed) &ndash; 当用户已结束输入通过键盘上按 return 键时引发。

> [!NOTE]
> [ `VisualElement` ](xref:Xamarin.Forms.VisualElement)类，从其[ `Entry` ](xref:Xamarin.Forms.Entry)继承，也有[ `Focused` ](xref:Xamarin.Forms.VisualElement.Focused)和[ `Unfocused` ](xref:Xamarin.Forms.VisualElement.Unfocused)事件。

### <a name="completed"></a>已完成

`Completed`使用事件做出反应的条目的交互完成。 `Completed` 当用户通过键盘上按 return 键 （或通过按 Tab 键在 UWP 上） 结束与某个字段的输入时引发。 该事件的处理程序是一个泛型事件处理程序，使发件人和`EventArgs`:

```csharp
void Entry_Completed (object sender, EventArgs e)
{
    var text = ((Entry)sender).Text; //cast sender to access the properties of the Entry
}
```

可以在 XAML 中订阅的已完成的事件：

```xaml
<Entry Completed="Entry_Completed" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.Completed += Entry_Completed;
```

之后[ `Completed` ](xref:Xamarin.Forms.Entry.Completed)事件触发时，任何`ICommand`指定的[ `ReturnCommand` ](xref:Xamarin.Forms.Entry.ReturnCommand)执行属性时，使用`object`指定[ `ReturnCommandParameter`](xref:Xamarin.Forms.Entry.ReturnCommandParameter)属性传递给`ICommand`。

### <a name="textchanged"></a>TextChanged

`TextChanged`事件用于对字段的内容中更改做出反应。

`TextChanged` 时引发`Text`的`Entry`更改。 为事件处理程序将执行的实例`TextChangedEventArgs`。 `TextChangedEventArgs` 提供对旧的和新值的访问`Entry``Text`通过`OldTextValue`和`NewTextValue`属性：

```csharp
void Entry_TextChanged (object sender, TextChangedEventArgs e)
{
    var oldText = e.OldTextValue;
    var newText = e.NewTextValue;
}
```

`TextChanged`事件可以在 XAML 中进行订阅：

```xaml
<Entry TextChanged="Entry_TextChanged" />
```

和 C#:

```csharp
var entry = new Entry ();
entry.TextChanged += Entry_TextChanged;
```


## <a name="related-links"></a>相关链接

- [文本 （示例）](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Text)
- [入口 API](xref:Xamarin.Forms.Entry)

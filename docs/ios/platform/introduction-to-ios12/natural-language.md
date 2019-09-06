---
title: 将自然语言框架与 Xamarin 配合使用
description: 本文档介绍自然语言框架。 自然语言框架是在 iOS 12 中引入的，它是用于语言识别的首选 iOS API，是词性标识和命名实体识别的组成部分。
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/20/2018
ms.openlocfilehash: 7b0d07f2153a0395146506a371631e0bcf75ebef
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292912"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>将自然语言框架与 Xamarin 配合使用

自然语言框架是在 iOS 12 中引入的，用于实现设备自然语言处理。 它支持语言识别、词汇切分和标记。 已标记化将文本拆分为其构成的单词、句子或段落;标记标识语音、人物、地点和组织的各个部分。

自然语言框架还可以使用自定义核心 ML 模型来分类和标记专用上下文中的文本。

[NSLinguisticTagger](xref:Foundation.NSLinguisticTagger)类仍可用。 但是，自然语言框架是用于自然语言处理的首选机制。

## <a name="sample-app-xamarinnl"></a>示例应用：XamarinNL

若要了解如何将自然语言框架与 Xamarin 一起使用，请查看[XamarinNL 示例应用](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)。
此示例应用演示了如何使用自然语言框架执行以下操作：

- [识别语言](#recognizing-languages)。
- 将[文本标记为词和句子](#tokenizing-text-into-words-sentences-and-paragraphs)。
- [标记命名实体和词性](#tagging-named-entities-and-parts-of-speech)。

## <a name="recognizing-languages"></a>识别语言

示例应用程序的 "**识别器**" 选项卡演示了如何使用[`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
确定文本块的语言。

> [!NOTE]
> 语言识别是一种特定类型的文本分类。 自然语言框架还支持通过开发人员提供的核心 ML 模型的自定义文本分类。 有关详细信息，请参阅 WWDC 2018 中的[自然语言框架会话简介](https://developer.apple.com/videos/play/wwdc2018/713/)。

### <a name="dominant-language"></a>主导语言

点击 "**语言**" 按钮，以确定用户输入中的主要语言。

的`HandleDetermineLanguageButtonTap`方法`LanguageRecognizerViewController`使用[`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
`NLLanguageRecognizer`用于提取[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
对于在文本中找到的主要语言：

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>语言概率

点击 "**语言概率**" 按钮，为用户输入提取语言假设的列表。

`LanguageRecognizerViewController`类的`NLLanguageRecognizer`方法实例化，并请求`HandleLanguageProbabilitiesButtonTap`[`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
用户的文本。 然后，它调用语言识别器的[`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
方法，用于获取语言字典和关联的概率。 然后`LanguageRecognizerTableViewController` ，类呈现这些语言和概率。

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

可能`NLLanguage`的值包括：

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

支持的语言的完整列表可作为[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
枚举 API 文档。

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>将文本词汇切分为单词、句子和段落

示例应用程序的 "**标记器**" 选项卡演示了如何使用[`NLTokenizer`](xref:NaturalLanguage.NLTokenizer)将文本块分隔到其分量关键字或句子。

点击 "**字词**" 或 "**句子**" 按钮，提取令牌列表。 每个标记与原始文本中的一个词或句子相关联。

`ShowTokens`通过调用将用户的输入拆分为令牌[`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
的`NLTokenizer`方法。 此方法返回的数组[`NSValue`](xref:Foundation.NSValue)
对象，每个对象`NSRange`都包装了与原始文本中的标记相对应的值。

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController`呈现每个表单元格中的单个标记。 它`NSRange`从标记`NSValue`中提取并在原始文本中查找相应的字符串，并在表视图单元上设置标签：

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>标记命名实体和词性

XamarinNL 示例应用程序的 "标记 **" 选项卡**演示了如何使用[`NLTagger`](xref:NaturalLanguage.NLTagger)
用于将类别与输入字符串的标记关联的类。
自然语言框架包含用于识别人员、地点、组织和词性的内置支持。

> [!NOTE]
> 自然语言框架还支持通过开发人员提供的核心 ML 模型的自定义标记方案。 有关详细信息，请参阅 WWDC 2018 中的[自然语言框架会话简介](https://developer.apple.com/videos/play/wwdc2018/713/)。

点击 "**命名实体**" 或 "**词性**" 按钮以获取：

- 对象的`NSValue`数组，每个对象都`NSRange`包装原始文本中的标记。
- [`NLTag`](xref:NaturalLanguage.NLTag)值的数组–同一数组索引处`NSValue`的标记的类别。

`LanguageTaggerViewController` `ShowTags` [`NLTagScheme`](xref:NaturalLanguage.NLTagScheme) `NLTagScheme.NameType`在和中，`NLTagScheme.LexicalClass`每次调用都传递–（对于词性的部分）或（对于命名实体）。 `HandleNamedEntitiesButtonTap` `HandlePartsOfSpeechButtonTap`

`ShowTags`创建一个`NLTagger`，并将其实例化为`NLTagScheme`要查询的类型的数组（在这种情况下， `NLTagScheme`仅为传入的值）。 然后，它使用[`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
的`NLTagger`方法，以确定与用户输入中的文本相关的标记。

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

标记随后将显示在表`LanguageTaggerTableViewController`中。

可能`NLTag`的值包括：

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

支持标记的完整列表可作为[`NLTag`](xref:NaturalLanguage.NLTag)
枚举 API 文档。

## <a name="related-links"></a>相关链接

- [示例应用– XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)
- [自然语言框架简介](https://developer.apple.com/videos/play/wwdc2018/713/)
- [自然语言（Apple）](https://developer.apple.com/documentation/naturallanguage?language=objc)

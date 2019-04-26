---
title: Xamarin.Android 片段演练-第 1 部分
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/21/2018
ms.openlocfilehash: 984e72f2b3ee11bcc0902663893509e5687fc099
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61026374"
---
# <a name="fragments-walkthrough-ndash-phone"></a>片段演练&ndash;电话

这是演练的将创建面向 Android 设备中纵向方向的 Xamarin.Android 应用的第一个部分。 本演练中将讨论如何在 Xamarin.Android 中创建片段以及如何将它们添加到一个示例。

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

为此应用，将创建以下类：

1. `PlayQuoteFragment` &nbsp; 此代码段将显示通过 William 莎士比亚播放的引文。 它将由托管`PlayQuoteActivity`。
1. `Shakespeare` &nbsp; 此类将作为属性保存两个硬编码的数组。
1. `TitlesFragment` &nbsp; 此代码段将显示的编写的 William 莎士比亚剧本的标题的列表。 它将由托管`MainActivity`。
1. `PlayQuoteActivity` &nbsp; `TitlesFragment` 将启动`PlayQuoteActivity`以响应用户选择在 play `TitlesFragment`。

## <a name="1-create-the-android-project"></a>1.创建 Android 项目

创建一个名为的新的 Xamarin.Android 项目**FragmentSample**。
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![创建新的 Xamarin.Android 项目](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![创建新的 Xamarin.Android 项目](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

建议选择**现代开发**对于本演练。

创建项目后，将文件重命名**layout/Main.axml**到**layout/activity_main.axml**。

-----

## <a name="2-add-the-data"></a>2.添加数据

为此应用程序的数据将存储在两个类名称的属性的硬编码字符串数组`Shakespeare`:

* `Shakespeare.Titles` &nbsp; 此数组将用于保存从 William 莎士比亚剧本的列表。 这是为数据源`TitlesFragment`。
* `Shakespeare.Dialogue` &nbsp; 此数组将包含一组从一个在播放中包含引号`Shakespeare.Titles`。 这是为数据源`PlayQuoteFragment`。

添加一个新C#类来**FragmentSample**项目，并命名**Shakespeare.cs**。 在此文件中，创建一个新C#类调用`Shakespeare`包含以下内容

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3.创建 PlayQuoteFragment

`PlayQuoteFragment`是将显示一个由先前在应用程序，用户选择了莎士比亚 play 的 Android 片段此片段不会使用一个 Android 布局文件; 相反，它将动态创建其用户界面。 添加一个新`Fragment`类名为`PlayQuoteFragment`到项目：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![添加一个新C#类](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![添加一个新C#类](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

然后，将更改为类似于此代码片段的片段的代码：

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

它是在 Android 应用提供将实例化一个片段的工厂方法中的常见模式。 这可确保将具有正常工作所需的参数创建的片段。 在此演练中，应用应使用`PlayQuoteFragment.NewInstance`方法来创建新的碎片引号所选的每个时段。 `NewInstance`方法将采用单个参数&ndash;报价后，若要显示的索引。

`OnCreateView`时就可以呈现在屏幕上的片段时，将由 Android 调用方法。 它将返回 Android`View`是片段的对象。 此代码段不使用的布局文件以创建视图。 相反，它以编程方式将通过实例化来创建视图**TextView**来保存报价，并将显示在该小组件**ScrollView**。

> [!NOTE]
> 片段类必须具有公共默认构造函数没有参数。

## <a name="4-create-the-playquoteactivity"></a>4.创建 PlayQuoteActivity

必须在活动内承载片段，因此此应用所需的活动，将承载`PlayQuoteFragment`。 该活动将动态将片段添加到其布局在运行时。 将新活动添加到应用程序并将其命名`PlayQuoteActivity`:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![将 Android 活动添加到项目](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![将 Android 活动添加到项目](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

编辑中的代码`PlayQuoteActivity`:

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

当`PlayQuoteActivity`是创建的它将实例化一个新`PlayQuoteFragment`并加载在其根视图中的上下文中该片段`FragmentTransaction`。 请注意，此活动不会加载其用户界面的 Android 布局文件。 相反，新`PlayQuoteFragment`添加到应用程序的根视图。 资源标识符`Android.Resource.Id.Content`用于无需知道其特定的标识符引用活动的根视图。

## <a name="5-create-titlesfragment"></a>5.创建 TitlesFragment

`TitlesFragment`将名为专用的片段的子类`ListFragment`封装用于显示逻辑`ListView`片段中。 一个`ListFragment`公开`ListAdapter`属性 (由`ListView`以显示其内容) 和名为一个事件处理程序`OnListItemClick`这样的片段响应的情况下将显示的行上单击`ListView`。

若要开始，将新片段添加到项目并将其命名**TitlesFragment**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![添加 Android 项目的片段](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

[![添加 Android 项目的片段](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

编辑的代码片段：

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Android 创建活动时将调用`OnActivityCreated`方法的片段; 这就是用于列表适配器`ListView`创建。  `ShowQuoteFromPlay`方法将启动的实例`PlayQuoteActivity`以显示所选播放的报价。

## <a name="display-titlesfragment-in-mainactivity"></a>在 MainActivity 中显示 TitlesFragment

最后一步是显示`TitlesFragment`内`MainActivity`。 活动不会动态加载片段。 而是该片段将以静态方式加载的活动使用的布局文件中声明`fragment`元素。 通过设置标识要加载的片段`android:name`片段类 （包括类型的命名空间） 的属性。 例如，若要使用`TitlesFragment`，然后`android:name`应设置为`FragmentSample.TitlesFragment`。

编辑布局文件**activity_mail.axml**，现有的 XML 替换为以下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> `class`属性是一个有效替代`android:name`。 没有正式的指南在哪种形式是首选，有许多将使用的基本代码的示例`class`互换使用`android:name`。

没有所需的 MainActivity 的代码更改。 在该类中的代码应非常类似于此代码片段：

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>运行应用

现在，代码已完成，若要在操作中看到它在设备上运行该应用程序。

[![在手机上运行的应用程序的屏幕截图。](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[本演练的第 2 部分](./walkthrough-landscape.md)将 optimtize 设备在横向模式下运行此应用程序。

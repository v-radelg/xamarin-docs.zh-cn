---
title: 图形和动画
description: Android 提供了非常丰富、 不同的框架支持二维图形和动画。 本主题介绍这些框架，并介绍了如何创建自定义图形和动画使用 Xamarin.Android 应用程序中。
ms.prod: xamarin
ms.assetid: 80086318-6FE4-4711-9A71-5C8F8C28C754
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7f4f7fd3af1e90307a84037f01ddf8e52b1ee030
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020007"
---
# <a name="graphics-and-animation"></a>图形和动画

_Android 提供了非常丰富、 不同的框架支持二维图形和动画。本主题介绍这些框架，并介绍了如何创建自定义图形和动画使用 Xamarin.Android 应用程序中。_


## <a name="overview"></a>概述

尽管是传统意义上的有限能力的设备上运行，最高的评级移动应用程序通常具有复杂用户体验 (UX)，具有高质量的图形和动画提供直观的响应式动态外观。 在移动应用程序获取的详细信息并更复杂，用户已经开始时会出现的情况越来越多的应用程序。

幸运的是对我们而言，新式移动平台都具有用于保留的易用性的同时创建复杂的动画和自定义图形非常功能强大的框架。 这使开发人员能够添加不费吹灰之力丰富的交互功能。

在 Android 中的 UI API 框架大致可以分为两类：图形和动画。

图形进一步分为不同的方法进行二维和三维图形。 3D 图形是可通过许多内置的框架，例如 OpenGL ES （移动特定版本的 OpenGL） 和 MonoGame （一个跨平台工具包与 XNA 工具包兼容） 等第三方框架。 虽然 3D 图形不在本文的讨论范围内，我们将查看内置的 2D 绘制技术。

Android 提供了两个不同的 API 的创建 2D 图形。 一个是高级别的声明性方法和其他编程的低级别 API:

-   **可绘制资源**&ndash;这些用于在 XML 文件中嵌入绘制指令 （更通常） 或以编程方式创建自定义图形。 可绘制资源通常定义为包含说明或适用于 Android 呈现 2D 图形操作的 XML 文件。 

-   **画布**&ndash;这是一个较低级别 API，包括直接在基础位图上绘制。 它提供了非常精细地控制显示的内容。 

除了这些 2D 图形技术，Android 还提供了多种不同的方式来创建动画：

-   **可绘制动画** &ndash; Android 还支持名为的帧的帧动画*可绘制动画*。 这是最简单的动画 API。 Android 按顺序加载，并显示可绘制资源 （类似于卡通） 的序列中。

-   **查看动画** &ndash; *视图动画*是原始动画 API 的 Android 中并且在所有版本的 Android 中可用。 此 API 的局限性在于它只适用于视图对象，并仅可以对这些视图执行简单转换。
    视图动画通常定义在 XML 文件中找到`/Resources/anim`文件夹。

-   **属性动画** &ndash; Android 3.0 引入了一组新的动画 API 的称为*属性动画*。 这些新的 API 引入了可用于对任何对象的属性进行动画处理，而不仅仅是查看对象的可扩展性和灵活性系统。 这种灵活性允许动画封装在不同的类，可让代码共享变得更容易。


视图动画是更适合应用程序必须支持较旧预 Android 3.0 API （API 级别为 11）。 否则应用程序应使用较新属性动画 API 的上面提到的原因。

所有这些框架都是可行选项，但是在可能的情况下，首选项应授予给属性的动画，因为它是一个更灵活的 API，可使用。 属性动画允许动画逻辑封装在不同的类，可以使代码变得更容易共享并简化了代码维护。


## <a name="accessibility"></a>可访问性

图形和动画帮助使 Android 应用程序更具吸引力和使用; 乐趣但是，务必要记住一些交互发生阅读器，备用输入设备通过或协助缩放。
此外，一些交互可能没有音频功能。

如果它们在设计时考虑到中的辅助功能应用是在这些情况下更易于使用： 提供提示和导航用户界面的帮助，并确保没有文本内容或 UI 的图形化元素说明。

请参阅[Google 的可访问性指南](https://developer.android.com/guide/topics/ui/accessibility/)有关如何使用 Android 的辅助功能 Api 的详细信息。



## <a name="2d-graphics"></a>2D 图形

可绘制资源是 Android 应用程序中的一种常用方法。 因为与其他资源，可绘制资源是声明性&ndash;在 XML 文件中定义。 此方法允许从资源的代码完全分离。 这可以简化开发和维护，因为不需要更改代码以更新或更改 Android 应用程序中的图形。 但是，可绘制资源可用于许多简单和常见图形要求，而它们缺乏 power 和控制的画布 API。

另一个方法，使用[画布](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)对象，则非常类似于其他传统的 API 框架，如 System.Drawing 或 iOS 的核心绘图。 使用画布对象提供了创建如何 2D 图形的最大控制。 它是适用于以下情况，可绘制资源不起作用，或将很难使用。 例如，可能需要绘制自定义滑块控件更改其外观将根据滑块的值与相关的计算。

让我们首先检查可绘制资源。 它们是更简单且包括最常见的自定义绘制用例。


### <a name="drawable-resources"></a>可绘制资源

在目录中的 XML 文件中定义可绘制资源`/Resources/drawable`。 与嵌入 PNG 或 JPEG 的不需要提供特定于密度的可绘制资源版本。
在运行时，Android 应用程序将加载这些资源，并使用包含在这些 XML 文件中的说明创建 2D 图形。
Android 定义多种不同类型的可绘制资源：

-   [ShapeDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape) &ndash;这是一个可绘制对象的绘制基本几何形状，并应用一组有限的该形状上的图形效果。 它们是等自定义按钮，或设置背景的 TextViews 非常有用。 我们会举例说明如何使用`ShapeDrawable`这篇文章中更高版本。

-   [StateListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#StateList) &ndash;这是一个可绘制资源，将更改基于状态的小组件/控件的外观。 例如，一个按钮可能会更改其外观，具体取决于是否按下或未。

-   [LayerDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LayerList) &ndash;将层叠在另一个的多个其他绘图的此可绘制资源。 举例*LayerDrawable*以下屏幕截图所示：

    ![LayerDrawable 示例](graphics-and-animation-images/image1.png)

-   [TransitionDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Transition) &ndash;这是*LayerDrawable*但有一处不同。 一个*TransitionDrawable*能够进行动画处理另一个显示基础上的一个层。

-   [LevelListDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#LevelList) &ndash;这是非常类似于*StateListDrawable* ，它将显示基于特定条件的映像。 但是，与不同*StateListDrawable*，则*LevelListDrawable*显示基于整数值的映像。 举例*LevelListDrawable*是显示 WiFi 信号的强度。 作为 WiFi 信号更改的强度，可绘制显示将相应地更改。

-   [ScaleDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Scale)/[ClipDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Clip) &ndash;这些绘图如其名称所示，提供缩放和剪辑的功能。 *ScaleDrawable*将扩展另一个可绘制，while *ClipDrawable*将剪辑另一个 Drawable。

-   [InsetDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Inset) &ndash;此可绘制将应用嵌入另一个可绘制资源两端。 使用视图需要小于该视图的实际边界的背景。

-   XML [BitmapDrawable](https://developer.android.com/guide/topics/resources/drawable-resource.html#Bitmap) &ndash;此文件是一组的说明进行操作，在 XML 中，若要在实际位图上执行。 Android 可以执行某些操作是拼贴、 抖动，和抗锯齿。 这很常见用途之一是在布局的背景磁贴位图。


#### <a name="drawable-example"></a>可绘制示例

让我们看看如何 2D 图形使用以下工具创建一个快速示例`ShapeDrawable`。 一个`ShapeDrawable`可以定义四个基本形状之一： 矩形、 椭圆、 线条和环。 还有可能要应用基本效果，如渐变、 颜色和大小。 以下 XML 是`ShapeDrawable`中可能找到*AnimationsDemo*配套项目 (文件中`Resources/drawable/shape_rounded_blue_rect.xml`)。
它定义一个具有紫色渐变背景的矩形和圆形角：

```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
<!-- Specify a gradient for the background -->
<gradient android:angle="45"
          android:startColor="#55000066"
          android:centerColor="#00000000"
          android:endColor="#00000000"
          android:centerX="0.75" />

<padding android:left="5dp"
          android:right="5dp"
          android:top="5dp"
          android:bottom="5dp" />

<corners android:topLeftRadius="10dp"
          android:topRightRadius="10dp"
          android:bottomLeftRadius="10dp"
          android:bottomRightRadius="10dp" />
</shape>
```

我们可以引用此可绘制资源以声明方式中的布局和其他 Drawable 以下 XML 中所示：

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="#33000000">
    <TextView android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:layout_centerInParent="true"
              android:background="@drawable/shape_rounded_blue_rect"
              android:text="@string/message_shapedrawable" />
</RelativeLayout>
```

此外可以以编程方式应用可绘制资源。 下面的代码段演示如何以编程方式设置的一个 TextView 背景：

```csharp
TextView tv = FindViewById<TextView>(Resource.Id.shapeDrawableTextView);
tv.SetBackgroundResource(Resource.Drawable.shape_rounded_blue_rect);
```

若要查看此将类似的内容，请运行*AnimationsDemo*项目，然后从主菜单中选择形状可绘制的项。 我们应看到类似于以下屏幕截图的内容：

![Textview 与自定义背景，可绘制具有渐变和圆角](graphics-and-animation-images/image1.png)

有关 XML 元素和语法的可绘制资源的更多详细信息，请查阅[Google 文档](https://developer.android.com/guide/topics/resources/drawable-resource.html#Shape)。


### <a name="using-the-canvas-drawing-api"></a>使用画布绘图 API

绘图功能非常强大，但有其局限性。 某些操作是不可能或非常复杂 (例如： 将筛选器应用于通过设备相机拍摄的图片)。 它会很难通过使用可绘制资源应用红眼。
相反，画布 API 允许应用程序能够非常精细地控制要有选择地更改图片的特定部分中的颜色。

一种常用于画布的类是[画图](https://developer.xamarin.com/api/type/Android.Graphics.Paint/)类。 此类包含有关如何绘制颜色和样式信息。 它用于提供内容，此类的颜色和透明度。

使用画布 API*绘画器的模型*绘制 2D 图形。
操作将应用在彼此的连续层中。 每个操作将覆盖基础位图的某些区域。 区域重叠先前绘制的区域，新画图将部分或完全遮盖旧。 这是 System.Drawing 和 iOS 的核心图形等许多其他图形 Api 的工作原理的相同方法。

有两种方法来获取`Canvas`对象。 第一种方法涉及到定义[位图](https://developer.xamarin.com/api/type/Android.Graphics.Bitmap/)对象，然后实例化`Canvas`对象与之。 例如，下面的代码段创建一个新的画布与基础位图：

```csharp
Bitmap bitmap = Bitmap.CreateBitmap(100, 100, Bitmap.Config.Argb8888);
Canvas canvas = new Canvas(b);
```

若要获取的其他方式`Canvas`对象是通过[OnDraw](https://developer.xamarin.com/api/member/Android.Views.View.OnDraw/)提供的回调方法[视图](https://developer.xamarin.com/api/type/Android.Views.View/)基类。 Android 调用此方法，在决定视图需要自我绘制，并且传入`Canvas`要使用的视图对象。

画布类公开方法，以编程方式提供的绘制说明。 例如：

-   [Canvas.DrawPaint](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPaint/p/Android.Graphics.Paint/) &ndash;用指定绘制填充整个画布的位图。

-   [Canvas.DrawPath](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawPath/p/Android.Graphics.Path/Android.Graphics.Paint/) &ndash;绘制指定的几何形状使用指定的画图。

-   [Canvas.DrawText](https://developer.xamarin.com/api/member/Android.Graphics.Canvas.DrawText/p/System.String/System.Single/System.Single/Android.Graphics.Paint/) &ndash;指定实心圆画布上绘制文本。 在位置绘制文本`x,y`。



#### <a name="drawing-with-the-canvas-api"></a>使用画布 API 绘图

让我们查看操作中的画布 API 的示例。 下面的代码段显示了如何绘制一个视图：

```csharp
public class MyView : View
{
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        Paint green = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0x99, 0xcc, 0),
        };
        green.SetStyle(Paint.Style.FillAndStroke);

        Paint red = new Paint {
            AntiAlias = true,
            Color = Color.Rgb(0xff, 0x44, 0x44)
        };
        red.SetStyle(Paint.Style.FillAndStroke);

        float middle = canvas.Width * 0.25f;
        canvas.DrawPaint(red);
        canvas.DrawRect(0, 0, middle, canvas.Height, green);
    }
}
```

上面这段代码首先创建红色画图和绿色绘制对象。 它使用红色，填充画布的内容，然后指示画布以绘制为画布宽度的 25%的绿色矩形。 此示例所示通过`AnimationsDemo`包含在本文的源代码的项目。 通过启动应用程序并从主菜单中选择绘制项，我们应类似于以下屏幕：

![屏幕上绘制红色和绿色绘制对象](graphics-and-animation-images/image3.png)


## <a name="animation"></a>动画

用户喜欢在其应用程序中移动的内容。 动画是改善应用程序的用户体验，并帮助其脱颖而出的好办法。最佳的动画是用户未注意到，因为他们觉得自然的。 Android 提供以下三个 API 用于动画：

-   **查看动画**&ndash;这是原始 API。 这些动画绑定到特定的视图，并可以对视图的内容执行简单转换。 由于它的简单性，此 API 仍可用于操作如 alpha 动画、 旋转等等。

-   **属性动画** &ndash; Android 3.0 中引入了属性动画。 它们使应用程序能够对几乎所有内容进行动画处理。 可以使用属性动画若要更改的任何属性的任何对象，即使该对象不在屏幕上可见。

-   **可绘制动画**&ndash;这用于将应用非常简单的动画的特殊可绘制资源影响布局。

一般情况下，属性动画是首选的系统，以用作更为灵活，提供了更多的功能。


### <a name="view-animations"></a>视图动画

视图动画限制为视图，并且只能对值，例如开始和结束点、 大小、 旋转和透明度执行动画。
这些类型的动画通常称为*tween 动画*。 可以通过两种方式定义视图动画&ndash;以编程方式在代码或通过使用 XML 文件。 XML 文件是声明视图动画的首选的方法，因为它们是更具可读性且更易于维护。

动画 XML 文件将存储在`/Resources/anim`Xamarin.Android 项目的目录。 此文件必须具有以下元素之一作为根元素：

-   `alpha` &ndash; 淡入或淡出动画。

-   `rotate` &ndash; 旋转动画。

-   `scale` &ndash; 调整大小的动画。

-   `translate` &ndash; 水平和/或垂直移动。

-   `set` &ndash; 可能包含一个或多个其他动画元素的容器。

默认情况下，将同时应用到 XML 文件中的所有动画。 若要使动画按顺序发生，请设置`android:startOffset`上面定义的元素之一上的属性。

可以通过使用影响中动画的行为更改的速率*内插器*。 内插器实现自定义动画加速、 重复出现，或 decelerated。 Android 框架提供的多个内的插器默认情况下，例如 （但不是限于）：

-   `AccelerateInterpolator` / `DecelerateInterpolator` &ndash; 这些内插器增加或减少中动画的行为更改的速率。

-   `BounceInterpolator` &ndash; 更改退回的末尾。

-   `LinearInterpolator` &ndash; 更改的速率保持不变。


以下 XML 显示了将合并这些元素的一些动画文件的示例：

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android=http://schemas.android.com/apk/res/android
     android:shareInterpolator="false">

    <scale android:interpolator="@android:anim/accelerate_decelerate_interpolator"
           android:fromXScale="1.0"
           android:toXScale="1.4"
           android:fromYScale="1.0"
           android:toYScale="0.6"
           android:pivotX="50%"
           android:pivotY="50%"
           android:fillEnabled="true"
           android:fillAfter="false"
           android:duration="700" />

    <set android:interpolator="@android:anim/accelerate_interpolator">
        <scale android:fromXScale="1.4"
               android:toXScale="0.0"
               android:fromYScale="0.6"
               android:toYScale="0.0"
               android:pivotX="50%"
               android:pivotY="50%"
               android:fillEnabled="true"
               android:fillBefore="false"
               android:fillAfter="true"
               android:startOffset="700"
               android:duration="400" />

        <rotate android:fromDegrees="0"
                android:toDegrees="-45"
                android:toYScale="0.0"
                android:pivotX="50%"
                android:pivotY="50%"
                android:fillEnabled="true"
                android:fillBefore="false"
                android:fillAfter="true"
                android:startOffset="700"
                android:duration="400" />
    </set>
</set>
```

此动画将所有动画同时执行。 第一个缩放动画将水平拉伸图像和垂直收缩，然后将映像将同时逆时针旋转 45 度和收缩，请从屏幕消失。

动画可以以编程方式应用于视图都动画，然后将它应用到一个视图。 Android 提供的帮助器类`Android.Views.Animations.AnimationUtils`，将放大量动画资源并返回的一个实例`Android.Views.Animations.Animation`。 此对象通过调用应用于视图`StartAnimation`并传递`Animation`对象。 下面的代码段显示了此示例：

```csharp
Animation myAnimation = AnimationUtils.LoadAnimation(Resource.Animation.MyAnimation);
ImageView myImage = FindViewById<ImageView>(Resource.Id.imageView1);
myImage.StartAnimation(myAnimation);
```

现在，我们已基本了解工作方式查看动画，允许将移到属性动画。


### <a name="property-animations"></a>属性动画

属性动画制作人员是 Android 3.0 中引入了一个新的 API。
它们提供一个更具扩展性 API，可以使用任何对象上的任何属性进行动画处理。

实例创建所有属性动画[动画器](https://developer.xamarin.com/api/type/Android.Animation.Animator/)子类。 应用程序不直接使用此类，而是使用它的子类之一：

-   [ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/) &ndash;此类是在整个属性动画 API 中最重要的类。 它会计算需要更改的属性的值。 `ViewAnimator`不会直接更新这些值; 相反，它会引发可用于更新经过动画处理的对象的事件。

-   [ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/) &ndash;此类是一个的子类`ValueAnimator`。 它旨在通过接受的目标对象和要更新属性来简化对象制作动画的过程。

-   [AnimationSet](https://developer.xamarin.com/api/type/Android.Animation.AnimatorSet/) &ndash;此类负责协调动画到另一个运行在关系中的方式。 动画它们之间可能会运行同时，按顺序，或指定的延迟。


*评估器*是动画制作人员用于在动画期间计算新值的特殊类。 默认情况下，Android 提供了下列计算器：

-   [IntEvaluator](https://developer.xamarin.com/api/type/Android.Animation.IntEvaluator/) &ndash;计算整数属性的值。

-   [FloatEvaluator](https://developer.xamarin.com/api/type/Android.Animation.FloatEvaluator/) &ndash;计算 float 属性值。

-   [ArgbEvaluator](https://developer.xamarin.com/api/type/Android.Animation.ArgbEvaluator/) &ndash;计算的颜色属性的值。

如果要进行动画处理的属性不是`float`，`int`或颜色，应用程序可能会创建其自己的计算器，通过实现`ITypeEvaluator`接口。 （实现自定义评估者是超出了本主题的范围。）

#### <a name="using-the-valueanimator"></a>使用 ValueAnimator

有两个部分的任何动画： 计算动画的值，然后某些对象上的属性上设置这些值。 
[ValueAnimator](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)仅计算值，但它将不会运行对象直接。 相反，在动画生命周期过程中将调用的事件处理程序将更新对象。 此设计允许多个属性以从一个经过动画处理的值进行更新。

获取的实例`ValueAnimator`通过调用以下工厂方法之一：

-  `ValueAnimator.OfInt`
-  `ValueAnimator.OfFloat`
-  `ValueAnimator.OfObject`

完成后执行的操作，`ValueAnimator`实例都必须具有其持续时间设置，然后启动它。 下面的示例演示如何设置一个介于 0 和 1 之间的动画效果的 1000年毫秒范围内：

```csharp
ValueAnimator animator = ValueAnimator.OfInt(0, 100);
animator.SetDuration(1000);
animator.Start();
```

本身，上面的代码段不是很有用，但&ndash;动画器将运行，但并没有针对更新后的值。 `Animator`类将引发时决定是通知的新值的侦听器所需的更新事件。 应用程序可能会提供一个事件处理程序以响应此事件，如下面的代码段中所示：

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

animator.Update += (object sender, ValueAnimator.AnimatorUpdateEventArgs e) =>
{
    int newValue = (int) e.Animation.AnimatedValue;
    // Apply this new value to the object being animated.
    myObj.SomeIntegerValue = newValue;
};
```

现在，我们已了解`ValueAnimator`，详细了解如何允许`ObjectAnimator`。

#### <a name="using-the-objectanimator"></a>使用 ObjectAnimator

[ObjectAnimator](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)是一个的子类`ViewAnimator`组合的计时引擎和值计算`ValueAnimator`与绑定事件处理程序所需的逻辑。 `ValueAnimator`要求应用程序以显式关联的事件处理程序&ndash;`ObjectAnimator`将为我们处理的此步骤。

有关 API`ObjectAnimator`非常类似于用于 API `ViewAnimator`，但需要您提供对象和要更新的属性的名称。 下面的示例演示使用的示例`ObjectAnimator`:

```csharp
MyCustomObject myObj = new MyCustomObject();
myObj.SomeIntegerValue = -1;

ObjectAnimator animator = ObjectAnimator.OfFloat(myObj, "SomeIntegerValue", 0, 100);
animator.SetDuration(1000);
animator.Start();
```

您可以看到从上一代码段中，`ObjectAnimator`可以减少并简化对对象进行动画处理所需的代码。


### <a name="drawable-animations"></a>可绘制动画

最后一个动画 API 是可绘制动画 API。 可绘制动画在它们加载一系列可绘制资源一并将其按顺序显示类似于翻转 it 卡通。

可绘制资源具有的 XML 文件中定义`<animation-list>`元素作为根元素和一系列`<item>`定义每个帧动画中的元素。 此 XML 文件存储在`/Resource/drawable`的应用程序文件夹。 以下 XML 是可绘制动画的示例：

```xml
<animation-list xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:drawable="@drawable/asteroid01" android:duration="100" />
  <item android:drawable="@drawable/asteroid02" android:duration="100" />
  <item android:drawable="@drawable/asteroid03" android:duration="100" />
  <item android:drawable="@drawable/asteroid04" android:duration="100" />
  <item android:drawable="@drawable/asteroid05" android:duration="100" />
  <item android:drawable="@drawable/asteroid06" android:duration="100" />
</animation-list>
```

此动画将运行六个帧。 `android:duration`属性声明将显示每个帧的时间。 下一步的代码段演示创建可绘制动画，然后启动它，当用户单击按钮在屏幕上的示例：

```csharp
AnimationDrawable _asteroidDrawable;

protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);

    _asteroidDrawable = (Android.Graphics.Drawables.AnimationDrawable)
    Resources.GetDrawable(Resource.Drawable.spinning_asteroid);

    ImageView asteroidImage = FindViewById<ImageView>(Resource.Id.imageView2);
    asteroidImage.SetImageDrawable((Android.Graphics.Drawables.Drawable) _asteroidDrawable);

    Button asteroidButton = FindViewById<Button>(Resource.Id.spinAsteroid);
    asteroidButton.Click += (sender, e) =>
    {
        _asteroidDrawable.Start();
    };
}
```

现在我们已介绍动画中的 Android 应用程序提供 Api 的基础知识。


## <a name="summary"></a>总结

本文介绍了许多新概念和 API 以帮助将一些图形添加到 Android 应用程序。 首先它讨论了各种二维图形 API 的并演示了如何 Android，应用程序可以直接使用画布对象在屏幕上绘制。 我们还了解到一些备用的技术，允许以声明方式创建使用 XML 文件的图形。 然后我们接着讨论的旧的和新 API 用于在 Android 中创建动画。



## <a name="related-links"></a>相关链接

- [动画演示 （示例）](https://developer.xamarin.com/samples/monodroid/AnimationDemo)
- [动画和图形](https://developer.android.com/guide/topics/graphics/index.html)
- [使用动画将转换为现实的移动应用](http://youtu.be/ikSk_ILg3d0)
- [AnimationDrawable](https://developer.xamarin.com/api/type/Android.Graphics.Drawables.AnimationDrawable/)
- [画布](https://developer.xamarin.com/api/type/Android.Graphics.Canvas/)
- [对象动画器](https://developer.xamarin.com/api/type/Android.Animation.ObjectAnimator/)
- [值动画器](https://developer.xamarin.com/api/type/Android.Animation.ValueAnimator/)

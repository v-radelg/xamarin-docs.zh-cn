---
title: 演练-在 Android 中使用触控
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: c4192f22ebd0ad1cde27745f5439c2d18a268ed3
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61012430"
---
# <a name="walkthrough---using-touch-in-android"></a>演练-在 Android 中使用触控

让我们了解如何从上一节中运行的应用程序使用的概念。 我们将使用四个活动创建一个应用。 第一个活动将一个菜单或将启动其他活动，以演示各种 Api 切换面板。 下面的屏幕截图显示了主活动：

[![示例屏幕截图，其中触摸冲动按钮](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

第一个活动，触控示例中，将显示如何使用事件处理程序，更改视图。 笔势识别器活动将演示如何创建子类`Android.View.Views`和处理事件，以及演示如何处理捏合手势。 在第三个和最后一个活动**自定义笔势**，将展示了如何使用自定义笔势。 若要使操作更轻松地遵循和 absorb，我们将分解为本演练部分中，将重点放在一个活动的每个部分。

## <a name="touch-sample-activity"></a>触控示例活动

-   打开项目**TouchWalkthrough\_启动**。 **MainActivity**是所有组转&ndash;都负责将活动中实现的触摸行为。 如果运行该应用程序并单击**触控示例**，下面的活动应启动：

    [![使用触摸开始显示的活动的屏幕截图](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   现在，我们确认活动启动之后，打开文件**TouchActivity.cs**并添加一个处理程序`Touch`事件的`ImageView`:

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   接下来，添加以下方法**TouchActivity.cs**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

请注意，在上面的代码，我们将视为`Move`和`Down`的相同的操作。 这是因为用户可能不提升他们的手指的即使`ImageView`、 可能四处移动，或由用户所施加的压力可能会更改。 这些类型的更改将生成`Move`操作。

每次在用户触摸`ImageView`，则`Touch`将引发事件和我们的处理程序将显示消息**接触开始**在屏幕上，如以下屏幕截图中所示：

[![使用触摸开始的活动的屏幕截图](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

只要用户触摸`ImageView`，**接触开始**将显示在`TextView`。 当用户不再触摸`ImageView`，该消息**接触结束**将显示在`TextView`，如以下屏幕截图中所示：

[![使用触摸结束的活动的屏幕截图](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>笔势识别器活动

现在让我们实现笔势识别器活动。 此活动将演示如何将视图在屏幕上四处拖动，并说明一种可实现捏合缩放。

-   将新活动添加到应用程序调用`GestureRecognizer`。
    编辑此活动的代码，使其类似于以下代码：

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

-   添加一个新的 Android 查看到项目中，并将其命名`GestureRecognizerView`。 将以下变量添加到此类：

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

-   添加以下构造函数为`GestureRecognizerView`。 此构造函数将添加`ImageView`到我们的活动。 此时代码仍将不会编译&ndash;我们需要创建该类`MyScaleListener`以帮助尺寸调整`ImageView`时用户 pinches 它：

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   若要在我们的活动上绘制图像，我们需要重写`OnDraw`视图类，如以下代码片段中所示的方法。 此代码将移动`ImageView`到指定的位置`_posX`和`_posY`也为调整大小的缩放因子根据映像：

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

-   接下来我们需要更新实例变量`_scaleFactor`作为用户 pinches `ImageView`。 我们将添加一个名为类`MyScaleListener`。 此类将侦听时用户 pinches 将由 Android 引发缩放事件`ImageView`。
    添加到以下的内部类`GestureRecognizerView`。 此类是`ScaleGesture.SimpleOnScaleGestureListener`。 此类是一个方便的类侦听器可以子类化，当你感兴趣的手势子集：

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _view.Invalidate();
            return true;
        }
    }
    ```

-   我们需要重写中的下一步方法`GestureRecognizerView`是`OnTouchEvent`。 下面的代码列出了此方法的完整实现。 有很多代码，因此允许花点时间看这里，这怎么回。 此方法执行的第一个操作是缩放图标，如有必要&ndash;这通过调用处理`_scaleDetector.OnTouchEvent`。 接下来，我们尝试找出哪些操作调用此方法：

    - 如果用户触摸屏幕，其中，我们记录的 X 和 Y 位置和接触屏幕的第一个指针的 ID。

    - 如果用户在屏幕上移动其触摸屏输入，然后我们确定用户移动鼠标指针的距离。

    - 如果用户具有提升了手指在屏幕上的，我们将停止跟踪手势。

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

-   现在运行应用程序，并启动笔势识别器活动。
    在启动时屏幕应如下所示的屏幕截图：

    [![笔势识别器与 Android 图标启动屏幕](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   现在触摸图标，并在屏幕上拖动它。 请尝试捏合缩放手势。 在某一时刻您的屏幕可能看起来类似于以下屏幕截图的内容：

    [![手势在屏幕上四处移动图标](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

此时您应该给自己加 pat 背面： 只是已在 Android 应用程序中实现捏合缩放 ！ 需要有短暂的休息，并允许将上移至在本演练中的第三个和最后一个活动&ndash;使用自定义笔势。

## <a name="custom-gesture-activity"></a>自定义笔势活动

在本演练中的最后一个屏幕将使用自定义笔势。

出于本演练的目的，手势库已使用手势工具创建并添加到项目文件中**资源/原始/手势**。 使用的独立地完成此位，允许继续本演练中的最后一个活动。

-   添加名为的布局文件**自定义\_手势\_layout.axml**到包含以下内容项目。 该项目已经包含所有映像**资源**文件夹：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

-   接下来将新活动添加到项目并将其命名`CustomGestureRecognizerActivity.cs`。 将两个实例变量作为显示在以下两行代码添加到类：

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   编辑`OnCreate`方法的活动中，以便其类似于下面的代码。 让我们看一分钟来说明这怎么回事在此代码中。 第一件事是实例化`GestureOverlayView`并将其设为活动的根视图。
    我们还将分配到一个事件处理程序`GesturePerformed`事件的`GestureOverlayView`。 接下来我们放大量更早版本，已创建的布局文件，并将它添加为子视图的`GestureOverlayView`。 最后一步是将变量初始化`_gestureLibrary`并从应用程序资源加载手势文件。 如果出于某种原因无法加载手势文件，没有太多可以执行此活动，因此关闭也一样：

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

-   我们需要确实实现了该方法的最后一件事`GestureOverlayViewOnGesturePerformed`如下面的代码段中所示。 当`GestureOverlayView`检测到笔势，它返回调用此方法。 尝试获取第一件事`IList<Prediction>`对象通过调用匹配手势`_gestureLibrary.Recognize()`。 我们使用 LINQ 的位来获取`Prediction`具有最高分数的笔势。

    如果不存在匹配手势具有足够的分数，则事件处理程序退出时不执行任何操作。 否则为，我们检查该预测的名称并更改所显示的图像基于手势的名称：

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

-   运行应用程序和自定义笔势识别器活动启动。 其外观应类似于以下屏幕截图的内容：

    [![屏幕截图检查我图像](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    现在，在屏幕上，绘制一个复选标记和显示位图应如下所示的下一个屏幕截图所示：

    [![识别绘制的复选框，选中标记](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    最后，在屏幕上绘制涂鸦。 复选框应改回其原始映像，如以下屏幕截图中所示：

    [![显示在屏幕上，原始图像的 scribble](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

你现在已了解了如何将触控和手势使用 Xamarin.Android 的 Android 应用程序中进行。


## <a name="related-links"></a>相关链接

- [Android 接触启动 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_start)
- [Android 接触最终 （示例）](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/Touch_final)

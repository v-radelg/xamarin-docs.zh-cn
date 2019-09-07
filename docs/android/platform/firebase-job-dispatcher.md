---
title: Firebase 作业调度程序
description: 本指南讨论如何使用Google的Firebase作业调度程序库安排后台工作。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/05/2018
ms.openlocfilehash: cb7e8aaca13405aedd422288421d497653ddbfe8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761207"
---
# <a name="firebase-job-dispatcher"></a>Firebase 作业调度程序

_本指南讨论如何使用Google的Firebase作业调度程序库安排后台工作。_

## <a name="overview"></a>概述

使 Android 应用程序对用户进行响应的最佳方式之一是确保在后台执行复杂的或长时间运行的工作。 但重要的是，后台工作不会对用户对设备的体验产生负面影响。 

例如，后台作业可能每三或四分钟轮询一次网站，以查询特定数据集的更改。 这似乎是良性的，但会对电池寿命产生灾难性影响。 应用程序将重复唤醒设备，将 CPU 提升到较高的电源状态，启动无线电收发器，使网络请求，然后处理结果。 这种情况更糟，因为设备不会立即断电并返回到低功耗空闲状态。 计划不良的后台工作可能会意外地使设备处于一种不必要和过多电源要求的状态。 此看似合法的活动（轮询网站）会使设备在相对较短的时间内不可用。

Android 提供以下 Api，可帮助在后台执行工作，但其本身并不是用于智能作业计划的。 

- **[意向服务](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意向服务非常适合用于执行工作，不过，它们无法计划工作。
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash;这些 api 只允许计划工作，而不提供实际执行工作的方式。 此外，AlarmManager 只允许基于时间的约束，这意味着在特定时间或经过一段时间后发出警报。 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule 是一个很好的 API，适用于操作系统来计划作业。 不过，它仅适用于面向 API 级别21或更高版本的 Android 应用程序。 
- **[广播接收](~/android/app-fundamentals/broadcast-receivers.md)** 方&ndash; Android 应用可设置广播接收器来执行工作，以响应系统范围内的事件或意向。 但是，广播接收器不提供对作业运行时间的任何控制。 同时，Android 操作系统中的更改将限制广播接收方的工作时间或它们可以响应的工作类型。 

有效地执行后台工作（有时称为_后台作业_或_作业_）有两个主要功能：

1. **智能地计划工作**&ndash;当应用程序在后台执行工作时，这一点非常重要。 理想情况下，应用程序不应要求运行作业。 相反，应用程序应指定在作业可以运行时必须满足的条件，然后计划在满足条件时运行的工作。 这允许 Android 智能地执行工作。 例如，可能会批处理网络请求同时运行全部，以充分利用网络所涉及的开销。
2. **封装工作**&ndash;用于执行后台工作的代码应封装在独立的组件中，该组件可以独立于用户界面运行，并且如果出于某种原因无法完成工作，将会相对容易地重新计划。

Firebase 作业调度程序是 Google 中的一个库，提供 Fluent API 来简化计划后台工作。 它旨在取代 Google Cloud Manager。 Firebase 作业调度程序包含以下 Api：

- `Firebase.JobDispatcher.JobService`是一个抽象类，必须使用要在后台作业中运行的逻辑进行扩展。
- 在应启动作业时声明。`Firebase.JobDispatcher.JobTrigger` 这通常以时间窗口的形式表示，例如，在启动作业之前等待至少30秒，但在5分钟内运行作业。
- 包含`Firebase.JobDispatcher.RetryStrategy`有关作业未能正常执行时应执行的操作的信息。 重试策略指定尝试再次运行作业之前等待多长时间。 
- `Firebase.JobDispatcher.Constraint`是一个可选值，用于描述在作业可以运行之前必须满足的条件，如设备在未计费的网络上或正在充电。
- 是一个 API，它将上一个 api 统一到可`JobDispatcher`由计划的工作单元。 `Firebase.JobDispatcher.Job` `Job.Builder`类用于实例化`Job`。
- `Firebase.JobDispatcher.JobDispatcher`使用前面的三个 api 来计划操作系统的工作，并在必要时提供取消作业的方法。

若要计划使用 Firebase 作业调度程序，Xamarin Android 应用程序必须将代码封装在扩展`JobService`类的类型中。 `JobService`有三种可以在作业的生存期内调用的生命周期方法：

- **`bool OnStartJob(IJobParameters parameters)`** &ndash;此方法用于执行工作，并且应始终实现此方法。 它在主线程上运行。 如果剩余工作或`true` `false`完成工作，则此方法将返回。 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash;出于某种原因停止作业时，将调用此。 如果应稍后`true`重新计划作业，则它应返回。
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** 当完成任何异步工作时，将调用此方法。`JobService` &ndash; 

若要计划作业，应用程序将实例化`JobDispatcher`对象。 然后， `Job.Builder`使用来创建一个`Job`对象， `JobDispatcher`该对象将用于尝试和计划作业运行的。

本指南将讨论如何向 Xamarin Android 应用程序添加 Firebase 作业调度程序，并使用它来计划后台工作。

## <a name="requirements"></a>要求

Firebase 作业调度程序需要 Android API 级别9或更高版本。 Firebase 作业调度程序库依赖于 Google Play Services 提供的某些组件;设备必须安装 Google Play Services。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>在 Xamarin 中使用 Firebase 作业调度程序库

若要开始 Firebase 作业调度程序，请首先将[Firebase JobDispatcher NuGet 包](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)添加到 Xamarin 项目。 在 NuGet 包管理器中搜索**JobDispatcher**包（仍处于预发行版）。

添加 Firebase 作业调度程序库后，创建一个`JobService`类，然后将其计划为使用的`FirebaseJobDispatcher`实例运行。

### <a name="creating-a-jobservice"></a>创建 JobService

Firebase 作业调度程序库执行的所有工作都必须在一个扩展`Firebase.JobDispatcher.JobService`抽象类的类型中完成。 创建非常类似于`Service`使用 Android framework 创建： `JobService` 

1. `JobService`扩展类
2. 用修饰子类`ServiceAttribute`。 尽管不是严格需要的，但建议显式设置`Name`参数以帮助`JobService`调试。 
3. 添加以在**androidmanifest.xml**中声明。 `JobService` `IntentFilter` 这也有助于 Firebase 作业调度程序库定位并调用`JobService`。

下面的代码是应用程序的最简单`JobService`示例，使用 TPL 异步执行一些工作：

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>创建 FirebaseJobDispatcher

在计划任何工作之前，需要创建一个`Firebase.JobDispatcher.FirebaseJobDispatcher`对象。 `FirebaseJobDispatcher` 负责`JobService`计划。 以下代码片段是创建实例`FirebaseJobDispatcher`的一种方法： 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在前面的代码片段中， `GooglePlayDriver`是一种`FirebaseJobDispatcher`有助于与设备上 Google Play Services 中的某些计划 api 进行交互的类。 参数`context`是任何 Android `Context`，如活动。 目前， `IDriver`是 Firebase 作业调度程序库中的唯一实现。 `GooglePlayDriver` 

Firebase 作业调度程序的 Xamarin 绑定提供了一个用于`FirebaseJobDispatcher` `Context`从中创建的扩展方法： 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

实例化后，可以`Job`创建并`JobService`运行类中的代码。 `FirebaseJobDispatcher` `Job` 由对象创建，将在下`Job.Builder`一节中讨论。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>使用作业创建 Firebase. JobDispatcher

类负责封装`JobService`运行所需的元数据。 `Firebase.JobDispatcher.Job` 包含一些信息，如在作业可以运行之前必须满足的任何约束（如果是重复`Job`的）或将导致作业运行的任何触发器。`Job`  至少`Job`必须有一个_标记_（用于`FirebaseJobDispatcher`标识该作业的唯一字符串）和应运行的的`JobService`类型。 在运行作业时，Firebase 作业`JobService`调度程序将实例化。  `Job`使用`Firebase.JobDispatcher.Job.JobBuilder`类的实例创建。 

下面的代码片段是如何使用 Xamarin for Android 绑定创建的`Job`最简单示例：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder`将对作业的输入值执行一些基本验证检查。 如果无法`Job.Builder` `Job`创建，则会引发异常。  将创建具有以下默认值的`Job`： `Job.Builder`

- 的生存期 （计划运行的时间长度）仅在设备重新启动&ndash; `Job`后设备重新启动时才会丢失。 `Job`
- 不会重复&ndash; ，它只运行一次。 `Job`
- `Job`将计划尽快运行。
- 的`Job`默认重试策略是使用_指数回退_（有关详细信息，请参见[设置 RetryStrategy](#Setting_a_RetryStrategy)部分的以下部分）

### <a name="scheduling-a-job"></a>计划作业

创建`Job`后，需要在运行`FirebaseJobDispatcher`之前通过计划。 计划有两种方法`Job`：

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

返回的值`FirebaseJobDispatcher.Schedule`将为以下整数值之一：

- `FirebaseJobDispatcher.ScheduleResultSuccess`&ndash; 已`Job`成功计划。
- `FirebaseJobDispatcher.ScheduleResultUnknownError`发生了阻止计划的`Job`某些未知问题。 &ndash;
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable`使用了`IDriver`无效的，或`IDriver`因某种原因而不可用。 &ndash; 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger`&ndash;不支持`Trigger` 。
- `FirebaseJobDispatcher.ScheduleResultBadService`&ndash;服务配置不正确或不可用。

### <a name="configuring-a-job"></a>配置作业

可以自定义作业。 如何自定义作业的示例包括：

- 将[参数传递到作业](#Passing_Parameters_to_a_Job)&ndash; 可能需要其他值来执行其工作，`Job`例如，下载文件。
- [设置约束](#Setting_Constraints)&ndash;在满足某些条件时，可能只需要运行作业。 例如，仅`Job`在设备充电时运行。 
- [指定何时`Job`应运行](#Setting_Job_Triggers) &ndash; Firebase 作业调度程序允许应用程序指定作业应运行的时间。  
- [声明失败作业的重试策略](#Setting_a_RetryStrategy)重试_策略_向`FirebaseJobDispatcher`提供有关无法完成的操作`Jobs`的指导。 &ndash; 

以下各节将详细讨论其中的每个主题。

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>将参数传递到作业

通过创建`Bundle` `Job.Builder.SetExtras`与方法一起传递的来将参数传递给作业：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

可从方法`OnStartJob`的属性访问： `IJobParameters.Extras` `Bundle`

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>设置约束

约束有助于降低设备的成本或电池消耗。 类`Firebase.JobDispatcher.Constraint`将这些约束定义为整数值：

- `Constraint.OnUnmeteredNetwork`&ndash;仅当设备连接到无法进行的网络时运行作业。 这有助于防止用户产生数据收费。
- `Constraint.OnAnyNetwork`&ndash;在设备连接到的任何网络上运行作业。 如果与`Constraint.OnUnmeteredNetwork`一起指定，则此值优先。
- `Constraint.DeviceCharging`&ndash;仅在设备充电时运行作业。

约束是通过`Job.Builder.SetConstraint`方法设置的： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

为`JobTrigger`操作系统提供有关何时应启动作业的指导。 具有一个_执行窗口_，该窗口定义了应在何时运行`Job`的计划时间。 `JobTrigger` 执行窗口有一个_开始窗口_值和一个_结束窗口_值。 "开始" 窗口是设备在运行作业之前应等待的秒数，而 "结束时间" 值是运行`Job`前等待的最大秒数。 

`JobTrigger` 可以`Firebase.Jobdispatcher.Trigger.ExecutionWindow`使用方法创建。  例如`Trigger.ExecutionWindow(15,60)` ，该作业的运行时间应介于15到60秒之间。 `Job.Builder.SetTrigger`方法用于 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

作业的`JobTrigger`默认值由值表示，此值`Trigger.Now`指定在计划后尽快运行作业。

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>设置 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy`用于指定在尝试重新运行失败的作业之前设备应使用的延迟量。 有一个策略，该策略定义将使用哪种时间基准算法来重新计划失败的作业，并指定一个执行窗口，该窗口指定应在其中安排作业的窗口。 `RetryStrategy` 此重新_计划窗口_由两个值定义。 第一个值是在重新安排作业之前等待的秒数（_初始回退_值），第二个数字是作业必须运行之前的最大秒数（_最大回退_值）。 

这两种类型的重试策略由以下 int 值标识：

- `RetryStrategy.RetryPolicyExponential`指数回退策略会在每次发生故障后以指数方式增加初始回退值。 &ndash; 第一次作业失败时，库将等待指定的初始时间间隔，然后再重新安排该&ndash;作业示例30秒。 第二次作业失败时，库将等待至少60秒，然后再尝试运行作业。 第三次尝试失败后，库将等待120秒，依此类推。 Firebase 作业`RetryStrategy`调度程序库的默认值由`RetryStrategy.DefaultExponential`对象表示。 它的初始回退为30秒，最大回退为3600秒。
- `RetryStrategy.RetryPolicyLinear`此策略是一种_线性回退_，应将作业重新安排为按设置的时间间隔运行（直到成功）。 &ndash; 线性回退最适用于必须尽快完成的工作，或者用于快速解决自身问题的问题。 Firebase 作业调度程序库定义了`RetryStrategy.DefaultLinear`一个至少为30秒且最长为3600秒的重新计划窗口。

`RetryStrategy` 可以`FirebaseJobDispatcher.NewRetryStrategy`使用方法定义自定义。 它采用三个参数：

1. `int policy` `RetryStrategy` `RetryStrategy.RetryPolicyLinear` `RetryStrategy.RetryPolicyExponential`此策略为先前的值之一：、或。 &ndash;
2. `int initialBackoffSeconds`初始回退是指再次尝试运行作业之前需要延迟的时间（以秒为单位）。 &ndash; 此值的默认值为30秒。 
3. `int maximumBackoffSeconds`_最大回退_值声明尝试再次运行作业之前延迟的最大秒数。 &ndash; 默认值为3600秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>取消作业

可以取消已计划的所有作业，或者只是使用`FirebaseJobDispatcher.CancelAll()`方法`FirebaseJobDispatcher.Cancel(string)`或方法的单个作业：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

任一方法都将返回一个整数值：

- `FirebaseJobDispatcher.CancelResultSuccess`&ndash;已成功取消作业。
- `FirebaseJobDispatcher.CancelResultUnknownError`&ndash;错误阻止作业被取消。
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable`无法取消作业，因为没有有效`IDriver`的可用。 &ndash; `FirebaseJobDispatcher`

## <a name="summary"></a>总结

本指南讨论了如何使用 Firebase 作业调度程序在后台智能地执行工作。 本文介绍了`JobService`如何封装要作为执行的工作，以及如何`FirebaseJobDispatcher`使用来计划该工作、使用`JobTrigger`指定条件`RetryStrategy`以及如何使用来处理失败。

## <a name="related-links"></a>相关链接

- [NuGet 上的 Firebase JobDispatcher](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-GitHub 上的调度程序](https://github.com/firebase/firebase-jobdispatcher-android)
- [Firebase. JobDispatcher 绑定](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智能作业-计划](https://developer.android.com/topic/performance/scheduling.html)
- [Android 电池和内存优化-Google i/o 2016 （视频）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)

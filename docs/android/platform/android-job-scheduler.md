---
title: Android 作业计划程序
description: 本指南介绍如何计划使用 Android 作业计划程序 API 的后台工作。
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: c0f638afbf044a2e3e6f309839cb22137cf95912
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60956278"
---
# <a name="android-job-scheduler"></a>Android 作业计划程序

_本指南介绍如何安排使用运行 Android 5.0 （API 级别 21） 的 Android 设备可用的 Android 作业计划程序 API 的后台工作和更高版本。_


## <a name="overview"></a>概述 

若要使 Android 应用程序保持响应状态向用户的最佳方法之一是确保复杂或长时间运行的工作在后台执行。 但是，务必与设备后台工作将带来负面影响的用户体验。 

例如，后台作业可能分钟轮询一次网站每三个或四个查询的特定的数据集的更改。 这看起来良性的的但它会上的电池使用寿命带来灾难性影响。 应用程序将重复唤醒设备，将 CPU 提升到更高版本的电源状态、 接通电源的无线电收发器，使网络请求，然后处理结果。 它获取更糟的是，因为该设备将不会立即关闭电源，并返回到低能耗空闲状态。 效果不佳计划的后台工作可能会无意中保护设备的不必要和过多电源要求的状态中。 此一项看似无害的活动 （轮询网站） 将导致设备相对较短的时间内不可用。

Android 提供了以下 Api，以帮助在后台执行工作，但本身不足够的智能作业计划。 

* **[意向服务](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意向服务非常适合用于执行工作，但它们不提供任何方法来计划工作。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash;这些 Api 仅允许以进行计划，但不提供任何方法来实际执行的工作的工作。 此外，AlarmManager 仅允许基于时间的限制，这意味着在某个时间或经过一段时间后会引发警报。 
* **[广播接收器](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 应用程序可以设置广播的接收器来执行工作以响应系统级事件或意向。 但是，广播的接收器不提供任何控制应何时运行该作业。 此外限制 Android 操作系统中的更改将广播的接收器将起作用，或类型的响应的工作。 

有两个有效地执行后台工作的关键功能 (有时称为_后台作业_或_作业_):

1. **以智能方式调度工作**&ndash;非常重要的应用程序在后台的工作执行该操作时它会作为一名良好公民。 理想情况下，应用程序不应要求，在运行作业。 相反，应用程序应指定当作业可以运行，然后安排该作业与满足条件时将执行工作的操作系统必须满足的条件。 这使 Android 可运行作业，以确保在设备上的最大效率。 例如，网络请求可能会进行批处理运行全部在同一时间，以便与网络相关的开销最大值使用。
2. **封装工作**&ndash;来执行后台工作的代码应封装在可运行独立于用户界面并将可以很轻松地重新计划如果无法完成工作的离散组件由于某种原因。

Android 作业计划程序是内置于 Android 操作系统，提供简化计划的后台工作的 fluent API 的框架。  Android 作业计划程序包括以下类型：

* `Android.App.Job.JobScheduler`是一种系统服务，用于计划、 执行和根据需要取消，代表 Android 应用程序的作业。
* `Android.App.Job.JobService`是一个抽象类，必须使用将在应用程序的主线程运行作业的逻辑扩展。 这意味着，`JobService`负责的工作方式是以异步方式执行。
* `Android.App.Job.JobInfo`对象保留的条件来确定作业应该运行指导 Android。

若要计划使用 Android 作业计划程序的工作，Xamarin.Android 应用程序必须封装在扩展类中的代码`JobService`类。 `JobService` 有三个作业的生存期内可以调用的生命周期方法：

* **bool （JobParameters 参数） OnStartJob** &ndash;调用此方法`JobScheduler`来执行工作，并可在应用程序的主线程上运行。 它负责`JobService`来以异步方式执行的工作并`true`剩余，如果没有工作或`false`如果完成工作。
    
    当`JobScheduler`调用此方法，它将请求并通过 Android wakelock 保留作业的持续时间。 完成作业后，它负责`JobService`告诉`JobScheduler`的调用这一事实`JobFinished`方法 （接下来介绍）。

* **（JobParameters 参数，bool needsReschedule） JobFinished** &ndash;必须调用此方法`JobService`告诉`JobScheduler`该操作完成。 如果`JobFinished`未调用`JobScheduler`将不会删除 wakelock，从而导致不必要的电池耗尽。 

* **bool （JobParameters 参数） OnStopJob** &ndash; Android 过早停止该作业时调用。 它应返回`true`如果应重新安排作业根据重试条件 （下面详细讨论）。

可以指定_约束_或_触发器_控制可以或应该运行作业。 例如，就可以限制作业，以便它仅将运行设备正在充电或启动作业时图片拍摄时。

本指南将详细地讨论如何实现`JobService`类并将其与安排`JobScheduler`。

## <a name="requirements"></a>要求

Android 作业计划程序需要 Android API 级别 21 (Android 5.0) 或更高版本。 

## <a name="using-the-android-job-scheduler"></a>使用 Android 作业计划程序

有三个步骤使用 Android JobScheduler API:

1. 实现 JobService 类型来封装工作。
2. 使用`JobInfo.Builder`对象来创建`JobInfo`将保存的条件的对象`JobScheduler`运行作业。 
3. 作业使用计划`JobScheduler.Schedule`。

### <a name="implement-a-jobservice"></a>实现 JobService

Android 作业计划程序库执行的所有工作必须都进行扩展的类型中`Android.App.Job.JobService`抽象类。 创建`JobService`非常类似于创建`Service`Android framework: 

1. 扩展`JobService`类。
2. 修饰具有子类`ServiceAttribute`并设置`Name`参数为包名称和类的名称组成的字符串 （请参阅下面的示例）。
3. 设置`Permission`上的属性`ServiceAttribute`到字符串`android.permission.BIND_JOB_SERVICE`。
4. 重写`OnStartJob`方法中，添加代码来执行工作。 Android 将调用要运行作业的应用程序的主线程上的此方法。 需要较长时间，应以避免阻止应用程序的线程执行几毫秒的工作。
5. 完成工作后，`JobService`必须调用`JobFinished`方法。 此方法是如何`JobService`告知`JobScheduler`工作完成。 调用失败`JobFinished`将导致`JobService`将不必要的需求放在设备上，缩短电池寿命。 
6. 它是一个好办法还重写`OnStopJob`方法。 当作业正在关闭之前已完成，并提供了，调用此方法由 Android`JobService`正确释放任何资源的机会。 此方法应返回`true`如有必要，重新计划作业，或`false`如果不希望发生溢出，若要重新运行该作业。
   

下面的代码是最简单的示例`JobService`应用程序，使用 TPL 执行一些操作，以异步方式：

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>创建对 JobInfo 安排作业计划

Xamarin.Android 应用程序不实例化`JobService`直接，改为将传递`JobInfo`对象传递给`JobScheduler`。 `JobScheduler`将实例化请求`JobService`对象，计划和运行`JobService`中的元数据根据`JobInfo`。 一个`JobInfo`对象必须包含以下信息：

* **JobId** &ndash;这是`int`值，该值用于标识作业到`JobScheduler`。 重复使用此值将更新任何现有作业。 值必须是唯一的应用程序。 
* **JobService** &ndash; ，此参数才`ComponentName`的显式标识的类型的`JobScheduler`应该用于运行作业。 
  

此扩展方法演示如何创建`JobInfo.Builder`与 Android `Context`，例如活动：

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Android 作业计划程序的一项强大功能是能够控制时的作业运行或在什么条件下一个作业可能会运行。 下表描述的一些方法对`JobInfo.Builder`的允许的应用程序来影响一个作业的运行时：  


|  方法 | 描述   |
|---|---|
| `SetMinimumLatency`  | 指定运行作业之前应观察到延迟时间 （以毫秒为单位）。 |
| `SetOverridingDeadline`  | 声明该作业必须运行，然后经过此时间 （以毫秒为单位）。 |
| `SetRequiredNetworkType`  | 指定作业的网络要求。 |
| `SetRequiresBatteryNotLow` | 设备不向用户显示"电池电量不足"警报时，可能仅运行该作业。 |
| `SetRequiresCharging` | 当电池充电时，可能仅运行该作业。 |
| `SetDeviceIdle` | 当设备繁忙时，将运行作业。 |
| `SetPeriodic` | 指定作业应定期运行。 |
| `SetPersisted` | 作业在设备重新启动后应 perisist。 | 


`SetBackoffCriteria`提供一些指导如何长`JobScheduler`尝试再次运行作业之前应等待。 有两个部分的退避算法条件： 毫秒 （30 秒的默认值） 和类型的退让延迟，应使用的 (有时称为_回退策略_或_重试策略_). 两个策略都封装在`Android.App.Job.BackoffPolicy`枚举：

* `BackoffPolicy.Exponential` &ndash; 指数退让策略将每个发生故障后呈指数级增加初始回退值。 第一次的作业失败，库将等待重新安排作业-示例 30 秒之前指定的初始间隔。 第二次作业失败时，库将等待至少 60 秒，然后再尝试运行该作业。 第三个尝试失败后，库将等待 120 秒，依此类推。 这是默认值。
* `BackoffPolicy.Linear` &ndash; 此策略是作业应重新安排给线性退避算法 （直到成功） 按设置的间隔运行。 线性退避算法最适合必须尽可能快地完成的工作或快速将自身解决的问题。 

有关更多详细信息创建`JobInfo`对象，请阅读[Google 的文档`JobInfo.Builder`类](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)。

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>将参数传递给通过 JobInfo 作业

通过创建参数传递到作业`PersistableBundle`连同传递`Job.Builder.SetExtras`方法：

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle`从访问`Android.App.Job.JobParameters.Extras`属性中的`OnStartJob`方法的`JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>计划的作业

若要计划作业，Xamarin.Android 应用程序将获取对的引用`JobScheduler`系统服务，并调用`JobScheduler.Schedule`方法替换`JobInfo`上一步中创建的对象。 `JobScheduler.Schedule` 将立即返回两个整数值之一：

* **JobScheduler.ResultSuccess** &ndash;成功计划作业。 
* **JobScheduler.ResultFailure** &ndash;无法计划作业。 这通常由冲突引起`JobInfo`参数。

此代码是计划的作业和通知用户计划尝试的结果的示例：

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>正在取消作业

它是可以取消所有作业的计划，或只是单个作业使用`JobsScheduler.CancelAll()`方法或`JobScheduler.Cancel(jobId)`方法：

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>总结

本指南介绍如何使用 Android 作业计划程序以智能方式在后台执行工作。 它介绍了如何封装为执行的工作`JobService`以及如何使用`JobScheduler`安排该工作，指定与条件`JobTrigger`方式与处理故障和`RetryStrategy`。

## <a name="related-links"></a>相关链接

- [智能的作业计划](https://developer.android.com/topic/performance/scheduling.html)
- [JobScheduler API 参考](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [计划与 JobScheduler 专业水准的作业](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android 的电池和内存优化-Google I/O 2016 （视频）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert-Xamarin 学院课程](https://www.youtube.com/watch?v=aSjBBPYjelE)

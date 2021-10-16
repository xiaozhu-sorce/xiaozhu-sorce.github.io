---
title: Activity生命周期
date: 2021-10-15 14:44:53
categories: Android
tags: Android
author: 小朱
comments: true
---

# 前言

众所周知，Android共有四大组件。而Activity作为四大组件之首，是我们使用最为频繁的一种组件。Activity是一种展示型组件，用于向用户直接的展示一个界面，并且可以接收用户的信息从而进行交互。Activity是四大组件中唯一可以被用户感知到的。

回想一下我们学过的语言，不管是在测试什么功能函数，都要将main作为函数的入口才能开始代码的执行。我们称main为函数的入口。而对于Android来说，Android程序从ActivityThread的main方法开始，接收**AMS**（ActivityManagerService）的调度启动“LaunchActivity”，也就是我们在AndroidMainfest中配置的为main的activity，当应用启动的时候，就会首先打开这个activity。有了这第一个界面，剩下的界面就根据用户的操作来进行跳转了。

<!-- more -->

那如何做到每个界面之间的彼此解耦，各自之间的显示不发生混乱，界面之间的跳转有条不紊？这些工作，Activity已经都帮我们做好了。我们需要负责的只是各个界面的设计和开发，实现相应界面的功能？但是activity没有main方法可以让我们用，那我们应该怎么办？答案就是**生命周期回调方法**。

Activity承担的责任非常多，需要初始化的逻辑也非常多。当Activity被启动，他会根据自身的启动情况，来回调不同的生命周期方法。接下来我们一起看看这些回调方法。

# 生命周期解析

为了在 Activity 生命周期的各个阶段之间导航转换，Activity 类提供六个核心回调：`onCreate()`、`onStart()`、`onResume()`、`onPause()`、`onStop()` 和 `onDestroy()`。当 Activity 进入新状态时，系统会调用其中每个回调。我们可以重写这一些方法，当进入不同的状态的时候，执行对应的逻辑。

![浮图秀图片_developer.android.com_20211015181043.png](https://i.loli.net/2021/10/15/kEZ8o9dVqeFbGaX.png)

如上图这就是一个Activity的生命周期的简化图示

## 回调方法

- **onCreate ( )**: 表示Activity正在被创建。在这个方法中我们可以进行Activity的一些初始化工作，比如使用setContentView加载布局，初始化Activity所需要的数据等。

> 该方法是我们使用最频繁的一个回调方法。
>
> 我们需要在这个方法中初始化基础组件和视图。如viewmodel，textview。同时必须在该方法中调用setContentView来给activity设置布局。

- **onStart ( )** : 表示Activity正在被启动，即将开始，此时Activity已经可见了，但是还没有出现在前台，还无法和用户进行交互。这个时候我们可以理解为Activity已经被显示出来了，但是我们还看不到。

- **onResume ( )** : 表示Activity不仅可见了，并且可以出现在前台开始活动。

- **onPause ( )** :表示Activity正在停止，正常情况下，紧接着onStop就会被调用。

> 这个方法一般在另一个activity要进入前台前被调用。只有当前activity进入后台，其他的activity才能进入前台。所以，**该方法不能做重量级的操作，不然则会引用界面切换卡顿**。
>
> 一般的使用场景为界面进入后台时的轻量级资源释放。
>
> 最好理解这个状态就是弹出另一个activity的窗口的时候。因为前台activity只能有一个，所以当前可交互的activity变成另一个activity后，原activity就必须调用onPause方法进入`ON_PAUSE`状态；但是，仍然是可见的，只是无法进行交互。

- **onStop ( )** :表示Activity即将停止，此时Activity不可见。这个阶段可以做一些微重量级的回收工作，同样不能太耗时。

- **onDestroy ( )** : 表示Activity即将被销毁，也是Activity的最后一个回调。在这里，我们可以做一些回收工作和最终的资源释放。

- **onRestart( )** ：表示Activity正在重新启动。当当前的Activity从不可见的状态转变为可见的状态时候，onRestart就会被调用。这种情形一般是用户行为所导致的，比如当用户按Home键切换到桌面或者一个新的Activity，这是当前的Activity就会暂停（即onPause和onStop被执行了），接着用户又回到了这个Activity，这时就会调用onRestart方法了。

从整个生命周期来说，onCreate和onDestroy是配对的，分别标识着Activity的创建和销毁，并且只可能有一次调用。

从Activity是否在前台来说，onResume和onPause是配对的，随着用户的操作或设备屏幕的点亮和熄灭，这两个方法可以被多次调用。

从Activity是否可见来说，onStart和onStop是配对的，随着用户的操作或设备屏幕的点亮和熄灭，这两个方法可以被多次调用。

## 举个🌰

这里以自控力的页面跳转为栗子，通过Logcat我们来看看每个方法被调用的先后顺序。

![截屏2021-10-16 下午4.17.22.png](https://i.loli.net/2021/10/16/9haKP6EDFcBUJjI.png)

1. 针对一个特定的Activity（ClockActivity）的启动，回调的顺序如下：onCreate -> onStart ->onResume
2. 当我们打开了一个新的Activity（ClockInActivity）时候，回调如下：onPause -> onStop
3. 当我们返回到了原来的Activity时候，回调如下：OnRestart ->onSart ->onResume
4. 当我们back到主页面的时候，回调如下：onPause -> onStop -> onDestroy

从这里我们也可以看出来从一个ActivityA进入到另外一个ActivityB的时候是先调用了ActivityA的`onPause`方法之后在调用ActivityB的`onResume`方法。同时我们也能注意到这里先调用了Activity B的`onCreate`之后才调用了Activity B的`onStop`方法。

# 关于启动一次activity的那些事儿

我们在启动一个activity，在显示调用时，只需要调用如下的代码。如此简单也只是因为系统对Activity这个组件进行了很大程度上的封装。而真正的藏在细枝末节里的东西使得我们无法得知，因为我们刚才也提到了应该使用Activity的`onCreate()`方法来启动一个activity，所以接下来我们就要进入到activity启动之后系统深处所做的事情。

```java
Intent intent = new Intent(this, ClockInActivity.class);
startActivity(intent);
```

从Activity的startActivity方法开始分析，startActivity方法有很多种重载方式，但它们最终都会调用startActivityForResult方法，它的实现如下图所示。

```java
public void startActivityForResult(@RequiresPermission Intent intent, int requestCode,
            @Nullable Bundle options) {
        if (mParent == null) {
            options = transferSpringboardActivityOptions(options);
            Instrumentation.ActivityResult ar =
                mInstrumentation.execStartActivity(
                    this, mMainThread.getApplicationThread(), mToken, this,
                    intent, requestCode, options);
            if (ar != null) {
                mMainThread.sendActivityResult(
                    mToken, mEmbeddedID, requestCode, ar.getResultCode(),
                    ar.getResultData());
            }
            if (requestCode >= 0) {
                mStartedActivity = true;
            }

            cancelInputsAndStartExitTransition(options);
        } else {
            if (options != null) {
                mParent.startActivityFromChild(this, intent, requestCode, options);
            } else {
                mParent.startActivityFromChild(this, intent, requestCode);
            }
        }
    }
```

在这里我们看到**mMainThread.getApplicationThread()**这个参数，请记住它！！

这段代码里面最值得我们注意的就是`execStartActivity`这个方法，如果是第一次启动，mParent会null，会直接调用Instrumentation`execStartActivity`，接下来我们来看一看它的代码。

## Instrumentation调用到ATMS

```java
public ActivityResult execStartActivity(
            Context who, IBinder contextThread, IBinder token, Activity target,
            Intent intent, int requestCode, Bundle options) {
        IApplicationThread whoThread = (IApplicationThread) contextThread;
        Uri referrer = target != null ? target.onProvideReferrer() : null;
        if (referrer != null) {
            intent.putExtra(Intent.EXTRA_REFERRER, referrer);
        }
        if (mActivityMonitors != null) {
            synchronized (mSync) {
                final int N = mActivityMonitors.size();
                for (int i=0; i<N; i++) {
                    final ActivityMonitor am = mActivityMonitors.get(i);
                    ActivityResult result = null;
                    if (am.ignoreMatchingSpecificIntents()) {
                        result = am.onStartActivity(intent);
                    }
                    if (result != null) {
                        am.mHits++;
                        return result;
                    } else if (am.match(who, null, intent)) {
                        am.mHits++;
                        if (am.isBlocking()) {
                            return requestCode >= 0 ? am.getResult() : null;
                        }
                        break;
                    }
                }
            }
        }
        try {
            intent.migrateExtraStreamToClipData(who);
            intent.prepareToLeaveProcess(who);
          
          //重点在这里
            int result = ActivityTaskManager.getService().startActivity(whoThread,
                    who.getBasePackageName(), who.getAttributionTag(), intent,
                    intent.resolveTypeIfNeeded(who.getContentResolver()), token,
                    target != null ? target.mEmbeddedID : null, requestCode, 0, null, options);
            checkStartActivityResult(result, intent);
        } catch (RemoteException e) {
            throw new RuntimeException("Failure from system", e);
        }
        return null;
    }

```

在这里面我们看到启动Activity的真正实现类是由**ActivityTaskManager.getService()**的`starActivity()`来完成。ActivityTaskManager.getService()得到了 IActivityTaskManager （IPC调用），IActivityTaskManager的服务端是ActivityTaskManagerService（ATMS），ActivityTaskManager.getService().startActivity最终调用的是IActivityTaskManager的服务端ATMS的`startActivity()`。

> 根据安卓开发艺术探索，在Android Studio上一步一步深究源码，你会发现你看到的源码和书上所看到的源码有一些细节的地方做出了改动，用来管理Activity的AMS被替换为了ATMS（ActivityTaskManagerService），上网之后发现是因为：Google将Android的版本更新到10.0版本（SDK-29）而用ATMS来替代之前的AMS管理Activity正式这次更新做出的改动，将AMS用于管理Activity及其容器（任务，堆栈，显示等）的系统服务分离出来放到ATMS中。

```java
public static IActivityTaskManager getService() {
        return IActivityTaskManagerSingleton.get();
    }

@UnsupportedAppUsage(trackingBug = 129726065)
private static final Singleton<IActivityTaskManager> IActivityTaskManagerSingleton =
            new Singleton<IActivityTaskManager>() {
                @Override
                protected IActivityTaskManager create() {
                    final IBinder b = ServiceManager.getService(Context.ACTIVITY_TASK_SERVICE);
                    return IActivityTaskManager.Stub.asInterface(b);
                }
            };
```

## ATMS处理启动Activity请求

接着我们分析ATMS的startActivity方法，如下

```java
public final int startActivity(IApplicationThread caller, String callingPackage,
            String callingFeatureId, Intent intent, String resolvedType, IBinder resultTo,
            String resultWho, int requestCode, int startFlags, ProfilerInfo profilerInfo,
            Bundle bOptions) {
        return startActivityAsUser(caller, callingPackage, callingFeatureId, intent, resolvedType,
                resultTo, resultWho, requestCode, startFlags, profilerInfo, bOptions,
                UserHandle.getCallingUserId());
    }



@Override
public int startActivityAsUser(IApplicationThread caller, String callingPackage,
            String callingFeatureId, Intent intent, String resolvedType, IBinder resultTo,
            String resultWho, int requestCode, int startFlags, ProfilerInfo profilerInfo,
            Bundle bOptions, int userId) {
        return startActivityAsUser(caller, callingPackage, callingFeatureId, intent, resolvedType,
                resultTo, resultWho, requestCode, startFlags, profilerInfo, bOptions, userId,
                true /*validateIncomingUser*/);
    }

private int startActivityAsUser(IApplicationThread caller, String callingPackage,
            @Nullable String callingFeatureId, Intent intent, String resolvedType,
            IBinder resultTo, String resultWho, int requestCode, int startFlags,
            ProfilerInfo profilerInfo, Bundle bOptions, int userId, boolean validateIncomingUser) {
        assertPackageMatchesCallingUid(callingPackage);
        enforceNotIsolatedCaller("startActivityAsUser");

        userId = getActivityStartController().checkTargetUser(userId, validateIncomingUser,
                Binder.getCallingPid(), Binder.getCallingUid(), "startActivityAsUser");

        // TODO: Switch to user app stacks here.
        return getActivityStartController().obtainStarter(intent, "startActivityAsUser")
                .setCaller(caller)
                .setCallingPackage(callingPackage)
                .setCallingFeatureId(callingFeatureId)
                .setResolvedType(resolvedType)
                .setResultTo(resultTo)
                .setResultWho(resultWho)
                .setRequestCode(requestCode)
                .setStartFlags(startFlags)
                .setProfilerInfo(profilerInfo)
                .setActivityOptions(bOptions)
                .setUserId(userId)
                .execute();

    }
```

getActivityStartController得到的是ActivityStartController。通过ActivityStartController的 `obtainStarter()`得到的是ActivityStarter（专门负责一个 Activity 的启动操作）。最终会调用ActivityStarter的`execute()`。

```java
int execute() {
		try {
      
    		···
          
        res = resolveToHeavyWeightSwitcherIfNeeded();
        if (res != START_SUCCESS) {
        		return res;
        }
      
        res = executeRequest(mRequest);
      
				···
       
        } finally {
            onExecutionComplete();
        }
    }
```

在` execute() `方法中，会再次调用其内部的 `executeRequest()` 方法。

咱们接着看看 `startActivityUnchecked()` ;

```java
private int executeRequest(Request request) {
        ···

        ActivityRecord sourceRecord = null;
        ActivityRecord resultRecord = null;
        
  			···
        
          mLastStartActivityResult = startActivityUnchecked(r, sourceRecord, voiceSession,
                request.voiceInteractor, startFlags, true /* doResume */, checkedOptions, inTask,
                restrictedBgActivity, intentGrants);

       	···

        return mLastStartActivityResult;
    }
```

又调用其内部的`startActivityUnchecked()`

```
private int startActivityUnchecked(final ActivityRecord r, ActivityRecord sourceRecord,
                IVoiceInteractionSession voiceSession, IVoiceInteractor voiceInteractor,
                int startFlags, boolean doResume, ActivityOptions options, Task inTask,
                boolean restrictedBgActivity, NeededUriGrants intentGrants) {
        ....
        try {
            ...
            result = startActivityInner(r, sourceRecord, voiceSession, voiceInteractor,
                    startFlags, doResume, options, inTask, restrictedBgActivity, intentGrants);
        } finally {
            ...
        }

        postStartActivityProcessing(r, result, startedActivityRootTask);

        return result;
    }
```

接着`startActivityInner()`

```java
int startActivityInner(final ActivityRecord r, ActivityRecord sourceRecord,
            IVoiceInteractionSession voiceSession, IVoiceInteractor voiceInteractor,
            int startFlags, boolean doResume, ActivityOptions options, Task inTask,
            boolean restrictedBgActivity, NeededUriGrants intentGrants) {
        setInitialState(r, options, inTask, doResume, startFlags, sourceRecord, voiceSession,
                voiceInteractor, restrictedBgActivity);

        //计算启动模式
        computeLaunchingTaskFlags();
        computeSourceRootTask();
        //设置启动模式
        mIntent.setFlags(mLaunchFlags);

        ...

        // 关键点来了
        mRootWindowContainer.resumeFocusedTasksTopActivities(
                        mTargetRootTask, mStartActivity, mOptions, mTransientLaunch);
        ...

        return START_SUCCESS;
    }
```

再来看看`resumeFocusedTasksTopActivities()`这个东西究竟干什么了。

```java
 boolean resumeFocusedTasksTopActivities(
            Task targetRootTask, ActivityRecord target, ActivityOptions targetOptions,
            boolean deferPause) {
        ...
        boolean result = false;
        if (targetRootTask != null && (targetRootTask.isTopRootTaskInDisplayArea()
                || getTopDisplayFocusedRootTask() == targetRootTask)) {
            result = targetRootTask.resumeTopActivityUncheckedLocked(target, targetOptions,
                    deferPause);
        }

        ...
        return result;
    }
```

终于看到希望了，targetRootTask是Task对象的实例。

`resumeTopActivityUncheckedLocked()`

```java
 @GuardedBy("mService")
    boolean resumeTopActivityUncheckedLocked(ActivityRecord prev, ActivityOptions options,
            boolean deferPause) {
        ...
        someActivityResumed = resumeTopActivityInnerLocked(prev, options, deferPause);
        ...
        return someActivityResumed;
    }
```

又是调用内部方法`resumeTopActivityInnerLocked()`

```java
@GuardedBy("mService")
    private boolean resumeTopActivityInnerLocked(ActivityRecord prev, ActivityOptions options,
            boolean deferPause) {
        ...
            try {
                ....
            } catch (Exception e) {
                ....
                //重点
                mTaskSupervisor.startSpecificActivity(next, true, false);
                return true;
            }
        ....
        return true;
    }
```

在这里我们终终于拿到了mTaskSupervisor： ActivityTaskSupervisor 对象的实例。

## ActivityStackSupervisor 启动Activity

```java
void startSpecificActivity(ActivityRecord r, boolean andResume, boolean checkConfig) {
        // 此 Activity 的 应用程序(进程) 是否已在运行？
        final WindowProcessController wpc =
                mService.getProcessController(r.processName, r.info.applicationInfo.uid);

        boolean knownToBeDead = false;
        if (wpc != null && wpc.hasThread()) {
            try {
                //进程已启动,开始启动activity
                realStartActivityLocked(r, wpc, andResume, checkConfig);
                return;
            } catch (RemoteException e) {
                
            }
        }

        r.notifyUnknownVisibilityLaunchedForKeyguardTransition();

        final boolean isTop = andResume && r.isTopRunningActivity();
        //进程未启动
        mService.startProcessAsync(r, knownToBeDead, isTop, isTop ? "top-activity" : "activity");
    }
```

在这里我们终于到了进程启动的时候了

看看`realStartActivityLocked()`

```java
boolean realStartActivityLocked(ActivityRecord r, WindowProcessController proc,
        boolean andResume, boolean checkConfig) throws RemoteException {

    ...
            // Create activity launch transaction.
            final ClientTransaction clientTransaction = ClientTransaction.obtain(
                    proc.getThread(), r.appToken);

            final boolean isTransitionForward = r.isTransitionForward();
            clientTransaction.addCallback(LaunchActivityItem.obtain(new Intent(r.intent),
                    System.identityHashCode(r), r.info,
                    mergedConfiguration.getGlobalConfiguration(),
                    mergedConfiguration.getOverrideConfiguration(), r.compat,
                    r.launchedFromPackage, task.voiceInteractor, proc.getReportedProcState(),
                    r.getSavedState(), r.getPersistentSavedState(), results, newIntents,
                    r.takeOptions(), isTransitionForward,
                    proc.createProfilerInfoIfNeeded(), r.assistToken, activityClientController,
                    r.createFixedRotationAdjustmentsIfNeeded(), r.shareableActivityToken,
                    r.getLaunchedFromBubble()));

            // Set desired final state.
            final ActivityLifecycleItem lifecycleItem;
            if (andResume) {
                lifecycleItem = ResumeActivityItem.obtain(isTransitionForward);
            } else {
                lifecycleItem = PauseActivityItem.obtain();
            }
            clientTransaction.setLifecycleStateRequest(lifecycleItem);

            // Schedule transaction.
            mService.getLifecycleManager().scheduleTransaction(clientTransaction);
     
    ...
    return true;
}
```

## ClientLifecycleManager处理ClientTransaction

ClientLifecycleManager.scheduleTransaction()

```java
void scheduleTransaction(ClientTransaction transaction) throws RemoteException {
       final IApplicationThread client = transaction.getClient();
       transaction.schedule();
       if (!(client instanceof Binder)) {
           // If client is not an instance of Binder - it's a remote call and at this point it is
           // safe to recycle the object. All objects used for local calls will be recycled after
           // the transaction is executed on client in ActivityThread.
           transaction.recycle();
       }
   }
```

到此，基本上已经比较清晰了，通过**transaction.getClient()**获取了要启动的应用程序进程的IApplicationThread。IApplicationThread实现者是ApplicationThread，它是ActivityThread的内部类，所以前面也说过ApplicationThread为进程间通信的桥梁。

绕了一大圈，Activity的启动过程最终回到了ApplicationThread中。接下来我们继续看ActivityThread是如何启动启动Activity的。

## ApplicationThread 处理进程间数据通信

到目前为止，我们应该看到ApplicationThread的`scheduleTransaction()`

```java
private class ApplicationThread extends IApplicationThread.Stub {
     @Override
        public void scheduleTransaction(ClientTransaction transaction) throws RemoteException {
          
            ActivityThread.this.scheduleTransaction(transaction);
        
        }
}
```

由以上代码，调用了ActivityThread的scheduleTransaction方法，ActivityThread继承了ClientTransactionHandler，scheduleTransaction在这里面实现

```java
/** Prepare and schedule transaction for execution. */
   void scheduleTransaction(ClientTransaction transaction) {
     
       transaction.preExecute(this);
       sendMessage(ActivityThread.H.EXECUTE_TRANSACTION, transaction);
     
   }
```

## ActivityThread.H 线程间消息处理

可以看到这里发送了一个Handler消息，而ActivityThread.H则是ActivityThread的内部Handler，它是整个应用程序的主线程Handler，这里为什么需要切换线程呢？**其原因为前面ATMS进程间通信则是运行在Binder线程，而Android更新UI则需要在主线程**，接着看到ActivityThread.H的消息处理

```java
 class H extends Handler {
 
  public void handleMessage(Message msg) {
            if (DEBUG_MESSAGES) Slog.v(TAG, ">>> handling: " + codeToString(msg.what));
            switch (msg.what) {
            case EXECUTE_TRANSACTION:
                    final ClientTransaction transaction = (ClientTransaction) msg.obj;
                    mTransactionExecutor.execute(transaction);
                    ......
                    break;
                    
             }       
}
```

## TransactionExecutor

由以上代码，首先获取了由ATMS传递过来的启动Activity进程的数据，之后调用了TransactionExecutor`execute()`方法来处理ClientTransaction的数据。

```java
public void execute(ClientTransaction transaction) {
        if (DEBUG_RESOLVER) Slog.d(TAG, tId(transaction) + "Start resolving transaction");

        .......

        executeCallbacks(transaction); 

        executeLifecycleState(transaction);
        mPendingActions.clear();
        if (DEBUG_RESOLVER) Slog.d(TAG, tId(transaction) + "End resolving transaction");
    }
```

接着调用了TransactionExecutor的`executeCallbacks()`

```java
/** Cycle through all states requested by callbacks and execute them at proper times. */
    @VisibleForTesting
    public void executeCallbacks(ClientTransaction transaction) {
        final List<ClientTransactionItem> callbacks = transaction.getCallbacks();
        if (callbacks == null || callbacks.isEmpty()) {
            // No callbacks to execute, return early.
            return;
        }
        if (DEBUG_RESOLVER) Slog.d(TAG, tId(transaction) + "Resolving callbacks in transaction");

        final IBinder token = transaction.getActivityToken();
        ActivityClientRecord r = mTransactionHandler.getActivityClient(token);

        // In case when post-execution state of the last callback matches the final state requested
        // for the activity in this transaction, we won't do the last transition here and do it when
        // moving to final state instead (because it may contain additional parameters from server).
        final ActivityLifecycleItem finalStateRequest = transaction.getLifecycleStateRequest();
        final int finalState = finalStateRequest != null ? finalStateRequest.getTargetState()
                : UNDEFINED;
        // Index of the last callback that requests some post-execution state.
        final int lastCallbackRequestingState = lastCallbackRequestingState(transaction);

        final int size = callbacks.size();
        for (int i = 0; i < size; ++i) {
          //对象
          	final ClientTransactionItem item = callbacks.get(i);
          
            if (DEBUG_RESOLVER) Slog.d(TAG, tId(transaction) + "Resolving callback: " + item);
            final int postExecutionState = item.getPostExecutionState();
            final int closestPreExecutionState = mHelper.getClosestPreExecutionState(r,
                    item.getPostExecutionState());
            if (closestPreExecutionState != UNDEFINED) {
                cycleToPath(r, closestPreExecutionState, transaction);
            }

          //注意
            item.execute(mTransactionHandler, token, mPendingActions);
            ........
        }
    }
```

## LaunchActivityItem

获取的ClientTransactionItem是LaunchActivityItem对象，它继承了ClientTransactionItem，并保存这需要启动的Activity数据，所以我们接下来要找的是LaunchActivityItem的`execute()`。

```java
@Override
  public void execute(ClientTransactionHandler client, IBinder token,
          PendingTransactionActions pendingActions) {
      Trace.traceBegin(TRACE_TAG_ACTIVITY_MANAGER, "activityStart");
    
      ActivityClientRecord r = new ActivityClientRecord(token, mIntent, mIdent, mInfo,
              mOverrideConfig, mCompatInfo, mReferrer, mVoiceInteractor, mState, mPersistentState,
              mPendingResults, mPendingNewIntents, mIsForward,
              mProfilerInfo, client, mAssistToken);
    
      client.handleLaunchActivity(r, pendingActions, null /* customIntent */);
      Trace.traceEnd(TRACE_TAG_ACTIVITY_MANAGER);
  }
```

恢复了要启动的Activity的数据，ActivityClientRecord是ActivityThread的内部类，这里的client为ClientTransactionHandler，而前面已经说过ActivityThread继承ClientTransactionHandler，所以这里的client调用的就是ActivityThread的`handleLaunchActivity()`

```java
/**
    * Extended implementation of activity launch. Used when server requests a launch or relaunch.
    */
   @Override
   public Activity handleLaunchActivity(ActivityClientRecord r,
           PendingTransactionActions pendingActions, Intent customIntent) {
       .......

       final Activity a = performLaunchActivity(r, customIntent);

       .......

       return a;
   }
```

继续调用了ActivityThread的performLaunchActivity方法来启动Activity，返回的也是Activity实例。所以`performLaunchActivity()`才是启动Activity实例的核心代码。

## Core implementation of activity launch!

```java
private Activity performLaunchActivity(ActivityClientRecord r, Intent customIntent) {
        ActivityInfo aInfo = r.activityInfo;
        if (r.packageInfo == null) {
            r.packageInfo = getPackageInfo(aInfo.applicationInfo, r.compatInfo,
                    Context.CONTEXT_INCLUDE_CODE);
        }

        ComponentName component = r.intent.getComponent();
        if (component == null) {
            component = r.intent.resolveActivity(
                mInitialApplication.getPackageManager());
            r.intent.setComponent(component);
        }

        if (r.activityInfo.targetActivity != null) {
            component = new ComponentName(r.activityInfo.packageName,
                    r.activityInfo.targetActivity);
        }

        ContextImpl appContext = createBaseContextForActivity(r);
        Activity activity = null;
        try {
            java.lang.ClassLoader cl = appContext.getClassLoader();
            activity = mInstrumentation.newActivity(
                    cl, component.getClassName(), r.intent);
            StrictMode.incrementExpectedActivityCount(activity.getClass());
            r.intent.setExtrasClassLoader(cl);
            r.intent.prepareToEnterProcess();
            if (r.state != null) {
                r.state.setClassLoader(cl);
            }
        } catch (Exception e) {
            if (!mInstrumentation.onException(activity, e)) {
                throw new RuntimeException(
                    "Unable to instantiate activity " + component
                    + ": " + e.toString(), e);
            }
        }

        try {
          //在这里创建app啦！！
            Application app = r.packageInfo.makeApplication(false, mInstrumentation);

            if (localLOGV) Slog.v(TAG, "Performing launch of " + r);
            if (localLOGV) Slog.v(
                    TAG, r + ": app=" + app
                    + ", appName=" + app.getPackageName()
                    + ", pkg=" + r.packageInfo.getPackageName()
                    + ", comp=" + r.intent.getComponent().toShortString()
                    + ", dir=" + r.packageInfo.getAppDir());

            if (activity != null) {
                CharSequence title = r.activityInfo.loadLabel(appContext.getPackageManager());
                Configuration config = new Configuration(mCompatConfiguration);
                if (r.overrideConfig != null) {
                    config.updateFrom(r.overrideConfig);
                }
                if (DEBUG_CONFIGURATION) Slog.v(TAG, "Launching activity "
                        + r.activityInfo.name + " with config " + config);
                Window window = null;
                if (r.mPendingRemoveWindow != null && r.mPreserveWindow) {
                    window = r.mPendingRemoveWindow;
                    r.mPendingRemoveWindow = null;
                    r.mPendingRemoveWindowManager = null;
                }

                // Activity resources must be initialized with the same loaders as the
                // application context.
                appContext.getResources().addLoaders(
                        app.getResources().getLoaders().toArray(new ResourcesLoader[0]));

                appContext.setOuterContext(activity);
              
              // 通过Activity的 attach 方法将 context等各种数据与Activity绑定，初始化Activity
                activity.attach(appContext, this, getInstrumentation(), r.token,
                        r.ident, app, r.intent, r.activityInfo, title, r.parent,
                        r.embeddedID, r.lastNonConfigurationInstances, config,
                        r.referrer, r.voiceInteractor, window, r.configCallback,
                        r.assistToken);

                if (customIntent != null) {
                    activity.mIntent = customIntent;
                }
                r.lastNonConfigurationInstances = null;
                checkAndBlockForNetworkAccess();
                activity.mStartedActivity = false;
                int theme = r.activityInfo.getThemeResource();
                if (theme != 0) {
                    activity.setTheme(theme);
                }

                activity.mCalled = false;
                if (r.isPersistable()) {
                 
                  //看这里看这里！！！！
                    mInstrumentation.callActivityOnCreate(activity, r.state, r.persistentState);
                } else {
                    mInstrumentation.callActivityOnCreate(activity, r.state);
                }
                if (!activity.mCalled) {
                    throw new SuperNotCalledException(
                        "Activity " + r.intent.getComponent().toShortString() +
                        " did not call through to super.onCreate()");
                }
                r.activity = activity;
                mLastReportedWindowingMode.put(activity.getActivityToken(),
                        config.windowConfiguration.getWindowingMode());
            }
            r.setState(ON_CREATE);

            // updatePendingActivityConfiguration() reads from mActivities to update
            // ActivityClientRecord which runs in a different thread. Protect modifications to
            // mActivities to avoid race.
            synchronized (mResourcesManager) {
                mActivities.put(r.token, r);
            }

        } catch (SuperNotCalledException e) {
            throw e;

        } catch (Exception e) {
            if (!mInstrumentation.onException(activity, e)) {
                throw new RuntimeException(
                    "Unable to start activity " + component
                    + ": " + e.toString(), e);
            }
        }

        return activity;
    }
```

最后需要我们注意的地方调用了Instrumentation对象的callActivityOnCreate方法。接着往下看

## onCreate()被调用

```java
public void callActivityOnCreate(Activity activity, Bundle icicle,PersistableBundle persistentState) {
        prePerformCreate(activity); 
        activity.performCreate(icicle, persistentState);
        postPerformCreate(activity);
    }
```

又调用了Activity的preformCreate方法，继续往下看

```java
final void performCreate(Bundle icicle, PersistableBundle persistentState) {
        dispatchActivityPreCreated(icicle);
        mCanEnterPictureInPicture = true;
        restoreHasCurrentPermissionRequest(icicle);
        if (persistentState != null) {
            onCreate(icicle, persistentState);
        } else {
            onCreate(icicle);
        }
        .......
    }
```

最终，在已经实例初始化好的Activity调用它的performCreate方法中又调用了onCreate方法。至此，也就是整个应用程序的Activity启动过程我们已经走完了。（喜极而泣！！！）

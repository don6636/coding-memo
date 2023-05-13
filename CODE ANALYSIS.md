[TOC]

# KEEP ANDROID



## SYSTEM BASIS

### Init

init进程(pid=1)是Linux系统中用户空间的第一个进程。[^1]

1. kernel启动后，在用户空间启动init进程，并调用init中的 `main()` 方法。
2. /system/core/init/init.cpp#main
3. *Device File Explorer:* /init.rc -> /init.${ro.zygote}.rc
4. /frameworks/base/cmds/app_process/app_main.cpp#main
5. /frameworks/base/core/jni/AndroidRuntime.cpp#start
6. **`ZygoteInit.main()`**





### Zygote -- the 1st Java process in Android System

[Zygote](https://juejin.im/post/6844903955177144333) 创建的第一个进程就是 `System Server`，`System Server` 负责管理和启动整个 Java Framework 层。创建完 `System Server` 之后，`Zygote` 就会完全进入受精卵的角色，等待进行无性繁殖，创建应用进程（所有的应用进程都是由 `Zygote` 进程 fork 而来的）。



1. 解析init.zygote.rc中的参数，创建AppRuntime并调用AppRuntime.start()方法；
2. 调用AndroidRuntime的startVM()方法创建虚拟机，再调用startReg()注册JNI函数；
3. 通过JNI方式调用ZygoteInit.main()，第一次进入Java世界；



`Zygote` 的启动过程是从Native层开始的，这里不会Native层作过多分析，直接进入其在 Java 世界的入口 **`ZygoteInit.main()`**：

1. *ZygoteServer#registerServerSocketFromEnv*，注册服务端 socket，用于跨进程通信，[这里为何不使用Binder通信？][为什么Zygote与SystemServer进程之间的通讯采用Socket而不是Binder？]

   （Binder的C/S架构采用多线程通信，而在多线程环境中fork进程是不安全的，可能会发生死锁）

2. *ZygoteInit#preload*，预加载，类，资源，so共享库等；

3. *ZygoteInit#gcAndFinalize*，在 forkSystemServer 之前主动 GC 一次；

4. *ZygoteInit#forkSystemServer*，创建 SystemServer 进程；

   *-> handleSystemServerProcess*

   *-> zygoteInit*

   *-> nativeZygoteInit*，启动Binder线程池

5. *ZygoteServer#runSelectLoop*，在父进程*(Zygote)*中循环等待处理客户端发来的 socket 请求（请求 socket 连接和请求 fork 应用进程）。



[Android 世界中，谁喊醒了 Zygote？](https://juejin.cn/post/6844903967340625933)

`Zygote` 通过调用其持有的 `ZygoteServer` 对象的 `runSelectLoop()` 方法开始等待客户端的呼唤，有求必应。客户端的请求无非是创建应用进程，以 `startActivity()` 为例，假如开启的是一个尚未创建进程的应用，那么就会向 Zygote 请求创建进程。下面将从 **客户端发送请求** 和 **服务端处理请求** 两方面来进行解析。

- 客户端发送请求

1. *ActivityManagerService#startProcessLocked* *( final String entryPoint = "android.app.ActivityThread"; )*，应用进程entry point；
2. *ActivityManagerService#startProcess*
3. *Process#start*，创建应用进程；
4. *ZygoteProcess#start*
5. *ZygoteProcess#startViaZygote*
6. *ZygoteProcess#openZygoteSocketIfNeeded*
7. *ZygoteProcess#zygoteSendArgsAndGetResult*

- `ZygoteProcess` 负责和 `Zygote` 进程建立 socket 连接，并将创建进程需要的参数发送给 Zygote 的 socket 服务端。



- Zygote 处理客户端请求

1. *ZygoteServer#runSelectLoop*

   *Os.poll(pollFds, -1);*，处理轮询状态，当pollFds有事件到来则往下执行，否则阻塞在这里

2. *ZygoteServer#acceptCommandPeer*，新创建一个 ZygoteConnection；

3. *ZygoteConnection#processOneCommand*

   1. *ZygoteConnection#readArgumentList*，读取 socket 客户端发送过来的参数列表；

   2. *Zygote#forkAndSpecialize*，fork 子进程；

      *VM_HOOKS.preFork();*，停止 Zygote进程 的4个Daemon子线程的运行，初始化gc堆；

      *Zygote#nativeForkAndSpecialize*，`nativeForkAndSpecialize()` 是一个native方法，在底层 `fork()` 创建新进程，设置新进程的主线程id，重置gc性能数据，设置信号处理函数等，并返回其 pid。不要忘记了这里的 **一次fork，两次返回** 。`pid > 0`  说明还是父进程。`pid = 0` 说明进入了子进程。子进程中会调用 `handleChildProc`，而父进程中会调用 `handleParentProc()`。

      *VM_HOOKS.postForkCommon();*，启动4个Deamon子线程。

   3. *ZygoteConnection#handleChildProc*，处于子进程时，处理子进程事务；
   
      *ZygoteInit#zygoteInit*，新建应用进程时 isZygote 参数为 false。当看到 `ZygoteInit.zygoteInit()` 时你应该感觉很熟悉了，接下来的流程就是：`ZygoteInit.zygoteInit()` -> `RuntimeInit.applicationInit()` -> `findStaticMain()`，和 `SystemServer` 进程的创建流程一致。这里要找的 main 方法就是 `ActivityThrad.main()` 。`ActivityThread` 虽然并不是一个线程，但你可以把它理解为应用的主线程。
   
   4. *ZygoteConnection#handleParentProc*，处于 Zygote 进程时，处理父进程事务；
   
   ***ps:*** **一次fork，两次返回？**
   
   `fork()` 采用 *copy-on-write* 技术，这是Linux创建进程的标准方法，调用一次，返回两次，返回值有3种类型：
   
   - 父进程中，返回新创建的子进程的 ***pid***；
   - 子进程中，返回 ***0***；
   - 当出现错误时，创建失败，返回 ***负数***（当进程数超过上限或者系统内存不足时会出错）。
   
   `fork()` 的主要工作是寻找空闲的进程号pid，然后从父进程拷贝进程信息，例如数据段和代码段，fork后子进程要执行的代码等。子进程和父进程是共享代码段的，父子进程都会继续执行下去。”一次 fork，两次返回”只是描述了结论，其实是不太严谨的，可以理解为两个不同进程分别调用了 `fork()` 。

- `Zygote` 服务端接收到参数之后调用 `ZygoteConnection.processOneCommand()` 处理参数，并 fork 进程。
- 最后通过 `findStaticMain()` 找到 `ActivityThread` 类的 `main()` 方法并执行，子进程就启动了。





### System Server -- the 1st child process forked from Zygote

Zygote 进程 fork 的第一个进程 `SystemServer`，它承载了各类系统服务的创建和启动。

[SystemServer](https://juejin.im/post/6844903965738401799) 启动流程：

*SystemServer#main*

*-> SystemServer#run*

1. 时区/间、语言地区等设置；
2. 虚拟机相关设置；
3. 指纹信息，Binder 调用设置；
4. *Looper.prepareMainLooper();*，创建主线程 Looper；
5. *System.loadLibrary("android_servers");*，初始化 native 服务，加载 `libandroid_servers.so`；
6. *SystemServer#createSystemContext*，初始化系统上下文；
7. *LocalServices.addService(SystemServiceManager.class, mSystemServiceManager);*，创建并注册 SystemServiceManager；
8. *SystemServer#startBootstrapServices*，启动系统引导服务；
9. *SystemServer#startCoreServices*，启动系统核心服务；
10. *SystemServer#startOtherServices*，启动其他服务；
    - *ActivityManagerService#systemReady*
    - *ActivityManagerService#startHomeActivityLocked*，启动桌面Activity。
11. *Looper.loop();*，开启消息循环。





#### ActivityManagerService

- 初始化

  SystemServer#main

  -> run

  -> startBootstrapServices

  `mActivityManagerService = mSystemServiceManager.startService(ActivityManagerService.Lifecycle.class).getService();`



- ***Launcher***

  *ActivityManagerService#systemReady*

  *ActivityManagerService#startHomeActivityLocked*

  *ActivityStartController#startHomeActivity*

  *ActivityStarter#execute*

  *-> startActivity*

  *-> startActivityUnchecked*

  *ActivityStackSupervisor#resumeFocusedStackTopActivityLocked*

  *ActivityStack#resumeTopActivityUncheckedLocked*

  *-> resumeTopActivityInnerLocked*

  *-> startPausingLocked*，先暂停 **mResumedActivity**

  *ActivityStackSupervisor#startSpecificActivityLocked*

  *-> Is this activity's application already running?*

  **Y:** *-> ActivityStackSupervisor#realStartActivityLocked*，启动Activity

  1. *ActivityStackSupervisor#allPausedActivitiesComplete*，所有activity都暂停完成后才能启动新activity；

  2. *ClientTransaction#obtain:1523*，Create activity launch transaction；

  3. *clientTransaction.addCallback(LaunchActivityItem, ...);*

  4. *ClientTransaction#setLifecycleStateRequest:1542*，Set desired final state *(ResumeActivityItem here)*；

  5. *ClientLifecycleManager#scheduleTransaction:1545*，Schedule transaction；

  6. *ClientTransaction#schedule*

  7. *ClientTransactionHandler#scheduleTransaction*，Prepare and schedule transaction for execution；

  8. *ActivityThread#sendMessage(**ActivityThread.H.EXECUTE_TRANSACTION**, transaction)*，向ActivityThread内部类H发送handler消息；

  9. *TransactionExecutor#execute*，Resolve transaction；

     9.1 *-> executeCallbacks*，Cycle through all states requested by callbacks and execute them at proper times.

     *LaunchActivityItem#execute*

     *ActivityThread#handleLaunchActivity*，Extended implementation of activity launch

     *ActivityThread#performLaunchActivity*

     *-> createBaseContextForActivity:2827*，创建context

     *-> Instrumentation#newActivity:2831*，反射创建activity实例

     *-> LoadedApk#makeApplication:2848*，反射创建application实例（或mApplication缓存实例，创建应用进程时已经实例化了）

     *-> Activity#attach:2873*，activity与context、application、window等绑定

     *-> Instrumentation#callActivityOnCreate*

     *-> Activity#performCreate*，回调 *onCreate()*

     9.2 *-> executeLifecycleState*，Transition to the final state if requested by the transaction.

     *TransactionExecutor#cycleToPath*，Cycle to the state right before the final requested state.

     *-> ActivityThread#handleStartActivity*，回调 *onStart()*

     *ResumeActivityItem#execute*

     *-> ActivityThread#handleResumeActivity*，回调 *onResume()*
  
     *-> r.activity.makeVisible();:3918*，使页面 *(DecorView)* 可见
  
     *-> Looper.myQueue().addIdleHandler(new Idler());:3925*，主线程空闲时会执行 *Idler*？？？
  
  **N:** *-> ActivityManagerService#startProcessLocked*，创建应用进程



- ***[startActivity][庖丁解牛 Activity 启动流程 | 秉心说TM]***

  *Activity#startActivity*

  *-> startActivityForResult*

  *Instrumentation#execStartActivity*

  *ActivityManagerService#startActivity*

  *-> startActivityAsUser*

  *ActivityStarter#execute*

  *-> startActivityMayWait*

  *-> startActivity*

  同 *Launcher* 后续步骤



### ServiceManager

- 注册服务

  ServiceManager.addService

  -> ServiceManager#getIServiceManager

  -> ServiceManagerNative#asInterface

  -> new ServiceManagerProxy(new BinderProxy())

  ServiceManagerProxy#addService

  BinderProxy#transact(ADD_SERVICE_TRANSACTION)

  BinderProxy#transactNative

- 获取服务

  ServiceManager.getService

  -> ServiceManager#getIServiceManager

  -> ...

  ServiceManagerProxy#getService

  BinderProxy#transact(GET_SERVICE_TRANSACTION)

  BinderProxy#transactNative





#### Binder

[为什么 Android 要采用 Binder 作为 IPC 机制？](https://www.zhihu.com/question/39440766/answer/89210950)

[Android源码的Binder权限是如何控制？](https://www.zhihu.com/question/41003297/answer/89328987?from=profile_answer_card)

[如何使用AIDL？](http://gityuan.com/2015/11/23/binder-aidl)







Android系统服务的注册方式：

------

1. `ServiceManager.addService();`
   - 功能：直接向ServiceManager注册该服务；
   - 特点：服务往往直接或间接继承于Binder服务；
   - 举例：input, window, package。
2. `SystemServiceManager.startService();`
   - 功能：
     - 创建服务对象；
     - 执行该服务的 `onStart()` 方法；该方法会执行方式 **1.** 的 `SM.addService()`；
     - 根据启动到不同的阶段会回调 `onBootPhase()` 方法；
     - 另外，还有多用户模式下用户状态的改变也会有回调方法；例如 `onStartUser()`。
   - 特点：服务往往自身或内部类继承于SystemService；
   - 举例：power, activity。



### ...





## CRUCIAL CODE

### 生命周期

[从源码看 Activity 生命周期](https://juejin.cn/post/6844904040266989576)

[从源码看 Activity 生命周期（上篇）| 秉心说](https://luyao.tech/archives/activitylifecycle1)



### 绘制流程

ActivityThread#handleResumeActivity

1. ActivityThread#performResumeActivity

   Activity#performResume

   1. [Activity#performRestart]

   2. Instrumentation#callActivityOnResume

      Activity#onResume

2. WindowManagerImpl#addView（`wm.addView(decor, l);`）

3. WindowManagerGlobal#addView

   1. ViewRootImpl初始化

      `root = new ViewRootImpl(view.getContext(), display);`

      1) IWindowSession代理对象，与WMS进行Binder通信 `mWindowSession = WindowManagerGlobal.getWindowSession();`

      2) 初始化AttachInfo`mAttachInfo = new View.AttachInfo(..);`

      3) 初始化 **Choreographer**，存储在Threadlocal中 `mChoreographer = Choreographer.getInstance();`

   2. **真正的绘制流程起点**:star:

      `root.setView(view, wparams, panelParentView);`

      1) 加入WindowManager之前发起**首次**绘制 `requestLayout();` // 853

      2) Binder调用 `Session.addToDisplay()`，将Window添加到屏幕 `res = mWindowSession.addToDisplay(..);`

      3) 指派ViewRootImpl作为DecorView的parent `view.assignParent(this);`



### ViewRootImpl

View#invalidate

View#requestLayout

1. ViewRootImpl#scheduleTraversals

   1) 建立同步屏障，优先处理异步消息 `mTraversalBarrier = mHandler.getLooper().getQueue().postSyncBarrier();`

   2) Choreographer#postCallback

   1. Choreographer#scheduleFrameLocked

      注册Vsync信号监听 Choreographer#scheduleVsyncLocked

   2. 回调 Choreographer.FrameDisplayEventReceiver#onVsync

   3. Choreographer#doFrame（MSG_DO_FRAME）

   4. Choreographer#doCallbacks

2. ViewRootImpl.TraversalRunnable#run

   ViewRootImpl#doTraversal

   移除同步屏障 `mHandler.getLooper().getQueue().removeSyncBarrier(mTraversalBarrier);`

3. 三大流程 ViewRootImpl#performTraversals

   1. 绑定Window

      `host.dispatchAttachedToWindow(mAttachInfo, 0);`

      `mAttachInfo.mTreeObserver.dispatchOnWindowAttachedChange(true);`

      `getRunQueue().executeActions(mAttachInfo.mHandler);`

   2. 请求WMS计算窗口大小 `relayoutResult = relayoutWindow(params, viewVisibility, insetsPending);`

   3. ViewRootImpl#performMeasure:2275

   4. ViewRootImpl#performLayout:2320

   5. ViewRootImpl#performDraw:2485

      -> ViewRootImpl#draw:3116

        -> ViewRootImpl#drawSoftware:3340

      ​    -> mView.draw(canvas);



### View#post

```java
public boolean post(Runnable action) {
    final AttachInfo attachInfo = mAttachInfo;
    if (attachInfo != null) {
        return attachInfo.mHandler.post(action);
    }
    // Postpone the runnable until we know on which thread it needs to run.
    // Assume that the runnable will be successfully placed after attach.
    getRunQueue().post(action);
    return true;
}
```

在 `onResume` 及之前的声明周期中，attachInfo是空的（AttachInfo在ViewRootImpl构造函数中初始化，而ViewRootImpl的初始化又在 `onResume` 之后）

[探秘View#post](https://juejin.cn/post/6895735092438630407#heading-3)



### setContentView

1. AppCompatActivity#setContentView

2. AppCompatDelegateImplV9#setContentView(int)

3. LayoutInflater#inflate(int, ViewGroup, boolean)

   -> `final Resources res = getContext().getResources();`

   -> `final XmlResourceParser parser = res.getLayout(resource);` *(IO)*

4. LayoutInflater#inflate(XmlPullParser, ViewGroup, boolean)

   -> LayoutInflater#createViewFromTag

   -> LayoutInflater#createView *(Reflection)*

   -> LayoutInflater#rInflateChildren // Inflate all children under temp against its context.

     -> `viewGroup.addView(view, params);`

5. `root.addView(temp, params);`

***ps:*** 在 installDecor -> generateLayout 应用当前theme属性。





### [Activity#finish][为什么 Activity.finish() 之后10s才onDestroy？]

![Activity#finish流程(API28)](./assets/Activity#finish(28).png)

>因此，Activity 的 onStop/onDestroy 是依赖 IdleHandler 来回调的，正常情况下，主线程空闲时会先回调 IdleHandler。但是由于某些特殊场景下的问题，导致主线程迟迟无法空闲，onStop/onDestroy 也就迟迟得不到调用。但这并不意味着 Activity 永远得不到回收，系统提供了一个兜底机制，当新 Activity 的 onResume 回调超出 10s 之后，若仍未回调原 Activity 的 onStop/onDestroy，系统便会主动触发。

排查问题：

打印消息队列处理的每条消息：`Looper.getMainLooper().setMessageLogging(new LogPrinter(Log.VERBOSE, TAG));`

格式如下：

`logging.println(">>>>> Dispatching to " + msg.target + " " + msg.callback + ": " + msg.what);`

`logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);`





### RecyclerView

1. LinearLayoutManager#onLayoutChildren
2. LinearLayoutManager#fill
3. LinearLayoutManager#layoutChunk
4. LinearLayoutManager.LayoutState#next





### ...





## [OPEN SOURCE](./OPEN SOURCE.md)

### OkHttp





### Retrofit





### ...





## QUICK NOTES





# APPENDIX

## Indexs





## Links

[Android Init Language](https://android.googlesource.com/platform/system/core/+/master/init/README.md)

[Android 操作系统架构开篇 | Gityuan](http://gityuan.com/android/)

[为什么Zygote与SystemServer进程之间的通讯采用Socket而不是Binder？]:https://blog.csdn.net/qq_39037047/article/details/88066589
[庖丁解牛 Activity 启动流程 | 秉心说TM]:https://juejin.im/post/6844904012974669837
[为什么 Activity.finish() 之后10s才onDestroy？]:https://juejin.cn/post/6898588053451833351

[^1]:Footnote Example
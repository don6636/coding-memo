[TOC]

# KEEP ANDROID

[NDK][ndk-guides]

CMake（or ndk-build）

LLDB

[应用实践][ndk-applies]

纯native应用实现方式 - `android.app.NativeActivity` 和 `native_activity.h` / `android_native_app_glue.h`

JavaVM

JNIEnv

[Android SDK 版本属性对NDK构建的影响][ndk-builds-version]





## CMake



[CMake命令](https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html)

[配置CMake](https://developer.android.google.cn/studio/projects/configure-cmake)

[关联Gradle](https://developer.android.google.cn/studio/projects/gradle-external-native-builds)





## ndk-build





## Linux Kernel

[The Linux Kernel Archives][linux-kernel-archives]

[Linux Kernel Source Code Online][linux-kernel-source-code]

[Linux内核源码目录结构][linux-kernel-structure]

[细说Linux内核目录结构][linux-kernel-structure-details]

[POSIX][posix]



## POSIX Signal

- e.g. [Linux][linux-kernel-signal-definition]/[ARM][linux-kernel-signal-definition-arm]

> In POSIX a signal is sent either to a specific thread (Linux task) or to the process as a whole (Linux thread group).  How the signal is sent determines whether it's to one thread or the whole group, which determines which signal mask(s) are involved in blocking it from being delivered until later.  When the signal is delivered, either it's caught or ignored by a user handler or it has a default effect that applies to the whole thread group (POSIX process).
>
> The possible effects an unblocked signal set to SIG_DFL can have are:
> ignore - Nothing Happens
> terminate - kill the process, i.e. all threads in the group, similar to exit_group.  The group leader (only) reports WIFSIGNALED status to its parent.
> coredump - write a core dump file describing all threads using the same mm and then kill all those threads.
> stop - stop all the threads in the group, i.e. TASK_STOPPED state
>
> SIGKILL and SIGSTOP cannot be caught, blocked, or ignored. Other signals when not blocked and set to SIG_DFL behaves as follows. The job control signals also have other special effects.

| POSIX signal     | code | default action | defination                                         | reason                                  |
| ---------------- | ---- | -------------- | -------------------------------------------------- | --------------------------------------- |
| SIGHUP           | 1    | terminate      | Hangup                                             | 终端的挂断或进程死亡                    |
| SIGINT           | 2    | terminate      | Interrupt                                          | 终端中断符                              |
| SIGQUIT          | 3    | coredump       | Quit                                               | 终端退出符                              |
| SIGILL           | 4    | coredump       | Illegal instruction                                | 非法硬件指令                            |
| SIGTRAP          | 5    | coredump       | Trace Trap                                         | 跟踪/断点自陷                           |
| SIGABRT/SIGIOT   | 6    | coredump       | Aborted/IOT trap                                   | 异常终止(abort)/IOT自陷                 |
| SIGBUS           | 7    | coredump       | Bus error                                          | 总线错误(内存访问错误)                  |
| SIGFPE           | 8    | coredump       | Floating-point exception                           | 浮点算术异常                            |
| SIGKILL          | 9    | terminate(+)   | Killed(unblockable)                                | 终止                                    |
| SIGUSR1          | 10   | terminate      | User signal 1                                      | 用户自定义信号1                         |
| SIGSEGV          | 11   | coredump       | Segmentation fault                                 | 段非法错误(无效内存引用)                |
| SIGUSR2          | 12   | terminate      | User signal 2                                      | 用户自定义信号2                         |
| SIGPIPE          | 13   | terminate      | Broken pipe                                        | 向无读进程管道(损坏)写数据              |
| SIGALRM          | 14   | terminate      | Alarm clock                                        | 超时(alarm)                             |
| SIGTERM          | 15   | terminate      | Terminated                                         | 终止                                    |
| SIGSTKFLT        | 16   | terminate      | Stack fault                                        | 协处理器栈错误                          |
| SIGCHLD/SIGCLD   | 17   | ignore         | Child exited                                       | 子进程状态改变(停止或终止)              |
| SIGCONT          | 18   | ignore(*)      | Continue                                           | 使暂停进程继续                          |
| SIGSTOP          | 19   | stop(*)(+)     | Stopped (unblockable signal)                       | 停止                                    |
| SIGTSTP          | 20   | stop(*)        | Stopped(Keyboard)                                  | 终端停止符                              |
| SIGTTIN          | 21   | stop(*)        | Stopped (background read from tty input)           | 后台读控制tty                           |
| SIGTTOU          | 22   | stop(*)        | Stopped (background write to tty output)           | 后台写控制tty                           |
| SIGURG           | 23   | ignore         | Urgent I/O condition on socket                     | 紧急情况(socket)                        |
| SIGXCPU          | 24   | coredump       | CPU time limit exceeded                            | 超过CPU时间限制                         |
| SIGXFSZ          | 25   | coredump       | File size limit exceeded                           | 超过文件长度限制                        |
| SIGVTALRM        | 26   | terminate      | Virtual timer expired                              | 虚拟时钟过期                            |
| SIGPROF          | 27   | terminate      | Profiling timer expired                            | 分析时钟过期                            |
| SIGWINCH         | 28   | ignore         | Window size changed                                | 终端窗口大小改变                        |
| SIGPOLL/SIGIO    | 29   | terminate      | Pollable event occurred(System V)/I/O now possible | 可轮询事件(poll)/描述符I/O操作可用      |
| SIGPWR           | 30   | terminate      | Power failure                                      | 电源失效/重启动                         |
| SIGSYS/SIGUNUSED | 31   | coredump       | Bad system call                                    | 无效系统调用/未使用信号(will be SIGSYS) |
| SIG32            | 32   |                | Signal 32                                          |                                         |

***ps:***

**(+)** For *SIGKILL* and *SIGSTOP* the action is "always", not just "default".

**(*)** Special job control effects:

When *SIGCONT* is sent, it resumes the process (all threads in the group) from *TASK_STOPPED* state and also clears any pending/queued stop signals (any of those marked with "**stop(*)**").  This happens regardless of blocking, catching, or ignoring *SIGCONT*.  When any stop signal is sent, it clears any pending/queued *SIGCONT* signals; this happens regardless of blocking, catching, or ignored the stop signal, though (except for *SIGSTOP*) the default action of stopping the process may happen later or never.



## Links

[ndk-guides]: https://developer.android.google.cn/ndk/guides "NDK开发指南"
[ndk-applies]: https://developer.android.google.cn/studio/projects/add-native-code "NDK应用实践"
[ndk-builds-version]: https://developer.android.google.cn/ndk/guides/sdk-versions "NDK构建版本影响"
[jni-specs-toc]: https://docs.oracle.com/javase/8/docs/technotes/guides/jni/spec/jniTOC.html "Java原生接口规范"
[linux-kernel-archives]: https://www.kernel.org/
[linux-kernel-source-code]: https://elixir.bootlin.com/linux/latest/source
[linux-kernel-structure]: https://www.bufeishi.cn/35947.html "Linux内核源码目录结构"
[linux-kernel-structure-details]: https://zhuanlan.zhihu.com/p/581327083 "细说Linux内核目录结构"
[posix]: https://zhuanlan.zhihu.com/p/392588996
[linux-kernel-signal-definition]: https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/include/linux/signal.h?h=v6.1.8 "Linux内核信号定义"
[linux-kernel-signal-definition-arm]: https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/tree/arch/arm/include/uapi/asm/signal.h?h=v6.1.8 "Linux内核信号定义[ARM平台]"



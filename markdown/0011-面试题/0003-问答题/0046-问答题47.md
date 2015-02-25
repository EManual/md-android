如何退出Activity？如何安全退出已调用多个Activity的Application？
答：对于单一Activity的应用来说，退出很简单，直接finish()即可。当然，也可以用killProcess()和System.exit()这样的方法。
对于多个activity，1、记录打开的Activity：每打开一个Activity，就记录下来。在需要退出时，关闭每一个Activity即可。2、发送特定广播：在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。3、递归退出：在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。为了编程方便，最好定义一个Activity基类，处理这些共通问题。
在2.1之前，可以使用ActivityManager的restartPackage方法。
它可以直接结束整个应用。在使用时需要权限android.permission.RESTART_PACKAGES。
注意不要被它的名字迷惑。
可是，在2.2，这个方法失效了。在2.2添加了一个新的方法，killBackground Processes()，需要权限android.permission.KILL_BACKGROUND_PROCESSES。可惜的是，它和2.2的restartPackage一样，根本起不到应有的效果。
另外还有一个方法，就是系统自带的应用程序管理里，强制结束程序的方法，forceStopPackage()。它需要权限android.permission.FORCE_STOP_PACKAGES。并且需要添加android:sharedUserId="android.uid.system"属性。同样可惜的是，该方法是非公开的，他只能运行在系统进程，第三方程序无法调用。
因为需要在Android.mk中添加LOCAL_CERTIFICATE := platform。
而Android.mk是用于在Android源码下编译程序用的。
从以上可以看出，在2.2，没有办法直接结束一个应用，而只能用自己的办法间接办到。
现提供几个方法，供参考：
#### 1、抛异常强制退出：
该方法通过抛异常，使程序Force Close。
验证可以，但是，需要解决的问题是，如何使程序结束掉，而不弹出Force Close的窗口。
#### 2、记录打开的Activity：
每打开一个Activity，就记录下来。在需要退出时，关闭每一个Activity即可。
#### 3、发送特定广播：
在需要结束应用时，发送一个特定的广播，每个Activity收到广播后，关闭即可。
#### 4、递归退出
在打开新的Activity时使用startActivityForResult，然后自己加标志，在onActivityResult中处理，递归关闭。
除了第一个，都是想办法把每一个Activity都结束掉，间接达到目的。但是这样做同样不完美。你会发现，如果自己的应用程序对每一个Activity都设置了nosensor，在两个Activity结束的间隙，sensor可能有效了。但至少，我们的目的达到了，而且没有影响用户使用。为了编程方便，最好定义一个Activity基类，处理这些共通问题。


#### Activity生命周期 
理解Activity的生命周期对应用程序开发来说是至关重要的，这样才能确保您的应用提供了一个很好的用户体验和妥善管理其资源。由于OPhone应用程序不控制自己的进程寿命，由OPhone Runtime管理每个应用程序进程，但是每个Activity的状态反过来会影响到OPhone Runtime是否将终止当前Activity和还是让它继续运行。
#### Actvity 堆栈
每个Actvity的状态由它所在Activity栈中的位置所决定，所有当前正在运行的Actvity将遵循照后进先出的原则。当一个新的 Activity启动，当前的Activity将移至堆栈的顶部，如果用户使用Back按钮，或在前台Activity被关闭，下一个Activity将被激活并且移至到堆栈的顶部。这个过程如下图所示

![img](http://emanual.github.io/md-android/img/component_activity/04_activity.jpg)  

#### Activity状态
随着Activity的创建和销毁，也就会进出栈如上图所示，其中可能会经历以下四种状态：
1、Active状态：这时候Activity处于栈顶，且是可见的，有焦点的，能够接收用户输入前景Activity。OPhone Runtime将试图不惜一切代价保持它活着，甚至杀死其他Activity以确保它有它所需的资源。当另一个Activity变成Active时，当前的将变成Paused状态。
2、Paused状态：在某些情况下，你的Activity是可见的，但没有焦点，在这时候，Actvity处于Paused状态。例如，如果有一个透明或非全屏幕上的Activity在你的Actvity上面，你的 Activity将。当处于Paused状态时，该Actvity仍被认为是Active的，但是它不接受用户输入事件。在极端情况下，OPhone Runtime将杀死Paused Activity，以进一步回收资源。当一个Actvity完全被遮住时，它将进入Stopped状态。
3、Stopped 状态：当Activity是不可见的时，Activity处于Stopped状态。Activity将继续保留在内存中保持当前的所有状态和成员信息，假设系统别的地方需要内存的话，这时它是被回收对象的主要候选。当Activity处于Stopped状态时，一定要保存当前数据和当前的UI状态，否则一旦Activity退出或关闭时，当前的数据和UI状态就丢失了。
4、Inactive状态：Activity被杀掉以后或者被启动以前，处于Inactive状态。这时Activity已被移除从Activity堆栈中，需要重新启动才可以显示和使用。
状态过渡具有不确定性并且由OPhone Runtime完全管理。OPhone Runtime将首先杀掉处于Stopped状态的Activity，在极端情况下，也会杀掉那些处于Paused状态的Activity。 
为确保无缝的用户体验，这些状态之间的过渡对用户来说应该做到透明的。不管Activity处于那种状态，最重要的是保留好UI状态和用户数据，一旦Actvity被激活，用户都能看到他想要的东西。
#### 如何监测Actvity的状态变化
为了确保Activity能够及时的响应状态的变化，OPhone提供了一系列的事件处理程序来处理Activity的状态转移，参考下图和示例代码。

![img](http://emanual.github.io/md-android/img/component_activity/04_activity2.jpg)  

```  
public class MyActivity extends Activity {
	// 在Activity生命周期开始时被调用
	public void onCreate(Bundle icicle) {
	}
	// onCreate完成后被调用，用来回复UI状态
	public void onRestoreInstanceState(Bundle savedInstanceState) {
	}
	// 当activity从停止状态重新启动时调用
	public void onRestart() {
	}
	// 当activity对用户即将可见的时候调用。
	public void onStart() {
	}
	// 当activity将要与用户交互时调用此方法，此时activity在activity栈的栈顶，用户输入已经 可以传递给它
	public void onResume() {
	}
	// Activity即将移出栈顶保留UI状态时调用此方法
	public void onSaveInstanceState(Bundle savedInstanceState) {
	}
	// 当系统要启动一个其他的activity时调用（其他的activity显示之前），这个方法被用来提交那些持久数据的改变、停止动画、和其他占用
	// CPU资源的东西。由于下一个activity在这个方法返回之前不会resumed，所以实现这个方法时代码执行要尽可能快。
	public void onPause() {
	}
	// 当另外一个activity恢复并遮盖住此activity,导致其对用户不再可见时调用。一个新activity启动、其它activity被切换至前景、当前activity被销毁时都会发生这种场景。
	public void onStop() {
	}
	// 在activity被销毁前所调用的最后一个方法，当进程终止时会出现这种情况
	public void onDestroy() {
	}
}
```
#### Activity完整的生命周期
完整的Activity生命周期之间从调用的OnCreate开始，到调用onDestroy结束。有可能在某些情况下，一个Activity被终止时并不调用onDestroy方法。      
使用OnCreate方法来初始化你的Activity：初始化的用户界面，分配引用类变量，绑定数据控件，并创建服务和线程。在OnCreate方法传递的对象Bundle包含最后一次调用onSaveInstanceState保存的UI状态。你可以使用这个Bundle恢复用户界面到以前的状态，无论是在OnCreate方法或通过覆盖onRestoreInstanceStateMethod方法。
覆盖onDestroy方法来清理OnCreate中创建的任何资源，并确保所有外部连接被关闭，例如网络或数据库的联系。
为了避免创造短期对象和增加垃圾收集的时间，以致对用户体验产生直接影响。如果你的Activity需要创建一些对象的话，最好在onCreate方法中创建，因为它仅调用一次在一个Actvity的完整生命周期中。
#### Activity可见的生命周期
一个Activity可见的生命周期始于OnStart调用，结束于OnStop调用。在这两个方法中间，你的Actvity将会对用户是可见的，尽管它可能没有焦点，也可能部分被遮挡着。在一个Activity完整的生命周期中可能会经过几个Activity可见的生命周期，因为你的Activity可能会经常在前台和后台之间切换。在极端情况下，OPhone Runtime将杀掉一个Activity即使它在可见状态并且并不调用onStop方法。
OnStop方法用于暂停或停止动画，线程，定时器，服务或其他专门用于更新用户界面程序。当用户界面是再次可见时，使用OnStart（或onRestart）方法来恢复或重新启动这些程序，。
onRestart方法优先于onStart被调用当一个Activity被重现可见时，使用它你可以实现一些Activity重新可见时的特殊的处理。
OnStart / OnStop方法也被用来注册和注销专门用于更新用户界面Intent接收者。
#### Activity活跃的生命周期
一个Activity活跃的生命周期始于OnResume调用，结束于OnPause调用。一个活跃的Actvity总是在前台并且接收用户输入事件。当一个新的Actvity启动，或该设备进入休眠状态，或失去焦点，Activity活跃的生命周期就结束了。尽量在onPause和onResume方法中执行较量轻的代码以确保您的应用程序能够快速响应Acitvity在前台和后台之间切换。
在调用onPause之前，onSaveInstanceState会被调用。这个方法提供了一个机会保存当前的UI状态到Bundle当中。 Bundle信息将会被传递到OnCreate和onRestoreInstanceState方法。使用onSaveInstanceState保存UI状态（如检查按钮状态，用户焦点，未提交用户输入）能够确保目前相同的用户界面当Activity下次被激活时。在Activity活跃生命周期中，你可以安全地认为onSaveInstanceState和onPause将被调到即使当前进程将终止。
Activity生命周期示例
父Activity启动子Activity，子Actvity退出，父Activity调用顺序如下
```  
onCreate()
onStart()
onResume()
onFreeze()
onPause()
onStop()
onRestart()
onStart(),onResume() …
```
用户点击Home，Actvity调用顺序如下
```  
onCreate()
onStart()
onResume()
onFreeze()
onPause()
onStop() -- Maybe
onDestroy() – Maybe
```
调用finish（）， Activity调用顺序如下：
```  
onCreate()
onStart()
onResume()
onPause()
onStop() 
onDestroy()
```
在Activity上显示dialog， Activity调用顺序如下：
```  
onCreate()
onStart()
onResume()
```
在父Activity上显示透明的或非全屏的activity，Activity调用顺序如下：
```  
onCreate()
onStart()
onResume()
onFreeze()
onPause()
```
设备进入睡眠状态，Activity调用顺序如下：
```  
onCreate()
onStart()
onResume()
onFreeze()
onPause()
```
做一个完整的Android程序，不想用到Activity，真的是比较困难的一件事情，除非是想做绿叶想疯了。因为Activity是Android程序与用户交互的窗口，在我看来，从这个层面的视角来看，Android的Activity特像网站的页面。在其上可以布置按钮，文本框等各种控件，简单来说就是Android的UI部分，在手机或平板电脑的屏幕上你看到的界面就是她喽！
Activity，在四大组件中，无疑是最复杂的，这年头，一样东西和界面挂上了勾，都简化不了，想一想，独立做一个应用有多少时间沦落在了界面上，就能琢磨清楚了。从视觉效果来看，一个Activity占据当前的窗口，响应所有窗口事件，具备有控件，菜单等界面元素。从内部逻辑来看，Activity需要为了保持各个界面状态，需要做很多持久化的事情，还需要妥善管理生命周期，和一些转跳逻辑。对于开发者而言，就需要派生一个Activity的子类，然后埋头苦干上述事情。
activity是用户和应用程序交互的窗口，一个activity相当于我们实际中的一个网页，当打开一个屏幕时，之前的那一个屏幕会被置为暂停状态，并且压入历史堆栈中，用户可以通过回退操作返回到以前打开过的屏幕。activity的生命周期：即“产生、运行、销毁”，但是这其中会调用许多方法onCreate（创建） 、onStart（激活） 、onResume（恢复） 、onPause（暂停） 、onStop（停止） 、onDestroy（销毁） 、onRestart（重启）。
```  
函数 是否可终止 说明 
onCreate() 否 Activity启动后第一个被调用的函数，常用来进行Activity的初始化，例如创建View、绑定数据或恢复信息等。 
onStart() 否 当Activity显示在屏幕上时，该函数被调用。 
onRestart() 否 当Activity从停止状态进入活动状态前，调用该函数。 
onResume() 否 当Activity能够与用户交互，接受用户输入时，该函数被调用。此时的Activity位于Activity栈的栈顶。 
onPause() 是 当Activity进入暂停状态时，该函数被调用。一般用来保存持久的数据或释放占用的资源。 
onStop() 是 当Activity进入停止状态时，该函数被调用。 
onDestroy() 是 在Activity被终止前，即进入非活动状态前，该函数被调用。
```
这七个方法定义了Activity的完整生命周期。实现这些方法可以帮助我们监视其中的三个嵌套生命周期循环：
Activity的完整生命周期自第一次调用onCreate()开始，直至调用onDestroy()为止。Activity在onCreate()中设置所有“全局”状态以完成初始化，而在onDestroy()中释放所有系统资源。例如，如果Activity有一个线程在后台运行从网络上下载数据，它会在onCreate()创建线程，而在 onDestroy()销毁线程。
Activity的可视生命周期自onStart()调用开始直到相应的onStop()调用结束。在此期间，用户可以在屏幕上看到Activity，尽管它也许并不是位于前台或者也不与用户进行交互。在这两个方法之间，我们可以保留用来向用户显示这个Activity所需来源。例如，当用户不再看见我们显示的内容时，我们可以在onStart()中注册一个BroadcastReceiver来监控会影响UI的变化，而在onStop()中来注消。onStart() 和 onStop() 方法可以随着应用程序是否为用户可见而被多次调用。
Activity的前台生命周期自onResume()调用起，至相应的onPause()调用为止。在此期间，Activity位于前台最上面并与用户进行交互。Activity会经常在暂停和恢复之间进行状态转换——例如:当设备转入休眠状态或者有新的Activity启动时，将调用onPause()方法。当Activity获得结果或者接收到新的Intent时会调用onResume()方法。
#### Activity的启动有三种，
1、onCreate()
第一次启动这个Activity时候一定是会先从这里启动Activity的。如果一些常量需要初始化，比如话机读取本地配置，获得线路类型、地理位置之类的信息都要在这里做
2、onRestart()
从当前Activity  A跳转到另外一个Activity  B时候，A如果不手动处理的话，是暂时被压到Activity堆栈里面了。如果再次从B或者另
外的Activity跳回A，此时A就会从onRestart()函数启动
3、onNewIntent(Intent intent)
从这个函数的启动不大常见，如果一个TabLayout的content内容填充的都是activity的话，例如系统的联系人程序，点击联系人或者拨号时候，出现的是一个TabActivity，这时候其实拨号程序、联系人程序、通话记录、收藏四个Activity都调用了onCreate函数，此时，如果点击的是联系人程序，打开的TabActivity默认的界面就是联系人列表的界面，此时如果点击拨号的Tab时候，拨号程序的启动就是从onNewIntent(Intent intent)这个函数再次启动的。到现在为止我也只是发现了此一种情况是从这个函数再次启动一个activity的，要是知道别的情况也是从这个函数启动的话麻烦告知下。
为什么要研究Activity的启动呢？有时候我们设置了一些全局变量，比如系统的通话记录程序，每次启动它的时候都要刷新下，不然刚刚拨打过电话的记录就不会显示，类似的还有如果我们有一些全局需要调用的变量是从一个本地配置文件读取的，但是当Activity压栈时候，本地配置或者全局变量发生变化呢?如果不重新读取下，它的值就不会改变，程序就会出错的。
#### Android中Activty的生命周期和栈
Activty的生命周期的也就是它所在进程的生命周期。

![img](http://emanual.github.io/md-android/img/component_activity/03_activity.jpg)  

每一个活动（ Activity ）都处于某一个状态，对于开发者来说，是无法控制其应用程序处于某一个状态的，这些均由系统来完成。
但是当一个活动的状态发生改变的时候，开发者可以通过调用 onXX() 的方法获取到相关的通知信息。
在实现 Activity 类的时候，通过覆盖（ override ）这些方法即可在你需要处理的时候来调用。
onCreate ：当活动第一次启动的时候，触发该方法，可以在此时完成活动的初始化工作。
onCreate 方法有一个参数，该参数可以为空（ null ），也可以是之前调用 onSaveInstanceState （）方法保存的状态信息。
onStart ：该方法的触发表示所属活动将被展现给用户。
onResume ：当一个活动和用户发生交互的时候，触发该方法。
onPause ：当一个正在前台运行的活动因为其他的活动需要前台运行而转入后台运行的时候，触发该方法。这时候需要将活动的状态持久化，比如正在编辑的数据库记录等。
onStop ：当一个活动不再需要展示给用户的时候，触发该方法。如果内存紧张，系统会直接结束这个活动，而不会触发 onStop 方法。 所以保存状态信息是应该在onPause时做，而不是onStop时做。活动如果没有在前台运行，都将被停止或者Linux管理进程为了给新的活动预留足 够的存储空间而随时结束这些活动。因此对于开发者来说，在设计应用程序的时候，必须时刻牢记这一原则。在一些情况下，onPause方法或许是活动触发的 最后的方法，因此开发者需要在这个时候保存需要保存的信息。
onRestart ：当处于停止状态的活动需要再次展现给用户的时候，触发该方法。
onDestroy ：当活动销毁的时候，触发该方法。和 onStop 方法一样，如果内存紧张，系统会直接结束这个活动而不会触发该方法。
onSaveInstanceState ：系统调用该方法，允许活动保存之前的状态，比如说在一串字符串中的光标所处的位置等。
通常情况下，开发者不需要重写覆盖该方法，在默认的实现中，已经提供了自动保存活动所涉及到的用户界面组件的所有状态信息。
Activity栈上面提到开发者是无法控制Activity的状态的，那Activity的状态又是按照何种逻辑来运作的呢？这就要知道Activity栈。
每个Activity的状态是由它在Activity栈（是一个后进先出LIFO，包含所有正在运行Activity的队列）中的位置决定的。
当一个新的Activity启动时，当前的活动的Activity将会移到Activity栈的顶部。
如果用户使用后退按钮返回的话，或者前台的Activity结束，在栈上的Activity将会移上来并变为活动状态。如下图所示：

![img](http://emanual.github.io/md-android/img/component_activity/03_activity2.jpg)  

一个应用程序的优先级是受最高优先级的Activity影响的。当决定某个应用程序是否要终结去释放资源，Android内存管理使用栈来决定基于Activity的应用程序的优先级。
#### Activity状态
一般认为Activity有以下四种状态：
1、活动的：当一个Activity在栈顶，它是可视的、有焦点、可接受用户输入的。Android试图尽最大可能保持它活动状态，杀死其它Activity来确保当前活动Activity有足够的资源可使用。当另外一个Activity被激活，这个将会被暂停。
2、暂停：在很多情况下，你的Activity可视但是它没有焦点，换句话说它被暂停了。有可能原因是一个透明或者非全屏的Activity被激活。
当被暂停，一个Activity仍会当成活动状态，只不过是不可以接受用户输入。在极特殊的情况下，Android将会杀死一个暂停的Activity来为活动的Activity提供充足的资源。当一个Activity变为完全隐藏，它将会变成停止。
3、停止： 当一个Activity不是可视的，它“停止”了。这个Activity将仍然在内存中保存它所有的状态和会员信息。尽管如此，当其它地方需要内存时，它 将是最有可能被释放资源的。当一个Activity停止后，一个很重要的步骤是要保存数据和当前UI状态。一旦一个Activity退出或关闭了，它将变 为待用状态。
4、待用： 在一个Activity被杀死后和被装在前，它是待用状态的。待用Acitivity被移除Activity栈，并且需要在显示和可用之前重新启动它。
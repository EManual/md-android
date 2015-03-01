Activity的生命周期？
答：和其他手机平台的应用程序一样，Android的应用程序的生命周期是被统一掌控的，也就是说我们写的应用程序命运掌握在别人(系统)的手里，我们不能改变它，只能学习并适应它。 
简单地说一下为什么是这样：我们手机在运行一个应用程序的时候，有可能打进来电话发进来短信，或者没有电了，这时候程序都会被中断，优先去服务电话的基本功能 ，另 外系统也不允许你占用太多资源 ，至少要保证电话功能吧,所以资源不足的时候也就有可 能被干掉。 
言归正传，Activity的基本生命周期如下代码 所示： 
Java代码 
```  
public class MyActivity extends Activity {
	protected void onCreate(Bundle savedInstanceState);
	protected void onStart();
	protected void onResume();
	protected void onPause();
	protected void onStop();
	protected void onDestroy();
}
public class MyActivity extends Activity {
	protected void onCreate(Bundle savedInstanceState);
	protected void onStart();
	protected void onResume();
	protected void onPause();
	protected void onStop();
	protected void onDestroy();
}
```
你自己写的Activity会按需要 重载这些方法，onCreate是免不了的，在一个Activity正常启动的过程中，他们被调用的顺序是 onCreate -> onStart -> onResume, 在Activity被干掉的时候顺序是onPause -> onStop -> onDestroy ，这样就是一个完整的生命周期，但是有人问了 ，程序正运行着呢来电话了，这个程序咋办?中止了呗，如果中止的时候新出的一个Activity是全屏的那么：onPause->onStop ，恢复的时候onStart->onResume ，如果打断 这个应用程序的是一个Theme为Translucent 或者Dialog 的Activity那么只是onPause ,恢复 的时候onResume 。 
详细介绍一下这几个方法中系统在做什么以及我们应该做什么：
```  
onCreate: 在这里创建界面 ，做一些数据 的初始化工作 
onStart: 到这一步变成用户可见不可交互 的 
onResume: 变成和用户可交互 的，(在activity 栈系统通过栈的方式管理这些个 Activity的最上面，运行完弹出栈，则回到上一个Activity) 
onPause:到这一步是可见但不可交互的，系统会停止动画等消耗CPU的事情从上文的描述已经知道，应该在这里保存你的一些数据,因为这个时候你的程序的优先级降低，有可能被系统收回。在这里保存的数据，应该在onResume里读出来，注意：这个方法里做的事情时间要短，因为下一个activity不会等到这个方法完成才启动 
onstop: 变得不可见 ，被下一个activity覆盖了 
onDestroy:这是activity被干掉前最后一个被调用方法了，可能是外面类调用finish方法或者是系统为了节省空间将它暂时性的干掉，可以用isFinishing()来判断它，如果你有一个ProgressDialog在线程中转动，请在onDestroy里把他cancel掉，不然等线程结束的时候，调用Dialog的cancel方法会抛 异常的。 
```
onPause，onstop，onDestroy三种状态下activity都有可能被系统干掉
为了保证程序的正确性，你要在onPause()里写上持久层操作的代码，将用户编辑的内容都保存到存储介质上(一般都是数据库)。实际工作中因为生命周期的变化而带来的问题也很多，比如你的应用程序起了新的线程在跑，这时候中断了，你还要去维护那个线程，是暂停还是杀掉还是数据回滚，是吧?因为Activity可能被杀掉，所以线程中使用的变量和一些界面元素就千万要注意了，一般我都是采用Android的消息机制[Handler,Message]来处理多线程和界面交互的问题。这个我后面会讲一些，最近因为这些东西头已经很大了，等我理清思绪再跟大家分享。  
/frameworks/base/services/java/InputMethodManagerService.java
这是整个系统当中，一切与输入法有关的地方的总控制中心。它通过管理下面三个模块来实现系统的输入法框架。
1、/frameworks/base/services/java/WindowManagerService
负责显示输入法，接收用户事件。
2、/frameworks/base/core/java/android.inputmethodservice/InputMethodService
输入法内部逻辑，键盘布局，选词等，最终把选出的字符通过commitText提交出来。要做一个像搜狗输入法这样的东西的话，主要就是在这里做文章。
3、InputManager
由UI控件（View,TextView,EditText等）调用，用来操作输入法。比如，打开，关闭，切换输入法等。
下面说一下InputMethodManagerService这个控制中心是怎么样与三个模块交互的。
1、与WindowManagerSerivce的交互。
首先，InputMethodManagerService在初始化时，会调用IWindowManager.Stub.asInterface(ServiceManager.getService(Context.WINDOW_SERVICE))，得到IWindowManager这个代理，然后通过IWindowManager与WindowManagerService交互。比如下面这些操作：
调用mIWindowManager.addWindowToken(mCurToken, WindowManager.LayoutParams.TYPE_INPUT_METHOD)，让WindowManagerService显示输入法界面。
调用mIWindowManager.removeWindowToken(mCurToken)让输入法界面关闭。
调用mIWindowManager.inputMethodClientHasFocus(client)判断输入法是否聚焦。
2、与InputMethodService的交互。
InputMethodManagerService在内部维护着一个ArrayList<InputMethodInfo> mMethodList。这个列表会在服务启动时通过PackageManager查询当前系统中的输入法程序来得到。与之对应的，每一个输入法程序的AndroidManifest.xml中都会有一个Service，而每个Service中都会有标记来告诉系统，自己是个输入法程序。下面这个是我从系统自带的例子Samples/SoftKeyboard/AndroidManifest.xml中的取出来的：
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.android.softkeyboard" >
    <application android:label="@string/ime_name" >
        <service
            android:name="SoftKeyboard"
            android:permission="android.permission.BIND_INPUT_METHOD" >
            <intent-filter>
                <action android:name="android.view.InputMethod" />
            </intent-filter>
            <meta-data
                android:name="android.view.im"
                android:resource="@xml/method" />
        </service>
    </application>
</manifest>
```
另外，InputMethodManagerService内部还有一个PackageReceiver，当系统中有程序的安装、删除、重启等事件发生时，会更新mMethodList。InputMethodManagerService打开，关闭，切换输入法时，其实就是在操作mMethodList中某个InputMethodInfo。把InputMethodInfo中的代表某个输入法的InputMethodService启动或者销毁，就实现了输入法的打开和关闭。
3、与InputMethodManager的交互
InputMethodManager中会包含一个IInputMethodManager，这个东西就是InputMethodManagerService的代理，打开关闭输入法这些操作就是由InputMethodManager中的某些方法调用IInputMethodManager中相应的方法来实现的。比如：
```  
mService.getInputMethodList()获取输入法列表。
mService.updateStatusIcon(imeToken, packageName, iconId)更新输入法图标，即屏幕上方状态栏中的输入法图标。
mService.finishInput(mClient)隐藏当前输入法。这所以不说关闭输入法，是因为输入法服务启动起来以后，只有在系统关闭或者切换输入法时才会关闭。
mService.showSoftInput(mClient, flags, resultReceiver)打开当前输入法。
```
分别介绍完三大模块之后，再介绍两个东西，输入法的实现和怎么样调用输入法。
1、以系统的SoftKeyboard为例，实现一个输入法至少需要Keyboard,KeyboardView,CandidateView,SoftKeyboard这四个东西。
CandidateView负责显示软键盘上面的那个候选区域。
Keyboard负责解析并保存键盘布局，并提供选词算法，供程序运行当中使用。其中键盘布局是以XML文件存放在资源当中的。比如我们在汉字输入法下，按下b、a两个字母。Keyboard就负责把这两个字母变成爸、把、巴等显示在CandidateView上。
KeyboardView负责显示，就是我们看到的按键。
上面这两人东西合起来，组成了InputView，就是我们看到的软键盘。
SoftKeyboard继承了InputMethodService，启动一个输入法，其实就是启动一个InputMethodService，当SoftKeyboard输入法被使用时，启动就会启动SoftKeyboard这个Service。InputMethodService中管理着一个继承自Dialog的SoftInputWindow，而SoftInputWindow里面就包括了InputView和CandidateView这两个东西。
2、怎么样调用输入法呢？
说起这个东西，很自然地想起EditText来，我们团队跟踪过这个Widget，EditText本身很简单，主要的代码在TextView和View当中。这两个Widget本身又很复杂，杂在一起说不清楚。这里我就把我们团队以前做过的一个小例子拿进来做参考，说明一下如何从一个View上调用输入法和如何接收输入法传过来的字符串。
小例子的起源来自于我们要做一个浏览器，需要在SurfaceView来在Canvas上面绘制自己需要的东西，开启自己的主控制循环线程，事件处理等。比如我要在SurfaceView上绘制输入浏览器中的按钮、文本、图片、输入框等，当然这些和ImageView,TextView没有关系，都是用自己的UI引擎来做的。最后所有问题都解决了，却在输入框上卡壳了。因为要实现输入，得调用EditText，否则就必须自己去和EditText一样连接输入法。以前找过相关资料，看网上也有人碰到过这个问题，但都没有答案。最后，还是团队中一个很牛的娃给解决了。代码很简单，不出二十行，但没资料，View的源码又太庞大，费的劲却是只有我们团队的人才能体会得到的。。。这里佩服张老二同学一下，没有他的努力，就没有下面这二十多行很重要很重要的源码的诞生。
首先，定义一个继承自BaseInputConnection的类。
```  
public class MyBaseInputConnection extends BaseInputConnection {
	public MyBaseInputConnection(View targetView, boolean fullEditor) {
		super(targetView, fullEditor);
	}
	public static String tx = "";
	@Override
	public boolean commitText(CharSequence text, int newCursorPosition) {
		// 输入法程序就是通过调用这个方法把最终结果输出来的。
		tx = text.toString();
		return true;
	}
}
```
BaseInputConnection相当于一个InputMethodService和View之间的一个通道。每当InputMethodService产生一个结果时，都会调用BaseInputConnection的commitText方法，把结果传递出来。
```  
public class MyView extends SurfaceView {
	//得到InputMethodManager。
	InputMethodManager input = (InputMethodManager) context.getSystemService(Context.INPUT_METHOD_SERVICE);
	ResultReceiver receiver = new ResultReceiver(new Handler() {//定义事件处理器。
		public void handleMessage(Message msg) {}
	});
	
	input.showSoftInput(this, 0, mRR);//在你想呼出输入法的时候，调用这一句。
	//这个方法继承自View。把自定义的BaseInputConnection通道传递给InputMethodService。
	@Override
	public InputConnection onCreateInputConnection(EditorInfo outAttrs) {
		return new MyBaseInputConnection(this, false);
	}
}
```
低级界面上面，自己调用输入法并接收输入法的输出结果，就是这样的。
通过这个问题，可以看出WebView上面的输入法是如何实现的。简单来说，WebView就是一个ViewGroup，它里面有两层，上层是一个EditText，下层是浏览器页面。当浏览器的输入框被用户点中，需要显示输入法时，就把上层EditText的位置移到浏览器的输入框的位置，高速好EditText的大小和样式后，让EditText和浏览器页面融为一体，效果就很好了。
通常来说，这个方式应该比自己调用输入法要好些。可以少做很多事。不过，如果产品经理是个很有想像力的人的话，你就不能满足他设计出来的有可能极端变态却非常炫的输入效果了。
HoneyComb中最下面是一栏类似Windows的状态栏，分为两个部分：左边是三个导航键：从左置右依次是：返回键，Home键和RecentApplication 键，就是查看最近打开的所有程序，多任务切换就在这里。  
这三个键为一个区域叫NavigationArea，即导航区。最右边是NotificationArea，也就是提示信息区，有电量，无线信号，蓝牙等信息显示。这条StatusBar,是无论打开哪个程序都会显示在最下方的。
因为最近项目中打开Browser时，按键盘的ESC键不会退出程序，但是按左下角的返回键可以退出。所以就想着看看这个返回键是如何让浏览器关闭的，那么我就在源码中让按ESC时的响应函数和它一样就可以了。
因为这条状态栏活跃在所有应用程序打开的时候，因此我想应该不会在Launcher中实现，大概是在FrameWorks下面实现。结果证明我的想法是对的。在Frameworks/base/Packages/SystemUI/ 下面找到了这三个按键的图片 ：默认显示的图片分别是ic_sysbar_back_default.png,ic_sysbar_home_default.png和ic_sysbar_recent_default.png。所以很自然可以知道界面肯定是在这里实现的。
查看布局文件（在res/Layout-xlarge/status_bar.xml)可以看到back键和home键是属于KeyButtonView这个类的，这个类后面再分析。而recentApp这个键是一个imageView。Menu键也在这里有定义，只是被默认设置为invisible。整个StatusBar的布局是TabletStatusBarView这个类，而这类是继承FrameLayout的。整个导航区三个键的布局还比较纠结，Back键是被包在RelativeLayout里面，靠左显示，而home键，recentApp键和menu键是又更深一层被包在一个LinearLayout里面。其它Notificatin的具体布局可以自己查看，都在这个status_bar.xml布局中。
在SystemUI/Tablet/TabletStatusBar.java中的 makeStatusBarView()函数中实现界面的初始化，这个函数的返回值类型是View。其中导航键实现
```  
mBackButton = (ImageView)sb.findViewById(R.id.back);
mNavigationArea = sb.findViewById(R.id.navigationArea);
mHomeButton = mNavigationArea.findViewById(R.id.home);
mMenuButton = mNavigationArea.findViewById(R.id.menu);
mRecentButton = mNavigationArea.findViewById(R.id.recent_apps);
mRecentButton.setOnClickListener(mOnClickListener);
```
由于mRecentButton是ImageView所以要单独实现OnClickListener监听。而mBackButton，mHomeButton和mMenuButton是属于KeyButtonView，这个类是ImageView的子类，具体实现在SystemUI/Policy/KeyButtonView.java中。在这个类中可以看到重载了onTouchEvent这个函数。在响应MotionEvent.ACTION_UP后，会调用这样一个函数
```  
sendEvent(KeyEvent.ACTION_UP, KeyEvent.FLAG_FROM_SYSTEM | KeyEvent.FLAG_VIRTUAL_HARD_KEY);
```
其中FLAG_FROM_SYSTEM表示是这是个没有被第三方所修改过的，系统消息。FLAG_VIRTUAL_HARD_KEY表示这是个虚拟按键消息，即从触摸屏发出，而非实体键盘发出的。
最终会调用这句
```  
final KeyEvent ev = new KeyEvent(mDownTime, when, action, mCode, mRepeat, 0, KeyCharacterMap.VIRTUAL_KEYBOARD, 0, flags, InputDevice.SOURCE_KEYBOARD);
```
这就是创建一个新的KeyEvent ，具体参数的含义参看KeyEvent.java中的相关构造函数。关键信息就是mCode,这个值
```  
mCode = a.getInteger(R.styleable.KeyButtonView_keyCode, 0);
```
也就是返回键和Home以及Menu键的键值。
最后调用
```  
mWindowManager.injectInputEventNoWait(ev);
```
它调用的是WindowManagerService中的injectInputEventNoWait(ev)函数，最终调用的是InputManager.java中的
```  
private static native int nativeInjectInputEvent(InputEvent event, int injectorPid, int injectorUid, int syncMode, int timeoutMillis);
```
这是个native的函数，也就是JNI调用了。可以看出点击Back和Home键，并没有单独的处理函数去处理事件，而只是生成了一个新的KeyEvent，也就是相当于发送了一个按键消息而已。其实一开始就应该想到，这个导航区中的Back和Home键，其实就是虚拟键，按虚拟键和实体键，只是发送键值的路径不同，最终应该都进入一个消息队列，而且应该对应的键值是相同的。所以处理也应该是相同的。
具体虚拟按键的按键事件的分发和处理都具体在哪里，我还没有细看，应该和按实体键盘类似在phoneWindowManager.java中有dispatch。只是估计，之后会细看下。这样的话，我们定制底下的那条Statusbar也应该可以了，包括多任务显示那一栏也都在这边实现。
WindowManager.LayoutParams 是 WindowManager 接口的嵌套类；继承于 ViewGroup.LayoutParams。
它的内容十分丰富。其实WindowManager.java的主要内容就是由这个类定义构成。下面来分析一下这个类：
定义
```  
public static class WindowManager.LayoutParams extends ViewGroup.LayoutParams implements Parcelable
```
继承关系
```  
java.lang.Object
android.view.ViewGroup.LayoutParams
android.view.WindowManager.LayoutParams
```
继承来的属性与常量
从ViewManager.LayoutParams 继承来的属性：
```  
android:layout_height
Specifies the basic height of the view.
android:layout_width
Specifies the basic width of the view.
```
从ViewManager.LayoutParams继承的常量：
```  
FILL_PARENT
WRAP_CONTENT
MATCH_PARENT
```
两个变量：
```  
width
height
```
属性及可用的常量定义
1. public int x;
如果忽略gravity属性，那么它表示窗口的绝对X位置。
什么是gravity属性呢？简单地说，就是窗口如何停靠。
当设置了 Gravity.LEFT 或 Gravity.RIGHT 之后，x值就表示到特定边的距离。
2. public int y;
如果忽略gravity属性，那么它表示窗口的绝对Y位置。
当设置了 Gravity.TOP 或 Gravity.BOTTOM 之后，y值就表示到特定边的距离。
3. public float horizontalWeight;
public float verticalWeight;
在纵/横向上，为关联的view预留了多少扩展空间（像素）。如果是0，那么此view不能被拉伸。
其他情况下，扩展空间（像素）将被widget所均分。
4. public int type;
窗口类型。
有3种主要类型：
Applicationwindows：
取值在 FIRST_APPLICATION_WINDOW 和 LAST_APPLICATION_WINDOW 之间。
是通常的、顶层的应用程序窗口。必须将 token 设置成 activity 的token。
Sub_windows：
取值在FIRST_SUB_WINDOW和LAST_SUB_WINDOW之间。
与顶层窗口相关联，token必须设置为它所附着的宿主窗口的token。
Systemwindows：
取值在 FIRST_SYSTEM_WINDOW 和 LAST_SYSTEM_WINDOW 之间。
用于特定的系统功能。它不能用于应用程序，使用时需要特殊权限。
下面定义了 type 的取值：
```  
// 应用程序窗口。
public static final int FIRST_APPLICATION_WINDOW = 1;
// 所有程序窗口的“基地”窗口，其他应用程序窗口都显示在它上面。
public static final int TYPE_BASE_APPLICATION = 1;
// 普通应用功能程序窗口。token必须设置为Activity的token，以指出该窗口属谁。
public static final int TYPE_APPLICATION = 2;
// 用于应用程序启动时所显示的窗口。应用本身不要使用这种类型。
// 它用于让系统显示些信息，直到应用程序可以开启自己的窗口。
public static final int TYPE_APPLICATION_STARTING = 3;
// 应用程序窗口结束。
public static final int LAST_APPLICATION_WINDOW = 99;
// 子窗口。子窗口的Z序和坐标空间都依赖于他们的宿主窗口。
public static final int FIRST_SUB_WINDOW = 1000;
// 面板窗口，显示于宿主窗口上层。
public static final int TYPE_APPLICATION_PANEL = FIRST_SUB_WINDOW;
// 媒体窗口，例如视频。显示于宿主窗口下层。
public static final int TYPE_APPLICATION_MEDIA = FIRST_SUB_WINDOW + 1;
// 应用程序窗口的子面板。显示于所有面板窗口的上层。（GUI的一般规律，越“子”越靠上）
public static final int TYPE_APPLICATION_SUB_PANEL = FIRST_SUB_WINDOW + 2;
// 对话框。类似于面板窗口，绘制类似于顶层窗口，而不是宿主的子窗口。
public static final int TYPE_APPLICATION_ATTACHED_DIALOG = FIRST_SUB_WINDOW + 3;
// 媒体信息。显示在媒体层和程序窗口之间，需要实现透明（半透明）效果。（例如显示字幕）
public static final int TYPE_APPLICATION_MEDIA_OVERLAY = FIRST_SUB_WINDOW + 4;
// 子窗口结束。（ End of types of sub-windows ）
public static final int LAST_SUB_WINDOW = 1999;
// 系统窗口。非应用程序创建。
public static final int FIRST_SYSTEM_WINDOW = 2000;
// 状态栏。只能有一个状态栏；它位于屏幕顶端，其他窗口都位于它下方。
public static final int TYPE_STATUS_BAR = FIRST_SYSTEM_WINDOW;
// 搜索栏。只能有一个搜索栏；它位于屏幕上方。
public static final int TYPE_SEARCH_BAR = FIRST_SYSTEM_WINDOW + 1;
// 电话窗口。它用于电话交互（特别是呼入）。它置于所有应用程序之上，状态栏之下。
public static final int TYPE_PHONE = FIRST_SYSTEM_WINDOW + 2;
// 系统提示。它总是出现在应用程序窗口之上。
public static final int TYPE_SYSTEM_ALERT = FIRST_SYSTEM_WINDOW + 3;
// 锁屏窗口。
public static final int TYPE_KEYGUARD = FIRST_SYSTEM_WINDOW + 4;
// 信息窗口。用于显示toast。
public static final int TYPE_TOAST = FIRST_SYSTEM_WINDOW + 5;
// 系统顶层窗口。显示在其他一切内容之上。此窗口不能获得输入焦点，否则影响锁屏。
public static final int TYPE_SYSTEM_OVERLAY = FIRST_SYSTEM_WINDOW + 6;
// 电话优先，当锁屏时显示。此窗口不能获得输入焦点，否则影响锁屏。
public static final int TYPE_PRIORITY_PHONE = FIRST_SYSTEM_WINDOW + 7;
// 系统对话框。（例如音量调节框）。
public static final int TYPE_SYSTEM_DIALOG = FIRST_SYSTEM_WINDOW + 8;
// 锁屏时显示的对话框。
public static final int TYPE_KEYGUARD_DIALOG = FIRST_SYSTEM_WINDOW + 9;
// 系统内部错误提示，显示于所有内容之上。
public static final int TYPE_SYSTEM_ERROR = FIRST_SYSTEM_WINDOW + 10;
// 内部输入法窗口，显示于普通UI之上。应用程序可重新布局以免被此窗口覆盖。
public static final int TYPE_INPUT_METHOD = FIRST_SYSTEM_WINDOW + 11;
// 内部输入法对话框，显示于当前输入法窗口之上。
public static final int TYPE_INPUT_METHOD_DIALOG = FIRST_SYSTEM_WINDOW + 12;
// 墙纸窗口。
public static final int TYPE_WALLPAPER = FIRST_SYSTEM_WINDOW + 13;
// 状态栏的滑动面板。
public static final int TYPE_STATUS_BAR_PANEL = FIRST_SYSTEM_WINDOW + 14;
// 系统窗口结束。
public static final int LAST_SYSTEM_WINDOW = 2999;
```
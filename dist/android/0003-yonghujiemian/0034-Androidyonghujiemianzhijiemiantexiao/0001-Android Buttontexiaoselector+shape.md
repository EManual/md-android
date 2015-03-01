当然除了使用drawable这样的图片外今天谈下自定义图形shape的方法，对于Button控件Android上支持以下几种属性shape、gradient、stroke、corners 等。
我们就以目前系统的 Button 的 selector 为例说下:
```  
<shape>
    <gradient
        android:angle="270"
        android:endColor="FFFFFF"
        android:startColor="ff8c00" />
    <stroke
        android:width="2dp"
        android:color="dcdcdc" />
    <corners android:radius="2dp" />
    <padding
        android:bottom="10dp"
        android:left="10dp"
        android:right="10dp"
        android:top="10dp" />
</shape>
```
对于上面，这条 shape 的定义，分别为渐变，在 gradient 中 startColor 属性为开始的颜色，endColor 为渐变结束的颜色，下面的 angle 是角度。接下来是 stroke 可以理解为边缘，corners为拐角这里 radius 属性为半径，最后是相对位置属性 padding。
对于一个 Button 完整的定义可以为：
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://www.norkoo.com">
    <item android:state_pressed="true"><shape>
            <gradient android:angle="270" android:endColor="FFFFFF" android:startColor="ff8c00" />
            <stroke android:width="2dp" android:color="dcdcdc" />
            <corners android:radius="2dp" />
            <padding android:bottom="10dp" android:left="10dp" android:right="10dp" android:top="10dp" />
        </shape></item>
    <item android:state_focused="true"><shape>
            <gradient android:angle="270" android:endColor="ffc2b7" android:startColor="ffc2b7" />
            <stroke android:width="2dp" android:color="dcdcdc" />
            <corners android:radius="2dp" />
            <padding android:bottom="10dp" android:left="10dp" android:right="10dp" android:top="10dp" />
        </shape></item>
    <item><shape>
            <gradient android:angle="270" android:endColor="ff9d77" android:startColor="ff9d77" />
            <stroke android:width="2dp" android:color="fad3cf" />
            <corners android:radius="2dp" />
            <padding android:bottom="10dp" android:left="10dp" android:right="10dp" android:top="10dp" />
        </shape></item>
</selector>
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android" >
<item
	android:color="hex_color
	android:state_pressed=["true" | "false"]
	android:state_focused=["true" | "false"]
	android:state_selected=["true" | "false"]
	android:state_active=["true" | "false"]
	android:state_checkable=["true" | "false"]
	android:state_checked=["true" | "false"]
	android:state_enabled=["true" | "false"]
	android:state_window_focused=["true" | "false"] />
</selector>
```
Elements：
```   
<selector>
```
必须。必须是根元素。包含一个或多个<item>元素。Attributes：xmlns:android String，必须。定义XML的命名空间，必须是"http://schemas.android.com/apk/res/android".<item>定义特定状态的color，通过它的特性指定。必须是<selector>的子元素。Attributes：android:color16进制颜色。必须。这个颜色由RGB值指定，可带Alpha。这个值必须以""开头，后面跟随 Alpha-Red-Green-Blue信息： 
```  
RGB
ARGB
RRGGBB
AARRGGBB
android:state_pressed
Boolean。"true"表示按下状态使用（例如按钮按下）；"false"表示非按下状态使用。
android:state_focused
Boolean。"true"表示聚焦状态使用（例如使用滚动球/D-pad聚焦 Button）；"false"表示非聚焦状态使用。 
android:state_selected Boolean。"true"表示选中状态使用（例如 Tab 打开）；"false"表示非选中状态使用。 
android:state_checkable Boolean。"true"表示可勾选状态时使用；"false"表示非可勾选状态使用。（只对能切换可勾选—非可勾选的构件有用。）
android:state_checkedBoolean。"true"表示勾选状态使用；"false"表示非勾选状态使用。
android:state_enabled Boolean。"true"表示可用状态使用（能接收触摸/点击事件）："false"表示不可用状态使用。
android:window_focused Boolean。"true"表示应用程序窗口有焦点时使用（应用程序在前台）；"false"表示无焦点时使用（例如Notification栏拉下或对话框显示）。 
```
XML:
```  
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:color="ffff0000"/>
 	<!-- pressed -->
    <item android:state_focused="true" android:color="ff0000ff"/>
 	<!-- focused -->
    <item android:color="ff000000"/>
 	<!-- default -->
</selector>
//这个 Layout XML 会应用 ColorStateList 到一个 View 上： 
<Button 
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="@string/button_text"
    android:textColor="@color/button_text" />
```
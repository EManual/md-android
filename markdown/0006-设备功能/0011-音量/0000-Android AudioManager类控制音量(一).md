一个好的Android应用免不了会自带背景音乐，比如游戏或者一款比较不错的书本阅读器。一些好的应用在自带音乐的时候会多添加一款小功能即可以帮助用户设置声音大小或者改变应用的声音模式。
本篇基于 Android API 中的 AudioManager 作讲述，使看过本篇的读者可以迅速的掌握这个类的实现过程。下面是本篇大纲：
```  
* 1、认识 AudioManager
* 2、AudioManager 主要方法介绍
* 3、程序逻辑实现过程
```
#### 1、认识 AudioManager
AudioManager 类位于 android.Media 包中，该类提供访问控制音量和钤声模式的操作。
#### 2、AudioManager 主要方法介绍
由于 AudioManager 该类方法过多，这里只讲述几个比较常用到的方法：
```  
* 方法：adjustVolume(int direction, int flags)
解释：这个方法用来控制手机音量大小，当传入的第一个参数为 AudioManager.ADJUST_LOWER 时，可将音量调小一个单位，传入 AudioManager.ADJUST_RAISE 时，则可以将音量调大一个单位。
* 方法：getMode()
解释：返回当前音频模式。
* 方法：getRingerMode()
解释：返回当前的铃声模式。
* 方法：getStreamVolume(int streamType)
解释：取得当前手机的音量，最大值为7，最小值为0，当为0时，手机自动将模式调整为“震动模式”。
* 方法：setRingerMode(int ringerMode)
解释：改变铃声模式
```
#### 3、程序逻辑实现过程
界面上设置了一个图片，表示当前铃声状态，一个进度条表示当前音量大小，五个图片按钮，用来表示增加/减小音量、普通模式、静音模式和震动模式。下面是界面的 XML 布局代码：
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/layout1"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/white" >
    <TextView
        android:id="@+id/myText1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="20px"
        android:layout_y="42px"
        android:text="@string/str_text1"
        android:textColor="@drawable/black"
        android:textSize="16sp" >
    </TextView>
    <ImageView
        android:id="@+id/myImage"
        android:layout_width="48px"
        android:layout_height="48px"
        android:layout_x="110px"
        android:layout_y="32px" >
    </ImageView>
    <TextView
        android:id="@+id/myText2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="20pxv
        android:layout_y="102px"
        android:text="@string/str_text2"
        android:textColor="@drawable/black"
        android:textSize="16sp" >
    </TextView>
    <ProgressBar
        android:id="@+id/myProgress"
        style="?android:attr/progressBarStyleHorizontal"
        android:layout_width="160dip"
        android:layout_height="wrap_content"
        android:layout_x="110px"
        android:layout_y="102px"
        android:max="7"
        android:progress="5" >
    </ProgressBar>
    <ImageButton
        android:id="@+id/downButton"
        android:layout_width="100px"
        android:layout_height="100px"
        android:layout_x="50px"
        android:layout_y="162px"
        android:src="@drawable/down" >
    </ImageButton>
    <ImageButton
        android:id="@+id/upButton"
        android:layout_width="100px"
        android:layout_height="100px"
        android:layout_x="150px"
        android:layout_y="162px"
        android:src="@drawable/up" >
    </ImageButton>
    <ImageButton
        android:id="@+id/normalButton"
        android:layout_width="60px"
        android:layout_height="60px"
        android:layout_x="50px"
        android:layout_y="272px"
        android:src="@drawable/normal" >
    </ImageButton>
    <ImageButton
        android:id="@+id/muteButton"
        android:layout_width="60px"
        android:layout_height="60px"
        android:layout_x="120px"
        android:layout_y="272px"
        android:src="@drawable/mute" >
    </ImageButton>
    <ImageButton
        android:id="@+id/vibrateButton"
        android:layout_width="60px"
        android:layout_height="60px"
        android:layout_x="190px"
        android:layout_y="272px"
        android:src="@drawable/vibrate" >
    </ImageButton>
</AbsoluteLayout>
```
程序类分别为：
#### 1、viewHolder
界面上的所有控件和元素都在这里静态声明
```  
import android.media.AudioManager;
import android.widget.ImageButton;
import android.widget.ImageView;
import android.widget.ProgressBar;
public class viewHolder {
	public static ImageButton downButton;
	public static ImageButton upButton;
	public static ImageButton normalButton;
	public static ImageButton muteButton;
	public static ImageButton vibrateButton;
	public static ProgressBar myProgressBar;
	public static ImageView myImageView;
	public static AudioManager audiomanage;
}
```
打开源码中CheckBox.java文件，我们可以看到如下内容：
```  
public class CheckBox extends CompoundButton {
	public CheckBox(Context context) {
		this(context, null);
	}
	public CheckBox(Context context, AttributeSet attrs) {
		this(context, attrs, com.android.internal.R.attr.checkboxStyle);
	}
	public CheckBox(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
}
```
可以看到，CheckBox是继承于CompoundButton，而CompoundButton：
public abstract class CompoundButton extends Button implements Checkable
是Button的一个子类，由此可知，CheckBox也为Button的一个子类。从上面的代码中可以看出，CheckBox的不同之处就是添加了属性checkboxStyle，我们在源码中找到该样式的定义如下：
```  
<style name="Widget.CompoundButton.CheckBox">
	<item name="android:background">@android:drawable/btn_check_label_background</item>
	<item name="android:button">@android:drawable/btn_check</item>
</style>
```
其中，在资源文件中找到btn_check_label_background，该图片是背景透明没有内容的一张图片，因此CheckBox的显示就是由btn_check决定，我们打开btn_check.xml文件，可以看到以下内容：
```  
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!-- Enabled states -->
    <item android:state_checked="true" android:state_window_focused="false
          android:state_enabled="true
          android:drawable="@drawable/btn_check_on" />
    <item android:state_checked="false" android:state_window_focused="false
          android:state_enabled="true
          android:drawable="@drawable/btn_check_off" />
    <item android:state_checked="true" android:state_pressed="true
          android:state_enabled="true
          android:drawable="@drawable/btn_check_on_pressed" />
    <item android:state_checked="false" android:state_pressed="true
          android:state_enabled="true
          android:drawable="@drawable/btn_check_off_pressed" />
    <item android:state_checked="true" android:state_focused="true
          android:state_enabled="true
          android:drawable="@drawable/btn_check_on_selected" />
    <item android:state_checked="false" android:state_focused="true
          android:state_enabled="true
          android:drawable="@drawable/btn_check_off_selected" />
    <item android:state_checked="false
          android:state_enabled="true
          android:drawable="@drawable/btn_check_off" />
    <item android:state_checked="true
          android:state_enabled="true
          android:drawable="@drawable/btn_check_on" />
    <!-- Disabled states -->
    <item android:state_checked="true" android:state_window_focused="false
          android:drawable="@drawable/btn_check_on_disable" />
    <item android:state_checked="false" android:state_window_focused="false
          android:drawable="@drawable/btn_check_off_disable" />
    <item android:state_checked="true" android:state_focused="true
          android:drawable="@drawable/btn_check_on_disable_focused" />
    <item android:state_checked="false" android:state_focused="true
          android:drawable="@drawable/btn_check_off_disable_focused" />
    <item android:state_checked="false" android:drawable="@drawable/btn_check_off_disable" />
    <item android:state_checked="true" android:drawable="@drawable/btn_check_on_disable" />
</selector>
```
每一个item就对应着CheckBox在不同的选择状态下显示的效果，了解该文件的内容后，我们就可以更具自己的需要来设计美观的CheckBox。RadioButton的显示与CheckBox基本相似，可以仿照CheckBox加以修改。
Android中自带的主题(theme)的集锦：
```  
android:theme="@android:style/Theme.Dialog"   将一个Activity显示为对话框模式
android:theme="@android:style/Theme.NoTitleBar"  不显示应用程序标题栏
android:theme="@android:style/Theme.NoTitleBar.Fullscreen"  不显示应用程序标题栏，并全屏
android:theme="@android:style/Theme.Light"  背景为白色
android:theme="@android:style/Theme.Light.NoTitleBar"  白色背景并无标题栏
android:theme="@android:style/Theme.Light.NoTitleBar.Fullscreen"  白色背景，无标题栏，全屏
android:theme="@android:style/Theme.Black"  背景黑色
android:theme="@android:style/Theme.Black.NoTitleBar"  黑色背景并无标题栏
android:theme="@android:style/Theme.Black.NoTitleBar.Fullscreen"    黑色背景，无标题栏，全屏
android:theme="@android:style/Theme.Wallpaper"  用系统桌面为应用程序背景
android:theme="@android:style/Theme.Wallpaper.NoTitleBar"  用系统桌面为应用程序背景，且无标题栏
android:theme="@android:style/Theme.Wallpaper.NoTitleBar.Fullscreen"  用系统桌面为应用程序背景，无标题栏，全屏
android:theme="@android:style/Translucent" 半透明效果
android:theme="@android:style/Theme.Translucent.NoTitleBar"  半透明并无标题栏
android:theme="@android:style/Theme.Translucent.NoTitleBar.Fullscreen"  半透明效果，无标题栏，全屏
android:theme="@android:style/Theme.Panel"
android:theme="@android:style/Theme.Light.Panel"
```
android中系统自带的样式(style)的集锦：
Android平台定义了三种字体大小。
```  
"?android:attr/textAppearanceLarge"
"?android:attr/textAppearanceMedium"
"?android:attr/textAppearanceSmall"
```
使用方法：
```  
android:textAppearance="?android:attr/textAppearanceLarge" 
android:textAppearance="?android:attr/textAppearanceMedium" 
android:textAppearance="?android:attr/textAppearanceSmall"
```
或者
```  
style="?android:attr/textAppearanceLarge" 
style="?android:attr/textAppearanceMedium" 
style="?android:attr/textAppearanceSmall"
```
Android字体颜色
```  
android:textColor="?android:attr/textColorPrimary" 
android:textColor="?android:attr/textColorSecondary" 
android:textColor="?android:attr/textColorTertiary" 
android:textColor="?android:attr/textColorPrimaryInverse" 
android:textColor="?android:attr/textColorSecondaryInverse"
```
AndroidProgressBar
```  
style="?android:attr/progressBarStyleHorizontal" 
style="?android:attr/progressBarStyleLarge" 
style="?android:attr/progressBarStyleSmall" 
style="?android:attr/progressBarStyleSmallTitle"
```
分隔符 横向：
```  
<View 
	android:layout_width="fill_parent"
	android:layout_height="1dip"
	android:background="?android:attr/listDivider" />
```
分隔符 纵向：
```  
<View android:layout_width="1dip" 
	android:layout_height="fill_parent"
	android:background="?android:attr/listDivider" />
```
CheckBox  
```  
style="?android:attr/starStyle"
```
其它  
```  
//类似标题栏效果的TextView 
style="?android:attr/listSeparatorTextViewStyle"
//其它有用的样式 
android:layout_height="?android:attr/listPreferredItemHeight"
android:paddingRight="?android:attr/scrollbarSize"
style="?android:attr/windowTitleBackgroundStyle"
style="?android:attr/windowTitleStyle"
android:layout_height="?android:attr/windowTitleSize"
android:background="?android:attr/windowBackground"
```
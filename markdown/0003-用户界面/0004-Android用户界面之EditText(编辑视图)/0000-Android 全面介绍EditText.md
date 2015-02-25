EditText继承关系：View-->TextView-->EditText。
EditText的属性很多，这里介绍几个：
```  
android:layout_gravity="center_vertical"
设置控件显示的位置：默认top，这里居中显示，还有bottom
android:hint="请输入数字!"
设置显示在空间上的提示信息
android:numeric="integer"
设置只能输入整数，如果是小数则是：decimal
android:singleLine="true"
设置单行输入，一旦设置为true，则文字不会自动换行。
android:password="true"
设置只能输入密码
android:textColor = "ff8c00"
字体颜色
android:textStyle="bold"
字体，bold, italic, bolditalic
android:textSize="20dip"
大小
android:capitalize = "characters"
以大写字母写
android:textAlign="center"
EditText没有这个属性，但TextView有，居中
android:textColorHighlight="cccccc"
被选中文字的底色，默认为蓝色
android:textColorHint="ffff00"
设置提示信息文字的颜色，默认为灰色
android:textScaleX="1.5
控制字与字之间的间距
android:typeface="monospace"
字型，normal, sans, serif, monospace
android:background="@null"
空间背景，这里没有，指透明
android:layout_weight="1"
权重，控制控件之间的地位,在控制控件显示的大小时蛮有用的。
android:textAppearance="?android:attr/textAppearanceLargeInverse"
EditText始终不弹出软件键盘
```
1.EditText默认不弹出软件键盘
#### 方法一：
在 AndroidMainfest.xml中选择哪个activity，设置windowSoftInputMode属性为adjustUnspecified|stateHidden
```  
<activity android:name=".Main"
	android:label="@string/app_name"
	android:windowSoftInputMode="adjustUnspecified|stateHidden"
	android:configChanges="orientation|keyboardHidden">
	<intent-filter>
		<action android:name="android.intent.action.MAIN" />
		<category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>
</activity>
```
#### 方法二：
让 EditText失去焦点，使用EditText的clearFocus方法
例如：
```  
EditText edit=(EditText)findViewById(R.id.edit);
edit.clearFocus();
```
#### 方法三：
强制隐藏Android输入法窗口
例如：
```  
EditText edit=(EditText)findViewById(R.id.edit);
InputMethodManager imm = (InputMethodManager)getSystemService(Context.INPUT_METHOD_SERVICE);
imm.hideSoftInputFromWindow(edit.getWindowToken(),0);
```
2.EditText始终不弹出软件键盘
例：
```  
EditText edit=(EditText)findViewById(R.id.edit);
edit.setInputType(InputType.TYPE_NULL);
```
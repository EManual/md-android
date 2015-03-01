TextView 标签的使用--更改与显示文字标签
①导入TextView包
```  
import android.widget.TextView;
```
②在mainActivity.java中声明一个TextView
```  
private TextView mTextView01;
```
③在main.xml中定义一个TextView
```  
<TextView android:text="TextView01" 
	android:id="@+id/TextView01"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" 
	android:layout_x="61px" 
	android:layout_y="69px">
</TextView>
```
④利用findViewById()方法获取main.xml中的TextView
```  
mTextView01 = (TextView) findViewById(R.id.TextView01);
```
⑤设置TextView标签内容
```  
String str_2 = "欢迎来到Android 的TextView 世界...";
mTextView01.setText(str_2);
```
⑥设置文本超级链接
```  
<TextView 
	android:id="@+id/TextView02"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" 
	android:autoLink="all" 
	android:text="请访问Android 开发者:http://developer.android.com/index.html">
</TextView>
```
代码中更改TextView 文字颜色
①创建工程
②编写布局 main.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" 
    android:layout_width="fill_parent" 
    android:layout_height="fill_parent" 
    android:orientation="vertical" >
    <TextView
        android:layout_width="fill_parent" 
        android:layout_height="wrap_content" 
        android:text="@string/hello" />
    <TextView
        android:id="@+id/TextView01" 
        android:layout_width="wrap_content" 
        android:layout_height="wrap_content" 
        android:text="TextView01" >
    </TextView>
    <TextView
        android:id="@+id/TextView02" 
        android:layout_width="wrap_content" 
        android:layout_height="wrap_content" 
        android:text="这里使用Graphics颜色静态常量" >
    </TextView>
</LinearLayout>
```
③　新加drawable.xml，其中添加一个white颜色值
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="white">ffffffff</color>
</resources>
```
④在代码中由ID获取TextView
```  
TextView text_A=(TextView)findViewById(R.id.TextView01);
TextView text_B=(TextView)findViewById(R.id.TextView02);
```
⑤获取Resources 对象
```  
Resources myColor_R=getBaseContext().getResources();
//getBaseContext()获得基础Context
//getResources()从Context获取资源实例对象
```
⑥ 获取Drawable对象
```  
Drawable myColor_D = myColor_R.getDrawable(R.color.white);
```
⑦设置文本背景颜色
```  
text_A.setBackgroundDrawable(myColor_D);
```
⑧利用android.graphics.Color的颜色静态变量来改变文本颜色
```  
text_A.setTextColor(android.graphics.Color.GREEN);
```
⑨利用Color的静态常量来设置文本颜色
```  
text_B.setTextColor(Color.RED);
```
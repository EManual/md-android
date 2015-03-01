#### 范例说明
在之前的范例运行结果,窗口的底色一律是”深黑色“，这是由于深黑色是SDK默认的颜色，如果更改Activity里的窗口底色有许多方法，最简单的方法就是将颜色色码事先定义在drawable当中，当程序onCreate创建的同时，就可加载预先定义的画面颜色。
此范例程序的设计方式是在drawable里指定的Layout的背景(BackGround)为白色，但这里的“白色”(颜色色码为FFFFFFFF)是预先定义在drawable当中,当程序运行时,背景就会转变为白色。
这里指定Activity Layout背景颜色最简单的方法，在范例最后，则将示范如何创建色彩板(color table)，让Android手机程序可以像使用“常数”般直接取用,并反映在应用程序的运行阶段。
#### 范例程序
res/values/strings.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="hello">Hello World, Demo03_02!</string>
    <string name="app_name">Demo03_02</string>
    <string name="str_id">帐号</string>
    <string name="str_pwd">密码</string>
</resources>
```
res/values/color.xml
```  
<?xml version="1.0" encoding="UTF-8"?>
<resources>
        <drawable name="darkgray">808080FF</drawable>
        <drawable name="white">FFFFFFFF</drawable>
</resources>
```
res/layout/main.xml
在页面布局上，使用了里那个个TextView对象，以及两个EditText对象，关键在于Android:background="@drawable/white"让程序背景变成了白色，而Android:textColor="@drawable/darkgray"将TextView里的文字颜色(textColor)设为灰色，当中"@drawable/white"及"@drawable/darkgray"的写法则是参考事先在drawable里定义好的颜色常数,你可以在res/values/color.xml里看见有关颜色定义的描述。
```  
<?xml version="1.0" encoding="utf-8"?>
<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/widget35"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:background="@drawable/white" >
    <EditText
        android:id="@+id/widget31"
        android:layout_width="120dip"
        android:layout_height="wrap_content"
        android:layout_x="114px"
        android:layout_y="57px"
        android:textSize="18sp" >
    </EditText>
    <TextView
        android:id="@+id/widget28"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="61px"
        android:layout_y="69px"
        android:text="@string/str_id"
        android:textColor="@drawable/darkgray" >
    </TextView>
    <EditText
        android:id="@+id/widget30"
        android:layout_width="120dip"
        android:layout_height="wrap_content"
        android:layout_x="112px"
        android:layout_y="142px"
        android:password="true"
        android:textSize="18sp" >
    </EditText>
    <TextView
        android:id="@+id/widget29"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="61px"
        android:layout_y="158px"
        android:text="@string/str_pwd"
        android:textColor="@drawable/darkgray" >
    </TextView>
</AbsoluteLayout>
```
#### 扩展学习
事先将定义好的颜色代码(color code)以drawable的名字(name)存放在resources当中,这是学习开发Android程序必须养成的好习惯,正如同字符串常数一样,颜色也是可以事先定义好的.在本范例中学会了使用drawable的resource的定义方法:
```  
<drawable name="color_name">color_value</drawable>
```
定义好的drawable name常数,必须存放于res/value下面作为资源使用,但定义好的背景颜色并非只能当作是“默认”颜色来声明适用,在程序的事件里,也是可以通过程序来更改的,如以下程序所示:
```  
Resources resources = getBaseContext().getResourves();
Drawable HippoDrawable = resources.getDrawable(R.drawable.white);
TextView tv = (TextView)findViewById(R.id.text);
tv.setBackground(HippoDrawable);
```
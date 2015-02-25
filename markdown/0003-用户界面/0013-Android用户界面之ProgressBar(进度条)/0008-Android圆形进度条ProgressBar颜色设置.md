xml布局文件需加入如下的进度条构件：
```  
<ProgressBar
	android:id="@+id/loadProgressBar"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:indeterminateDrawable="@drawable/progressbar" />
```
其中的indeterminteDrawable属性就是用来设置进度条颜色等属性的，其内容如下： 
```  
<?xml version="1.0" encoding="utf-8"?>
<rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromDegrees="0"
    android:pivotX="50%"
    android:pivotY="50%"
    android:toDegrees="360" >
    <shape
        android:innerRadiusRatio="3"
        android:shape="ring"
        android:thicknessRatio="8"
        android:useLevel="false" >
        <gradient
            android:centerColor="FFFFFF"
            android:centerY="0.50"
            android:endColor="FFFF00"
            android:startColor="000000"
            android:type="sweep"
            android:useLevel="false" />
    </shape>
</rotate>
```
默认情况下Indeterminate Progressbar是白色的，如果容器的背景也是白色的，这样就根本看不到Progressbar了。 
幸好Android自带了一些反转样式，你可以采用其中一个合适的： 
```  
<ProgressBar style="@android:style/Widget.ProgressBar.Inverse"/> 
<ProgressBar style="@android:style/Widget.ProgressBar.Large.Inverse"/> 
<ProgressBar style="@android:style/Widget.ProgressBar.Small.Inverse"/>
```
进度条: 
```  
<ProgressBar
	style="?android:attr/progressBarStyleHorizontal"
	android:layout_width="fill_parent"
	android:layout_height="wrap_content" />
<ProgressBar
	android:id="@+id/circleProgressBar"
	style="?android:attr/progressBarStyleLarge"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	mce_style="?android:attr/progressBarStyleLarge" />
```
一、通过动画实现 
定义res/anim/loading.xml如下： 
```  
<?xml version="1.0" encoding="UTF-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android
    android:oneshot="false" >
    <item
        android:drawable="@drawable/loading_01"
        android:duration="150"/>
    <item
        android:drawable="@drawable/loading_02"
        android:duration="150"/>
    <item
        android:drawable="@drawable/loading_03"
        android:duration="150"/>
    <item
        android:drawable="@drawable/loading_04"
        android:duration="150"/>
    <item
        android:drawable="@drawable/loading_05"
        android:duration="150"/>
    <item
        android:drawable="@drawable/loading_06"
        android:duration="150"/>
    <item
        android:drawable="@drawable/loading_07"
        android:duration="150"/>
</animation-list>
```
在layout文件中引用如下： 
```  
<ProgressBar
	android:id="@+id/loading_process_dialog_progressBar"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:indeterminate="false
	android:indeterminateDrawable="@anim/loading" />
```
二、通过自定义颜色实现 
定义res/drawable/dialog_style_xml_color.xml如下： 
```  
<?xml version="1.0" encoding="utf-8"?>
<rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromDegrees="0"
    android:pivotX="50%"
    android:pivotY="50%"
    android:toDegrees="360" >
    <shape
        android:innerRadiusRatio="3"
        android:shape="ring"
        android:thicknessRatio="8"
        android:useLevel="false" >
        <gradient
            android:centerColor="FFDC35"
            android:centerY="0.50"
            android:endColor="CE0000"
            android:startColor="FFFFFF"
            android:type="sweep"
            android:useLevel="false" />
    </shape>
</rotate>
```
在layout文件中引用如下： 
```  
<ProgressBar
	android:id="@+id/loading_process_dialog_progressBar"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:indeterminate="false
	android:indeterminateDrawable="@drawable/dialog_style_xml_color" />
```
三、使用一张图片进行自定义 
定义res/drawable/dialog_style_xml_icon.xml如下： 
```  
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item>
        <rotate
            android:drawable="@drawable/dialog_progress_round"
            android:fromDegrees="0.0"
            android:pivotX="50.0%"
            android:pivotY="50.0%"
            android:toDegrees="360.0" />
    </item>
</layer-list>
```
在layout文件中引用如下： 
```  
<ProgressBar
	android:id="@+id/loading_process_dialog_progressBar"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	android:indeterminate="false
	android:indeterminateDrawable="@drawable/dialog_style_xml_icon" />
```
或者 
Java代码
```  
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:id="@android:id/background">
        <shape>
            <corners android:radius="5dip" />
            <gradient
                android:angle="270"
                android:centerColor="ff5a5d5a"
                android:centerY="0.75"
                android:endColor="ff747674"
                android:startColor="ff9d9e9d" />
        </shape>
    </item>
    <item android:id="@android:id/secondaryProgress">
        <clip>
            <shape>
                <corners android:radius="5dip" />
                <gradient
                    android:angle="270"
                    android:centerColor="80ffb600"
                    android:centerY="0.75"
                    android:endColor="a0ffcb00"
                    android:startColor="80ffd300" />
            </shape>
        </clip>
    </item>
    <item android:id="@android:id/progress">
        <clip>
            <shape>
                <corners android:radius="5dip" />
                <gradient
                    android:angle="270"
                    android:endColor="@color/progress_end"
                    android:startColor="@color/progress_start" />
            </shape>
        </clip>
    </item>
</layer-list>
```
代码中设置: 
```  
mProgress = (ProgressBar) findViewById(R.id.progress_bar);
Drawable d = this.getResources().getDrawable(R.drawable.my_progress);
mProgress.setProgressDrawable(d);
```
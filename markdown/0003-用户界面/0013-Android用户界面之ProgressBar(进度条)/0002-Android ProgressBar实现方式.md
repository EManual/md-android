在drawable中创建一张图片progress_bar.xml：
```  
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:id="@android:id/background">
        <shape>
            <corners android:radius="5dip" />
            <gradient
                android:angle="0
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
                    android:angle="0"
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
                    android:angle="0"
                    android:endColor="8000ff00"
                    android:startColor="80ff0000" />
            </shape>
        </clip>
    </item>
</layer-list>
```
java代码： 
```  
<ProgressBar
    android:id="@+id/progressBar1"
    style="?android:attr/progressBarStyleHorizontal"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:max="100"
    android:progress="80"
    android:progressDrawable="@drawable/progress_bar"
    android:secondaryProgress="90" />
```
#### 普通圆形 ProgressBar
![img](http://emanual.github.io/md-android/img/view_progressbar/05_progressbar.jpg) 
该类型进度条也就是一个表示运转的过程，例如发送短信，连接网络等等，表示一个过程正在执行中。
一般只要在 XML 布局中定义就可以了。
```  
<ProgressBar android:id="@+id/widget43"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" android:layout_gravity="center_vertical">
</ProgressBar>
```
#### 超大号圆形 ProgressBar
![img](http://emanual.github.io/md-android/img/view_progressbar/05_progressbar2.jpg)  
此时，给设置一个 style 风格属性后，该 ProgressBar 就有了一个风格，这里大号ProgressBar的风格是：
```  
style="?android:attr/progressBarStyleLarge"
```
完整 XML 定义是：
```  
<ProgressBar android:id="@+id/widget196"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	style="?android:attr/progressBarStyleLarge">
</ProgressBar>
```
#### 小号圆形 ProgressBar
![img](http://emanual.github.io/md-android/img/view_progressbar/05_progressbar3.jpg)  
小号 ProgressBar 对应的风格是：
```  
style="?android:attr/progressBarStyleSmall"
```
完整 XML 定义是：
```  
<ProgressBar android:id="@+id/widget108"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	style="?android:attr/progressBarStyleSmall">
</ProgressBar>
```
#### 标题型圆形 ProgressBar
![img](http://emanual.github.io/md-android/img/view_progressbar/05_progressbar4.jpg) 
标题型 ProgressBar 对应的风格是：
```  
style="?android:attr/progressBarStyleSmallTitle"
```
完整 XML 定义是：
```  
<ProgressBar android:id="@+id/widget110"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content"
	style="?android:attr/progressBarStyleSmallTitle">
</ProgressBar>
```
代码如下：
```  
@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	requestWindowFeature(Window.FEATURE_INDETERMINATE_PROGRESS);
	// 请求窗口特色风格，这里设置成不明确的进度风格
	setContentView(R.layout.second);
	setProgressBarIndeterminateVisibility(true);
	// 设置标题栏中的不明确的进度条是否可以显示
}
```
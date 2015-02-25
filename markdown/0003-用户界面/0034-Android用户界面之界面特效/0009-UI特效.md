#### 一、背景色渐变
背景色渐变可以通过在res/drawable中定义一个XML文件实现，gradient.xml的代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <gradient
        android:angle="45"
        android:endColor="000000"
        android:startColor="FFFFFF" />
</shape>
```
其中，shape是用来定义形状的，gradient定义该形状里面为渐变色填充，startColor起始颜色，endColor结束颜色，angle表示方向角度。当angle=0时，渐变色是从左向右。 然后逆时针方向转，当angle=45时为从左下到右上，当angle=90时为从下往上。
然后，设置Activity的背景为：android:background="@drawable/gradient"，这样即可实现背景色渐变效果。
#### 二、标题栏进度条
在后台线程中执行各种操作（网络连接、大数据存储）的时候，我们希望让客户能看到后台有操作在进行，那么既能有效的提示用户，又不占用当前操作空间，最好的方法就是在标题栏有个进度条。实现的方法很简单，代码如下：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	// 先给Activity注册界面进度条功能
	requestWindowFeature(Window.FEATURE_INDETERMINATE_PROGRESS);
	setContentView(R.layout.main);
	// 在需要显示进度条的时候调用这个方法
	setProgressBarIndeterminateVisibility(true);
	// 在不需要显示进度条的时候调用这个方法 //
	setProgressBarIndeterminateVisibility(false);
	setContentView(R.layout.main);
}
```
#### 三、界面边框圆角
界面边框圆角的实现方式同样是在res/drawable中定义一个XML文件，corners.xml的代码如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <solid android:color="FF9900" />
    <corners
        android:bottomLeftRadius="10dp"
        android:bottomRightRadius="10dp"
        android:topLeftRadius="10dp"
        android:topRightRadius="10dp" />
</shape>
```
其中，solid的表示填充颜色，而corners则是表示圆角，注意的是这里bottomRightRadius是左下角而不是右下角，bottomLeftRadius右下角。
然后，在Activity中设置背景为上面的xml，android:background="@drawable/corners"，这样即可实现边框圆角。
效果如下：
![img](P)  
![img](P)  
![img](P)  
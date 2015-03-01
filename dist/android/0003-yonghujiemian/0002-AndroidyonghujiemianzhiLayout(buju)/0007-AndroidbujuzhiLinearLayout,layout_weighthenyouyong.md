layout_weight这个参数很有用，在LinearLayout布局中可以通过这个调整各个组件占的面积的权重，如下是一个例子
```  
<?xml version="1.0" encoding="utf-8"?>
<!--
   <LinearLayout>
       线性版面配置，在这个标签中，所有元件都是按由上到下的排队排成的
-->
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <!--
	android:orientation="vertical" 表示竖直方式对齐
	android:orientation="horizontal"表示水平方式对齐
	android:layout_width="fill_parent"定义当前视图在屏幕上
				 可以消费的宽度，fill_parent即填充整个屏幕。
	android:layout_height="wrap_content":随着文字栏位的不同
	而改变这个视图的宽度或者高度。有点自动设置框度或者高度的意思
	layout_weight 用于给一个线性布局中的诸多视图的重要度赋值。
	所有的视图都有一个layout_weight值，默认为零，意思是需要显示
	多大的视图就占据多大的屏幕空 间。若赋一个高于零的值，则将父视
	图中的可用空间分割，分割大小具体取决于每一个视图的layout_weight
	值以及该值在当前屏幕布局的整体 layout_weight值和在其它视图屏幕布
	局的layout_weight值中所占的比率而定。
	举个例子：比如说我们在 水平方向上有一个文本标签和两个文本编辑元素。
    该文本标签并无指定layout_weight值，所以它将占据需要提供的最少空间。
    如果两个文本编辑元素每一个的layout_weight值都设置为1，则两者平分
    在父视图布局剩余的宽度(因为我们声明这两者的重要度相等)。如果两个
	文本编辑元素其中第一个的layout_weight值设置为1，而第二个的设置为2，
	则剩余空间的三分之二分给第一个，三分之一分给第二个(数值越小，重要度越高)。
    -->
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_weight="1"
        android:orientation="horizontal" >
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="fill_parent"
            android:layout_weight="1"
            android:background="aa0000"
            android:gravity="center_horizontal"
            android:text="red" />
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="fill_parent"
            android:layout_weight="1"
            android:background="00aa00"
            android:gravity="center_horizontal"
            android:text="green" />
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="fill_parent"
            android:layout_weight="1"
            android:background="0000aa"
            android:gravity="center_horizontal"
            android:text="blue" />
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="fill_parent"
            android:layout_weight="1"
            android:background="aaaa00"
            android:gravity="center_horizontal"
            android:text="yellow" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:layout_weight="2"
        android:orientation="vertical" >
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="row one"
            android:textSize="15pt" />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="row two"
            android:textSize="15pt" />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="row three"
            android:textSize="15pt" />
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="row four"
            android:textSize="15pt" />
    </LinearLayout>
</LinearLayout>
```
代码如下：
```  
import android.app.Activity;
import android.os.Bundle;
public class Views extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
	}
}
```
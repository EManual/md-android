#### LinerLayout线性布局：
这种布局方式是指在这个里面的控件元素显线性，我们可以通过setOrientation(int orientation)来指定线性布局的显示方式，其值有：HORIZONTAL(0)、VERTICAL(1).默认为HORIZONTAL.与之相关的我们也可以在布局文件中通过android：orientation来指定。同理，其值也有：horizontal、vertical。
LinearLayout是线性布局控件，它包含的子控件将以横向或竖向的方式排列，按照相对位置来排列所有的widgets或者其他的containers，超过边界时，某些控件将缺失或消失，不能完全显示.因此垂直方式排列时，每一行只会有一个widget或者是container，而不管他们有多宽，而水平方式排列是将会只有一个行高(高度为最高子控件的高度加上边框高度)。LinearLayout保持其所包含的widget或者是container之间的间隔以及互相对齐(相对一个控件的右对齐、中间对齐或者左对齐)。
#### 关于layout_weight：
LinearLayout还支持为其包含的widget或者是container指定填充权值。允许其包含的widget或者是container可以填充屏幕上的剩余空间。剩余的空间会按这些widgets或者是containers指定的权值比例分配屏幕。默认的weight值为0，表示按照widgets或者是containers实际大小来显示，若高于0的值，则将Container剩余可用空间分割，分割大小具体取决于每一个widget或者是 container的layout_weight及该权值在所有widgets或者是containers中的比例。例如，如果有三个文本框，前两个文本框的取值一个为2，一个为1，显示第三个文本框后剩余的空间的2/3给权值为2的，1/3大小给权值为1的。而第三个文本框不会放大，按实际大小来显示。也就是权值越大，重要度越大，显示时所占的剩余空间越大。
示例1：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <EditText
        android:id="@+id/txt01"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="1111" />
    <EditText
        android:id="@+id/txt02"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_weight="2"
        android:text="2222" />
    <EditText
        android:id="@+id/txt03"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="3333" />
</LinearLayout>
```

![img](http://emanual.github.io/md-android/img/view_layout/07_layout.jpg) 

几个常用的XML属性的详解：
```  
属性名称	相关方法	描述
android：baselineAligned	setBaselineAligned (boolean baselineAligned)	是否允许用户调整它内容的基线。
android：baselineAlignedChildIndex	setBaselineAlignedChildIndex (int i)	是当前LinearLayout与其它View的对齐方式
android：gravity	setGravity (int gravity)	指定控件中内容的基本内容的对齐方式(本元素里的所有元素的重力方向)。
其值有：top、bottom、left、right、center_vertical、fill_vertical、center_horizontal、fill_horizontal、center、fill、clip_vertical、clip_horizontal
android：layout_gravity	是当前元素相对于父元素的重力方向
android：measureWithLargestChild	当被设置为真时，所有的子控件将被认为是具有重量最小面积最大的子控件
android：orientation	setOrientation (int orientation)	置它内容的对其方向，有两个可以选择的值：horizontal和vertical.分别表示水平排列和垂直排列。
android：weightSum	在Android里我们可以通过两种方式来设置布局文件，一种是可以通过XML文件来设置布局，这也是官方推荐，另外一种方式就是我们可以通过代码来设置我们的布局模式
```
方式一：通过XML文件.只要在onCreate()方法里通过setContentView()指定布局文件即可。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
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
        android:layout_weight="1"
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
方式二：代码方式
LinerLayout类的常用方法及常量
```  
方法及常量	类型	描述
public static final int HORIZONTAL	常量	设置水平对齐
public static final int VERTICAL	常量	设置垂直对齐
public LinerLayout(Context context)	构造方法	创建LinerLayout类的对象
public void addView(View child， ViewGroup.LayoutParams params)	普通方法	增加组组件并且指定布局参数
public void addView(View childView)	普通方法	增加组件
public void setOrientation(int orientaiton)	普通方法	设置对齐方式
```
LinerLayout.LayoutParams用于指定线性布局的参数
类结构图：
```  
java.lang.Object
android.view.ViewGroup.LayoutParams
android.view.ViewGroup.MarginLayoutParams
android.widget.LinearLayout.LayoutParams
```
常用布局参数：
```  
import android.app.Activity;
import android.os.Bundle;
import android.view.ViewGroup;
import android.widget.LinearLayout;
import android.widget.TextView;
 /**
  * 动态设置布局
  */
public class Dyanmic_Layout_Activity extends Activity {
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// 定义线性布局管理器
		LinearLayout layout = new LinearLayout(this);
		// 定义布局管理器的指定宽和高
		LinearLayout.LayoutParams params = new LinearLayout.LayoutParams(
				ViewGroup.LayoutParams.FILL_PARENT,
				ViewGroup.LayoutParams.FILL_PARENT);
		layout.setOrientation(LinearLayout.VERTICAL);
		// 定义要显示组件的布局管理器
		LinearLayout.LayoutParams txtParam = new LinearLayout.LayoutParams(
				ViewGroup.LayoutParams.FILL_PARENT,
				ViewGroup.LayoutParams.WRAP_CONTENT);
		TextView textView = new TextView(this);
		// 显示的文字
		textView.setText("动态设置布局增加的TextView组件");
		// 设置文本的参数
		textView.setLayoutParams(txtParam);
		// 增加组件
		layout.addView(textView, txtParam);
		// 增加新的布局管理器
		super.setContentView(layout, params);
	}
}
```
实现效果
![img](http://emanual.github.io/md-android/img/view_layout/07_layout2.jpg)  
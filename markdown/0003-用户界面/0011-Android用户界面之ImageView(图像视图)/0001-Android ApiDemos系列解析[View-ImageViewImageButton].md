今天来讲一下两个经典的图像组件：ImageButton和ImageView。
第一讲：ImageView
ImageView layout 文件 位于ApiDemos 的位置： ApiDemos/res/layout/image_view_1.xml，源码为：
```  
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical" >
        <!-- The following four examples use a large image -->
        <!-- 1. Non-scaled view, for reference -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_large_normal" />
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true
            android:src="@drawable/sample_1" />
        <!-- 2. Limit to at most 50x50 -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_large_at_most" />
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true
            android:maxHeight="50dip"
            android:maxWidth="50dip"
            android:src="@drawable/sample_1" />
        <!-- 3. Limit to at most 70x70, with 10 pixels of padding all around -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_large_at_most_padded" />
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true
            android:background="66FFFFFF"
            android:maxHeight="70dip"
            android:maxWidth="70dip"
            android:padding="10dip"
            android:src="@drawable/sample_1" />
        <!-- 4. Limit to exactly 70x70, with 10 pixels of padding all around -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_large_exactly_padded" />
        <ImageView
            android:layout_width="70dip"
            android:layout_height="70dip"
            android:background="66FFFFFF"
            android:padding="10dip"
            android:scaleType="centerInside"
            android:src="@drawable/sample_1" />
        <!-- Repeating the previous four examples with small image -->
        <!-- 1. Non-scaled view, for reference -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_small_normal" />
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true"
            android:background="FFFFFFFF"
            android:src="@drawable/stat_happy" />
        <!-- 2. Limit to at most 50x50 -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_small_at_most" />
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true
            android:background="FFFFFFFF"
            android:maxHeight="50dip"
            android:maxWidth="50dip"
            android:src="@drawable/stat_happy" />
        <!-- 3. Limit to at most 70x70, with 10 pixels of padding all around -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_small_at_most_padded" />
        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:adjustViewBounds="true
            android:background="FFFFFFFF"
            android:maxHeight="70dip"
            android:maxWidth="70dip"
            android:padding="10dip"
            android:src="@drawable/stat_happy" />
        <!-- 4. Limit to exactly 70x70, with 10 pixels of padding all around -->
        <TextView
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:paddingTop="10dip"
            android:text="@string/image_view_small_exactly_padded" />
        <ImageView
            android:layout_width="70dip"
            android:layout_height="70dip"
            android:background="FFFFFFFF"
            android:padding="10dip"
            android:scaleType="centerInside"
            android:src="@drawable/stat_happy" />
    </LinearLayout>
</ScrollView>
```
ScrollView 子节点只允许一个View 视图，如果有多于一个子节点将会报错；
```  
android:paddingTop 与上节点边距的填充；
android:adjustViewBounds 如果设置为true图像将自动调整自己的宽主；
android:maxWidth 设置图像的最大高；
android:maxHeight 设置图像的最大高；
android:scaleType  控制如何调整图像大小或者移动范围，以配合ImageView 的大小。下面是 scaleType 所支持的类型：
```
![img](P)  
ImageView java 文件 位于ApiDemos 的位置： ApiDemos/src/com.example.android.apis.view/ImageView1
源码为：
```  
import android.app.Activity;
import android.os.Bundle;
import com.example.android.apis.R;
public class ImageView1 extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.image_view_1);
	}
}
```
在这里，无须多讲此类。
运行效果图如下：
![img](P)  
扩展 LinearLayout
看上图效果和代码，ImgeView 始终跟着一个TextView ，从代码角度上有点不雅观，那如何改变它呢？一步一步跟着我做吧。
1、第一步，新建一个包
com.terry.Ext 做为组件扩展包
2、第二步，新建一个类
imageViewExt.java 做为组件扩展类
3、第三步，使此类继承 LinearLayout 布局
public class imageViewExt extends LinearLayout
4、第四步，在构造函数中通过参数判断传进来的值做逻辑，具体代码后如下：
```  
import android.content.Context;
import android.graphics.drawable.Drawable;
import android.util.AttributeSet;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;
public class imageViewExt extends LinearLayout {
	private TextView tv;
	private String labelText;
	private int FontSize;
	private String Position;
	private Drawable bitmap;
	private ImageView iv;
	public imageViewExt(Context context, AttributeSet attrs) {
		super(context, attrs);
		int resourceid;
		resourceid = attrs.getAttributeResourceValue(null, "labelText", 0);
		if (resourceid == 0) {
			labelText = attrs.getAttributeValue(null, "labelText");
		} else {
			labelText = getResources().getString(resourceid);
		}
		if (labelText == null) {
			labelText = "默认";
		}
		resourceid = attrs.getAttributeResourceValue(null, "imgSrc", 0);
		if (resourceid > 0) {
			bitmap = getResources().getDrawable(resourceid);
		} else {
			throw new RuntimeException("图像资源为空");
		}
		resourceid = attrs.getAttributeResourceValue(null, "FontSize", 0);
		if (resourceid == 0) {
			FontSize = attrs.getAttributeIntValue(null, "FontSize", 12);
		} else {
			FontSize = getResources().getInteger(resourceid);
		}
	}
}
```
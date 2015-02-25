主要代码是设置一下Tabwidget的orientation以及重写TabWidget里的addview方法 ，TabHost的主要结构如下
![img](P)  
所以要想让TabSpec的头（spec的Indicator）竖直排列也就需要我们把TabWidget的排列方式设成Vertical的然后Tabwidget与TabSpec的content部分横着排列，而TabWidget继承自LinearLayout，所以原本想在布局文件中直接加android:orientation="horizontal"可是悲剧的是失败了，究其原因是因为在源码中TabWidget在initTabWidget中又做了一次setOrientation(LinearLayout.HORIZONTAL);的初始化，所以最后决定重写Tabwidget部分。代码部分如下：
布局文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TabHost
        android:id="@android:id/tabhost"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:orientation="horizontal"
            android:padding="5dp" >
            <com.gw.android.VerticalTabWigdet
                android:id="@android:id/tabs"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content" />
            <FrameLayout
                android:id="@android:id/tabcontent"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:padding="5dp" />
        </LinearLayout>
    </TabHost>
</LinearLayout>
```
重写的TabWidget代码：
```  
public class VerticalTabWigdet extends TabWidget {
	Resources res;
	public VerticalTabWigdet(Context context, AttributeSet attrs) {
		super(context, attrs);
		res = context.getResources();
		setOrientation(LinearLayout.VERTICAL);
	}
	@Override
	public void addView(View child) {
		LinearLayout.LayoutParams lp = new LayoutParams(
		LayoutParams.FILL_PARENT, LayoutParams.WRAP_CONTENT, 1.0f);
		lp.setMargins(0, 0, 0, 0);
		child.setLayoutParams(lp);
		super.addView(child);
		child.setBackgroundDrawable(res.getDrawable(R.drawable.vertical_tab_selector));
	}
}
```
效果图：
![img](P)  
![img](P)  
前些日子在开发中用到了需要ScrollView嵌套GridView的情况，由于这两款控件都自带滚动条，当他们碰到一起的时候便会出问题，即GridView会显示不全。
解决办法，自定义一个GridView控件
```  
public class MyGridView extends GridView {
	public MyGridView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	public MyGridView(Context context) {
		super(context);
	}
	public MyGridView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
	}
	@Override
	public void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE >> 2, MeasureSpec.AT_MOST);
		super.onMeasure(widthMeasureSpec, expandSpec);
	}
}
```
该自定义控件只是重写了GridView的onMeasure方法，使其不会出现滚动条，ScrollView嵌套ListView也是同样的道理，不再赘述。
XML布局代码
```  
<ScrollView android:layout_height="wrap_content" 
	android:layout_width="fill_parent" android:id="@+id/scroll_content">
	<com.yourclass.MyGridView xmlns:android="http://schemas.android.com/apk/res/android" 
		android:id="@+id/grid_view" android:layout_width="fill_parent"
		android:layout_height="wrap_content" android:numColumns="auto_fit"
		android:horizontalSpacing="1dip" android:verticalSpacing="1dip"
		android:columnWidth="150dip" android:stretchMode="columnWidth"
		android:gravity="center">
	</com.yourclass.MyGridView>
</ScrollView>
```
Java调用代码
```  
MyGridView gridview = (MyGridView) findViewById(R.id.grid_view); 
gridview.setAdapter(new ImageAdapter(this));
```
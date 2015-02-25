ListView与ScrollView同在一界面会导致ListView显示变形，因为ListView也有自带的滚动事件，故无法与ScrollView相容，可能造成的现象是ListView只能显示一行或者两行，其他数据在那一点儿宽的地方做滚动，甚不雅观。
下面是我的一个实现 步骤：
1、继承LinearLayout，既然会冲突那就不用ListView 改成线性布局做动态布局效果。
2、继承BaseAdapter ，可以参照一下Android app源码中 Widget 目录下的SimpleAdapter 为前面扩展的LinearLayout做数据。
3、模拟数据填充扩展后的BaseAdapter 为扩展后的LinearLayout 加载数据。
第一步：新建LinearLayoutForListView 类使其扩展LinearLayout重写以下两个方法：
```  
public LinearLayoutForListView(Context context) {
	super(context);
}
public LinearLayoutForListView(Context context, AttributeSet attrs) {
	super(context, attrs);
}
```
这两个方法可选，不过建议都写上，第一个方法可以让我们通过 编程的方式 实例化出来，第二个方法可以允许我们通过 XML的方式注册 控件，可以在第二个方法里面为扩展的复合组件加属性，详细使用方法请点击这里 。
为其添加get/set 方法
```  
[Tags]/**
 [Tags]* 获取Adapter
 [Tags]* @return adapter
 [Tags]*/
public AdapterForLinearLayout getAdpater() {
	return adapter;
}
[Tags]/**
 [Tags]* 设置数据
 [Tags]* @param adpater
 [Tags]*/
public void setAdapter(AdapterForLinearLayout adpater) {
	this.adapter = adpater;
	bindLinearLayout();
}
[Tags]/**
 [Tags]* 获取点击事件
 [Tags]*/
public OnClickListener getOnclickListner() {
	return onClickListener;
}
[Tags]/**
 [Tags]* 设置点击事件
 [Tags]* @param onClickListener
 [Tags]*/
public void setOnclickLinstener(OnClickListener onClickListener) {
	this.onClickListener = onClickListener;
}
```
第二步：新建AdapterForLinearLayout 类继承自BaseAdapter，并为其添加构造函数
```  
private LayoutInflater mInflater;
private int resource;
private List<? extends Map<String, ?>> data;
private String[] from;
private int[] to;

public AdapterForLinearLayout(Context context,
		List<? extends Map<String, ?>> data, int resouce, String[] from,
		int[] to) {
	this.data = data;
	this.resource = resouce;
	this.data = data;
	this.from = from;
	this.to = to;
	this.mInflater = (LayoutInflater) context
			.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
}
```
此构造函数模仿 simpleAdapter 通过传进来的resouce 为布局设置数据。通过继承BaseAdapter 重要的实现方法在下面getView ，此方法判断通过传进来的 String[] from 与 int[] to 为分别查找出View 并为View 设置相应的Text，代码如下：
```  
@Override
public View getView(int position, View convertView, ViewGroup parent) {
	convertView = mInflater.inflate(resource, null);
	Map<String, ?> item = data.get(position);
	int count = to.length;
	for (int i = 0; i < count; i++) {
		View v = convertView.findViewById(to[i]);
		bindView(v, item, from[i]);
	}
	convertView.setTag(position);
	return convertView;
}
[Tags]/**
 [Tags]* 绑定视图
 [Tags]* @param view
 [Tags]* @param item
 [Tags]* @param from
 [Tags]*/
private void bindView(View view, Map<String, ?> item, String from) {
	Object data = item.get(from);
	if (view instanceof TextView) {
		((TextView) view).setText(data == null ? "" : data.toString());
	}
}
```
Tip:
BindView 方法是一个自定义方法，在方法体内可以为通过判断使本类更具灵活性，如上，你不仅可以判断是TextView 并且可以传入任何你想要的View 只要在方法体内加入相应判断即可，数据可以通过data 做相应处理，具体如何操作读者可另行测试。
convertView.setTag(position); 此句代码为View 设置tag 在以后我们可以通过 getTag 找出下标，后文有介绍如何通过下标操作数据。
下面是两个类的全部代码，读者可以无须更改直接使用：
LinearLayoutForListView
```  
import android.content.Context;
import android.util.AttributeSet;
import android.util.Log;
import android.view.View;
import android.widget.LinearLayout;
public class LinearLayoutForListView extends LinearLayout {
	private AdapterForLinearLayout adapter;
	private OnClickListener onClickListener = null;
	[Tags]/**
	 [Tags]* 绑定布局
	 [Tags]*/
	public void bindLinearLayout() {
		int count = adapter.getCount();
		for (int i = 0; i < count; i++) {
			View v = adapter.getView(i, null, null);
			v.setOnClickListener(this.onClickListener);
			if (i == count - 1) {
				LinearLayout ly = (LinearLayout) v;
				ly.removeViewAt(2);
			}
			addView(v, i);
		}
		Log.v("countTAG", "" + count);
	}
	public LinearLayoutForListView(Context context) {
		super(context);
	}
	public LinearLayoutForListView(Context context, AttributeSet attrs) {
		super(context, attrs);
	}
	[Tags]/**
	 [Tags]* 获取Adapter
	 [Tags]* @return adapter
	 [Tags]*/
	public AdapterForLinearLayout getAdpater() {
		return adapter;
	}
	[Tags]/**
	 [Tags]* 设置数据
	 [Tags]* @param adpater
	 [Tags]*/
	public void setAdapter(AdapterForLinearLayout adpater) {
		this.adapter = adpater;
		bindLinearLayout();
	}
	[Tags]/**
	 [Tags]* 获取点击事件
	 [Tags]*/
	public OnClickListener getOnclickListner() {
		return onClickListener;
	}
	[Tags]/**
	 [Tags]* 设置点击事件
	 [Tags]* @param onClickListener
	 [Tags]*/
	public void setOnclickLinstener(OnClickListener onClickListener) {
		this.onClickListener = onClickListener;
	}
}
```
AdapterForLinearLayout
```  
import java.util.List;
import java.util.Map;
import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.TextView;
public class AdapterForLinearLayout extends BaseAdapter {
	private LayoutInflater mInflater;
	private int resource;
	private List<? extends Map<String, ?>> data;
	private String[] from;
	private int[] to;
	public AdapterForLinearLayout(Context context,
			List<? extends Map<String, ?>> data, int resouce, String[] from,
			int[] to) {
		this.data = data;
		this.resource = resouce;
		this.data = data;
		this.from = from;
		this.to = to;
		this.mInflater = (LayoutInflater) context
				.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
	}
	@Override
	public int getCount() {
		return data.size();
	}
	@Override
	public Object getItem(int position) {
		return data.get(position);
	}
	@SuppressWarnings("unchecked")
	public String get(int position, Object key) {
		Map<String, ?> map = (Map<String, ?>) getItem(position);
		return map.get(key).toString();
	}
	@Override
	public long getItemId(int position) {
		return position;
	}
	@Override
	public View getView(int position, View convertView, ViewGroup parent) {
		convertView = mInflater.inflate(resource, null);
		Map<String, ?> item = data.get(position);
		int count = to.length;
		for (int i = 0; i < count; i++) {
			View v = convertView.findViewById(to[i]);
			bindView(v, item, from[i]);
		}
		convertView.setTag(position);
		return convertView;
	}
	[Tags]/**
	 [Tags]* 绑定视图
	 [Tags]* @param view
	 [Tags]* @param item
	 [Tags]* @param from
	 [Tags]*/
	private void bindView(View view, Map<String, ?> item, String from) {
		Object data = item.get(from);
		if (view instanceof TextView) {
			((TextView) view).setText(data == null ? "" : data.toString());
		}
	}
}
```
对应的XML 如下：
```  
<?xml version="1.0" encoding="UTF-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical" android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView android:id="@+id/TextView01"
        android:layout_marginLeft="10px" 
		android:textAppearance="?android:attr/textAppearanceLarge"
        android:layout_width="wrap_content" android:layout_height="wrap_content">
    </TextView>
    <TextView android:id="@+id/TextView02" android:layout_width="wrap_content"
        android:textAppearance="?android:attr/textAppearanceSmall"
        android:layout_marginLeft="10px" android:layout_height="wrap_content">
    </TextView>
    <View android:layout_height="1px" android:background="FFFFFF"
        android:layout_width="fill_parent"></View>
</LinearLayout>
```
第三步：主页面使用控件并为其设置数据
* XML如下：
```  
<com.terry.widget.LinearLayoutForListView
    android:id="@+id/ListView01"
    android:layout_width="450px"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
</com.terry.widget.LinearLayoutForListView>
```
* 加载数据如下：
```  
lv = (LinearLayoutForListView) findViewById(R.id.ListView01);
for (int i = 0; i < 10; i++) {
	HashMap<String, Object> map = new HashMap<String, Object>();
	map.put("key_name", "name" + i);
	map.put("value_name", "value" + i);
	list.add(map);
}
final AdapterForLinearLayout Layoutadpater = new AdapterForLinearLayout(
		this, list, R.layout.test, new String[] { "key_name",
				"value_name" }, new int[] { R.id.TextView01,
				R.id.TextView02 });
```
* 事件操作，并通过下标得到数据源：
```  
lv.setOnclickLinstener(new OnClickListener() {
	@Override
	public void onClick(View v) {
	  // TODO Auto-generated method stub
	  Toast.makeText(
			  BlueToothActivity.this,
			  Layoutadpater.get(Integer.parseInt(v.getTag()
					  .toString()), "key_name"), 1000).show();
	}
});
lv.setAdapter(Layoutadpater);
```
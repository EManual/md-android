
PackageManager是个非常好的东西，其他的详细的细节等日后有时间整理
PackageManager的功能：
```  
1、安装，卸载应用
2、查询permission相关信息
3、查询Application相关信息(application，activity，receiver，service，provider及相应属性等）
4、查询已安装应用
5、增加，删除permission
6、清除用户数据、缓存，代码段等
```
我们可以用PackageManager来显示系统安装的应用程序列表或者系统程序列表
```  
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import android.app.Activity;
import android.content.Context;
import android.content.pm.PackageInfo;
import android.content.pm.PackageManager;
import android.graphics.drawable.Drawable;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.ListView;
import android.widget.SimpleAdapter;
import android.widget.TextView;
owActivity extends Activity {
	ListView lv;
	MyAdapter adapter;
	ArrayList<HashMap<String, Object>> items = new ArrayList<HashMap<String, Object>>();
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		lv = (ListView) findViewById(R.id.lv);
		// 得到PackageManager对象
		PackageManager pm = getPackageManager();
		// 得到系统安装的所有程序包的PackageInfo对象
		// List<ApplicationInfo> packs = pm.getInstalledApplications(0);
		List<PackageInfo> packs = pm.getInstalledPackages(0);
		for (PackageInfo pi : packs) {
			HashMap<String, Object> map = new HashMap<String, Object>();
			// 显示用户安装的应用程序，而不显示系统程序
			// if((pi.applicationInfo.flags&ApplicationInfo.FLAG_SYSTEM)==0&&
			// (pi.applicationInfo.flags&ApplicationInfo.FLAG_UPDATED_SYSTEM_APP)==0)
			// {
			// //这将会显示所有安装的应用程序，包括系统应用程序
			// map.put("icon", pi.applicationInfo.loadIcon(pm));//图标
			// map.put("appName", pi.applicationInfo.loadLabel(pm));//应用程序名称
			// map.put("packageName", pi.applicationInfo.packageName);//应用程序包名
			// //循环读取并存到HashMap中，再增加到ArrayList上，一个HashMap就是一项
			// items.add(map);
			// }
			// 这将会显示所有安装的应用程序，包括系统应用程序
			map.put("icon", pi.applicationInfo.loadIcon(pm));// 图标
			map.put("appName", pi.applicationInfo.loadLabel(pm));// 应用程序名称
			map.put("packageName", pi.applicationInfo.packageName);// 应用程序包名
			// 循环读取并存到HashMap中，再增加到ArrayList上，一个HashMap就是一项
			items.add(map);
		}
		 /**
		  * 参数：Context ArrayList(item的集合) item的layout 包含ArrayList中的HashMap的key的数组
		  * key所对应的值的相应的控件id
		  */
		adapter = new MyAdapter(this, items, R.layout.piitem, new String[] {
				"icon", "appName", "packageName" }, new int[] { R.id.icon,
				R.id.appName, R.id.packageName });
		lv.setAdapter(adapter);
	}
}
class MyAdapter extends SimpleAdapter {
	private int[] appTo;
	private String[] appFrom;
	private ViewBinder appViewBinder;
	private List<? extends Map<String, ?>> appData;
	private int appResource;
	private LayoutInflater appInflater;
	public MyAdapter(Context context, List<? extends Map<String, ?>> data,
			int resource, String[] from, int[] to) {
		super(context, data, resource, from, to);
		appData = data;
		appResource = resource;
		appFrom = from;
		appTo = to;
		appInflater = (LayoutInflater) context
				.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
	}
	public View getView(int position, View convertView, ViewGroup parent) {
		return createViewFromResource(position, convertView, parent,
				appResource);
	}
	private View createViewFromResource(int position, View convertView,
			ViewGroup parent, int resource) {
		View v;
		if (convertView == null) {
			v = appInflater.inflate(resource, parent, false);
			final int[] to = appTo;
			final int count = to.length;
			final View[] holder = new View[count];
			for (int i = 0; i < count; i++) {
				holder[i] = v.findViewById(to[i]);
			}
			v.setTag(holder);
		} else {
			v = convertView;
		}
		bindView(position, v);
		return v;
	}
	private void bindView(int position, View view) {
		final Map dataSet = appData.get(position);
		if (dataSet == null) {
			return;
		}
		final ViewBinder binder = appViewBinder;
		final View[] holder = (View[]) view.getTag();
		final String[] from = appFrom;
		final int[] to = appTo;
		final int count = to.length;
		for (int i = 0; i < count; i++) {
			final View v = holder[i];
			if (v != null) {
				final Object data = dataSet.get(from[i]);
				String text = data == null ? "" : data.toString();
				if (text == null) {
					text = "";
				}
				boolean bound = false;
				if (binder != null) {
					bound = binder.setViewValue(v, data, text);
				}
				if (!bound) {
					 /**
					  * 自定义适配器，关在在这里，根据传递过来的控件以及值的数据类型，
					  * 执行相应的方法，可以根据自己需要自行添加if语句。另外，CheckBox等
					  * 集成自TextView的控件也会被识别成TextView，这就需要判断值的类型
					  */
					if (v instanceof TextView) {
						// 如果是TextView控件，则调用SimpleAdapter自带的方法，设置文本
						setViewText((TextView) v, text);
					} else if (v instanceof ImageView) {
						// 如果是ImageView控件，调用自己写的方法，设置图片
						setViewImage((ImageView) v, (Drawable) data);
					} else {
						throw new IllegalStateException(
								v.getClass().getName()
										+ " is not a
										+ "view that can be bounds by this SimpleAdapter");
					}
				}
			}
		}
	}
	public void setViewImage(ImageView v, Drawable value) {
		v.setImageDrawable(value);
	}
}
```
main.xml 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <ListView
        android:id="@+id/lv"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
    </ListView>
</LinearLayout>
```
piitem.xml 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="horizontal" >
    <ImageView
        android:id="@+id/icon"
        android:layout_width="48dip"
        android:layout_height="48dip" />
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical" >
        <TextView
            android:id="@+id/appName"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" />
        <TextView
            android:id="@+id/packageName"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content" />
    </LinearLayout>
</LinearLayout>
```
![img](http://emanual.github.io/md-android/img/device_info/07_manager.jpg) 
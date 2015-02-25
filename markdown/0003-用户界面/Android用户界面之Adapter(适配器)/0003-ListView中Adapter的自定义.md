虽然android类库给我们提供了很多与ListView适配的Adapter并且使用起来非常方便，但是想要开发出美观的程序，自带的是不够的所以我们要重写Adapter。
1.继承BaseAdapter类。
2.重写getView()--每一项显示成什么样有它决定。
3.重写getCount()--一共有多少项由它决定。
4.实现OnItemClickListener事件--为每一项添加事件实现onItemClick(AdapterView<?> arg0, View arg1, int arg2, long arg3)事件。
下面是一个简单的例子：
![img](P)  
```  
import java.util.ArrayList;
import java.util.List;
import android.app.Activity;
import android.app.AlertDialog;
import android.content.Context;
import android.os.Bundle;
import android.text.Html;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AdapterView;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.BaseAdapter;
import android.widget.LinearLayout;
import android.widget.ListView;
import android.widget.RatingBar;
import android.widget.TextView;
public class Default extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.deaultxml);
		List<String> list = new ArrayList<String>();
		list.add("aa");
		list.add("bb");
		// 得到想要填充的ListView
		ListView lv = (ListView) findViewById(R.id.lvjokes);
		RatingAdapter adapter = new RatingAdapter(this, list);
		// 此处必须为通过上面这种方式new的RatingAdapter否则listInner会为空
		// 为ListView添加事件
		lv.setOnItemClickListener(adapter);
		lv.setAdapter(adapter);
	}
	// 定义一个内部类继承BaseAdapter类并实现OnItemClickLister接口
	class RatingAdapter extends BaseAdapter implements OnItemClickListener {
		LayoutInflater layoutInflater;
		String inflater = Context.LAYOUT_INFLATER_SERVICE;
		private List<String> listInner = null;
		public RatingAdapter(Context context) {
		}
		public RatingAdapter(Context context, List<String> list) {
			layoutInflater = (LayoutInflater) context
					.getSystemService(inflater);
			this.listInner = list;
		}
		public int getCount() {
			// 通过此项决定ListView一共有多少Item
			return listInner.size();
		}
		public Object getItem(int arg0) {
			return listInner.get(arg0);
		}
		public long getItemId(int arg0) {
			return arg0;
		}
		// 为每一项中的控件赋值,每一项显示的数据有它决定
		public View getView(int arg0, View arg1, ViewGroup arg2) {
			// 得到模板布局文件对象 ,并为其中的控件赋值
			LinearLayout linearLayout = (LinearLayout) layoutInflater.inflate(
					R.layout.defaultmodel, null);
			TextView tvTitle = (TextView) linearLayout
					.findViewById(R.id.tvTitle);
			// tvTitle.setText(list.get(arg0));
			tvTitle.setText(Html.fromHtml("<font color=\"0000ff\">
					+ listInner.get(arg0) + "</font>"));
			TextView tvContent = (TextView) linearLayout
					.findViewById(R.id.tvContent);
			// 此处直接给内容了
			tvContent.setText("Content");
			RatingBar rbLeavel = (RatingBar) linearLayout
					.findViewById(R.id.rbLevel);
			rbLeavel.setRating(Float.parseFloat("4.0"));
			return linearLayout;
		}
		// 实现onItemClick()方法
		public void onItemClick(AdapterView<?> arg0, View arg1, int arg2,
				long arg3) {
			// 对单击每一项的处理
			new AlertDialog.Builder(Default.this).setTitle(listInner.get(arg2))
					.setMessage(String.valueOf(arg3)).show();
		}
	}
}
```
布局文件：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content" >
    <ListView
        android:id="@+id/lvjokes"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
    </ListView>
</LinearLayout>
```
每项布局文件(模板)：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/tvTitle"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
    <RelativeLayout
        android:id="@+id/llDes"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >
        <TextView
            android:id="@+id/tvContent"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content" />
        <RatingBar
            android:id="@+id/rbLevel"
            style="?android:attr/ratingBarStyleSmall"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true"
            android:numStars="5"
            android:rating="3" >
        </RatingBar>
    </RelativeLayout>
</LinearLayout>
```
希望对大家有帮助
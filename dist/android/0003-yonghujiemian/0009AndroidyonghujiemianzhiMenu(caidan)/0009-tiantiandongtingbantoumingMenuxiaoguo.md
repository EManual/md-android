这个咱再分享个天天动听的半透明Menu效果，个人感觉挺漂亮。
看下效果。
![img](P)  
感觉如何啊？
分解一下:
1. 利用Shaper设置一个半透明圆角背景。
2. 定义Menu布局，主要就GridView，把图标都放在这个GridView。
3. Menu事件, 通过PopupWindow或者AlertDialog或者透明Activity显示到页面即可。
4. 按钮的监听事件，实例中没加。需要的话自己在Adapter里加。
比较简单，不多说了。
半透明圆角背景：
```  
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle" >
    <solid android:color="b4000000" />
    <stroke
        android:dashGap="0.0dip"
        android:dashWidth="3.0dip"
        android:width="2.0dip"
        android:color="b4ffffff" />
    <padding
        android:bottom="7.0dip"
        android:left="7.0dip"
        android:right="7.0dip"
        android:top="7.0dip" />
    <corners android:radius="8.0dip" />
</shape>
```
Menu布局：
```  
<?xml version="1.0" encoding="UTF-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <GridView
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/menuGridChange"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:background="@drawable/menu_bg_frame"
        android:columnWidth="60.0dip"
        android:gravity="center"
        android:horizontalSpacing="10.0dip"
        android:numColumns="auto_fit"
        android:padding="5.0dip"
        android:stretchMode="columnWidth"
        android:verticalSpacing="3.0dip" />
</LinearLayout>
```
主要类:
```  
import android.app.Activity;
import android.app.AlertDialog;
import android.app.AlertDialog.Builder;
import android.content.Context;
import android.graphics.drawable.BitmapDrawable;
import android.os.Bundle;
import android.util.Log;
import android.view.ContextMenu;
import android.view.Gravity;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import android.view.ContextMenu.ContextMenuInfo;
import android.widget.BaseAdapter;
import android.widget.GridView;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.PopupWindow;
import android.widget.TextView;
import android.widget.LinearLayout.LayoutParams;

public class MenuTest extends Activity {
	private String TAG = this.getClass().getSimpleName();
	private int[] resArray = new int[] { R.drawable.icon_menu_addto,
			R.drawable.icon_menu_audioinfo, R.drawable.icon_menu_findlrc,
			R.drawable.icon_menu_scan };
	private String[] title = new String[] { "添加歌曲", "歌曲信息", "查找歌词", "搜索歌词" };
	private static boolean show_flag = false;
	private PopupWindow pw = null;
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		Log.e(TAG, "------  onCreateOptionsMenu ------");
		// 用AlertDialog弹出menu
		// View view = LayoutInflater.from(this).inflate(R.layout.menu, null);
		// GridView grid1 = (GridView)view.findViewById(R.id.menuGridChange);
		// grid1.setAdapter(new ImageAdapter(this));
		// Builder build = new AlertDialog.Builder(this);
		// build.setView(view);
		// build.show();
		LayoutInflater inflater = (LayoutInflater) this
				.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
		View view = inflater.inflate(R.layout.menu, null);
		GridView grid1 = (GridView) view.findViewById(R.id.menuGridChange);
		grid1.setAdapter(new ImageAdapter(this));
		// 用Popupwindow弹出menu
		pw = new PopupWindow(view, LayoutParams.FILL_PARENT,
				LayoutParams.WRAP_CONTENT);
		// NND, 第一个参数， 必须找个View
		pw.showAtLocation(findViewById(R.id.tv), Gravity.CENTER, 0, 300);
		return true;
	}
	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		return super.onOptionsItemSelected(item);
	}
	public class ImageAdapter extends BaseAdapter {
		private Context context;
		public ImageAdapter(Context context) {
			this.context = context;
		}
		@Override
		public int getCount() {
			return resArray.length;
		}
		@Override
		public Object getItem(int arg0) {
			return resArray[arg0];
		}
		@Override
		public long getItemId(int arg0) {
			return arg0;
		}
		@Override
		public View getView(int arg0, View arg1, ViewGroup arg2) {
			LinearLayout linear = new LinearLayout(context);
			LinearLayout.LayoutParams params = new LayoutParams(
					LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);
			linear.setOrientation(LinearLayout.VERTICAL);
			ImageView iv = new ImageView(context);
			iv.setImageBitmap(((BitmapDrawable) context.getResources()
					.getDrawable(resArray[arg0])).getBitmap());
			LinearLayout.LayoutParams params2 = new LayoutParams(
					LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);
			params2.gravity = Gravity.CENTER;
			linear.addView(iv, params2);
			TextView tv = new TextView(context);
			tv.setText(title[arg0]);
			LinearLayout.LayoutParams params3 = new LayoutParams(
					LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);
			params3.gravity = Gravity.CENTER;
			linear.addView(tv, params3);
			return linear;
		}
	}
}
```
就这样了~~~ 
效果：
![img](P)  
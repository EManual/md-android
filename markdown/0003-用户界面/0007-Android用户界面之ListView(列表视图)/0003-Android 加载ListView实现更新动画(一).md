在Android应用中，如果ListView或是GridView里面的数据比较多的时候，加载会比较费时间，特别是里面有图片的时候，需要花费的时间就更长，这样就会出现一个长时间的等待黑屏界面，这样有时会给用户造成一种错觉，就是这个程序已经“死”了。
对于这个问题可以的一个方法是，添加一个ProgressDialog，显示正在处理的窗口，等待加载完之后再关闭这个窗口。
但是这样等所有的数据加载完之后，就是特别突然的显示，这样用户体验也不佳，以前看Android优化大师的时候，打开进程管理的时候，它是前面显示正在加载的ProgressDialog，然后也可以看到后台的数据正在逐条加载，这样给人的感觉就好很多。
主要实现的思路是这样的，新建一个线程，然后在线程里面获取已经安装的程序，再逐条把这些程序(数据)添加到缓冲数组中，再发送一个消息，更新显示ListView的内容，当线程中所有的数据已经加载完的时候，再把ProgressDialog关掉。
```  
import java.util.ArrayList;
import java.util.List;
import android.app.Activity;
import android.app.ProgressDialog;
import android.content.Context;
import android.content.Intent;
import android.content.pm.PackageManager;
import android.content.pm.ResolveInfo;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.KeyEvent;
import android.widget.ArrayAdapter;
import android.widget.ListView;
 /**
  *  实现动态加载一个ListView
  */
public class ProcessorBarTest extends Activity {
	public static final int MSG_UPDATE_LIST = 18;
	private ListView mApps;
	private Context mContext;
	private List<String> mAppList;
	private ProgressDialog dialog;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.processorbar_test);
		// ListView 列表 mApps
		mApps = (ListView) findViewById(R.id.lvProcessbar);
		mContext = this;
		mAppList = new ArrayList<String>();
		// 设置正在处理窗口
		dialog = new ProgressDialog(mContext);
		dialog.setIcon(R.drawable.icon);
		dialog.setTitle("ProgressDialog");
		dialog.setMessage("Please wait while loading application list...");
		dialog.setCancelable(false);
		dialog.show();
		// 开始动态加载线程
		mThreadLoadApps.start();
	}
	private Thread mThreadLoadApps = new Thread() {
		@Override
		public void run() {
			int i = 0;
			// 获取已经安装程序列表
			PackageManager pm = mContext.getPackageManager();
			Intent mainIntent = new Intent(Intent.ACTION_MAIN, null);
			mainIntent.addCategory(Intent.CATEGORY_LAUNCHER);
			List<ResolveInfo> list = pm.queryIntentActivities(mainIntent, 0);
			// 逐项添加程序，并发送消息更新ListView列表。
			for (i = 0; i < list.size(); i++) {
				mAppList.add(list.get(i).loadLabel(pm).toString());
				handler.sendEmptyMessage(MSG_UPDATE_LIST);
			}
			// 关闭正在处理窗口
			dialog.dismiss();
		}
	};
	private Handler handler = new Handler() {
		@Override
		public void handleMessage(Message msg) {
			switch (msg.what) {
			case MSG_UPDATE_LIST:
				// 更新应用程序列表
				mApps.setAdapter(new ArrayAdapter(mContext,
						android.R.layout.simple_list_item_1, mAppList));
				break;
			}
			super.handleMessage(msg);
		}
	};
}
```
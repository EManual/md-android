后面发现有效率更高的方法，其实不用每次更新一项都需要构建一个Adapter的。
把Adapter和一个List<?>绑定在一起之后，可以直接改变List<?>的内容，然后再使用Adapter的数据集更新通知，即可改变ListView的内容，所以后面改了一下源码变成这个样子。
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
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.ListView;
[Tags]/**
 [Tags]*  实现动态加载一个ListView
 [Tags]*/
public class ProcessorBarTest extends Activity {
	public static final int MSG_UPDATE_LIST = 18;
	private ListView mApps;
	private Context mContext;
	private List<String> mAppList;
	private ProgressDialog dialog;
	private ArrayAdapter mAdapter;
	private boolean mIsLoaded = false;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.processorbar_test);
		// ListView 列表 mApps
		mApps = (ListView) findViewById(R.id.lvProcessbar);
		mContext = this;
		mAppList = new ArrayList<String>();
		mAdapter = new ArrayAdapter(mContext, android.R.layout.simple_list_item_1, mAppList);
		mApps.setAdapter(mAdapter);
		// 设置正在处理窗口
		dialog = new ProgressDialog(mContext);
		dialog.setIcon(R.drawable.icon);
		dialog.setTitle("ProgressDialog");
		dialog.setMessage("Please wait while loading application list...");
		dialog.setCancelable(false);
		dialog.show();
		// 开始动态加载线程
		mThreadLoadApps.start();
		mApps.setOnItemClickListener(new AdapterView.OnItemClickListener() {
			public void onItemClick(AdapterView<?> parent, View v,
					int position, long id) {
				mAppList.remove(position);
				mAdapter.notifyDataSetChanged();
			}
		});
		// 获取已经安装程序列表
		PackageManager pm = mContext.getPackageManager();
		Intent mainIntent = new Intent(Intent.ACTION_MAIN, null);
		mainIntent.addCategory(Intent.CATEGORY_LAUNCHER);
		List<ResolveInfo> list = pm.queryIntentActivities(mainIntent, 0);
		// 逐项添加程序，并发送消息更新ListView列表。
		for (int i = 0; i < list.size(); i++) {
			mAppList.add(list.get(i).loadLabel(pm).toString());
			mAdapter.notifyDataSetChanged();
		}
		mIsLoaded = true;
	}
	private Thread mThreadLoadApps = new Thread() {
		@Override
		public void run() {
			int i = 0;
			while (!mIsLoaded) {
				try {
					sleep(100);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
			// 关闭正在处理窗口
			dialog.dismiss();
		}
	};
}
```
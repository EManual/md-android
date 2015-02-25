网上已经有很多关于如何将TabHost的标签放在底部，这里就不说了
主要是把这些都做成框架，只需要提供图片和文字就可以实现这样的效果。
直接上图，代码解释的很清楚。
![img](P)  
使用代码:
```  
import java.util.ArrayList;
import java.util.List;
import android.content.Intent;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.TabWidget;
import android.widget.TextView;
import com.api.R;
import com.api.TabHostActivity;
[Tags]/**
 [Tags]* 整个流程就像使用ListView自定BaseAdapter一样
 [Tags]* 如果要自定义TabHostActivity的Theme，并且不想要头部阴影 一定要添加这个android:windowContentOverlay = null
 [Tags]* 如果想在别的项目里面使用TabHostActivity 可以项目的属性里面找到Android，然后在Library部分添加这个项目(Api) 
 [Tags]* [Tags]*/
public class ExampleActivity extends TabHostActivity {
	List mItems;
	private LayoutInflater mLayoutInflater;
	[Tags]/**
	 [Tags]* 在初始化TabWidget前调用 和TabWidget有关的必须在这里初始化
	 [Tags]*/
	@Override
	protected void prepare() {
		TabItem home = new TabItem("首页", // title
				R.drawable.icon_home, // icon
				R.drawable.example_tab_item_bg, // background
				new Intent(this, Tab1Activity.class)); // intent
		TabItem info = new TabItem("资料", R.drawable.icon_selfinfo,
				R.drawable.example_tab_item_bg, new Intent(this,
						Tab2Activity.class));
		TabItem msg = new TabItem("信息", R.drawable.icon_meassage,
				R.drawable.example_tab_item_bg, new Intent(this,
						Tab3Activity.class));
		TabItem square = new TabItem("广场", R.drawable.icon_square,
				R.drawable.example_tab_item_bg, new Intent(this,
						Tab4Activity.class));
		TabItem more = new TabItem("更多", R.drawable.icon_more,
				R.drawable.example_tab_item_bg, new Intent(this,
						Tab5Activity.class));
		mItems = new ArrayList();
		mItems.add(home);
		mItems.add(info);
		mItems.add(msg);
		mItems.add(square);
		mItems.add(more);
		// 设置分割线
		TabWidget tabWidget = getTabWidget();
		tabWidget.setDividerDrawable(R.drawable.tab_divider);
		mLayoutInflater = getLayoutInflater();
	}
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setCurrentTab(0);
	}
	[Tags]/** tab的title，icon，边距设定等等 */
	@Override
	protected void setTabItemTextView(TextView textView, int position) {
		textView.setPadding(3, 3, 3, 3);
		textView.setText(mItems.get(position).getTitle());
		textView.setBackgroundResource(mItems.get(position).getBg());
		textView.setCompoundDrawablesWithIntrinsicBounds(0, mItems
				.get(position).getIcon(), 0, 0);

	}
	[Tags]/** tab唯一的id */
	@Override
	protected String getTabItemId(int position) {
		return mItems.get(position).getTitle(); // 我们使用title来作为id，你也可以自定
	}
	[Tags]/** 点击tab时触发的事件 */
	@Override
	protected Intent getTabItemIntent(int position) {
		return mItems.get(position).getIntent();
	}
	@Override
	protected int getTabItemCount() {
		return mItems.size();
	}
	[Tags]/** 自定义头部文件 */
	@Override
	protected View getTop() {
		return mLayoutInflater.inflate(R.layout.example_top, null);
	}
}
```
框架代码：
```  
import android.app.TabActivity;
import android.content.Intent;
import android.os.Bundle;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.LinearLayout;
import android.widget.TabHost;
import android.widget.TabHost.TabSpec;
import android.widget.TabWidget;
import android.widget.TextView;
public abstract class TabHostActivity extends TabActivity {
	private TabHost mTabHost;
	private TabWidget mTabWidget;
	private LayoutInflater mLayoutflater;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// set theme because we do not want the shadow
		setTheme(R.style.Theme_Tabhost);
		setContentView(R.layout.api_tab_host);
		mLayoutflater = getLayoutInflater();
		mTabHost = getTabHost();
		mTabWidget = getTabWidget();
		// mTabWidget.setStripEnabled(false); // need android2.2
		prepare();
		initTop();
		initTabSpec();
	}
	private void initTop() {
		View child = getTop();
		LinearLayout layout = (LinearLayout) findViewById(R.id.tab_top);
		layout.addView(child);
	}
	private void initTabSpec() {
		int count = getTabItemCount();
		for (int i = 0; i < count; i++) {
			// set text view
			View tabItem = mLayoutflater.inflate(R.layout.api_tab_item, null);
			TextView tvTabItem = (TextView) tabItem.findViewById(R.id.tab_item_tv);
			setTabItemTextView(tvTabItem, i);
			// set id
			String tabItemId = getTabItemId(i);
			// set tab spec
			TabSpec tabSpec = mTabHost.newTabSpec(tabItemId);
			tabSpec.setIndicator(tabItem);
			tabSpec.setContent(getTabItemIntent(i));
			mTabHost.addTab(tabSpec);
		}
	}
	[Tags]/** 在初始化界面之前调用 */
	protected void prepare() {
		// do nothing or you override it
	}
	[Tags]/** 自定义头部布局 */
	protected View getTop() {
		// do nothing or you override it
		return null;
	}
	protected int getTabCount() {
		return mTabHost.getTabWidget().getTabCount();
	}
	[Tags]/** 设置TabItem的图标和标题等 */
	abstract protected void setTabItemTextView(TextView textView, int position);
	abstract protected String getTabItemId(int position);
	abstract protected Intent getTabItemIntent(int position);
	abstract protected int getTabItemCount();
	protected void setCurrentTab(int index) {
		mTabHost.setCurrentTab(index);
	}
	protected void focusCurrentTab(int index) {
		mTabWidget.focusCurrentTab(index);
	}
}
```
下面的实例主要讲的就是滑动加载数据，widget中的ProgressBar、TextView。还有就是我们主要用的是这个包import android.view.View;有时候大家就会用的别的包。我们还得在代码中写上LinearLayout.LayoutParams.WRAP_CONTENT,这样的代码。这样动态加载就算是完成了，我们还是先看看代码吧：
```  
import android.app.ListActivity;
import android.os.Bundle;
import android.view.Gravity;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AbsListView;
import android.widget.BaseAdapter;
import android.widget.LinearLayout;
import android.widget.ProgressBar;
import android.widget.TextView;
import android.widget.AbsListView.OnScrollListener;
public class EndLessActivity extends ListActivity implements OnScrollListener {
	Aleph0 adapter = new Aleph0();
	int mProgressStatus = 0;
	ProgressBar progressBar;
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		LinearLayout searchLayout = new LinearLayout(this);
		searchLayout.setOrientation(LinearLayout.HORIZONTAL);
		progressBar = new ProgressBar(this);
		progressBar.setPadding(0, 0, 15, 0);
		searchLayout.addView(progressBar, new LinearLayout.LayoutParams(
				LinearLayout.LayoutParams.WRAP_CONTENT,
				LinearLayout.LayoutParams.WRAP_CONTENT));
		TextView textView = new TextView(this);
		textView.setText("加载中...");
		textView.setGravity(Gravity.CENTER_VERTICAL);
		searchLayout.addView(textView, new LinearLayout.LayoutParams(
				LinearLayout.LayoutParams.FILL_PARENT,
				LinearLayout.LayoutParams.FILL_PARENT));
		searchLayout.setGravity(Gravity.CENTER);
		LinearLayout loadingLayout = new LinearLayout(this);
		loadingLayout.addView(searchLayout, new LinearLayout.LayoutParams(
				LinearLayout.LayoutParams.WRAP_CONTENT,
				LinearLayout.LayoutParams.WRAP_CONTENT));
		loadingLayout.setGravity(Gravity.CENTER);
		getListView().addFooterView(loadingLayout);
		setListAdapter(adapter);
		getListView().setOnScrollListener(this);
	}
	public void onScroll(AbsListView view, int firstVisible, int visibleCount,
			int totalCount) {
		boolean loadMore = /* maybe add a padding */
		firstVisible + visibleCount >= totalCount;
		if (loadMore) {
			adapter.count += visibleCount; // or any other amount
			adapter.notifyDataSetChanged();
		}
	}
	public void onScrollStateChanged(AbsListView v, int s) {
	}
	class Aleph0 extends BaseAdapter {
		int count = 40; /* starting amount */
		public int getCount() {
			return count;
		}
		public Object getItem(int pos) {
			return pos;
		}
		public long getItemId(int pos) {
			return pos;
		}
		public View getView(int pos, View v, ViewGroup p) {
			TextView view = new TextView(EndLessActivity.this);
			view.setText("entry " + pos);
			return view;
		}
	}
}
```
listview下部是按钮控制：
```  
import android.app.ListActivity;
import android.os.Bundle;
import android.view.Gravity;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AbsListView;
import android.widget.BaseAdapter;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.ProgressBar;
import android.widget.TextView;
import android.widget.AbsListView.OnScrollListener;
public class EndLessActivity extends ListActivity {
	Aleph0 adapter = new Aleph0();
	int mProgressStatus = 0;
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		LinearLayout searchLayout = new LinearLayout(this);
		searchLayout.setOrientation(LinearLayout.HORIZONTAL);
		Button textView = new Button(this);
		textView.setText("加载中...");
		textView.setGravity(Gravity.CENTER_VERTICAL);
		searchLayout.addView(textView, new LinearLayout.LayoutParams(
				LinearLayout.LayoutParams.FILL_PARENT,
				LinearLayout.LayoutParams.FILL_PARENT));
		searchLayout.setGravity(Gravity.CENTER);
		LinearLayout loadingLayout = new LinearLayout(this);
		loadingLayout.addView(searchLayout, new LinearLayout.LayoutParams(
				LinearLayout.LayoutParams.WRAP_CONTENT,
				LinearLayout.LayoutParams.WRAP_CONTENT));
		loadingLayout.setGravity(Gravity.CENTER);
		getListView().addFooterView(loadingLayout);
		textView.setOnClickListener(new Button.OnClickListener() {
			@Override
			public void onClick(View v) {
				adapter.count += 10;
				adapter.notifyDataSetChanged();
			}
		});
		setListAdapter(adapter);
		// getListView().setOnScrollListener(this);
	}
	/*
	 [Tags]* public void onScroll(AbsListView view, int firstVisible, int
	 [Tags]* visibleCount, int totalCount) {
	 [Tags]* boolean loadMore = firstVisible + visibleCount >= totalCount;
	 [Tags]* if(loadMore) { adapter.count += visibleCount;
	 [Tags]* adapter.notifyDataSetChanged(); } }
	 [Tags]*/
	public void onScrollStateChanged(AbsListView v, int s) {
	}
	class Aleph0 extends BaseAdapter {
		int count = 40; /* starting amount */
		public int getCount() {
			return count;
		}
		public Object getItem(int pos) {
			return pos;
		}
		public long getItemId(int pos) {
			return pos;
		}
		public View getView(int pos, View v, ViewGroup p) {
			TextView view = new TextView(EndLessActivity.this);
			view.setText("entry " + pos);
			return view;
		}
	}
}
```
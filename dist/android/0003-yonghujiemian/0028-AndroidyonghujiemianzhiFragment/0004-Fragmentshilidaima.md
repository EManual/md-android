Android 3.0平板系统的重要特性Fragment示例代码今天Androideoe给大家两个例子，一起来看下
Fragment的实战代码:
一、通过Fragment实现简单的上下文菜单
```  
public class FragmentContextMenu extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		ContextMenuFragment content = new ContextMenuFragment();
		getFragmentManager().beginTransaction()
				.add(android.R.id.content, content).commit();
		// 在Activity中通过这个与Fragment通讯
	}
}
```
下面ContextMenuFragment是我们从Fragment派生的子类，里面重写了onCreateView来布局自己的Fragment
```  
public static class ContextMenuFragment extends Fragment {
	@Override
	public View onCreateView(LayoutInflater inflater, ViewGroup container,
			Bundle savedInstanceState) {
		View root = inflater.inflate(R.layout.fragment_context_menu, container,
				false);
		registerForContextMenu(root.findViewById(R.id.long_press));
		return root;
	}
	@Override
	public void onCreateContextMenu(ContextMenu menu, View v,
			ContextMenuInfo menuInfo) {
		super.onCreateContextMenu(menu, v, menuInfo);
		menu.add(Menu.NONE, R.id.a_item, Menu.NONE, "菜单1");
		menu.add(Menu.NONE, R.id.b_item, Menu.NONE, "菜单2");
	}
	@Override
	public boolean onContextItemSelected(MenuItem item) {
		switch (item.getItemId()) {
		case R.id.a_item:
			Log.i("CWJ", "Item 1a was chosen");
			return true;
		case R.id.b_item:
			Log.i("CWJ", "Item 1b was chosen");
			return true;
		}
		return super.onContextItemSelected(item);
	}
}
```
涉及到的布局文件fragment_context_menu.xml源码
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="8dp" >
    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="@string/fragment_context_menu_msg"
        android:textAppearance="?android:attr/textAppearanceMedium" />
    <Button
        android:id="@+id/long_press"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="@string/long_press" >
        <requestFocus />
    </Button>
</LinearLayout>
```
二、控制Fragment的显示或隐藏同时包含淡入淡出效果
```  
public class FragmentHideShow extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.fragment_hide_show);
		FragmentManager fm = getFragmentManager();
		addShowHideListener(R.id.frag1hide, fm.findFragmentById(R.id.fragment1));
		addShowHideListener(R.id.frag2hide, fm.findFragmentById(R.id.fragment2));
		// 上面的两行代码是在Activity中为Fragment中的按钮添加监听事件
	}
	void addShowHideListener(int buttonId, final Fragment fragment) {
		final Button button = (Button) findViewById(buttonId);
		button.setOnClickListener(new OnClickListener() {
			public void onClick(View v) {
				FragmentTransaction ft = getFragmentManager()
						.beginTransaction();
				ft.setCustomAnimations(android.R.animator.fade_in,
						android.R.animator.fade_out);
				// 为Fragment设置淡入淡出效果，Androideoe提示这里这两个动画资源是android内部资源无需我们手动定义。
				if (fragment.isHidden()) {
					ft.show(fragment);
					button.setText("隐藏");
				} else {
					ft.hide(fragment);
					button.setText("显示");
				}
				ft.commit();
			}
		});
	}
	public static class FirstFragment extends Fragment {
		TextView mTextView;
		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState) {
			View v = inflater.inflate(R.layout.labeled_text_edit, container, false);
			View tv = v.findViewById(R.id.msg);
			((TextView) tv).setText("The fragment saves and restores this text.");
			mTextView = (TextView) v.findViewById(R.id.saved);
			if (savedInstanceState != null) {
				mTextView.setText(savedInstanceState.getCharSequence("text"));
			}
			return v;
		}
		@Override
		public void onSaveInstanceState(Bundle outState) {
			super.onSaveInstanceState(outState);
			outState.putCharSequence("text", mTextView.getText());
		}
	}
	public static class SecondFragment extends Fragment {
		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState) {
			View v = inflater.inflate(R.layout.labeled_text_edit, container, false);
			View tv = v.findViewById(R.id.msg);
			((TextView) tv).setText("The TextView saves and restores this text.");
			((TextView) v.findViewById(R.id.saved)).setSaveEnabled(true);
			return v;
		}
	}
}
```
涉及资源布局文件fragment_hide_show.xml代码: 
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center_horizontal"
    android:orientation="vertical" >
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center_vertical|center_horizontal"
        android:text="Demonstration of hiding and showing fragments."
        android:textAppearance="?android:attr/textAppearanceMedium" />
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center_vertical"
        android:orientation="horizontal"
        android:padding="4dip" >
        <Button
            android:id="@+id/frag1hide"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hide" />
    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:gravity="center_vertical"
        android:orientation="horizontal"
        android:padding="4dip" >
        <Button
            android:id="@+id/frag2hide"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Hide" />
    </LinearLayout>
</LinearLayout>
```
这两个例子大家看过以后就应该能明白了吧，其实主要还是用到了判断当条件满足时，我们就设置Button隐藏，当条件没有满足的话，Button就显示，这样我们就实现我们想要的效果了。其中主要的还有xml布局，这样我们就可以来完成了。
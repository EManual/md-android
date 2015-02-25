#### 二、作为Tab切换Fragment
```  
public class ActionBarTabs extends Activity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.action_bar_tabs);
	}
	public void onAddTab(View v) {
		final ActionBar bar = getActionBar();
		final int tabCount = bar.getTabCount();
		final String text = "Tab " + tabCount;
		bar.addTab(bar.newTab().setText(text)
				.setTabListener(new TabListener(new TabContentFragment(text))));
	}
	public void onRemoveTab(View v) {
		final ActionBar bar = getActionBar();
		bar.removeTabAt(bar.getTabCount() - 1);
	}
	public void onToggleTabs(View v) {
		final ActionBar bar = getActionBar();
		if (bar.getNavigationMode() == ActionBar.NAVIGATION_MODE_TABS) {
			bar.setNavigationMode(ActionBar.NAVIGATION_MODE_STANDARD);
			bar.setDisplayOptions(ActionBar.DISPLAY_SHOW_TITLE,
					ActionBar.DISPLAY_SHOW_TITLE);
		} else {
			bar.setNavigationMode(ActionBar.NAVIGATION_MODE_TABS);
			bar.setDisplayOptions(0, ActionBar.DISPLAY_SHOW_TITLE);
		}
	}
	public void onRemoveAllTabs(View v) {
		getActionBar().removeAllTabs();
	}
	private class TabListener implements ActionBar.TabListener {
		private TabContentFragment mFragment;
		public TabListener(TabContentFragment fragment) {
			mFragment = fragment;
		}
		public void onTabSelected(Tab tab, FragmentTransaction ft) {
			ft.add(R.id.fragment_content, mFragment, mFragment.getText());
		}
		public void onTabUnselected(Tab tab, FragmentTransaction ft) {
			ft.remove(mFragment);
		}
		public void onTabReselected(Tab tab, FragmentTransaction ft) {
			Toast.makeText(ActionBarTabs.this, "Reselected!",
					Toast.LENGTH_SHORT).show();
		}
	}
	private class TabContentFragment extends Fragment {
		private String mText;
		public TabContentFragment(String text) {
			mText = text;
		}
		public String getText() {
			return mText;
		}
		@Override
		public View onCreateView(LayoutInflater inflater, ViewGroup container,
				Bundle savedInstanceState) {
			View fragView = inflater.inflate(R.layout.action_bar_tab_content,
					container, false);
			TextView text = (TextView) fragView.findViewById(R.id.text);
			text.setText(mText);
			return fragView;
		}
	}
}
```
涉及的布局文件action_bar_tabs.xml代码为
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
    <FrameLayout
        android:id="@+id/fragment_content"
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1" />
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:orientation="vertical" >
        <Button
            android:id="@+id/btn_add_tab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="onAddTab"
            android:text="@string/btn_add_tab" />
        <Button
            android:id="@+id/btn_remove_tab"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="onRemoveTab"
            android:text="@string/btn_remove_tab" />
        <Button
            android:id="@+id/btn_toggle_tabs"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="onToggleTabs"
            android:text="@string/btn_toggle_tabs" />
        <Button
            android:id="@+id/btn_remove_all_tabs"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="onRemoveAllTabs"
            android:text="@string/btn_remove_all_tabs" />
    </LinearLayout>
</LinearLayout>
```
涉及布局文件action_bar_tab_content.xml的代码为
```  
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
	android:id="@+id/text"
	android:layout_width="wrap_content"
	android:layout_height="wrap_content" />
```
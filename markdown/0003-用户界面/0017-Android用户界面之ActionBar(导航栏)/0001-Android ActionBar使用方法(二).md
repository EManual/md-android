#### 三、添加标签 Tabs
在ActionBar中实现标签页可以实现android.app.ActionBar.TabListener，重写onTabSelected、onTabUnselected和onTabReselected方法来关联Fragment。
代码如下
```  
private class MyTabListener implements ActionBar.TabListener {
	private TabContentFragment mFragment;
	public TabListener(TabContentFragment fragment) {
		mFragment = fragment;
	}
	@Override
	public void onTabSelected(Tab tab, FragmentTransaction ft) {
		ft.add(R.id.fragment_content, mFragment, null);
	}
	@Override
	public void onTabUnselected(Tab tab, FragmentTransaction ft) {
		ft.remove(mFragment);
	}
	@Override
	public void onTabReselected(Tab tab, FragmentTransaction ft) {
	}
}
```
接下来我们创建ActionBar在Activity中，代码如下
```  
@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	setContentView(R.layout.main);
	final ActionBar actionBar = getActionBar(); // 提示getActionBar方法一定在setContentView后面
	actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_TABS);
	actionBar.setDisplayOptions(0, ActionBar.DISPLAY_SHOW_TITLE);
	Fragment artistsFragment = new ArtistsFragment();
	actionBar.addTab(actionBar.newTab().setText(R.string.tab_artists)
			.setTabListener(new TabListener(artistsFragment)));
	Fragment albumsFragment = new AlbumsFragment();
	actionBar.addTab(actionBar.newTab().setText(R.string.tab_albums)
			.setTabListener(new TabListener(albumsFragment)));
}
```
#### 四、添加下拉导航 Drop-down Navigation
创建一个SpinnerAdapter提供下拉选项，和Tab方式不同的是Drop-down只需要修改下setNavigationMode的模式，将ActionBar.NAVIGATION_MODE_TABS改为ActionBar.NAVIGATION_MODE_LIST，最终改进后的代码为
```  
ActionBar actionBar = getActionBar();
actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_LIST);
actionBar.setListNavigationCallbacks(mSpinnerAdapter, mNavigationCallback);
```
上面我们通过setListNavigationCallbacks方法绑定一个SpinnerAdapter控件，具体的OnNavigationListener代码示例为
```  
mOnNavigationListener = new OnNavigationListener() {
	String[] strings = getResources().getStringArray(
			R.array.action_list);
	@Override
	public boolean onNavigationItemSelected(int position, long itemId) {
		ListContentFragment newFragment = new ListContentFragment();
		FragmentTransaction ft = openFragmentTransaction();
		ft.replace(R.id.fragment_container, newFragment,
				strings[position]);
		ft.commit();
		return true;
	}
};
```
而其中的ListContentFragment的代码为
```  
public class ListContentFragment extends Fragment {
	private String mText;
	@Override
	public void onAttach(Activity activity) {
		super.onAttach(activity);
		mText = getTag();
	}
	@Override
	public View onCreateView(LayoutInflater inflater, ViewGroup container,
			Bundle savedInstanceState) {
		TextView text = new TextView(getActivity());
		text.setText(mText);
		return text;
	}
}
```
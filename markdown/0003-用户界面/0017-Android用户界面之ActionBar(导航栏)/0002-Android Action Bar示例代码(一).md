我们通过活动栏做一个简单选项菜单。下面这个例子将演示ActionBar.NAVIGATION_MODE_STANDARD、ActionBar.NAVIGATION_MODE_TABS和:ActionBar.NAVIGATION_MODE_STANDARD等模式的效果。最后eoeAndroid仍然提示大家Action Bar是Android 3.0 honeycomb才开始有的特性，老版本的SDK和固件无法使用。
```  
public class ActionBarDisplayOptions extends Activity implements
		View.OnClickListener, ActionBar.TabListener {
	private View mCustomView;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.action_bar_display_options);
		findViewById(R.id.toggle_home_as_up).setOnClickListener(this);
		findViewById(R.id.toggle_show_home).setOnClickListener(this);
		findViewById(R.id.toggle_use_logo).setOnClickListener(this);
		findViewById(R.id.toggle_show_title).setOnClickListener(this);
		findViewById(R.id.toggle_show_custom).setOnClickListener(this);
		findViewById(R.id.toggle_navigation).setOnClickListener(this);
		findViewById(R.id.cycle_custom_gravity).setOnClickListener(this);
		mCustomView = getLayoutInflater().inflate(
				R.layout.action_bar_display_options_custom, null);
		final ActionBar bar = getActionBar();
		bar.setCustomView(mCustomView, new ActionBar.LayoutParams(
				LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT));
		bar.addTab(bar.newTab().setText("Tab 1").setTabListener(this));
		bar.addTab(bar.newTab().setText("Tab 2").setTabListener(this));
		bar.addTab(bar.newTab().setText("Tab 3").setTabListener(this));
	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		getMenuInflater().inflate(R.menu.display_options_actions, menu);
		return true;
	}
	public void onClick(View v) {
		final ActionBar bar = getActionBar();
		int flags = 0;
		switch (v.getId()) {
		case R.id.toggle_home_as_up:
			flags = ActionBar.DISPLAY_HOME_AS_UP;
			break;
		case R.id.toggle_show_home:
			flags = ActionBar.DISPLAY_SHOW_HOME;
			break;
		case R.id.toggle_use_logo:
			flags = ActionBar.DISPLAY_USE_LOGO;
			break;
		case R.id.toggle_show_title:
			flags = ActionBar.DISPLAY_SHOW_TITLE;
			break;
		case R.id.toggle_show_custom:
			flags = ActionBar.DISPLAY_SHOW_CUSTOM;
			break;
		case R.id.toggle_navigation:
			bar.setNavigationMode(bar.getNavigationMode() == ActionBar.NAVIGATION_MODE_STANDARD ? ActionBar.NAVIGATION_MODE_TABS
					: ActionBar.NAVIGATION_MODE_STANDARD);
			return;
		case R.id.cycle_custom_gravity:
			ActionBar.LayoutParams lp = (ActionBar.LayoutParams) mCustomView
					.getLayoutParams();
			int newGravity = 0;
			switch (lp.gravity & Gravity.HORIZONTAL_GRAVITY_MASK) {
			case Gravity.LEFT:
				newGravity = Gravity.CENTER_HORIZONTAL;
				break;
			case Gravity.CENTER_HORIZONTAL:
				newGravity = Gravity.RIGHT;
				break;
			case Gravity.RIGHT:
				newGravity = Gravity.LEFT;
				break;
			}
			lp.gravity = lp.gravity & ~Gravity.HORIZONTAL_GRAVITY_MASK
					| newGravity;
			bar.setCustomView(mCustomView, lp);
			return;
		}
		int change = bar.getDisplayOptions() ^ flags;
		bar.setDisplayOptions(change, flags);
	}
	public void onTabSelected(Tab tab, FragmentTransaction ft) {
	}
	public void onTabUnselected(Tab tab, FragmentTransaction ft) {
	}
}
```
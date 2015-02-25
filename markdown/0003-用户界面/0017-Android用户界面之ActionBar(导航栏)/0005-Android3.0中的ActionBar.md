#### Android3.0中ActionBar的新特性
1. ActionBar（活动栏）替代了显示在屏幕顶端的标题栏。主要负责显示菜单，widget，导航等功能，主要包括：
1、显示选项菜单中的菜单项到活动栏；
2、添加可交互的视图到活动栏作为活动视图；
3、使用应用的图标作为活动项，代表返回home或者向上等重要操作；
4、提供标签导航，方便不同的Fragment之间切换；
5、提供下拉导航功能。
2. Android3.0针对ActionBar新增的类如下：
#### ActionBar
ActionBar.LayoutParams
1、android:layout_gravity：设置控件本身相对于父控件的显示位置。（而android:gravity：设置的是控件自身上面的内容位置）
ActionBar.OnMenuVisibilityListener
2、onMenuVisibilityChanged(boolean isVisible)
ActionBar.OnNavigationListener
3、onNavigationItemSelected(int itemPosition, long itemId)
ActionBar.Tab
ActionBar.TabListener
4、onTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
5、onTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
6、onTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
#### 导航的三种显示模式：
```  
NAVIGATION_MODE_STANDARD
NAVIGATION_MODE_LIST
NAVIGATION_MODE_TABS
```
3. 具体功能的实现
#### 1. 隐藏、显示、删除活动栏
```  
ActionBar actionBar = getActionBar();
actionBar.hide();
//actionBar.show(); // 显示活动栏
```
删除活动栏
```  
<activity android:theme="@android:style/Theme.Holo.NoActionBar">
```
#### 2. 添加活动项到活动栏
1.利用menu目录下的布局文件，对于你想添加的每个活动项，你必须添加一个菜单项到选项菜单，并设置菜单项作为活动项；例如下图的xml布局：
```  
<menu xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:id="@+id/menu_save"
        android:icon="@drawable/ic_menu_save"
        android:showAsAction="ifRoom|withText"
        android:title="@string/menu_save"/>
    <item
        android:id="@+id/menu_delete"
        android:icon="@drawable/ic_menu_delete"
        android:showAsAction="ifRoom|withText"
        android:title="@string/menu_delete"/>
    <item
        android:id="@+id/menu_edit"
        android:icon="@drawable/ic_menu_edit"
        android:showAsAction="ifRoom|withText"
        android:title="@string/menu_edit"/>
</menu>
```
#### 2.直接在Activity里的onCreateOptionsMenu里实现，
例如：
```  
public boolean onCreateOptionsMenu(Menu menu) {
	menu.add("Normal item");
	MenuItem actionItem = menu.add("Save");
	actionItem.setShowAsAction(MenuItem.SHOW_AS_ACTION_IF_ROOM);
	actionItem.setIcon(android.R.drawable.ic_menu_save);
	return true;
}
```
响应事件的调用和Options Menu的一样，都是
```  
public boolean onOptionsItemSelected(MenuItem item)
```
#### 3. 添加可交互的视图到活动栏作为活动视图
你可以把widget作为活动项添加到活动栏（如下图），有两种方式实现；
第一种是通过布局文件；
```  
<item
    android:id="@+id/menu_search"
    android:actionLayout="@layout/searchview"
    android:icon="@drawable/ic_menu_search"
    android:showAsAction="ifRoom"
    android:title="Search"/>
```
第二种是通过指定活动视图的全修饰名，即包名和类名;
```  
<item
    android:id="@+id/action_search"
    android:actionViewClass="android.widget.SearchView"
    android:icon="@android:drawable/ic_menu_search"
    android:showAsAction="always"
    android:title="@string/action_bar_search"/>
```
#### 4. 使用应用的图标作为活动项，代表返回home或者向上等操作
由于活动栏默认情况下，左边是应用的图标，接着是应用标题，我们可以利用它来处理一些经常且关键的操作，默认情况下应用的图标设置ID为android.R.id.home.
如下面的例子，是返回到home.
```  
public boolean onOptionsItemSelected(MenuItem item) {
	switch (item.getItemId()) {
	case android.R.id.home:
		Intent intent = new Intent(this, ActionBarDemo.class);
		startActivity(intent);
		break;
	}
	return super.onOptionsItemSelected(item);
}
```
#### 5. 提供标签导航，方便不同的Fragment之间切换
活动栏可以显示标签，允许用户在不同的fragment之间切换。
1. 确定布局中包含有tab关联的fragment的视图；
2. 创建一个对ActionBar.TabListener的实现，并实现onTabSelected(Tab tab, FragmentTransaction ft), onTabUnselected(), and onTabReselected()方法；
3. 在setContentView方法之后得到ActionBar,并设置导航模式为NAVIGATION_MODE_TABS.
final ActionBar actionBar = getActionBar();
actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_TABS);
4. 创建每一个tab为ActionBar,如下
1. Create a new ActionBar.Tab by calling newTab() on the ActionBar.
2. Add title text and/or an icon for the tab by calling setText() and/or setIcon().
3. Declare the ActionBar.TabListener to use for the tab by passing an instance of your implementation to setTabListener().
5. Add each ActionBar.Tab to the Action Bar by calling addTab() on the ActionBar and passing the ActionBar.Tab.
Fragment artistsFragment = new ArtistsFragment();
actionBar.addTab(actionBar.newTab().setText(R.string.tab_artists).setTabListener(new TabListener(artistsFragment)));
#### 6. 提供下拉导航功能
1. 利用下拉选择项和布局，构建SpinnerAdapter。
SpinnerAdapter mSpinnerAdapter = ArrayAdapter.createFromResource(this, R.array.action_list, android.R.layout.simple_spinner_dropdown_item);
2. 实现ActionBar.OnNavigationListener，来记录用户选择list中项目的行为。
public boolean onNavigationItemSelected(int position, long itemId)
3. 设置导航模式为ActionBar.NAVIGATION_MODE_LIST
ActionBar actionBar = getActionBar();
actionBar.setNavigationMode(ActionBar.NAVIGATION_MODE_LIST);
4. 设置回调函数setListNavigationCallbacks()
actionBar.setListNavigationCallbacks(mSpinnerAdapter, mNavigationCallback);
#### 7. 定制活动栏
1. setBackgroundDrawable (Drawable d)，the drawable should be Nine-patch image, a shape, or a solid color, so the system can resize the drawable based on the size of the Action Bar (you should not use a fixed-size bitmap image).
2. setDisplayUseLogoEnabled (boolean useLogo)，由于应用的logo含有更多的信息，是否用应用的logo替换应用的图标
3. The Action Bar has two standard themes, "dark" and "light". The dark theme is applied with the default holographic theme, as specified by the Theme.Holo theme.
4. android:actionBarTabStyle( Style for tabs in the Action Bar)
5. android:actionBarTabBarStyle(Style for the bar that appears below tabs in the Action Bar)
6. android:actionBarTabTextStyle(Style for the text in the tabs)
7. android:actionDropDownStyle(Style for the drop-down list used for the overflow menu and drop-down navigation)
8. android:actionButtonStyle(Style for the background image used for buttons in the Action Bar)
#### 8. 总结：
1. 在屏幕或应用上，关键，操作频繁的动作，可以放到活动栏；
2. 帮助传达动作或位置信息的图标；
3. 用logo或者暗示性的图标作为home或者up等操作；
分析：只需要在设置界面上增加是否全屏的checkBox，然后BrowserActivity中读取这个值，来设置窗口的Style。 
修改： 
1. 修改项目下的res/xml文件夹下的browser_preferences.xml文件， 添加<CheckBoxPreference 
```  
android:key="full_screen" 
android:defaultValue="false" 
android:title="@string/pref_full_screen" 
android:summary="@string/pref_full_screen_summary" /> 
```
2. BrowserActivity中创建SetScreen()方法 
```  
public void setScreen() {
	// set to full screen if necessary
	SharedPreferences sp = getSharedPreferences(this.getPackageName()
			+ "_preferences", Context.MODE_WORLD_READABLE);

	boolean isFullScreen = sp.getBoolean(BrowserSettings.PREF_FULL_SCREEN,
			false);
	// if search dialog is open, we should quit full screen.
	if (isFullScreen && !isSearchDialogOpen) {
		getWindow().addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
		getWindow().clearFlags(
				WindowManager.LayoutParams.FLAG_FORCE_NOT_FULLSCREEN);
	} else {
		getWindow().addFlags(
				WindowManager.LayoutParams.FLAG_FORCE_NOT_FULLSCREEN);
		getWindow().clearFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
	}
}
```
如果我们第一次进入浏览器是非全屏， 然后进入设置修改成全屏模式， 这时候返回到BrowserActivity， 全屏模式必须马上切换过来。 所以我们在OnResume()里面调用setScreen()， 不要在onCreate()里面调。
3. 大家可能注意到了， 判断全屏切换时有个isSearchDialogOpen变量， 这是用来控制在搜索框出现时的全屏切换的。 因为点击进地址栏时会调用系统的搜索框控件，而这个控件不属于浏览器，是个单独的窗口，并且一开始创建时是有标题栏的。这时候如果设置成无标题栏的风格时，就会出现标题栏先出现，然后又隐藏上去，并且有2-3次反复的情况，用户体验非常糟糕。这里就做了个折中，当搜索框出现时，切换成非全屏模式，这样标题栏就一直在那里，不会来回闪了。 退出搜索时，如果设置的是全屏，再切换成全屏模式。 所以在搜索框出现和消失时的代码部分， 还要做相应修改。 
见如下代码：
```  
public void startSearch(String initialQuery, boolean selectInitialQuery,
		Bundle appSearchData, boolean globalSearch) {
	if (appSearchData == null) {
		appSearchData = createGoogleSearchSourceBundle(GOOGLE_SEARCH_SOURCE_TYPE);
	}

	SearchEngine searchEngine = mSettings.getSearchEngine();
	if (searchEngine != null && !searchEngine.supportsVoiceSearch()) {
		// appSearchData.putBoolean(SearchManager.DISABLE_VOICE_SEARCH,
		// true);
	}

	// show status bar when search window pops up. isSearchDialogOpen =
	// true;

	// show status bar when search window pops up.
	// getWindow().addFlags(WindowManager.LayoutParams.FLAG_FORCE_NOT_FULLSCREEN);
	getWindow().clearFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);

	super.startSearch(initialQuery, selectInitialQuery, appSearchData,
			globalSearch);
}
```
搜索框消失部分： 
```  
// switch to full screen if necessary when search window disappears.
public void onDismiss() {
	isSearchDialogOpen = false;
	setScreen();
}
```

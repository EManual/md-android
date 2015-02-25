比如说现在要在时间后面加上AM、PM等显示，应该怎样做呢？介绍一个方法，大家如果有其它的方法，也可以拿来分享一下哦 ^_^  我的方法是在SystemUI包下的clock.java类中修改，具体修改代码如下：
```  
private String addAM_PM(String re) {
	ContentResolver cv = getContext().getContentResolver();
	String strTimeFormat = android.provider.Settings.System.getString(cv,
			android.provider.Settings.System.TIME_12_24);
	if (strTimeFormat.equals("24")) {
		return re;
	} else {
		String amPmValues;
		if (mCalendar.get(Calendar.AM_PM) == 0) {
			mPmValues = "AM";
		} else {
			amPmValues = "PM";
		}
		return re + " " + amPmValues;
	}
}
```
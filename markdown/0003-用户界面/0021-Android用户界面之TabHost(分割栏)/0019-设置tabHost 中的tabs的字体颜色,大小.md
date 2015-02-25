设置tabHost 中的tabs的字体颜色、大小等；
```  
TabWidget tabWidget=this.getTabWidget();
for (int i = 0; i < tabWidget.getChildCount(); i++) {
	TextView tv=(TextView)tabWidget.getChildAt(i).findViewById(android.R.id.title);
	tv.setGravity(BIND_AUTO_CREATE);
	tv.setPadding(10, 10,10, 10);
	tv.setTextSize(16);//设置字体的大小；
	tv.setTextColor(Color.WHITE);//设置字体的颜色；
	//获取tabs图片；
	ImageView iv=(ImageView)tabWidget.getChildAt(i).findViewById(android.R.id.icon);
}
```
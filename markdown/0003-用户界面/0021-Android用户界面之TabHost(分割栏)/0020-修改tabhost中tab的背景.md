效果如下：
![img](P)  
代码如下：
```  
for (int i = 0; i < tabHost.getTabWidget().getChildCount(); i++) {
	View view = tabHost.getTabWidget().getChildAt(i);
	view.setBackgroundDrawable(getResources().getDrawable(R.drawable.tab_indicator));
}
```
其中tab_indicator可以參考android源碼
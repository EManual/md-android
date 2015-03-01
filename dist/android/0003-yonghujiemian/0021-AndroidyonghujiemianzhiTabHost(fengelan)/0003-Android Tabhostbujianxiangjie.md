本文结合源代码和实例来说明TabHost的用法。使用TabHost可以在一个屏幕间进行不同版面的切换，例如Android自带的拨号应用，查看tabhost的源代码，主要实例变量有：
```  
private TabWidget mTabWidget;private FrameLayout mTabContent;
private List mTabSpecs;
```
也就是说我们的tabhost必须有这三个东西，所以我们的.xml文件就会有规定：继续查看源代码：
```  
if (mTabWidget == null) {
	throw new RuntimeException(
			"Your TabHost must have a TabWidget whose id attribute is ‘android.R.id.tabs’");
}
mTabContent = (FrameLayout) findViewById(com.android.internal.R.id.tabcontent);
if (mTabContent == null) {
	throw new RuntimeException(
			"Your TabHost must have a FrameLayout whose id attribute is ‘android.R.id.tabcontent’");
}
```
也就是说我们的.xml文件需要TabWidget和FrameLayout标签。接下来构建我们自己的tab实例：
有两种方式可以实现：
一种是继承TabActivity类,可以使用android的自己内部定义好的.xml资源文件作容器文件。也就是在我们的代码中使用getTabHost();，而相应的后台源码是这样的：this.setContentView(com.android.internal.R.layout.tab_content);在系统的资源文件中可以看见这个layout,有了容器，然后我们就需要我们为每个tab分配内容，当然要可以是如何类型的标签：
例如我们构建一下.xml文件
首先tab1.xml 是一个LinearLayout布局
```  
android:id="@+id/LinearLayout01" 
android:layout_width="wrap_content" 
android:layout_height="wrap_content">
android:id="@+id/TextView01" 
android:layout_width="wrap_content" 
android:layout_height="wrap_content">
```
然后是tab2.xml是一个FrameLayout布局
```  
android:id="@+id/FrameLayout02" 
android:layout_width="wrap_content" 
android:layout_height="wrap_content">
android:layout_width="wrap_content" 
android:layout_height="wrap_content"> 
android:id="@+id/TextView01" 
android:layout_width="wrap_content" 
android:layout_height="wrap_content">
```
接着要注册这两个FrameLayout为tabhost的Content，也就是接下来的代码：
```  
LayoutInflater inflater_tab1 = LayoutInflater.from(this);
inflater_tab1.inflate(R.layout.tab1, mTabHost.getTabContentView());
inflater_tab1.inflate(R.layout.tab2, mTabHost.getTabContentView());
```
那么我们就来看看完整的代码吧：
```  
TabHost mTabHost = getTabHost();
LayoutInflater inflater_tab1 = LayoutInflater.from(this); 
0inflater_tab1.inflate(R.layout.tab1, mTabHost.getTabContentView());
inflater_tab1.inflate(R.layout.tab2, mTabHost.getTabContentView());
mTabHost.addTab(mTabHost.newTabSpec("tab_test1").setIndicator("TAB 11").setContent(R.id.LinearLayout01)); 
mTabHost.addTab(mTabHost.newTabSpec("tab_test1").setIndicator("TAB 11").setContent(R.id.FrameLayout02)); 
```
关于spinner控件有很多特殊的样式甚至是表现的很夸张的样式,下面我们就看看spinner的效果吧。
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	// setContentView(R.layout.main);//不用xml
	Spinner sp = new Spinner(this);
	ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
			android.R.layout.simple_spinner_item);
	adapter.add("red");
	adapter.add("green");
	adapter.add("yellow");
	adapter.add("black");
	adapter.add("write");
	adapter.add("blue");
	adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
	sp.setAdapter(adapter);
	LinearLayout l = new LinearLayout(this);
	LinearLayout.LayoutParams ll = new LinearLayout.LayoutParams(
			LinearLayout.LayoutParams.WRAP_CONTENT,
			LinearLayout.LayoutParams.WRAP_CONTENT);
	l.addView(sp, ll);
	setContentView(l);
}
(this,android.R.layout.simple_spinner_item);
```
在这里我们主要把android.R.后面的代码改了就行，下面就是改完代码的效果图。
如果换成android.R.layoutbrowser_link_context_header
![img](P)  
android.R.pinner_dropdown
![img](P)  
android.R.preference_category
![img](P)  
android.R.simple_spinner_item
![img](P)  
android.R.select_dialog_item
![img](P)  
android.R.select_dialog_multichoice
![img](P)  
android.R.simple_dropdown_item_1line
![img](P)  
android.R.simple_expandable_list_item_1
![img](P)  
android.R.simple_gallery_item
![img](P)  
android.R.simple_list_item_1
![img](P)  
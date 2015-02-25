在Android中Spinner就是下拉菜单，它相当于HTML中的<select>标签。
Android中提供的Spinner Widget下拉菜单已经非常好用了，样式也适用，不过我们还是可以通过定义xml的方式来改变下拉菜单的样式。
l Spinner.getItemAtPosition(Spinner.getSelectedItemPosition());获取下拉列表框的值
l 调用setOnItemSelectedListener()方法，处理下拉列表框被选择事件，把AdapterView.OnItemSelectedListener实例作为参数传入
在layout目录下新建一个xml文件，名字随便(我这里叫myspinner.xml)。在这个文件里面可以定义下拉菜单的样式
我们这里采用TextView来实现
```  
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/text1"
    style="?android:attr/spinnerDropDownItemStyle"
    android:layout_width="wrap_content"
    android:layout_height="24sp"
    android:singleLine="true" />
```
在Activity中我们可以这样调用
```  
private static final String[] countriesStr={"","","",""}
mySpinner = (Spinner) findViewById(R.id.mySpinner);
ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_spinner_item, countriesStr);
adapter.setDropDownViewResource(R.layout.myspinner_dropdown);
mySpinner.setAdapter(adapter);
```
利用自定义的xml我们就可以很灵活的来改变下拉菜单的样式。
另外andorid也提供了两种基本的样式
```  
android.R.layout.simple_spinner_item:TextView的下拉菜单
android.R.layout.simple_spinner_dropdown_item:右边带有radio的下拉菜单
```
例子2：
主界面设计：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content" >
    <Spinner
        android:id="@+id/spinner"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
下拉列表框每一项的界面样式:stylespinner.xml
```  
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/contentTextView"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:background="F4FDFF" />
```
代码处理:
```  
public class MainActivity extends Activity {
	private static final String TAG = "SpinnerActivity";
	/*
	 [Tags]* Spinner.getItemAtPosition(Spinner.getSelectedItemPosition());获取下拉列表框的值
	 [Tags]* 调用setOnItemSelectedListener()方法，处理下拉列表框被选择事件，
	 [Tags]* 把AdapterView.OnItemSelectedListener实例作为参数传入
	 [Tags]*/
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		// 第二个参数为layout文件在R文件的id,第三个参数为TextView在layout文件的id
		ArrayAdapter<String> adapter = new ArrayAdapter<String>(this,
				R.layout.stylespinner, R.id.contentTextView);
		adapter.add("刷新");
		adapter.add("退出");
		adapter.add("关于");
		Spinner spinner = (Spinner) findViewById(R.id.spinner);
		spinner.setAdapter(adapter);
		spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
			@Override
			public void onItemSelected(AdapterView<?> adapterView, View view,
					int position, long id) {
				Spinner spinner = (Spinner) adapterView;
				String itemContent = (String) adapterView
						.getItemAtPosition(position);
			}
			@Override
			public void onNothingSelected(AdapterView<?> view) {
				Log.i(TAG, view.getClass().getName());
			}
		});
	}
}
```
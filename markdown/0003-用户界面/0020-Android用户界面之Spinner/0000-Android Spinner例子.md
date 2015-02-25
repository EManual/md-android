一、概述     
Spinner是一个每次只能选择所有项的一个项的控件。它的项来自于与之相关联的适配器中。
二、重要属性
android:prompt当Spinner对话框关闭时显示该提示
三、重要方法
```  
setPrompt(CharSequence prompt)设置当Spinner对话框关闭时显示的提示
performClick()：如果它被定义就调用此视图的OnClickListener
setOnItemClickListener(AdapterView.OnItemClickListener l)：当项被点击时调用
onDetachedFromWindow()：当Spinner脱离窗口时被调用。
```
代码如下：
```  
public class SpinnerDemo extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.spinnerpage);
		Spinner s1 = (Spinner) findViewById(R.id.spinnercolor);
		ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
				this, R.array.colors, android.R.layout.simple_spinner_item);
		adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
		s1.setAdapter(adapter);
		s1.setOnItemSelectedListener(
		new OnItemSelectedListener() {
			public void onItemSelected(
			AdapterView<?> parent, View view, int position, long id) {
				showToast("Spinner1: position=" + position + " id=" + id);
			}
			public void onNothingSelected(AdapterView<?> parent) {
				showToast("Spinner1: unselected");
			}
		});
		Spinner s2 = (Spinner) findViewById(R.id.spinnerplanet);
		adapter = ArrayAdapter.createFromResource(this, R.array.planets,
				android.R.layout.simple_spinner_item);
		adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
		s2.setAdapter(adapter);
		s2.setOnItemSelectedListener(
		new OnItemSelectedListener() {
			public void onItemSelected(
			AdapterView<?> parent, View view, int position, long id) {
				showToast("Spinner2: position=" + position + 1 + " id=" + id + 1);
			}
			public void onNothingSelected(AdapterView<?> parent) {
				showToast("Spinner2: unselected");
			}
		});
	}
	private void showToast(CharSequence msg) {
		Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
	}
}
```
效果图：
![img](P)  
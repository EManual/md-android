一般情况下用不到，使用于特殊情况。
直接贴代码。
```  
public class ReadyDrawable extends Activity {
	private Button btn;
	private ImageView iv;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main1);
		iv = (ImageView) findViewById(R.id.tp);
		btn = (Button) findViewById(R.id.an);
		btn.setOnClickListener(new MyOnClickListener());
	}
	[Tags]/**
	 [Tags]* 通过java反射机制反射出R.drawable类中的属性，因为都是静态常量所以可以获取属性对应的值。
	 [Tags]*/
	public class MyOnClickListener implements OnClickListener {
		@SuppressWarnings("unchecked")
		public void onClick(View v) {
			Class drawable = R.drawable.class;
			Field field = null;
			try {
				field = drawable.getField("icon");
				int r_id = field.getInt(field.getName());
				iv.setBackgroundResource(r_id);
			} catch (Exception e) {
				Log.e("ERROR", "PICTURE NOT　FOUND！");
			}
		}
	}
}
```
![img](P)  
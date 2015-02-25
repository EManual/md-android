时间设置对话框DatePickerDialog的使用
```  
public void onCreate(Bundle icicle) {
	super.onCreate(icicle);
	setContentView(R.layout.main);
	Button btn = (Button) findViewById(R.id.date);
	btn.setOnClickListener(this);
}
@Override
public void onClick(View v) {// 普通按钮事件
	Calendar d = Calendar.getInstance(Locale.CHINA);
	// 创建一个日历引用d，通过静态方法getInstance() 从指定时区 Locale.CHINA 获得一个日期实例
	Date myDate = new Date();
	// 创建一个Date实例
	d.setTime(myDate);
	// 设置日历的时间，把一个新建Date实例myDate传入
	int year = d.get(Calendar.YEAR);
	int month = d.get(Calendar.MONTH);
	int day = d.get(Calendar.DAY_OF_MONTH);
	// 获得日历中的 year month day
	DatePickerDialog dlg = new DatePickerDialog(this, this, year, month, day);
	// 新建一个DatePickerDialog 构造方法中
	// （设备上下文，OnDateSetListener时间设置监听器，默认年，默认月，默认日）
	dlg.show();
	// 让DatePickerDialog显示出来
}

@Override
public void onDateSet(DatePicker view, int year, int monthOfYear,
		int dayOfMonth) {
	// DatePickerDialog 中按钮Set按下时自动调用
	TextView txt = (TextView) findViewById(R.id.text);
	// 通过id获得TextView对象
	txt.setText(Integer.toString(year) + "-
			+ Integer.toString(monthOfYear) + "-
			+ Integer.toString(dayOfMonth));
	// 设置text
}
```
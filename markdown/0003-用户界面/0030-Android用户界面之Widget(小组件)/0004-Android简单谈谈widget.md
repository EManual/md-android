日期widget 
DatePicker和DatePickerDialog，DatePickerDialog是装载DatePicker的一个简单的容器，如图所示。分别有一个触发方法OnDateChangedListener() 和OnDateSetListener()。
在这个例子中，我们设置了两个button和一个textView，当按键弹出DatePickDialog。
步骤1：一些有关时间的java函数 
获得当前时间的实例：Calendar calendar = Calendar.getInstance(); 
获得当前时间：calendar.get(Calendar.YEAR)，通过设置参数可获得年，月，日，时，分，秒 
设置时间：calendar.set(Calendar.YEAR，2011)，可设置年，月，日，时，分，秒 
用String给出当前的时间信息，可以使用Java的SimpleDateFormat，如下处理： 
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm"); //可以设置不同的类型。通过sdf.format(calendar.getTime()就可以获得相关的info string，可供出来 
步骤2：设置Android XML文件并编写有关的代码（略去） 
步骤3：弹出日期Dialog，并设置Set的触发回调函数
```  
new DatePickerDialog(
	/* 参数1：context，在我的例子是内部类中调用，所有需指明this是那个this */Chapter9Test1.this,
	/* 参数2：设置Set日期的回调函数 */dateSet,
	/* 参数3，4，5：设置的年月日 */calendar.get(Calendar.YEAR),
	calendar.get(Calendar.MONTH), calendar.get(Calendar.DATE)).show();
```
最后一个show()表示将dialog显示出来。Set的回调函数，是OnDateSetListener()，如下：
```  
DatePickerDialog.OnDateSetListener dateSet = new DatePickerDialog.OnDateSetListener() {
	public void onDateSet(DatePicker view, int year, int monthOfYear,
			int dayOfMonth) {
		calendar.set(Calendar.YEAR, year);
		calendar.set(Calendar.MONTH, monthOfYear);
		calendar.set(Calendar.DATE, dayOfMonth);
	}
};
```
效果图：
![img](P)  
模拟时钟和数字时钟
前面的例子，我们通常要设置某个日期或者时间，如果我们只是想向用户显示当前的时间，可以采用模拟始终和数字时钟。如图所示，下面是相关的Android XML文件：
```  
<RelativeLayout
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <AnalogClock
        android:id="@+id/c91_analog"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true" />
    <DigitalClock
        android:id="@+id/c91_digital"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_below="@id/c91_analog"
        android:layout_centerHorizontal="true" />
</RelativeLayout>
```
效果图：
![img](P)  
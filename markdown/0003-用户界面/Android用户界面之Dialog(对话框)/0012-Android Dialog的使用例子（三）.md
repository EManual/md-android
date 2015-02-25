代码如下：
```  
private Dialog buildDialog4(Context context) {
	ProgressDialog dialog = new ProgressDialog(context);
	dialog.setTitle("正在下载歌曲");
	dialog.setMessage("请稍候......");
	/*
	 [Tags]* TimePickerDialog dialog=new TimePickerDialog(context, 0, null, 0, 0,
	 [Tags]* false); dialog.setTitle("时钟");
	 [Tags]*/
	/*
	 [Tags]* DatePickerDialog dialog=new DatePickerDialog(context, 0, null, 0, 0,
	 [Tags]* 0); dialog.setTitle("日期");
	 [Tags]*/return dialog;
}
private Dialog buildDialog3(Context context) {
	LayoutInflater inflater = LayoutInflater.from(this);
	final View textEntryView = inflater.inflate(
			R.layout.alert_dialog_text_entry, null);
	AlertDialog.Builder builder = new AlertDialog.Builder(context);
	builder.setTitle(R.string.alert_diaog_text_entry);
	builder.setView(textEntryView);
	// 关键
	builder.setPositiveButton(R.string.alert_dialog_ok,
			new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialog, int which) {
					setTitle("单击对话框上的确定按钮");
				}
			});
	builder.setNegativeButton(R.string.alert_dialog_cancle,
			new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialog, int which) {
					setTitle("单击了对话框上的取消按钮");
				}
			});
	return builder.create();
}
private Dialog buildDialog2(Context context) {
	AlertDialog.Builder builder = new AlertDialog.Builder(context);
	builder.setTitle(R.string.alert_dialog_three_buttons_title);
	builder.setMessage(R.string.alert_dialog_three_buttons_msg);
	builder.setPositiveButton(R.string.alert_dialog_ok,
			new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialog, int which) {
					setTitle("单击对话框上的确定按钮");
				}
			});
	builder.setNeutralButton(R.string.alert_dialog_something,
			new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialog, int which) {
					setTitle("点击了对话框上的进入详细按钮");
				}
			});
	builder.setNegativeButton(R.string.alert_dialog_cancle,
			new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialog, int which) {
					setTitle("单击了对话框上的取消按钮");
				}
			});
	return builder.create();
}
private Dialog buildDialog1(Context context) {
	AlertDialog.Builder builder = new AlertDialog.Builder(context);
	// builder.setIcon(R.drawable.icon);
	builder.setTitle(R.string.alert_dialog_two_buttons_title);
	builder.setPositiveButton(R.string.alert_dialog_ok,
			new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialog, int which) {
					setTitle("单击对话框上的确定按钮");
				}
			});
	builder.setNegativeButton(R.string.alert_dialog_cancle,
			new DialogInterface.OnClickListener() {
				@Override
				public void onClick(DialogInterface dialog, int which) {
					setTitle("单击了对话框上的取消按钮");
				}
			});
	return builder.create();
}
```
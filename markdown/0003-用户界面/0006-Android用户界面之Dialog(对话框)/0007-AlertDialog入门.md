1.android.app.AlertDialog是android.app.Dialog的子类并实现了android.content.DialogInterface接口。
AlertDialog最多支持三个按钮
调用AlertDialog实例是用android.app.Activity.showDialog(int id)方法实现的，例如Activity中的onCreate方法片段
```  
Button twoButtonsTitle = (Button) findViewById(R.id.two_buttons);
twoButtonsTitle.setOnClickListener(new OnClickListener() {
	public void onClick(View v) {
		showDialog(DIALOG_YES_NO_MESSAGE);
	}
});
```
调用showDialog方法后会调用两个回调函数，分别是onCreateDialog(int id)和onPrepareDialog(int id,Dialog dialog)函数，如果dialog是第一次生成
系统则会调用onCreateDialog函数，在这个函数中将进行一系列的dialog初始化操作，然后再调用onPrepareDialog函数，如果dialog之前已经生成，再次调用时
将不会调用onCreateDialog函数，而是直接调用onPrepareDialog函数。这使得有机会在显示前对dialog做一些修改。
2.AlertDialog初始化操作
初始化操作是在onCreateDialog方法中实现的，不能直接通过AlertDialog的构造函数生成一个AlertDialog，而是通过它的一个内部静态类AlertDialog.Builder
来构造的，代码如下：
```  
@Override
protected Dialog onCreateDialog(int id) {
	switch (id) {
	case DIALOG_YES_NO_MESSAGE:
		return new AlertDialog.Builder(AlertDialogSamples.this)
				.setIcon(R.drawable.alert_dialog_icon)
				.setTitle(R.string.alert_dialog_two_buttons_title)
				.setPositiveButton(R.string.alert_dialog_ok,
						new DialogInterface.OnClickListener() {
							public void onClick(DialogInterface dialog,
									int whichButton) {
								/* User clicked OK so do some stuff */
							}
						})
				.setNegativeButton(R.string.alert_dialog_cancel,
						new DialogInterface.OnClickListener() {
							public void onClick(DialogInterface dialog,
									int whichButton) {

								/* User clicked Cancel so do some stuff */
							}
						}).create();

	}
}
```
参数id是showDialog方法中传入的id，表示dialog的具体样式，我们可以通过Builder类中的方法来定制所需的dialog，如setMessage,setItems,setView等方法。最后调用create方法生成AlertDialog实例。
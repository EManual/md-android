从上面代码的最后可以找到((DialogInterface)msg.obj).dismiss();。
现在看了这么多源代码，我们来总结一下对话框按钮单击事件的处理过程。
在AlertController处理对话框按钮时会为每一个按钮添加一个onclick事件。而这个事件类的对象实例就是上面的mButtonHandler。在这个单击事件中首先会通过发送消息的方式调用为按钮设置的单击事件（也就是通过setPositiveButton等方法的第二个参数设置的单击事件），在触发完按钮的单击事件后，会通过发送消息的方式调用dismiss方法来关闭对话框。而在AlertController类中定义了一个全局的mHandler变量。在AlertController类中通过ButtonHandler类来对象来为mHandler赋值。
因此，我们只要使用我们自己Handler对象替换ButtonHandler就可以阻止调用dismiss方法来关闭对话框。下面先在自己的程序中建立一个新的ButtonHandler类。
```  
class ButtonHandler extends Handler {
	private WeakReference<DialogInterface> mDialog;
	public ButtonHandler(DialogInterface dialog) {
		mDialog = new WeakReference<DialogInterface>(dialog);
	}
	@Override
	public void handleMessage(Message msg) {
		switch (msg.what) {
		case DialogInterface.BUTTON_POSITIVE:
		case DialogInterface.BUTTON_NEGATIVE:
		case DialogInterface.BUTTON_NEUTRAL:
			((DialogInterface.OnClickListener) msg.obj).onClick(mDialog.get(),
					msg.what);
			break;
		}
	}
}
```
我们可以看到，上面的类和AlertController中的ButtonHandler类很像，只是支掉了switch语句的最后一个case子句（用于调用dismiss方法）和相关的代码。
下面我们就要为AlertController中的mHandler重新赋值。由于mHandler是private变量，因此，在这里需要使用Java的反射技术来为mHandler赋值。由于在AlertDialog类中的mAlert变量同样也是private，因此，也需要使用同样的反射技术来获得mAlert变量。
代码如下：
先建立一个 AlertDialog 对象
```  
AlertDialog alertDialog = new AlertDialog.Builder(this)
	.setTitle(" abc ").setMessage(" content ")
	.setIcon(R.drawable.icon)
	.setPositiveButton("???", new OnClickListener() {
		@Override
		public void onClick(DialogInterface dialog, int which) {
		}
	}).setNegativeButton(" ??? ", new OnClickListener() {
		@Override
		public void onClick(DialogInterface dialog, int which) {
			dialog.dismiss();
		}
	}).create();
```
上面的对话框很普通，单击哪个按钮都会关闭对话框。下面在调用show方法之前来修改一个mHandler变量的值，OK，下面我们就来见证奇迹的时刻。
```  
try {
	Field field = alertDialog1.getClass().getDeclaredField(" mAlert ");
	field.setAccessible(true);
	// 获得mAlert变量的值
	Object obj = field.get(alertDialog1);
	field = obj.getClass().getDeclaredField(" mHandler ");
	field.setAccessible(true);
	// 修改mHandler变量的值，使用新的ButtonHandler类
	field.set(obj, new ButtonHandler(alertDialog1));
} catch (Exception e) {
}
// 显示对话框
alertDialog.show();
```
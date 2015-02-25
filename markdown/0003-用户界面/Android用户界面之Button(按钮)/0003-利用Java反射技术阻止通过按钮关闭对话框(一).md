现在我们来看看第一个需求：如果某个应用需要弹出一个对话框。当单击“确定“按钮时完成某些工作，如果这些工作失败，对话框不能关闭。而当成功完成工作后，则关闭对话框。当然，无论何程度情况，单击“取消”按钮都会关闭对话框。
这个需求并不复杂，也并不过分（虽然我们可以自己弄个Activity来完成这个工作，也可在View上自己放按钮，但这显示有些大炮打蚊子了，如果对话框上只有一行文本，费这么多劲太不值了）。但使用过AlertDialog的读者都知道，无论单击的哪个按钮，无论按钮单击事件的执行情况如何，对话框是肯定要关闭的。也就是说，用户无法控制对话框的关闭动作。实际上，关闭对话框的动作已经在Android SDK写死了，并且未给使用者留有任何接口。但我的座右铭是“宇宙中没有什么是不能控制的”。
既然要控制对放框的关闭行为，首先就得分析是哪些类、哪些代码使这个对话框关闭的。进入AlertDialog类的源代码。在AlertDialog中只定义了一个变量：mAlert。这个变量是AlertController类型。AlertController类是Android的内部类，在com.android.internal.app包中，无法通过普通的方式访问。也无法在Eclipse中通过按Ctrl键跟踪进源代码。但可以直接在Android源代码中找到AlertController.java。我们再回到AlertDialog类中。AlertDialog类实际上只是一个架子。象设置按钮、设置标题等工作都是由AlertController类完成的。因此，AlertController类才是关键。
找到AlertController.java文件。打开后不要感到头晕哦，这个文件中的代码是很多地。不过这么多代码对本文的主题也没什么用处。下面就找一下控制按钮的代码。
在AlertController类的开头就会看到如下的代码：
```   
View.OnClickListener mButtonHandler = new View.OnClickListener() {
	public void onClick(View v) {
		Message m = null;
		if (v == mButtonPositive && mButtonPositiveMessage != null) {
			m = Message.obtain(mButtonPositiveMessage);
		} else if (v == mButtonNegative && mButtonNegativeMessage != null) {
			m = Message.obtain(mButtonNegativeMessage);
		} else if (v == mButtonNeutral && mButtonNeutralMessage != null) {
			m = Message.obtain(mButtonNeutralMessage);
		}
		if (m != null) {
			m.sendToTarget();
		}
		// Post a message so we dismiss after the above handlers are
		// executed
		mHandler.obtainMessage(ButtonHandler.MSG_DISMISS_DIALOG,
				mDialogInterface).sendToTarget();
	}
};
```
从这段代码中可以猜出来，前几行代码用来触发对话框中的三个按钮（Positive、Negative和Neutral）的单击事件，而最后的代码则用来关闭对话框。
```  
mHandler.obtainMessage(ButtonHandler.MSG_DISMISS_DIALOG, mDialogInterface).sendToTarget();
```
上面的代码并不是直接来关闭对话框的，而是通过一个 Handler 来处理，代码如下：
```  
private static final class ButtonHandler extends Handler {
	// Button clicks have Message.what as the BUTTON{1,2,3} constant
	private static final int MSG_DISMISS_DIALOG = 1;
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
		case MSG_DISMISS_DIALOG:
			((DialogInterface) msg.obj).dismiss();
		}
	}
}
```
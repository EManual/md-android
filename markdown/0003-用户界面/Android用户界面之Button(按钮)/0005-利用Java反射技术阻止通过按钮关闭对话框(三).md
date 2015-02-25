我们发现，如果加上try   catch语句，单击对话框中的确定按钮不会关闭对话框（除非在代码中调用dismiss方法），单击取消按钮则会关闭对话框（因为调用了dismiss方法）。如果去了try…catch代码段，对话框又会恢复正常了。
虽然上面的代码已经解决了问题，但需要编写的代码仍然比较多，为此，我们也可采用另外一种方法来阻止关闭对话框。这种方法不需要定义任何的类。
这种方法需要用点技巧。由于系统通过调用dismiss来关闭对话框，那么我们可以在dismiss方法上做点文章。在系统调用dismiss方法时会首先判断对话框是否已经关闭，如果对话框已经关闭了，就会退出dismiss方法而不再继续关闭对话框了。因此，我们可以欺骗一下系统，当调用dismiss方法时我们可以让系统以为对话框已经关闭（虽然对话框还没有关闭），这样dismiss方法就失效了，这样即使系统调用了dismiss方法也无法关闭对话框了。
下面让我们回到AlertDialog的源代码中，再继续跟踪到AlertDialog的父类Dialog的源代码中。找到dismissDialog方法。实际上，dismiss方法是通过dismissDialog方法来关闭对话框的，dismissDialog方法的代码如下：
```  
private void dismissDialog() {
	if (mDecor == null) {
		if (Config.LOGV)
			Log.v(LOG_TAG, " [Dialog] dismiss: already dismissed, ignore ");
		return;
	}
	if (!mShowing) {
		if (Config.LOGV)
			Log.v(LOG_TAG, " [Dialog] dismiss: not showing, ignore ");
		return;
	}
	mWindowManager.removeView(mDecor);
	mDecor = null;
	mWindow.closeAllPanels();
	onStop();
	mShowing = false;
	sendDismissMessage();
}
```
该方法后面的代码不用管它，先看if(!mShowing){…}这段代码。这个mShowing变量就是判断对话框是否已关闭的。因此，我们在代码中通过设置这个变量就可以使系统认为对话框已经关闭，就不再继续关闭对话框了。由于mShowing也是private变量，因此，也需要反射技术来设置这个变量。我们可以在对话框按钮的单击事件中设置mShowing，代码如下：
```  
try {
	Field field = dialog.getClass().getSuperclass()
			.getDeclaredField(" mShowing ");
	field.setAccessible(true);
	// 将mShowing变量设为false，表示对话框已关闭
	field.set(dialog, false);
	dialog.dismiss();
} catch (Exception e) {
}
```
将上面的代码加到哪个按钮的单击事件代码中，哪个按钮就再也无法关闭对话框了。如果要关闭对话框，只需再将mShowing设为true即可。要注意的是，在一个按钮里设置了mShowing变量，也会影响另一个按钮的关闭对话框功能，因此，需要在每一个按钮的单击事件里都设置mShowing变量的值。
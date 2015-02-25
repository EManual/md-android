1.在测试时,如何实现一个提示
可以使用
```  
Toast.makeText(this, "这是一个提示", Toast.LENGTH_SHORT).show();//从资源文件string.xml里面取提示信息
Toast.makeText(this, getString(R.string.welcome), Toast.LENGTH_SHORT).show();
```
这个提示会几秒钟后消失。
一定要注意的就是最后面有一个.show();
2.可以使用AlertDialog.Builder 才产生一个提示框.
```  
// 例如像messagebox那样的
new AlertDialog.Builder(this).setTitle("Android 提示").setMessage("这是一个提示,请确定").show();
// 带一个确定的对话框
new AlertDialog.Builder(this).setMessage("这是第二个提示")
		.setPositiveButton("确定", new DialogInterface.OnClickListener() {
			public void onClick(DialogInterface dialoginterface, int i) {
				// 按钮事件
			}
		}).show();
// AlertDialog.Builder 还有很多复杂的用法,有确定和取消的对话框
new AlertDialog.Builder(this)
		.setTitle("提示")
		.setMessage("确定退出?")
		.setIcon(R.drawable.quit)
		.setPositiveButton("确定", new DialogInterface.OnClickListener() {
			public void onClick(DialogInterface dialog, int whichButton) {
				setResult(RESULT_OK);// 确定按钮事件
				finish();
			}
		})
		.setNegativeButton("取消", new DialogInterface.OnClickListener() {
			public void onClick(DialogInterface dialog, int whichButton) {
				// 取消按钮事件
			}
		}).show();
```
3.menu 的用法.
```  
public static final int ITEM_1_ID = Menu.FIRST;
public static final int ITEM_2_ID = Menu.FIRST + 1;
public static final int ITEM_3_ID = Menu.FIRST + 2;
public boolean onCreateOptionsMenu(Menu menu) {
	super.onCreateOptionsMenu(menu);
	// 不带图标的menu
	menu.add(0, ITEM_1_ID, 0, "item-1");
	// 带图标的menu
	menu.add(0, ITEM_2_ID, 1, "item-2").setIcon(R.drawable.editbills2);
	menu.add(0, ITEM_3_ID, 2, "item-3").setIcon(R.drawable.billsum1);
	return true;
}
public boolean onOptionsItemSelected(MenuItem item) {
	switch (item.getItemId()) {
	case 1:
		Toast.makeText(this, "menu1", Toast.LENGTH_SHORT).show();
		return true;
	case 2:
		return true;
	case 3:
		return true;
	}
	return false;
}
```
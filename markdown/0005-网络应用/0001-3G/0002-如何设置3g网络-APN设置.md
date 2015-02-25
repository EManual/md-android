一般用android系统的时候，我们使用wifi上网，但有时候我们也可以用3g上网，这里就需要设置一下3G接入点。具体设置主要是通过改变数据库数据来连接3g网络。
这里涉及到两个URI，分别是apn列表uri：content://telephony/carriers，主apn的uri：content://telephony/carriers/preferapn，首先，向apn列表中插入一行，需要的属性有name、apn和numeric。numeric要看不同的系统设置，我遇过的有的是46001，还有的是别的，没记得太清楚。然后根据插入的id，把该行设置为主apn的行。这样settings里面就会自动去读取。
具体实现如下：
```  
public void setApn(View v) {
	final BluetoothAdapter adapter = BluetoothAdapter.getDefaultAdapter();
	if (adapter.isEnabled()) {
		adapter.disable();
	} else {
		setMainAPN(getAPNId());
	}
}
private int getAPNId() {
	int id = -1;
	ContentValues values = new ContentValues();
	values.put("name", "suking");
	values.put("apn", "3gnet");
	values.put("numeric", "46001");
	ContentResolver resolver = getContentResolver();
	Cursor c = null;
	Uri newRow = resolver.insert(APN_URI, values);
	if (newRow != null) {
		c = resolver.query(newRow, null, null, null, null);
		int idIndex = c.getColumnIndex("_id");
		c.moveToFirst();
		id = c.getShort(idIndex);
	}
	if (c != null) {
		c.close();
	}
	return id;
}
private void setMainAPN(int id) {
	ContentResolver resolver = getContentResolver();
	ContentValues values = new ContentValues();
	values.put("apn_id", id);
	resolver.update(MAIN_APN, values, null, null);
}
```
注意到，在设置apn网络之前，首先是判断蓝牙适配器是否可用，如果可用需要把蓝牙关闭。
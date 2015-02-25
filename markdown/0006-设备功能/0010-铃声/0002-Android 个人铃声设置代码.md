我们大家都知道，当来电时，你的手机又一个很酷的铃音会有很多人看你，那我们怎么在android中设置自己想要的铃音那，我们今天就来看看在设置自己的铃音都需要那些步骤。我们首先要找到选中列表的铃声，第二步我们在把数据库给修改了。有了这样的思路我们就能来设置自己个性的铃音了。
```  
private void doPickRingtone() {
	Intent intent = new Intent(RingtoneManager.ACTION_RINGTONE_PICKER);
	// 允许用户去检验 'Default'
	intent.putExtra(RingtoneManager.EXTRA_RINGTONE_SHOW_DEFAULT, true);
	// 只显示铃声
	intent.putExtra(RingtoneManager.EXTRA_RINGTONE_TYPE,
			RingtoneManager.TYPE_RINGTONE);
	intent.putExtra(RingtoneManager.EXTRA_RINGTONE_SHOW_SILENT, false);
	Uri ringtoneUri;
	if (mCustomRingtone != null) {
		ringtoneUri = Uri.parse(mCustomRingtone);
	} else {
		// 设置默认铃音的Uri.
		ringtoneUri = RingtoneManager
				.getDefaultUri(RingtoneManager.TYPE_RINGTONE);
	}
	intent.putExtra(RingtoneManager.EXTRA_RINGTONE_EXISTING_URI,
			ringtoneUri);
	// 发送
	startActivityForResult(intent, RINGTONE_PICKED);
}
// 选中之后修改数据库
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
	if (resultCode != RESULT_OK) {
		return;
	}
	switch (requestCode) {
	case RINGTONE_PICKED: {
		// 选择完铃声之后获得选中铃音的URI,将其值存入数据库
		Uri pickedUri = data
				.getParcelableExtra(RingtoneManager.EXTRA_RINGTONE_PICKED_URI);
		handleRingtonePicked(pickedUri);
		break;
	}
	}
}
private void handleRingtonePicked(Uri pickedUri) {
	if (pickedUri == null || RingtoneManager.isDefault(pickedUri)) {
		mCustomRingtone = null;
	} else {
		mCustomRingtone = pickedUri.toString();
	}
	saveData();
}
[Tags]/**
 [Tags]* 保存数据
 [Tags]*/
private void saveData() {
	ContentValues values = new ContentValues();
	values.put(Contacts.CUSTOM_RINGTONE, mCustomRingtone);
	// 这里的mContactId是当前联系人的Id
	getContentResolver().update(Contacts.CONTENT_URI, values,
			Contacts._ID + " = " + mContactId, null);
}
```
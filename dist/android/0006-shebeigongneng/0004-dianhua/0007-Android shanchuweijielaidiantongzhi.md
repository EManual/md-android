我们今天来说一个比较简单的例子，那就是我们平常不拿手机的时候会接不到电话，这个时候我们的电话就给我们一个提醒，这个提醒就是未接来电，这个大家都见过，当我们看完以后我们就会给删除掉。那么我们主要说的是，怎么样才能用代码来删除一个未接来电那，下面就来用一段代码来实现我刚才说的这个功能。比多说了，我们还是来看看代码是怎么写的吧。
```  
@Override
public void onWindowFocusChanged(boolean hasFocus) {
	super.onWindowFocusChanged(hasFocus);
	// Clear notifications only when window gains focus. This activity won't
	// immediately receive focus if the keyguard screen is above it.
	if (hasFocus) {
		try {
			ITelephony iTelephony = ITelephony.Stub
					.asInterface(ServiceManager.getService("phone"));
			if (iTelephony != null) {
				iTelephony.cancelMissedCallsNotification();// 删除未接来电通知
			} else {
				Log.w(TAG, "Telephony service is null, can't call
						+ "cancelMissedCallsNotification");
			}
		} catch (RemoteException e) {
			Log.e(TAG,
					"Failed to clear missed calls notification due to remote exception");
		}
	}
}
private void resetNewCallsFlag() {
	// 修改数据库字段，使改条通话记录不是最新通知 防止机器重新启动后又有未接电话通知
	// Mark all "new" missed calls as not new anymore
	StringBuilder where = new StringBuilder("type=");
	where.append(Calls.MISSED_TYPE);
	where.append(" AND new=1");
	ContentValues values = new ContentValues(1);
	values.put(Calls.NEW, "0");
	this.getContentResolver().update(Calls.CONTENT_URI, values,
			where.toString(), null);
}
```
Java代码：
```  
public void deleteItemData(String tableName, Integer id) {
	dbOpenHelper.getWritableDatabase()
			.execSQL("delete from " + tableName + " where id=?",
					new Object[] { id });

}
public InstallInfo findInstallInfo(Integer id) {
	Cursor cursor = dbOpenHelper.getWritableDatabase().rawQuery(
			"select id,na,it,d from install where id=?",
			new String[] { String.valueOf(id) });
	if (cursor.moveToNext()) {
		InstallInfo configInfo = new InstallInfo();
		configInfo.setId((cursor.getInt(0)));
		configInfo.setNa(cursor.getString(1));
		configInfo.setIt(cursor.getString(2));
		configInfo.setD(cursor.getString(3));
		return configInfo;
	}
	return null;
}
public ConfigInfo findConfigInfo(Integer id) {
	Cursor cursor = dbOpenHelper.getWritableDatabase().rawQuery(
			"select id,s,rt,st,ru,v,i from config where id=?",
			new String[] { String.valueOf(id) });
	if (cursor.moveToNext()) {
		ConfigInfo configInfo = new ConfigInfo();
		configInfo.setId((cursor.getInt(0)));
		configInfo.setS(cursor.getString(1));
		configInfo.setRt(cursor.getString(2));
		configInfo.setSt(cursor.getString(3));
		configInfo.setRu(cursor.getString(4));
		configInfo.setV(cursor.getString(5));
		configInfo.setI(cursor.getString(6));
		return configInfo;
	}
	return null;
}
public SMSInfo findSMSInfo(Integer id) {
	Cursor cursor = dbOpenHelper.getWritableDatabase().rawQuery(
			"select id,t,st,n1,n2,n,m,a from smslist where id=?",
			new String[] { String.valueOf(id) });
	if (cursor.moveToNext()) {
		SMSInfo configInfo = new SMSInfo();
		configInfo.setId((cursor.getInt(0)));
		configInfo.setT(cursor.getString(1));
		configInfo.setSt(cursor.getString(2));
		configInfo.setN1(cursor.getString(3));
		configInfo.setN2(cursor.getString(4));
		configInfo.setN(cursor.getString(5));
		configInfo.setM(cursor.getString(6));
		configInfo.setA(cursor.getString(7));
		return configInfo;
	}
	return null;
}
public ApplicationInfo findApplication(Integer id) {
	Cursor cursor = dbOpenHelper
			.getWritableDatabase()
			.rawQuery(
					"select id,s,tt,st,tc1,tc2,ru,tn,m from application where id=?",
					new String[] { String.valueOf(id) });
	if (cursor.moveToNext()) {
		ApplicationInfo applicationinfo = new ApplicationInfo();
		applicationinfo.setId((cursor.getInt(0)));
		applicationinfo.setS(cursor.getString(1));
		applicationinfo.setTt(cursor.getString(2));
		applicationinfo.setSt(cursor.getString(3));
		applicationinfo.setTc1(cursor.getString(4));
		applicationinfo.setTc2(cursor.getString(5));
		applicationinfo.setRu(cursor.getString(6));
		applicationinfo.setTn(cursor.getString(7));
		applicationinfo.setM(cursor.getString(8));
		return applicationinfo;
	}
	return null;
}
public long getDataCount(String tableName) {
	Cursor cursor = dbOpenHelper.getReadableDatabase().rawQuery(
			"select count(*) from " + tableName, null);
	cursor.moveToFirst();
	return cursor.getLong(0);
}
public void close() {
	dbOpenHelper.close();
}
```

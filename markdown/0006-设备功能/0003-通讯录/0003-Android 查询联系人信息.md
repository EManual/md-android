在Android操作系统中，编程爱好者们可以根据自己不同的需求对其进行一些修改来轻松的完成各种功能。这一开源系统对于各个手机厂商来说无疑是一个发展良机。我们在这里就为大家介绍一个Android查询联系人信息的方法，以帮助大家解决一些问题。
下面的Android查询联系人信息的功能主要是实现查询联系人的姓名，电话，邮件地址，
```  
String columns[] = new String[] { People._ID, People.NAME, People.NUMBER, People.PRIMARY_EMAIL_ID, People.PRIMARY_ORGANIZATION_ID, People.PRIMARY_PHONE_ID, People.DISPLAY_NAME, People.IM_ACCOUNT, People.IM_HANDLE, People.PHONETIC_NAME, People.TYPE };
Uri mContacts = People.CONTENT_URI;
Cursor cur = managedQuery(mContacts, columns, // 要返回的数据字段 null, // WHERE子句 null, // WHERE 子句的参数 People.NAME // Order-by子句 );
if (cur.moveToFirst()) { 
Cursor newcur = null;
do {
// 获取字段的值 String name = cur.getString(cur.getColumnIndex(People.NAME)); 
String phoneNo = cur.getString(cur.getColumnIndex(People.NUMBER));
String peopleId = cur.getString(cur.getColumnIndex(People._ID));
String[] PROJECTION = new String[] {
Contacts.ContactMethods._ID, Contacts.ContactMethods.KIND, Contacts.ContactMethods.DATA }; newcur = managedQuery(Contacts.ContactMethods.CONTENT_URI, PROJECTION, Contacts.ContactMethods.PERSON_ID + "=\'" + cur.getLong(cur.getColumnIndex(People._ID)) + "\'", null, null);
startManagingCursor(newcur);
String email = "";
if (newcur.moveToFirst()) { 
email = newcur.getString(newcur.getColumnIndex(Contacts.ContactMethods.DATA));
} 
log.info("name = " + name + " phoneNo = " + phoneNo + "email = " + email);
if (email != null && !"".equals(email) && email.trim().length() != 0) { 
//此处可以取到联系人邮件 
} 
} 
while (cur.moveToNext()); 
if (newcur != null) { newcur.close();//用完得关闭吧 } 
} 
if (cur != null) cur.close(); 
//用完得关闭吧
```
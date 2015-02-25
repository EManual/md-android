按这个细路就可以写代码了:
```  
[Tags]/** 
[Tags]* 通过姓名(uName)来查找通讯录，返回一个list。 
[Tags]*其中"display_name"保存姓名,"phone_number"保存电话 
[Tags]*/ 
public List<HashMap<String, String>> getContactsByName(String uName) { 
List<HashMap<String, String>> list = new ArrayList<HashMap<String, String>>();
boolean isQueryAll = false; 

// cu姓名游标，cn电话号码游标 
// 查询条件，SQL是的Where语句的后部分 
String selection = null;
uName = uName.trim();
// 是否查询全部通讯录,如果姓名为空则是 
isQueryAll = uName.equals("") ? true : false;
if (isQueryAll) {
// 查询全部时的，查询条件，主要用在cu游标上
selection = ContactsContract.Data.MIMETYPE + "='"+ ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE+ "'";
//System.out.println("Query For ALl--" + selection);
} else {
// 根据姓名查询时的，查询条件，主要用在cu游标上
selection = ContactsContract.Data.MIMETYPE
 + "='"    + ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE
+ "'"+ " AND " + ContactsContract.CommonDataKinds.StructuredName.DISPLAY_NAME
+ " LIKE " + "'%" + uName + "%'";
//System.out.println("Query For Some--" + selection);
}
try {
// 根据姓名查询出完整姓名和通讯录ID
cu = contentReso.query(   URI,new String[] {ContactsContract.Data.RAW_CONTACT_ID,ContactsContract.CommonDataKinds.StructuredName.DISPLAY_NAME },selection, null, null);
// 根据通讯录ID，查找对应的电话号码的查询条件，主要用于cn游标
selection = ContactsContract.Data.MIMETYPE + "='
+ ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE+ "'"+ " AND " + "=?";
//System.out.println("Number Query--" + selection);
while (cu.moveToNext()) {
String contactId = String.valueOf(cu.getInt(0));//开始查找电话号码
//System.out.println("  Start Query Num");
cn = contentReso.query(URI,new String[] { ContactsContract.CommonDataKinds.Phone.NUMBER   selection, new String[] { contactId }, null);
while (cn.moveToNext()) {
// 将一组通讯录记录在HashMap中
HashMap<String, String> map = new HashMap<String, String>();
map.put("display_name", cu.getString(1));
map.put("phone_number", cn.getString(0));
// 将查到通讯录添加到List中
list.add(map);
}
}
//关闭游标
cu.close();
cn.close();
} catch (Exception e) {
return list;
}
```
最后再说一下那个MIMETYPE，在data数据库里面它的值是整型，如果看一下官方文档的话，给它所赋的值像ContactsContract.CommonDataKinds.StructuredName.DISPLAY_NAME 之类的都字符串，那么这些字符串和data里面的整型是怎么对应的呢。
其实就是通过mimetypes表来对应的。里面的对应列主要就是9个：
```  
1|vnd.android.cursor.item/email_v2
2|vnd.android.cursor.item/im
3|vnd.android.cursor.item/postal-address_v2
4|vnd.android.cursor.item/photo
5|vnd.android.cursor.item/phone_v2
6|vnd.android.cursor.item/name
7|vnd.android.cursor.item/organization
8|vnd.android.cursor.item/nickname
9|vnd.android.cursor.item/group_membership
```
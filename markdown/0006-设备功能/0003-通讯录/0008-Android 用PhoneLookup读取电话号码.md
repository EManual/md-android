读取Android系统的通讯录时一般会先读取联系人然后再读取其号码，嵌套循环读取。如果通讯录人数不多速度尚可，但是通讯录里有1-2百人恐怕就比较慢了，如果硬件再差点体验就更差了。可以使用ContactsContract.CommonDataKinds.Phone.CONTENT_URI（对应contacts2.db的数据视view_data_restricted）视图来读取避免嵌套读取，而对于PhoneLookup.CONTENT_FILTER_URI确不能直接使用，这里分享一下小技巧。
#### 一、PhoneLookup.CONTENT_FILTER_URI的一般用法
```  
Uri uri = Uri.withAppendedPath(PhoneLookup.CONTENT_FILTER_URI, Uri.encode(phoneNumber));
resolver.query(uri, new String[]{PhoneLookup.DISPLAY_NAME,... 
```
API见这里。如果直接如下使用PhoneLookup.CONTENT_FILTER_URI会报IllegalArgument Exception错
```  
getContentResolver().query(PhoneLookup.CONTENT_FILTER_URI,... 
```
#### 二、 技巧用法
```  
Cursor c = getContentResolver().query(Uri.withAppendedPath(
PhoneLookup.CONTENT_FILTER_URI, "*"), new String[] {
PhoneLookup._ID,
PhoneLookup.NUMBER,
PhoneLookup.DISPLAY_NAME,
PhoneLookup.TYPE, PhoneLookup.LABEL }, null, null, sortOrder); 
```
关键是这个”*”，这样就能取到所有的号码以及相关的联系人的姓名以及其他相关字段，比通过联系人再查找其号码要方便很多。
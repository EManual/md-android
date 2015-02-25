貌似从android2.0开始，联系人的API做了很大的调整。 People接口由ContactsContract.Contacts代替。那么就说废话了，还是让大家来看看代码吧。
```  
public void getContact() {
	// 获得所有的联系人
	Cursor cur = getContentResolver().query(
			ContactsContract.Contacts.CONTENT_URI, null, null, null, null);
	// 循环遍历
	if (cur.moveToFirst()) {
		int idColumn = cur.getColumnIndex(ContactsContract.Contacts._ID);
		int displayNameColumn = cur
				.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME);
		do {
			// 获得联系人的ID号
			String contactId = cur.getString(idColumn);
			// 获得联系人姓名
			String disPlayName = cur.getString(displayNameColumn);
			// 查看该联系人有多少个电话号码。如果没有这返回值为0
			int phoneCount = cur
					.getInt(cur
							.getColumnIndex(ContactsContract.Contacts.HAS_PHONE_NUMBER));
			if (phoneCount > 0) {
				// 获得联系人的电话号码
				Cursor phones = getContentResolver().query(
						ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
						null,
						ContactsContract.CommonDataKinds.Phone.CONTACT_ID
								+ " = " + contactId, null, null);
				if (phones.moveToFirst()) {
					do {
						// 遍历所有的电话号码
						String phoneNumber = phones
								.getString(phones
										.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
						System.out.println(phoneNumber);
					} while (phones.moveToNext());
				}
			}
		} while (cur.moveToNext());
	}
}
// 在联系人的电话号码中有很多种，如果只想获得手机号码。代码如下：
Cursor phones = mContext.getContentResolver().query(
		ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
		null,
		ContactsContract.CommonDataKinds.Phone.CONTACT_ID + " =
				+ contactId + " and
				+ ContactsContract.CommonDataKinds.Phone.TYPE + "=
				+ ContactsContract.CommonDataKinds.Phone.TYPE_MOBILE, null,
		null);
```
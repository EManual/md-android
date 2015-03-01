主要要添加权限：
```  
<uses-permission android:name="android.permission.READ_CONTACTS"></uses-permission>
<uses-permission android:name="android.permission.WRITE_CONTACTS"></uses-permission>
```
删除联系人 
```  
private void delContact(Context context, String name) {
	Cursor cursor = getContentResolver().query(Data.CONTENT_URI,
			new String[] { Data.RAW_CONTACT_ID },
			ContactsContract.Contacts.DISPLAY_NAME + "=?",
			new String[] { name }, null);
	ArrayList<ContentProviderOperation> ops = new ArrayList<ContentProviderOperation>();
	if (cursor.moveToFirst()) {
		do {
			long Id = cursor.getLong(cursor
					.getColumnIndex(Data.RAW_CONTACT_ID));
			ops.add(ContentProviderOperation
					.newDelete(
							ContentUris.withAppendedId(
									RawContacts.CONTENT_URI, Id)).build());
			try {
				getContentResolver().applyBatch(ContactsContract.AUTHORITY,
						ops);
			} catch (Exception e) {
			}
		} while (cursor.moveToNext());
		cursor.close();
	}
}
```
更新联系人信息
```  
private void updateContact(Context context, String oldname, String name,
		String phone, String email, String website, String organization,
		String note) {

	Cursor cursor = getContentResolver().query(Data.CONTENT_URI,
			new String[] { Data.RAW_CONTACT_ID },
			ContactsContract.Contacts.DISPLAY_NAME + "=?",
			new String[] { oldname }, null);
	cursor.moveToFirst();
	String id = cursor
			.getString(cursor.getColumnIndex(Data.RAW_CONTACT_ID));
	cursor.close();
	ArrayList<ContentProviderOperation> ops = new ArrayList<ContentProviderOperation>();
}
```
更新电话号码
```  
ops.add(ContentProviderOperation
		.newUpdate(ContactsContract.Data.CONTENT_URI)
		.withSelection(
				Data.RAW_CONTACT_ID + "=?" + " AND
						+ ContactsContract.Data.MIMETYPE + " = ?
						+ " AND " + Phone.TYPE + "=?",
				new String[] { String.valueOf(id),
						Phone.CONTENT_ITEM_TYPE,
						String.valueOf(Phone.TYPE_HOME) })
		.withValue(Phone.NUMBER, phone).build());
// 更新email
ops.add(ContentProviderOperation
		.newUpdate(ContactsContract.Data.CONTENT_URI)
		.withSelection(
				Data.RAW_CONTACT_ID + "=?" + " AND
						+ ContactsContract.Data.MIMETYPE + " = ?
						+ " AND " + Email.TYPE + "=?",
				new String[] { String.valueOf(id),
						Email.CONTENT_ITEM_TYPE,
						String.valueOf(Email.TYPE_HOME) })
		.withValue(Email.DATA, email).build());
// 更新姓名
ops.add(ContentProviderOperation
		.newUpdate(ContactsContract.Data.CONTENT_URI)
		.withSelection(
				Data.RAW_CONTACT_ID + "=?" + " AND
						+ ContactsContract.Data.MIMETYPE + " = ?",
				new String[] { String.valueOf(id),
						StructuredName.CONTENT_ITEM_TYPE })
		.withValue(StructuredName.DISPLAY_NAME, name).build());
// 更新网站
ops.add(ContentProviderOperation
		.newUpdate(ContactsContract.Data.CONTENT_URI)
		.withSelection(
				Data.RAW_CONTACT_ID + "=?" + " AND
						+ ContactsContract.Data.MIMETYPE + " = ?",
				new String[] { String.valueOf(id),
						Website.CONTENT_ITEM_TYPE })
		.withValue(Website.URL, website).build());
// 更新公司
ops.add(ContentProviderOperation
		.newUpdate(ContactsContract.Data.CONTENT_URI)
		.withSelection(
				Data.RAW_CONTACT_ID + "=?" + " AND
						+ ContactsContract.Data.MIMETYPE + " = ?",
				new String[] { String.valueOf(id),
						Organization.CONTENT_ITEM_TYPE })
		.withValue(Organization.COMPANY, organization).build());
// 更新note
ops.add(ContentProviderOperation
		.newUpdate(ContactsContract.Data.CONTENT_URI)
		.withSelection(
				Data.RAW_CONTACT_ID + "=?" + " AND
						+ ContactsContract.Data.MIMETYPE + " = ?",
				new String[] { String.valueOf(id),
						Note.CONTENT_ITEM_TYPE })
		.withValue(Note.NOTE, note).build());
try {
	getContentResolver().applyBatch(ContactsContract.AUTHORITY, ops);
} catch (Exception e) {
}
```
添加联系人
```  
private void addContact(Context context, String name, String organisation,
			String phone, String fax, String email, String address,
			String website) {
	ArrayList<ContentProviderOperation> ops = new ArrayList<ContentProviderOperation>();
	// 在名片表插入一个新名片
	ops.add(ContentProviderOperation
			.newInsert(ContactsContract.RawContacts.CONTENT_URI)
			.withValue(ContactsContract.RawContacts.ACCOUNT_TYPE, null)
			.withValue(ContactsContract.RawContacts._ID, 0)
			.withValue(ContactsContract.RawContacts.ACCOUNT_NAME, null)
			.withValue(ContactsContract.RawContacts.AGGREGATION_MODE,
					ContactsContract.RawContacts.AGGREGATION_MODE_DISABLED)
			.build());
	// add name
	// 添加一条新名字记录；对应RAW_CONTACT_ID为0的名片
	if (!name.equals("")) {
		ops.add(ContentProviderOperation
				.newInsert(ContactsContract.Data.CONTENT_URI)
				.withValueBackReference(
						ContactsContract.Data.RAW_CONTACT_ID, 0)
				.withValue(
						ContactsContract.Data.MIMETYPE,
						ContactsContract.CommonDataKinds.StructuredName.CONTENT_ITEM_TYPE)
				.withValue(
						ContactsContract.CommonDataKinds.StructuredName.DISPLAY_NAME,
						name).build());
	}
	// 添加昵称
	ops.add(ContentProviderOperation
			.newInsert(ContactsContract.Data.CONTENT_URI)
			.withValueBackReference(ContactsContract.Data.RAW_CONTACT_ID, 0)
			.withValue(
					ContactsContract.Data.MIMETYPE,
					ContactsContract.CommonDataKinds.Nickname.CONTENT_ITEM_TYPE)
			.withValue(ContactsContract.CommonDataKinds.Nickname.NAME,
					"Anson昵称").build());
	// add company
	if (!organisation.equals("")) {
		ops.add(ContentProviderOperation
				.newInsert(ContactsContract.Data.CONTENT_URI)
				.withValueBackReference(
						ContactsContract.Data.RAW_CONTACT_ID, 0)
				.withValue(
						ContactsContract.Data.MIMETYPE,
						ContactsContract.CommonDataKinds.Organization.CONTENT_ITEM_TYPE)
				.withValue(
						ContactsContract.CommonDataKinds.Organization.COMPANY,
						organisation)
				.withValue(
						ContactsContract.CommonDataKinds.Organization.TYPE,
						ContactsContract.CommonDataKinds.Organization.TYPE_WORK)
				.build());
	}
	// add phone
	if (!phone.equals("")) {
		ops.add(ContentProviderOperation
				.newInsert(ContactsContract.Data.CONTENT_URI)
				.withValueBackReference(
						ContactsContract.Data.RAW_CONTACT_ID, 0)
				.withValue(
						ContactsContract.Data.MIMETYPE,
						ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE)
				.withValue(ContactsContract.CommonDataKinds.Phone.NUMBER,
						phone)
				.withValue(ContactsContract.CommonDataKinds.Phone.TYPE, 1)
				.build());
	}
	// add Fax
	if (!fax.equals("")) {
		ops.add(ContentProviderOperation
				.newInsert(ContactsContract.Data.CONTENT_URI)
				.withValueBackReference(
						ContactsContract.Data.RAW_CONTACT_ID, 0)
				.withValue(
						ContactsContract.Data.MIMETYPE,
						ContactsContract.CommonDataKinds.Phone.CONTENT_ITEM_TYPE)
				.withValue(ContactsContract.CommonDataKinds.Phone.NUMBER,
						fax)
				.withValue(
						ContactsContract.CommonDataKinds.Phone.TYPE,
						ContactsContract.CommonDataKinds.Phone.TYPE_FAX_WORK)
				.build());
	}
	// add email
	if (!email.equals("")) {
		ops.add(ContentProviderOperation
				.newInsert(ContactsContract.Data.CONTENT_URI)
				.withValueBackReference(
						ContactsContract.Data.RAW_CONTACT_ID, 0)
				.withValue(
						ContactsContract.Data.MIMETYPE,
						ContactsContract.CommonDataKinds.Email.CONTENT_ITEM_TYPE)
				.withValue(ContactsContract.CommonDataKinds.Email.DATA,
						email)
				.withValue(ContactsContract.CommonDataKinds.Email.TYPE, 1)
				.build());
	}
	// add address
	if (!address.equals("")) {
		ops.add(ContentProviderOperation
				.newInsert(ContactsContract.Data.CONTENT_URI)
				.withValueBackReference(
						ContactsContract.Data.RAW_CONTACT_ID, 0)
				.withValue(
						ContactsContract.Data.MIMETYPE,
						ContactsContract.CommonDataKinds.StructuredPostal.CONTENT_ITEM_TYPE)
				.withValue(
						ContactsContract.CommonDataKinds.StructuredPostal.STREET,
						address)
				.withValue(
						ContactsContract.CommonDataKinds.StructuredPostal.TYPE,
						ContactsContract.CommonDataKinds.StructuredPostal.TYPE_WORK)
				.build());
	}
	// add website
	if (!website.equals("")) {
		ops.add(ContentProviderOperation
				.newInsert(ContactsContract.Data.CONTENT_URI)
				.withValueBackReference(
						ContactsContract.Data.RAW_CONTACT_ID, 0)
				.withValue(
						ContactsContract.Data.MIMETYPE,
						ContactsContract.CommonDataKinds.Website.CONTENT_ITEM_TYPE)
				.withValue(ContactsContract.CommonDataKinds.Website.URL,
						website)
				.withValue(ContactsContract.CommonDataKinds.Website.TYPE,
						ContactsContract.CommonDataKinds.Website.TYPE_WORK)
				.build());
	}
	// add IM
	String qq1 = "452824089";
	ops.add(ContentProviderOperation
			.newInsert(ContactsContract.Data.CONTENT_URI)
			.withValueBackReference(ContactsContract.Data.RAW_CONTACT_ID, 0)
			.withValue(ContactsContract.Data.MIMETYPE,
					ContactsContract.CommonDataKinds.Im.CONTENT_ITEM_TYPE)
			.withValue(ContactsContract.CommonDataKinds.Im.DATA1, qq1)
			.withValue(ContactsContract.CommonDataKinds.Im.PROTOCOL,
					ContactsContract.CommonDataKinds.Im.PROTOCOL_QQ)
			.build());
	// add logo image
	// Bitmap bm = logo;
	// if (bm != null) {
	// ByteArrayOutputStream baos = new ByteArrayOutputStream();
	// bm.compress(Bitmap.CompressFormat.PNG, 100, baos);
	// byte[] photo = baos.toByteArray();
	// if (photo != null) {
	//
	// ops.add(ContentProviderOperation.newInsert(ContactsContract.Data.CONTENT_URI)
	// .withValueBackReference(ContactsContract.Data.RAW_CONTACT_ID, 0)
	// .withValue(ContactsContract.Data.MIMETYPE,
	// ContactsContract.CommonDataKinds.Photo.CONTENT_ITEM_TYPE)
	// .withValue(ContactsContract.CommonDataKinds.Photo.PHOTO, photo)
	// .build());
	// }
	// }
	try {
		context.getContentResolver().applyBatch(ContactsContract.AUTHORITY,
				ops);
	} catch (Exception e) {
	}
}
```
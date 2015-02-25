要删除某一个分组，其实对于Android,比我们想象中的要简单许多。在这里只是简单的说一下用法。
在操作联系人的ContactsProvider2源码中，
```  
protected int deleteInTransaction(Uri uri, String selection, String[] selectionArgs) {
	if (VERBOSE_LOGGING) {
		Log.v(TAG, "deleteInTransaction: "; + uri);
	}
	flushTransactionalChanges();
	final boolean callerIsSyncAdapter =
	readBooleanQueryParameter(uri, ContactsContract.CALLER_IS_SYNCADAPTER, false);
	final int match = sUriMatcher.match(uri);
	switch (match) {
		case GROUPS_ID: {
			mSyncToNetwork |= !callerIsSyncAdapter;
			return deleteGroup(uri, ContentUris.parseId(uri), callerIsSyncAdapter);
		}
		case GROUPS: {
			int numDeletes = 0;
			Cursor c = mDb.query(Tables.GROUPS, new String[]{Groups._ID},
			appendAccountToSelection(uri, selection), selectionArgs, null, null, null);
			try {
			while (c.moveToNext()) {
			numDeletes += deleteGroup(uri, c.getLong(0), callerIsSyncAdapter);
			}
			} finally {

			c.close();
			}
			if (numDeletes > 0) {
			mSyncToNetwork |= !callerIsSyncAdapter;
		}
		return numDeletes;
		}
		default: {
			mSyncToNetwork = true;
			return mLegacyApiSupport.delete(uri, selection, selectionArgs);
		}
	}
}
```
我们可以看到删除分组的地方是方法deleteGroup(uri, ContentUris.parseId(uri), callerIsSyncAdapter);其中uri就是我们要操作的uri，这里组是 ContactsContract.Groups.CONTENT_URI,ContentUris.parseId(uri)是要删除的分组id，而 callerIsSyncAdapter则表示是否是直接删除Groups表的数据，还是标记该分组的deleted和dirty字段为1来表示已删除 (实际删除是在同步联系人的时候进行的)。
deleteGroup方法源代码
```  
public int deleteGroup(Uri uri, long groupId, boolean callerIsSyncAdapter) {
	mGroupIdCache.clear();
	final long groupMembershipMimetypeId = mDbHelper
			.getMimeTypeId(GroupMembership.CONTENT_ITEM_TYPE);

	mDb.delete(Tables.DATA, DataColumns.MIMETYPE_ID + "=
			+ groupMembershipMimetypeId + " AND
			+ GroupMembership.GROUP_ROW_ID + "=" + groupId, null);
	try {
		if (callerIsSyncAdapter) {
			return mDb.delete(Tables.GROUPS, Groups._ID + "=" + groupId,
					null);
		} else {
			mValues.clear();
			mValues.put(Groups.DELETED, 1);
			mValues.put(Groups.DIRTY, 1);
			return mDb.update(Tables.GROUPS, mValues, Groups._ID + "=
					+ groupId, null);
		}
	} finally {
		mVisibleTouched = true;
	}
}
```
可见，我们虽然提供的uri是Groups.CONTENT_URI，实际上android为我们进行了两步操作：
1.根据我们提供的分组delId，删除Data表中的表示分组关系的那条数据，即Data.MIMETYPE是GroupMemberShip.CONTENT_ITEM_TYPE,data1等于delId的那条数据。这样就删除了联系人与该分组的关系。
2.如果callerIsSyncAdapter=true，则删除Groups表Groups._ID为delId的数据，这样就删除了该分组;否则，标记改组数据为已删除，数据需要同步，实际删除操作在同步联系人时进行。
```  
ContentResolver cr = mContext.getContentResolver();
```
于是，我们删除一个分组的时候，如果想删除某一分组关系，可以不提供callerIsSyncAdapter参数(默认为false),表示标记删除Groups表对应组数据，删除对应的Data表数据。
```  
cr.delete(Groups.CONTENT_URI,Groups._ID+"="+groupID,null);
```
提供callerIsSyncAdapter参数，表示表示删除Groups表对应组数据，删除对应的Data表数据。
```  
cr.delete(Uri.parse(ContactsContract.RawContacts.CONTENT_URI.toString() +"?" + ContactsContract.CALLER_IS_SYNCADAPTER+"=true"),Groups._ID+"="+groupID,null)
```
如果要删除某一个联系人与某一个分组的关系，根据源代码只需要这样做：
```  
cr.delete(DATA.CONTENT_URI, Datas.MIMETYPE + "=" +GroupMembership.CONTENT_ITEM_TYPE + " AND " +GroupMembership.GROUP_ROW_ID + "=" groupId, null);
```
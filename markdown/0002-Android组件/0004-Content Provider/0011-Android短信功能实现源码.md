一段完整的Android平台上短信功能的接口源码，利用扩展的API可以通过js实现如下功能：
1. getContentUris()：读取短信相关的所有数据库表的Uri地址；
2. get(int number)：读取若干条短信；
3. getUnread(int number)：读取若干条未读短信；
4. getRead(int number)：读取若干条已读短信；
5. getInbox(int number)：从收件箱读取若干条短信；
6. getSent(int number)：读取若干条已发短信；
7. getByThread(int threadID)：读取会话中所有短信；
8. getThreads(int number)：读取若干条会话；
9. getData(String selection, int number)：根据条件读取若干条短信。
源码如下，分享出来：
```  
[Tags]/* 
 [Tags]* Licensed under the Rexsee License, Version 1.0 (the "License"); 
 [Tags]* you may not use this file except in compliance with the License. 
 [Tags]* You may obtain a copy of the License at 
 [Tags]* 
 [Tags]* Unless required by applicable law or agreed to in writing, software 
 [Tags]* distributed under the License is distributed on an "AS IS" BASIS, 
 [Tags]* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 [Tags]* See the License for the specific language governing permissions and 
 [Tags]* limitations under the License. 
 [Tags]*/
import android.content.Context;
import android.database.Cursor;
import android.net.Uri;
public class RexseeSMS implements JavascriptInterface {
	private static final String INTERFACE_NAME = "SMS";
	@Override
	public String getInterfaceName() {
		return mBrowser.application.resources.prefix + INTERFACE_NAME;
	}
	@Override
	public JavascriptInterface getInheritInterface(RexseeBrowser childBrowser) {
		return this;
	}
	@Override
	public JavascriptInterface getNewInterface(RexseeBrowser childBrowser) {
		return new RexseeSMS(childBrowser);
	}
	public static final String CONTENT_URI_SMS = "content://sms";
	public static final String CONTENT_URI_SMS_INBOX = "content://sms/inbox";
	public static final String CONTENT_URI_SMS_SENT = "content://sms/sent";
	public static final String CONTENT_URI_SMS_CONVERSATIONS = "content://sms/conversations";
	public static String[] SMS_COLUMNS = new String[] { "_id", // 0
			"thread_id", // 1
			"address", // 2
			"person", // 3
			"date", // 4
			"body", // 5
			"read", // 6; 0:not read 1:read; default is 0
			"type", // 7; 0:all 1:inBox 2:sent 3:draft 4:outBox 5:failed
					// 6:queued
			"service_center" // 8
	};
	public static String[] THREAD_COLUMNS = new String[] { "thread_id",
			"msg_count", "snippet", };
	private final Context mContext;
	private final RexseeBrowser mBrowser;
	public RexseeSMS(RexseeBrowser browser) {
		mBrowser = browser;
		mContext = browser.getContext();
	}
	// JavaScript Interface
	public String getContentUris() {
		String rtn = "{";
		rtn += "\"sms\":\"" + CONTENT_URI_SMS + "\"";
		rtn += ",\"inbox\":\"" + CONTENT_URI_SMS_INBOX + "\"";
		rtn += ",\"sent\":\"" + CONTENT_URI_SMS_SENT + "\"";
		rtn += ",\"conversations\":\"" + CONTENT_URI_SMS_CONVERSATIONS + "\"";
		rtn += "}";
		return rtn;
	}
	public String get(int number) {
		return getData(null, number);
	}
	public String getUnread(int number) {
		return getData("type=1 AND read=0", number);
	}
	public String getRead(int number) {
		return getData("type=1 AND read=1", number);
	}
	public String getInbox(int number) {
		return getData("type=1", number);
	}
	public String getSent(int number) {
		return getData("type=2", number);
	}
	public String getByThread(int thread) {
		return getData("thread_id=" + thread, 0);
	}
	public String getData(String selection, int number) {
		Cursor cursor = null;
		ContentResolver contentResolver = mContext.getContentResolver();
		try {
			if (number > 0) {
				cursor = contentResolver.query(Uri.parse(CONTENT_URI_SMS),
						SMS_COLUMNS, selection, null, "date desc limit
								+ number);
			} else {
				cursor = contentResolver.query(Uri.parse(CONTENT_URI_SMS),
						SMS_COLUMNS, selection, null, "date desc");
			}
			if (cursor == null || cursor.getCount() == 0)
				return "[]";
			String rtn = "";
			for (int i = 0; i < cursor.getCount(); i++) {
				cursor.moveToPosition(i);
				if (i > 0)
					rtn += ",";
				rtn += "{";
				rtn += "\"_id\":" + cursor.getString(0);
				rtn += ",\"thread_id\":" + cursor.getString(1);
				rtn += ",\"address\":\"" + cursor.getString(2) + "\"";
				rtn += ",\"person\":\
						+ ((cursor.getString(3) == null) ? "" : cursor
								.getString(3)) + "\"";
				rtn += ",\"date\":" + cursor.getString(4);
				rtn += ",\"body\":\"" + Escape.escape(cursor.getString(5))
						+ "\"";
				rtn += ",\"read\":
						+ ((cursor.getInt(6) == 1) ? "true" : "false");
				rtn += ",\"type\":" + cursor.getString(7);
				rtn += ",\"service_center\":" + cursor.getString(8);
				rtn += "}";
			}
			return "[" + rtn + "]";
		} catch (Exception e) {
			mBrowser.exception(getInterfaceName(), e);
			return "[]";
		}
	}
	public String getThreads(int number) {
		Cursor cursor = null;
		ContentResolver contentResolver = mContext.getContentResolver();
		try {
			if (number > 0) {
				cursor = contentResolver.query(
						Uri.parse(CONTENT_URI_SMS_CONVERSATIONS),
						THREAD_COLUMNS, null, null, "thread_id desc limit
								+ number);
			} else {
				cursor = contentResolver.query(
						Uri.parse(CONTENT_URI_SMS_CONVERSATIONS),
						THREAD_COLUMNS, null, null, "thread_id desc");
			}
			if (cursor == null || cursor.getCount() == 0)
				return "[]";
			String rtn = "";
			for (int i = 0; i < cursor.getCount(); i++) {
				cursor.moveToPosition(i);
				if (i > 0)
					rtn += ",";
				rtn += "{";
				rtn += "\"thread_id\":" + cursor.getString(0);
				rtn += ",\"msg_count\":" + cursor.getString(1);
				rtn += ",\"snippet\":\"" + Escape.escape(cursor.getString(2))
						+ "\"";
				rtn += "}";
			}
			return "[" + rtn + "]";
		} catch (Exception e) {
			mBrowser.exception(getInterfaceName(), e);
			return "[]";
		}
	}
}
```
NotificationManager(通知管理器)：
NotificationManager负责通知用户事件的发生。
NotificationManager有三个公共方法:
1. cancel(int id) 取消以前显示的一个通知。假如是一个短暂的通知，试图将隐藏，假如是一个持久的通知，将从状态条中移走。
2. cancelAll() 取消以前显示的所有通知。
3. notify(int id, Notification notification) 把通知持久的发送到状态条上。
```  
//初始化NotificationManager:
NotificationManager nm = (NotificationManager)getSystemService(NOTIFICATION_SERVICE);
```
Notification代表着一个通知.
Notification的属性：
```  
audioStreamType 当声音响起时，所用的音频流的类型。
contentIntent 当通知条目被点击，就执行这个被设置的Intent。
contentView 当通知被显示在状态条上的时候，同时这个被设置的视图被显示。
defaults 指定哪个值要被设置成默认的。
deleteIntent 当用户点击"Clear All Notifications"按钮区删除所有的通知的时候，这个被设置的Intent被执行。
icon 状态条所用的图片。
iconLevel 假如状态条的图片有几个级别，就设置这里。
ledARGB LED灯的颜色。
ledOffMS LED关闭时的闪光时间(以毫秒计算)。
ledOnMS LED开始时的闪光时间(以毫秒计算)。
number 这个通知代表事件的号码。
sound 通知的声音。
tickerText 通知被显示在状态条时，所显示的信息。
vibrate 振动模式。
when 通知的时间戳。
```
Notification的公共方法：
```  
describeContents() Describe the kinds of special objects contained in this Parcelable's marshalled representation.
setLatestEventInfo(Context context, CharSequence contentTitle, CharSequence contentText, PendingIntent contentIntent) 设置Notification留言条的参数 
writeToParcel(Parcel parcel, int flags) Flatten this notification from a parcel.
toString()
```
将Notification发送到状态条上：
```  
Notification notification = new Notification();
```
Notification的设置过程
```  
nm.notify(0, notification); //发送到状态条上
```
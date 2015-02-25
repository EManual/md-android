#### 创建Notification
创建和配置新的Notification需要经历三步。
首先，你要创建一个新的Notification对象，传入要在状态条上显示的图标、文字和Notification的当前时间，如下面的代码片段所示：
```  
// Choose a drawable to display as the status bar icon
int icon = R.drawable.icon;
// Text to display in the status bar when the notification is launched
String tickerText = "Notification";
// The extended status bar orders notification in time order
long when = System.currentTimeMillis();
Notification notification = new Notification(icon, tickerText, when); 
```
当Notification触发时，文本将沿着状态条进行滚动显示。
其次，使用setLatestEventInfo方法来配置Notification在扩展的状态窗口中的外观。扩展的状态窗口将显示图标和在构造函数中传入的时间，以及显示标题和一个详细的字符串。Notification一般表示为对一个动作的请求或引起用户的注意，所以，当用户点击Notification项目时你可以指定一个PendingIntent来触发。
下面的代码片段使用了setLastestEventInfo来设置这些值：
```  
Context context = getApplicationContext();
// Text to display in the extended status window
String expandedText = "Extended status text";
// Title for the expanded status
String expandedTitle = "Notification Title";
// Intent to launch an activity when the extended text is clicked
Intent intent = new Intent(this, MyActivity.class);
PendingIntent launchIntent = PendingIntent.getActivity(context, 0, intent, 0);
notification.setLatestEventInfo(context, expandedTitle, expandedText, launchIntent);
```
一个好的形式是显示相同事件（例如，接收多个SMS消息）的多个对象时使用一个Notification图标。为了呈现给用户，使用setLastestEventInfo更新数据集来呈现最近的消息以及重新触发Notification来更新它的值。
你还可以使用number属性来显示一个状态条图标所表示的事件数目。
设置为比1大的数，如下所示，将在状态条图标上以一个小小的数字进行显示：
```  
notification.number++; 
```
任何对Notification的变更，你都需要重新触发来进行更新。如果要删除这个数字，设置number的值为0或者-1。
最后，你可以对Notification对象使用多种属性来增强Notification的效果，如闪烁LED、震动电话和播放音乐文件。这些高级特征将在本章的后面部分进行详细描述。
#### 触发Notification
为了触发一个Notification，使用NotificationManager的notify方法，将一个整数的ID和Notification对象传入，如下的片段所示：
```  
int notificationRef = 1;
notificationManager.notify(notificationRef, notification); 
```
为了更新一个已经触发过的Notification，传入相同的ID。你既可以传入相同的Notification对象，也可以是一个全新的对象。只要ID相同，新的Notification对象会替换状态条 图标和扩展的状态窗口的细节。
你还可以使用ID来取消Notification，通过调用NotificationManager的cancel方法，如下所示：
```  
notificationManager.cancel(notificationRef); 
```
取消一个Notification时，将移除它的状态条图标以及清除在扩展的状态窗口中的信息。
Notification与Toast都可以起到通知、提醒的作用。但它们的实现原理和表现形式却完全不一样。Toast其实相当于一个组件（Widget）。有些类似于没有按钮的对话框。而Notification是显示在屏幕上方状态栏中的信息。还有就是Notification需要用NotificationManager来管理，而Toast只需要简单地创建Toast对象即可。
下面来看一下创建并显示一个Notification的步骤。创建和显 示一个Notification需要如下5步：
1.通过getSystemService方法获得一个NotificationManager对象。
2.创建一个Notification对象。每一个Notification对应一个Notification对象。在这一步需要设置显示在屏幕上方状态栏的通知消息、通知消息前方的图像资源ID和发出通知的时间。一般为当前时间。
3.由于Notification可以与应用程序脱离。也就是说，即使应用程序被关闭，Notification仍然会显示在状态栏中。当应用程序再次启动后，又可以重新控制这些Notification。如清除或替换它们。因此，需要创建一个PendingIntent对象。该对象由Android系统负责维护，因此，在应用程序关闭后，该对象仍然不会被释放。
4.使用Notification类的setLatestEventInfo方法设置Notification的详细信息。
5.使用NotificationManager类的notify方法显示Notification消息。在这一步需要指定标识Notification的唯一ID。这个ID必须相对于同一个NotificationManager对象是唯一的，否则就会覆盖相同ID的Notificaiton。
心动不如行动，下面我们来演练一下如何在状 态栏显示一个Notification，代码如下：
```  
//第1步
NotificationManager notificationManager = (NotificationManager)getSystemService(NOTIFICATION_SERVICE);
//第2步
Notification notification = new Notification(R.drawable.icon, "您有新消息了", System.currentTimeMillis());
//第3步
PendingIntent contentIntent = PendingIntent.getActivity(this, 0, getIntent(), 0);
//第4步
notification.setLatestEventInfo(this, "天气预报", "晴转多云", contentIntent);
//第5步
notificationManager.notify(R.drawable.icon, notification);
showNotification("今 天非常高兴", "今天考试得了全年级第一", "数学100分、语文99分、英语100分，yeah！", R.drawable.smile, R.drawable.smile);
showNotification("这是为什么呢？", "这 道题为什么会出错呢？", "谁有正确答案啊.", R.drawable.why, R.drawable.why);
showNotification("今天心情不好", "也 不知道为什么，这几天一直很郁闷.", "也许应该去公园散心了", R.drawable.why, R.drawable.wrath);
```
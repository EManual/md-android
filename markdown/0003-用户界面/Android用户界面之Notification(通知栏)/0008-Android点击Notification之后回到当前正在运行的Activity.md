之前在写好Notification之后，发现按Home回到主界面，再按通知栏的消息（Notification），并没有回到退出之前正在运行的Acticity，后来尝试了挺多方法总是失败。不过我最终还是解决了这个问题，主要是要在代码中加入两行代码作为声名即可。废话不多说，如下：
```  
Intent notificationIntent = new Intent(this.context, this.context.getClass());
/*
 [Tags]* add the followed two lines to resume the app same with previous statues
 [Tags]*/
notificationIntent.setAction(Intent.ACTION_MAIN);
notificationIntent.addCategory(Intent.CATEGORY_LAUNCHER);
PendingIntent contentIntent = PendingIntent.getActivity(this.context,
		0, notificationIntent, PendingIntent.FLAG_UPDATE_CURRENT);
notification.setLatestEventInfo(context, contentTitle, contentText, contentIntent);
mNotificationManager.notify(NOTIFICATION_SERVICE_ID, notification);
```
在声明Notification的跳转Intent时，需要给其添加上述红色标出的两行代码，即可使每次按Notification时回到原先正在运行的Activity上面。希望对大家有帮助。
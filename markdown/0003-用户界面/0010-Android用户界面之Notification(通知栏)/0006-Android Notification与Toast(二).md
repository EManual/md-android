其中第2个和第3个Notification使用的是同一个ID（R.drawabgle.why），因此，第3个Notification会覆盖第2个Notification。
在显示Notification时还可以设置显示通 知时的默认发声、震动和Light效果。要实现这个功能需要设置Notification类的defaults属性，代码如下：
```  
notification.defaults = Notification.DEFAULT_SOUND; //使用默认的声音
notification.defaults = Notification.DEFAULT_VIBRATE; //使用默认的震动
notification.defaults = Notification.DEFAULT_LIGHTS; //使用默认的Light
notification.defaults = Notification.DEFAULT_ALL;  //所有的都使用默认值
```
注意：设置默认发声、震动和Light的方法是setDefaults。该方法与showNotification方法的实现代码基本相同，只是在调用notify方法之前需要设置defaults属性（defaults属性必须在调用notify方法之前调用，否则不起作用）。在设置默认震动效果时还需要在AndroidManifest.xml文件中通过<uses-permission>标签设置android.permission.VIBRATE权限。
如果要清除某个消息，可以使用NotificationManager类的cancel方法，该方法只有一个参数，表示要清除的Notification的ID。使用cancelAll可以清除当前NotificationManager对象中的所有Notification。
运行本节的例子，单击屏幕上显示Notification的按钮，会显示如图1所示的消息。每一个消息会显示一会，然后就只显示整个Android系统（也包括其他应用程序）的Notification（只显示图像部分）。如图2所示。如果将状态栏拖下来，可以看到Notification的详细信息和发出通知的时间（也就是Notification类的构造方法的第3个参数值），如图3所示。当单击【清除通知】按钮，会清除本应用程序显示的所有Notification，清除后的效果如图4所示。
![img](http://emanual.github.io/md-android/img/view_notification/07_menu.jpg)  
![img](http://emanual.github.io/md-android/img/view_notification/07_menu2.jpg) 
![img](http://emanual.github.io/md-android/img/view_notification/07_menu3.jpg)  
![img](http://emanual.github.io/md-android/img/view_notification/07_menu4.jpg)  
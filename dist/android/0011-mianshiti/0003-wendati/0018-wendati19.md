调用与被调用：我们的通信使者Intent 
答：要说Intent了，Intent就是这个这个意图 ，应用程序间Intent进行交流，打个电话啦，来个 电话啦都会发Intent, 这个是Android架构的松耦合的精髓部分，大大提高了组件的复用性，比如你要在你的应用程序中点击按钮，给某人打电话，很简单啊，看下代码先： 
```  
Intent intent = new Intent(); 
intent.setAction(Intent.ACTION_CALL); 
intent.setData(Uri.parse("tel:" + number)); 
startActivity(intent); 
Intent intent = new Intent(); 
intent.setAction(Intent.ACTION_CALL); 
intent.setData(Uri.parse("tel:" + number)); 
startActivity(intent); 
```
扔出这样一个意图，系统看到了你的意图就唤醒了电话拨号程序，打出来电话。什么读联系人，发短信啊，邮件啊，统统只需要扔出intent就好了，这个部分设计 地确实很好啊。 
那Intent通过什么来告诉系统需要谁来接受他呢? 
通常使用Intent有两种方法，第一种是直接说明需要哪一个类来接收代码如下: 
```  
Intent intent = new Intent(this, MyActivity.class); 
intent.getExtras().putString("id", "1"); 
StartActivity(intent); 
Intent intent = new Intent(this, MyActivity.class); 
intent.getExtras().putString("id", "1"); 
StartActivity(intent); 
```
第一种方式很明显，直接指定了MyActivity为接受者,并且传了一些数据给MyActivity，在MyActivity里可以用getIntent()来的到这个intent和数据。 
第二种就需要先看一下AndroidMenifest中的intentfilter的配置了 
Xml代码 
```  
<intent-filter> 
	<action android:name="android.intent.action.VIEW" />
	<action android:value="android.intent.action.EDIT" />
	<action android:value="android.intent.action.PICK" />
	<category android:name="android.intent.category.DEFAULT" />
	<data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
</intent-filter> 
```
这里面配置用到了action, data, category这些东西，那么聪明的你一定想到intent里也会有这些东西，然后一匹配不就找到接收者了吗? 
action其实就是一个意图的字符串名称。 
上面这段intent-filter的配置文件说明了这个Activity可以接受不同的Action，当然相应的程序逻辑也不一样咯,提一下那个mimeType,他是在ContentProvider里定义的，你要是自己实现一个ContentProvider就知道了，必须指定 mimeType才能让数据被别人使用。 
不知道原理说明白没，总结一句，就是你调用别的界面不是直接new那个界面，而是通过扔出一个intent，让系统帮你去调用那个界面，这样就多么松藕合啊，而且符合了生命周期被系统管理的原则。 
想知道category都有啥，Android为你预先定制好的action都有啥等等，请亲自访问官方链接Intent 
ps:想知道怎么调用系统应用程序的同学，可以仔细看一下你的logcat，每次运行一个程序的时候是不是有一些信息比如: 
```  
Starting activity: Intent { action=android.intent.action.MAINcategories={android.intent.category.LAUNCHER} flags=0x10200000comp={com.android.camera/com.android.camera.GalleryPicker} } 
```

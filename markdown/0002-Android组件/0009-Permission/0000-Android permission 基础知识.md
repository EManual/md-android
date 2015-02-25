Android是一个多进程系统，在这个系统中，应用程序（或者系统的部分）会在自己的进程中运行。系统和应用之间的安全性是通过Linux的facilities（工具，功能）在进程级别来强制实现的，比如会给应用程序分配user ID和Group ID。更细化的安全特性是通过"Permission"机制对特定的进程的特定的操作进行限制，而"per-URI permissions"可以对获取特定数据的access专门权限进行限制。 
#### 安全架构 
Android安全架构中一个中心思想就是：应用程序在默认的情况下不可以执行任何对其他应用程序，系统或者用户带来负面影响的操作。这包括读或写用户的私有数据（如联系人数据或email数据），读或写另一个应用程序的文件，网络连接，保持设备处于非睡眠状态。 
一个应用程序的进程就是一个安全的沙盒。它不能干扰其它应用程序，除非显式地声明了"permissions"，以便它能够获取基本沙盒所不具备的额外的能力。它请求的这些权限"permissions"可以被各种各样的操作处理，如自动允许该权限或者通过用户提示或者证书来禁止该权限。应用程序需要的那些"permissions"是静态的在程序中声明，所以他们会在程序安装时就被知晓，并不会再改变。 
所有的Android应用程序（.apk文件）必须用证书进行签名认证，而这个证书的私钥是由开发者保有的。该证书可以用以识别应用程序的作者。该证书也不需要CA签名认证（注：CA就是一个第三方的证书认证机构，如verisign等）。Android应用程序允许而且一般也都是使用self-signed证书（即自签名证书）。证书是用于在应用程序之间建立信任关系，而不是用于控制程序是否可以安装。签名影响安全性的最重要的方式是通过决定谁可以进入基于签名的permisssions，以及谁可以share 用户IDs。 
#### 用户IDs和文件存取 
每一个Android应用程序（.apk文件）都会在安装时就分配一个独有的Linux用户ID，这就为它建立了一个沙盒，使其不能与其他应用程序进行接触（也不会让其它应用程序接触它）。这个用户ID会在安装时分配给它，并在该设备上一直保持同一个数值。 
由于安全性限制措施是发生进程级，所以两个package中的代码不会运行在同一个进程当中，他们要作为不同的Linux用户出现。我们可以通过使用AndroidManifest.xml文件中的manifest标签中的sharedUserId属性，来使不同的package共用同一个用户ID。通过这种方式，这两个package就会被认为是同一个应用程序，拥有同一个用户ID（实际不一定），并且拥有同样的文件存取权限。注意：为了保持安全，只有当两个应用程序被同一个签名签署的时候（并且请求了同一个sharedUserId）才会被分配同样的用户ID。
所有存储在应用程序中的数据都会赋予一个属性-该应用程序的用户ID,这使得其他package无法访问这些数据。当通过这些方法getSharedPreferences(String, int), openFileOutput(String, int), or openOrCreateDatabase(String, int, SQLiteDatabase.CursorFactory)来创建一个新文件时，你可以通过使用MODE_WORLD_READABLE and/or MODE_WORLD_WRITEABLE标志位来设置是否允许其他package来访问读写这个文件。当设置这些标志位时，该文件仍然属于该应用程序，但是它的global read and/or write权限已经被设置，使得它对于其他任何应用程序都是可见的。 
#### Using Permissions 使用权限 
一个基本的Android程序通常是没有任何permissions与之关联的，这就是说它不能做任何扰乱用户或破坏数据的勾当。那么为了使用设备被保护的features，我们就必须在AndroidManifest.xml添加一个或多个<uses-permission>标签，用以声明你的应用程序需要的permissions。
下面是个例子 
For example, an application that needs to monitor incoming SMS messages would specify: 
Xml代码  
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
	package="com.android.app.myapp" >
	<uses-permission android:name="android.permission.RECEIVE_SMS" />
</manifest> 
```
应用程序安装的时候，应用程序请求的permissions是通过package installer来批准获取的。package installer是通过检查该应用程序的签名和/或用户的交换结果来确定是否给予该程序request的权限。在用户使用过程中不会去检查权限，也就是说要么在安装的时候就批准该权限，使其按照设计可以使用该权限；要么就不批准，这样用户也就根本无法使用该feature,也不会有任何提示告知用户尝试失败。 
很多时候, 一个permission failure会导致一个SecurityException被抛回该应用程序。 但是Android并不保证这种情况会处处发生。例如，当数据被deliver到每一个receiver的时候，sendBroadcast(Intent)方法会去检查permissions,在这个方法调用返回之后，你也不会收到任何exception。几乎绝大多数情况，一个permission failure都会打印到log当中。 
Android系统定义的权限可以在Manifest.permission中找到。任何一个程序都可以定义并强制执行自己独有的permissions，因此Manifest.permission中定义的permissions并不是一个完整的列表（即有肯能有自定义的permissions)。 
一个特定的permission可能会在程序操作的很多地方都被强制实施： 
当系统有来电的时候，用以阻止程序执行其它功能。
当启动一个activity的时候，会阻止应用程序启动其它应用的Acitivity。
在发送和接收广播的时候，去控制谁可以接收你的广播或谁可以发送广播给你。
当进入并操作一个content provider的时候
当绑定或开始一个service的时候 
为了实现你自己的permissions，你必须首先在AndroidManifest.xml文件中声明该permissions.通常我们通过使用一到多个<permission> tag来进行声明。
下面例子说明了一个应用程序它想控制谁才可以启动它的Activity： 
Java代码  
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
	package="com.me.app.myapp" >
    <permission android:name="com.me.app.myapp.permission.DEADLY_ACTIVITY"
        android:label="@string/permlab_deadlyActivity"
		android:description="@string/permdesc_deadlyActivity"
        android:permissionGroup="android.permission-group.COST_MONEY"
        android:protectionLevel="dangerous" />
</manifest> 
```
这里<protectionLevel>属性是需要声明的（通常系统自有的permission都会有它对应的protection level，而我们自己定义的permission一般都需要定义protecdtion level，若不去定义，则默认为normal）。通过声明该属性，我们就可以告知系统如何去通告用户以及通告哪些内容，或者告知系统谁才可以拥有该permission。具体请参看链接的文档。 
这我多说两句啊，这个protectionLevel分四个等级，分别是Normal, Dangerous, Signature, SignatureOrSystem,越往后安全等级越高。 
这个<permissionGroup>属性是可选项，只是用于帮助系统显示permissions给用户（实际是告知系统该permission是属于哪个permission group的）。你通常会选择使用标准的system group来设定该属性，或者用你自己定义的group（更为罕见）。通常使用一个已经存在的group会更合适，因为这样UI显示的时候会更简单。 
需要注意的是label和description都是需要为permission提供的。这些都是字符串资源，当用户去看permission列表(android:label)或者某个permission的详细信息(android:description)时，这些字符串资源就可以显示给用户。label应当尽量简短，之需要告知用户该permission是在保护什么功能就行。而description可以用于具体描述获取该permission的程序可以做哪些事情，实际上让用户可以知道如果他们同意程序获取该权限的话，该程序可以做什么。我们通常用两句话来描述permission，第一句描述该permission，第二句警告用户如果批准该权限会可能有什么不好的事情发生。
下面是一个描述CALL_PHONE permission的label和description的例子： 
Xml代码  
```  
<string name="permlab_callPhone">directly call phone numbers</string> 
<string name="permdesc_callPhone">Allows the application to call   
phone numbers without your intervention. Malicious applications may   
cause unexpected calls on your phone bill. Note that this does not   
allow the application to call emergency numbers.</string> 
```
你可以通过shell指令 adb shell pm list permissions 来查看目前系统已有的permissions。 特别的，"-s"选项会以一种用户会看到的格式一样的格式来显示这些permissions。
#### Security and Permissions安全与权限（五） 
用于限制进入系统或应用程序的Components的高层permissions可以再AndroidManifest.xml中实现.所有这些都可以通过在相应的component中包含android:permission属性，命名该permission以使其被用以控制进入的权限。 
Activity permissions限制了谁才可以启动相应的activity.permission会在Context.startActivity()和Activity.startActivityForResult()的时候进行检查。如果caller没有所需的权限，则会抛出一个SecurityException。 
Service permissions用于限制谁才可以start或bind该service。在Context.startService(),Context.stopService()和Context.bindService()调用的时候会进行权限检查。如果caller没有所需的权限，则会抛出一个SecurityException。 
BroadcastReceiver permissions用于限制谁才可以向该receiver发送广播。权限检查会在Context.sendBroadcast()返回时进行，由系统去发送已经提交的广播给相应的Receiver。最终，一个permission failure不会再返回给Caller一个exception；它只是不会去deliver该Intent而已。同样地，Context.registerReceiver()也可以有自己permission用于限制谁才可以向一个在程序中注册的receiver发送广播。另一种方式是，一个permission也可以提供给Context.sendBroadcast()用以限制哪一个BroadcastReceiver才可以接收该广播。 
ContentProvider permission用于限制谁才可以访问ContentProvider提供的数据。（Content providers有一套而外的安全机制叫做URI permissions，这些在稍后讨论）不同于其它的Components，这里有两种不同的permission属性可以设置:android:readPermission用于限制谁可以读取provider中的数据，而android:writePermission用于限制谁才可以向provider中写入数据。需要注意的是如果provider既被read permis保护，也被write permission保护的话，如果这时只有write permission并不意味着你就可以读取provider中的数据了。当你第一次获取provider的时候就要进行权限检查（如果你没有任何permission，则会抛出SecurityException），并且这时你对provider执行了某些操作。当使用ContentResolver.query()时需要读权限，而当使用ContentResolver.insert()，ContentResolver.update()，ContentResolver.delete()时需要写权限。在所有这些情况下，没有所需的permission将会导致SecurityException 被抛出。 
除了之前说过的Permission（用于限制谁才可以发送广播给相应的broadcastReceiver），你还可以在发送广播的时候指定一个permission。在调用Context.sendBroadcast()的时候使用一个permission string，你就可以要求receiver的宿主程序必须有相应的permission。
值得注意的是Receiver和broadcaster都可以要求permission。当这种情况发生时，这两种permission检查都需要通过后才会将相应的intent发送给相关的目的地。
在调用service的过程中可以设置任意的fine-grained permissions（这里我理解的是更为细化的权限）。这是通过Context.checkCallingPermission() 方法来完成的。呼叫的时候使用一个想得到的permission string，并且当该权限获批的时候可以返回给呼叫方一个Integer（没有获批也会返回一个Integer）。需要注意的是这种情况只能发生在来自另一个进程的呼叫，通常是一个service发布的IDL接口或者是其他方式提供给其他的进程。 
Android提供了很多其他的方式用于检查permissions。如果你有另一个进程的pid，你就可以通过context method Context.checkPermission(String, int, int) 去针对那个pid去检查permission。如果你有另一个应用程序的package name，你可以直接用PackageManager method PackageManager.checkPermission(String, String)来确定该package是否已经拥有了相应的权限。 
到目前为止我们讨论的标准的permission系统对于content provider来说是不够的。一个content provider可能想保护它的读写权限，而同时与它对应的直属客户端也需要将特定的URI传递给其它应用程序，以便其它应用程序对该URI进行操作。一个典型的例子就是邮件程序处理带有附件的邮件。进入邮件需要使用permission来保护，因为这些是敏感的用户数据。然而，如果有一个指向图片附件的URI需要传递给图片浏览器，那个图片浏览器是不会有访问附件的权利的，因为他不可能拥有所有的邮件的访问权限。 
针对这个问题的解决方案就是per-URIpermission:当启动一个activity或者给一个activity返回结果的时候，呼叫方可以设置Intent.FLAG_GRANT_READ_URI_PERMISSION和/或Intent.FLAG_GRANT_WRITE_URI_PERMISSION。这会使接收该intent的activity获取到进入该Intent指定的URI的权限，而不论它是否有权限进入该intent对应的content provider。 
这种机制允许一个通常的capability-style模型，这种模型是以用户交互（如打开一个附件，从列表中选择一个联系人）为驱动，特别获取更细粒化的权限。这是一种减少不必要权限的重要方式，这种方式主要针对的就是那些和程序的行为直接相关的权限。 
这些URI permission的获取需要content provider（包含那些URI）的配合。强烈推荐在content provider中提供这种能力，并通过android:grantUriPermissions 或者<grant-uri-permissions> 标签来声明支持。 
更多的信息可以参考Context.grantUriPermission() , Context.revokeUriPermission()，and Context.checkUriPermission() methods。
Android应用程序是用Java语言编写的。编译过后的字节码，以及应用程序要求的其他数据和资源文件，通过aapt工具被绑定在一起，称为Android包，这是一个带.apk后缀的档案文件。这个文件也是用户下载到他们设备上的文件。所有的代码在一个单一的.apk文件中，组成一个“应用程序”。
从许多方面来说，每个Android应用程序存活在它们自己的世界中：
默认地，每一个应用程序运行在它自己的Linux进程中。当应用程序的任何代码需要被执行时，Android启动进程；当不再需要时，或者系统资源被其他应用程序所要求时，关闭进程。
每一个进程有它自己的Java虚拟机（JVM），因此应用程序代码独立与所有其他应用程序代码而运行。
默认地，每一个应用程序被分配一个唯一的Linux用户ID。通过设置权限许可，应用程序的文件只对该用户可见，只对应用程序本身可见，虽然有办法将其导出到其他应用程序。
有可能安排两个应用程序共享同一用户ID，在这种情况下，它们可以彼此看见对方的文件。要保全系统资源，具有相同ID的应用程序也可以被组织起来运行在同一Linux进程中，共享同一VM。
#### 激活组件：intents
当一个请求来自一个ContentResolver时，内容提供器被激活。其他三个组件----activities，services和 broadcast receivers----被名为intents的异步消息所激活。一个intent是一个Intent对象，持有消息的内容。对于activities 和services来说，它意味着位于其他事物中被请求的动作和指定要操作的数据的URI。例如，它可能会为一个activity传送一个请求以代表给用户的一个图片，或者让用户编辑一些文本。对于broadcast receivers，Intent对象意味着被公告/通知的动作。例如，它可能会通告有兴趣的相关方，相机的按钮被按下了。
#### 有各自的方法来用于激活每一类组件：
通过传递一个Intent对象到Context.startActivity()或Activity.startActivityForResult()，来启动一个activity（或者让做一些新的东西）。进行响应的activity可以通过调用其getIntent()方法来查看引起它被启动的原始内容（intent）。Android调用该activity的onNewIntent()方法来向其传递任何后续的intent。
一个activity经常启动下一个activity。如果它期望从它所启动的activity获得一个返回的结果，那么它就要调用startActivityForResult()而不是startActivity()。例如，如果它启动一个让用户挑选照片的activity，那么它可能期望返回被选中的照片。结果在一个Intent对象中被返回，而该Intent对象被传递给进行调用的activity的 onActivityResult()方法中。
通过传递一个Intent对象给Context.startService()，一个service被启动（或者一个新的指令传达给正在运行的service）。Android调用该service的onStart()方法并将Intent对象传递给它。相似地，将一个intent传递给Context.bindService()，能够在进行调用的组件和目标service之间建立一个持续的连接。该service在一个onBind()调用中接收该Intent对象。(如果该service还没有运行，bindService()能有选择地启动它。)例如，一个activity可能会与音乐播放服务建立一个连接，这样它就能向用户提供控制播放的方式（一个用户接口）。该activity将调用bindService()来建立这个连接，然后调用service所定义的方法来影响播放。
应用程序能通过传递一个Intent对象到诸如Context.sendBroadcast()，Context.sendOrderedBroadcast()，和Context.sendStickyBroadcast()这些方法中来创建一个广播。Android通过调用它们的onReceive()方法发布该Intent到所有感兴趣的broadcast receivers。（更多有关Intent的信息，在稍后一节“Intents and Intent Filters”中会专门讲述）
#### 停止组件
一个内容提供器只有当它在响应来自一个ContentResolver的请求时是活动的。一个广播接收器只有当它在响应一个广播消息时是活动的。因此没有必要显式地关闭这些组件。
另一方面，activities提供用户接口。它们与用户处于一个长时间运行的会话中，并且可能保存有active，甚至在空闲时，只要会话在继续。相似地，services也可能持续运行很长时间。因此Android有方法以一种有序的方式来关闭activities和services。
通过调用其finish()方法来关闭一个activity。一个activity可以通过调用finishActivity()来关闭另一个activity(它使用startActivityForResult()方法启动的activity)。
通过调用其stopSelf()方法来停止一个service，或者通过调用Context.stopService()。
当组件不再被使用时，或者当Android必须为更多活动的组件而收回内存时，它们有可能被系统所关闭。在后面“组件的生命周期”一节中，将讨论这种可能性及更多地细节。
#### manifest文件
在Android能启动一个应用程序组件之前，它必须知道组件的存在。因此，应用程序在一个manifest文件中声明它们的组件。manifest文件被绑定在一个Android包中。Android包是一个.apk文件，它还持有应用程序的代码、文件和资源。
manifest是结构化的XML文件，并且对所有的应用程序来说，它总是被命名为AndroidManifest.xml。除了声明应用程序的组件之外，它还做很多事情，如命名应用程序需要链接到的库（除了默认的Android库之外），以及应用程序所期望被授权的任何权限验证。
但是manifest的首要任务是告知Android关于应用程序的组件。例如，一个activity可能被声明如下的内容：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest>
    <application>
        <activity
            android:name="com.example.project.FreneticActivity"
            android:icon="@drawable/small_pic.png"
            android:label="@string/freneticLabel" >
        </activity>
    </application>
</manifest>
```
在这个manifest文件中，元素<activity>的name属性命名实现了activity的Activity类的子类。而icon和label属性则指向包含有一个图标和标签的资源文件，这些图标和标签可以被显示给用户以代表这个activity。
另一个组件以相似的方式被声明--<service>元素声明services，<receiver>元素声明 broadcast receivers，以及<provider>元素声明内容提供器(content providers)。不在manifest文件中声明的activities，services和content providers对系统是不可见的，相应地永远不会运行。然而，broadcast receivers既可以在manifest文件中声明，也可以在代码中(如BroadcastReceiver对象)动态地创建并通过调用Context.registerReceiver()向系统注册。
#### Intent过滤器(Intent filters)
一个Intent对象能显式地命名一个目标组件。如果这样的话，Android会查找那个组件（基于在manifest文件中的声明）并激活它。但是如果一个目标组件没有被显式地命名，Android必须定位最合适的组件来响应该Intent。它通过对Intent对象和潜在目标的intent filters的比较来完成这种定位。一个组件的intent过滤器告知Android该组件能够处理的intent种类。象其他组件的基本信息一样，它们是在manifest文件中声明的。下面是前面示例代码的一个扩展，为activity添加了两个intent过滤器。
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest>
    <application>
        <activity
            android:name="com.example.project.FreneticActivity"
            android:icon="@drawable/small_pic.png"
            android:label="@string/freneticLabel" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <intent-filter>
                <action android:name="com.example.project.BOUNCE" />
                <data android:mimeType="image/jpeg" />
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```
上面示例代码中的第一个过滤器—action"android.intent.action.MAIN"和category"android.intent.category.LAUNCHER"—是一个通用的过滤器。它使得activity成为在应用程序启动器中显示出来的应用程序之一。应用程序启动器指的是显示（列出）用户可以在设备上启动的应用程序的屏幕。换句话说，这个activity是应用程序的入口，是用户在启动器中选择应用程序时看得见的最初的一个。
第二个过滤器声明一个该activity可以在一个特定类型的数据上执行的动作。
一个组件可以拥有任何数量的intent过滤器，每一个都声明一套不同的功能。如果没有任何过滤器，那么它只能被组件作为目标显式命名的intents所激活。
对于在代码中创建和注册的一个broadcst receiver，intent过滤器是直接作为一个IntentFilter对象创建的。所有其他过滤器都是在manifest中建立的。
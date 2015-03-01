#### Android高级开发
首先会更进一步地了解安全，特别是权限的工作原理，以及如何使用它们来保证应用程序的安全性。
然后，我们会详细地讲解Android的接口定义语言(AIDL)，并学习如何创建丰富的用户界面，从而让它支持在不同进程中运行的Android应用程序之间的完全基于对象的IPC(interprocess communication，进程间通信)。
接着，将会更进一步地学习在为你的活动创建用户界面时可用的丰富的工具仓。首先是动画，将学习如何在View和ViewGroup中应用补间动画(Tweened animation)，以及构建基于cell的帧动画(frame-by-frame animation)。
接下来将深入分析Android的光栅图形引擎中的各种潜在功能。我们首先会介绍可用的基本图形，然后是使用Paint的某些高级功能。将会学习如何使用透明度以及如何创建渐变的shader和位图刷。我们还会介绍mask和颜色过滤，以及Path Effects和使用不同转换模式的可能性。
之后将更加深入地探讨如何设计和执行具有更加复杂用户界面的Views，学习如何使用Surface View来创建三维的和高帧速的交互式控件以及如何使用触摸屏、轨迹球和设备的按键来为你的UI创建直观的输入选择。
#### Android的安全性
关于Android安全性的很多内容都来源于底层的Linux内核。资源被放置在它们应用程序的沙盒中，从而使它们不能被其他的应用程序访问。Android提供了广播意向、服务和内容提供器来放宽这些严格的过程边界，并使用权限机制来维护应用程序级别的安全。
我们已经学习了如何使用权限系统来为应用程序请求对本地系统服务的访问--特别是访问基于位置的服务和联系人内容提供器。
下面的部分更加详细地探讨了可用的安全机制。为了有一个全面的概念，Android文档提供了非常好的资源来深入地描述了安全功能。
code.google.com/android/devel/security.html
#### Linux内核安全
每一个Android包在安装的时候，都会分配给它一个唯一的Linux用户ID。这个ID具有隔离进程和它所创建的资源的作用，这样它就不会影响(或者被影响)其他的应用程序了。
由于这种内核级安全性的存在，要想在应用程序间进行通信，就需要额外的步骤。可以使用内容提供器、广播Intent和AIDL接口。这些机制中的任何一个都在应用程序之间打开了一个信息流的通道。而Android权限则在两端扮演了通道边界警卫的角色，它控制着是否允许数据通过这个通道。
#### 权限简介
权限是一种应用程序级的安全机制，它可以限制对应用程序组件的访问。权限可以用来阻止恶意破坏应用程序的数据，限制对敏感信息的访问以及对硬件资源或者外部通信信道的过度(或未授权)使用。
正如在前面的章节所了解到的，很多Android本地组件都有对权限的要求。Android本地活动和服务所使用的本地权限字符串可以在android.Manifest.permission类中以静态常量的形式找到。
要使用受权限保护的组件，需要在应用程序的清单中添加uses-permission标签，来指定每一个应用程序所要求的权限字符串。
当安装一个应用程序包时，通过检查可信的专家和用户反馈，在应用程序的清单中的权限请求将被分析和授权。与很多现存的移动平台不同，Android权限检查是在安装时进行的。一旦一个应用程序被安装了之后，那么用户就不会再被提示来重新评估它们的权限。
#### 安全结构
Android安全学中的一个重要的设计点是在默认情况下应用程序没有权限执行对其它应用程序、操作系统或用户有害的操作。这些操作包括读/写用户的隐私数据（例如联系方式或e-mail），读/写其它应用程序的文件，执行网络访问，保持设备活动，等等。 
应用程序的进程是一个安全的沙箱。它不能干扰其它应用程序，除非在它需要添加原有沙箱不能提供的功能时明确声明权限。这些权限请求能够被不同方式的操作所处理，特别的要基于证书和用户的提示被自动的允许或禁止。权限的请求在那个应用程序中通过一个应用程序被声明为静态的，所以在此之后在安装时或没有改变时它们会预先知道。 
#### 应用程序签名
所有的Android应用程序（.apk文件）必须通过一个证书的签名，此证书的私钥必须被开发者所掌握。这个证书的标识是应用程序的作者。这个证书不需要通过证书组织的签署：Android应用程序对于使用自签署的证书是完全允许的和特别的。这个证书仅仅被用于与应用程序建立信任关系，不是为了大规模的控制应用程序可否被安装。最重要的方面是通过确定能够访问原始签名权限和能够共享用户ID的签名来影响安全。
#### 用户标识和文件访问
安装在设备中的每一个Android包文件(.apk)都会被分配给一个属于自己的统一的Linux用户ID，并且为它创建一个沙箱以防止影响其它应用程序（或者其它应用程序影响它）。用户ID 在应用程序安装到设备中时被分配，并且在这个设备中保持它的永久性。 
因为安全执行发生在进程级，所以一些不同包中的代码在相同进程中不能正常的运行，自从他们需要以不同Linux用户身份运行时。你可以使用每一个包中的AndroidManifest.xml文件中的manifest 标签属性sharedUserId 拥有它们分配的相同用户ID。通过这样做，两个包被视为相同的应用程序的安全问题被解决了，注意为了保持安全，仅有相同签名（和请求相同sharedUserId标签）的两个应用程序签名将会给相同的用户ID。 
应用创建的任何文件都会被赋予应用的用户标识，并且，正常情况下不能被其它包访问。当你通过getSharedPreferences(String, int), openFileOutput(String, int) 或者 openOrCreateDatabase(String, int, SQLiteDatabase.CursorFactory)创建一个新文件时, 你可以同时或分别使用 MODE_WORLD_READABLE 和 MODE_WORLD_WRITEABLE 标志允许其它包读/写此文件。当设置了这些标志时，这个文件仍然属于你的应用程序，但是它的全局读、写和读写权限已经设置所以其它任何应用程序可以看到它。
#### 权限命名
一个基本的Android应用程序没有与其相关联的权限，意味着它不能做任何影响用户体验或设备中的数据的有害操作。要利用这个设备的保护特性，在你的应用程序需要时，你必须在AndroidManifest.xml文件中包含一个或更多的<uses-permission> 标签来声明此权限。 
例如：需要监听来自SMS消息的应用程序将要指定如下内容：
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.android.app.myapp" >
	<uses-permission android:name="android.permission.RECEIVE_SMS" />
</manifest>
```
在安装应用程序时，通过包安装器应用程序要通过权限请求的许可，使建立在与应用程序签名的核对下声明对于用户的那些权限和影响。在应用运行期间对用户不做检查：它要么在安装时被授予特定的许可，并且使用想用的特性；要么不被授予许可，并且使得一切使用特性的尝试失败而不提示用户。 
例如，sendBroadcast(Intent)方法就是当数据被发送给到每个接收器时检查许可的，在方法调用返回之后，因此当许可失败时你不会收到一个异常。然而，几乎在所有例子中，许可失败都会被打印到系统日志中。通常，多次的许可错误会产生抛回至应用程序的SecurityException异常。 
Android系统提供的许可可以在Manifest.permission中找到。每个引用也可以定义和Enforce它自己的许可，因此这不是全面的所有可能的列表。
在程序操作期间，个别权限在一些地方可能被强制：
在系统接到呼叫的时候，预防一个应用程序去执行特定的函数。 
在启动Activity时，防止一个应用启动其它应用程序的Activities。 
发送和接收Intent广播时，控制谁能接收你的广播或者谁能发送广播给你。 
在一个内容提供器上访问和操作时。 
绑定或开始一个服务时。 
#### 权限的声明和支持
为了执行你自己的权限，你必须首先在你的AndroidManifest.xml中使用一个或多个<permission> 标签声明它们。 
例如，一个应用程序想用控制谁能启动一个activities，它可以为声明一个做这个操作的许可，如下: 
```  
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.me.app.myapp" >
    <permission
        android:name="com.me.app.myapp.permission.DEADLY_ACTIVITY"
        android:description="@string/permdesc_deadlyActivity"
        android:label="@string/permlab_deadlyActivity"
        android:permissionGroup="android.permission-group.COST_MONEY"
        android:protectionLevel="dangerous" />
</manifest>
```
<protectionLevel> 属性是必需的，告诉系统用户应如何处理应用程序接到请求此权限的通知，或者在这个文档中对这个权限的许可的描述。 
<permissionGroup> 属性是可选的，仅仅用于帮助系统为用户显示权限。通常，你要设置这些，向一个标准的系统组（列在android.Manifest.permission_group中），或者在更多的情况下要自定义。它更偏向于使用一个已经存在的组，做为简化的权限用户界面显示给用户。 
注意：应该为每个权限提供标签(label) 和描述(description)。当用户浏览权限列表时，它们可以为用户展示字符资源，如(android:label) 或者一个许可的详细信息 ( android:description) 。标签(label)比较短，用几个词来描述该权限保护的关键功能。描述(description)应该是一组句子，用于描述获得权限的用户可以做什么。我们写描述的习惯是两句话，第一句声明权限，第二句警告用户如果应用许可该权限时，会发生什么不好的事情。 
下面是一个CALL_PHONE权限的标签和描述的例子： 
```  
<string name="permlab_callPhone">directly call phone numbers</string>
<string name="permdesc_callPhone">Allows the application to call
phone numbers without your intervention. Malicious applications may
cause unexpected calls on your phone bill. Note that this does not
allow the application to call emergency numbers.</string>
```
你可以在系统中通过shell命令 adb shell pm list permissions查看权限当前定义。特别，'-s'  操作以简单粗略的方式为使用者显示权限： 
```  
$ adb shell pm list permissions -s      
All Permissions:
Network communication: view Wi-Fi state, create Bluetooth connections, full
Internet access, view network state
Your location: access extra location provider commands, fine (GPS) location,
mock location sources for testing, coarse (network-based) location
Services that cost you money: send SMS messages, directly call phone numbers
```
#### 在AndroidManifest.xml文件中支持权限
通过 AndroidManifest.xml 文件可以设置高级权限，以限制访问系统的所有组件或者使用应用程序。所有的这些请求都包含在你所需要的组件中的 android:permission属性，命名这个权限可以控制访问此组件。 
Activity 权限 (使用 <activity> 标签) 限制能够启动与 Activity 权限相关联的组件或应用程序。此权限在 Context.startActivity() 和 Activity.startActivityForResult() 期间要经过检查；如果调用者没有请求权限，那么会为调用抛出一个安全异常( SecurityException )。 
Service 权限(应用 <service> 标签)限制启动、绑定或启动和绑定关联服务的组件或应用程序。此权限在 Context.startService(), Context.stopService() 和 Context.bindService() 期间要经过检查；如果调用者没有请求权限，那么会为调用抛出一个安全异常( SecurityException )。 
BroadcastReceiver 权限(应用 <receiver> 标签)限制能够为相关联的接收者发送广播的组件或应用程序。在 Context.sendBroadcast() 返回后此权限将被检查，同时系统设法将广播递送至相关接收者。因此，权限失败将会导致抛回给调用者一个异常；它将不能递送到目的地。在相同方式下，可以使 Context.registerReceiver() 支持一个权限，使其控制能够递送广播至已登记节目接收者的组件或应用程序。其它的，当调用 Context.sendBroadcast() 以限制能够被允许接收广播的广播接收者对象一个权限（见下文）。 
ContentProvider 权限(使用 <provider> 标签)用于限制能够访问 ContentProvider 中的数据的组件或应用程序。(Content providers有一个重要的附加安全设施可用于它们调用被描述后的URI权限。)不同于其它组件，它有两个不相连系的权限属性要设置：android:readPermission用于限制能够读取提供器的组件或应用程序,android:writePermission用于限制能够写入提供器的组件或应用程序。注意，如果一个提供者的读写权限受保护，意思是你只能从提供器中读，而没有写权限。当你首次收回提供者（如果你没有任何权限，将会抛出一个SecurityException异常），那么权限要被检查，并且做为你在这个提供者上的执行操作。使用ContentResolver.query()请求获取读权限;使用ContentResolver.insert(),ContentResolver.update()和ContentResolver.delete()请求获取写权限。在所有这些情况下，一个SecurityException异常从一个调用者那里抛出时不会存储请求权限结果。 
#### 发送广播时支持权限
当发送一个广播时你能总指定一个请求权限，此权限除了权限执行外，其它能发送Intent到一个已注册的BroadcastReceiver 的权限均可以。 通过调用 Context.sendBroadcast() 及一些权限字符串, 为了接收你的广播，你请求一个接收器应用程序必须持有那个权限。 
注意，接收者和广播者都能够请求一个权限。当这样的事发生了，对于Intent来说，这两个权限检查都必须通过，为了交付到共同的目的地。 
#### 其它权限支持
任意一个好的粒度权限都能够在一些调用者的一个服务中被执行。 
和Context.checkCallingPermission() method方法一起被完成。调用并产生一个需要的权限字符串，它将返回一个整型，以确定当前调用进程是否被许可。注意，仅仅当你执行的调用进入到其它进程的时候这些才会被使用，通常，通过IDL接口从一个服务发布，或从一些其它方式通知其它进程。 
这有许多其它有益的方式去检查权限。如果你有一个其它进程的PID，你可以使用上下文的方法 Context.checkPermission(String, int, int) 检查权限违反PID。 如果你有一个其它应用程序的包名, 你可以使用直接的包管理器方法 PackageManager.checkPermission(String, String) 去查看特定的包是否被指定的权限所许可。
#### URI权限
迄今为止，在与内容提供器共同使用时，标准权限系统描述通常是不充分的。一个内容提供器要保护它自己及读和写权限，当为了它们产生作用，它的直接客户端总是需要手动的对其它应用程序指定URI。一个典型的例子是邮件应用程序中的附件。访问邮件的权限应该被保护，因为这是敏感用户数据。可以，如果一个网址图片附件提供给一个图片查看器，那个图片查看器将没有权限打开这个附件，因为它没有原因去拥有一个权限从而不能访问所有的电子邮件。
对于这个问题的解决是通过网址权限：当开始一个活动或对一个活动返回一个结果，调用者可通过设置Intent.FLAG_GRANT_READ_URI_PERMISSION 和Intent.FLAG_GRANT_WRITE_URI_PERMISSION中的一个或者两个。允许接收活动权限访问在Intent中特定的数据地址，不论它有权限访问数据在内容提供器相应的Intent中。
这种机制允许一个公用的功能性模型使用户相互交互（打开一个附件，从一个列表中选择一个联系人，等等）驱动ad-hoc在优粒度权限的许可下。这可能是一个主要设备，应用程序为了减少这个权限而需要，仅仅直接关系到它们的行为。
这优粒度URI权限的许可工作，然而，请求一些协作和内容提供者保持那些URI。强烈推荐内容提供者实现这种设备，并且通过android:grantUriPermissions 属性或者 <grant-uri-permissions> 标签声明支持它。
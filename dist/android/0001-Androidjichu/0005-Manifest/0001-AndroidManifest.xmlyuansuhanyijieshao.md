每一个Android项目都包含一个清单(Manifest)文件--AndroidManifest.xml，它存储在项目层次中的最底层。清单可以定义应用程序及其组件的结构和元数据。
它包含了组成应用程序的每一个组件(活动、服务、内容提供器和广播接收器)的节点，并使用Intent过滤器和权限来确定这些组件之间以及这些组件和其他应用程序是如何交互的。
它还提供了各种属性来详细地说明应用程序的元数据(如它的图标或者主题)以及额外的可用来进行安全设置和单元测试顶级节点，如下所述。
清单由一个根manifest标签构成，该标签带有一个设置项目包的package属性。它通常包含一个xmlns:android属性来提供文件内使用的某些系统属性。下面的XML代码段展示了一个典型的声明节点：
```  
<manifest xmlns:android=http://schemas.android.com/apk/res/android "
	package="com.my_domain.my_app"> [ ... manifest nodes ... ]
</manifest>
```
manifest标签包含了一些节点(node)，它们定义了应用程序组件、安全设置和组成应用程序的测试类。下面列出了一些常用的manifest节点标签，并用一些XML代码段说明了它们是如何使用的。
#### application  
一个清单只能包含一个application节点。它使用各种属性来指定应用程序的各种元数据(包括标题、图标和主题)。它还可以作为一个包含了活动、服务、内容提供器和广播接收器标签的容器，用来指定应用程序组件。
```  
<application android:icon="@drawable/icon" 
	android:theme="@style/my_theme">
	[ ... application nodes ... ]
</application>
```
Activity应用程序显示的每一个Activity都要求有一个activity标签，并使用android:name属性来指定类的名称。这必须包含核心的启动Activity和其他所有可以显示的屏幕或者对话框。启动任何一个没有在清单中定义的Activity时都会抛出一个运行时异常。每一个Activity节点都允许使用intent-filter子标签来指定哪个Intent启动该活动。
```  
<activity 
	android:name=".MyActivity"
	android:label="@string/app_name">
	<intent-filter>
		<action android:name="android.intent.action.MAIN" />
		<category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>
</activity>
```
service和activity标签一样，应用程序中使用的每一个Service类都要创建一个新的service标签。(Service标签也支持使用intent-filter子标签来允许后面的运行时绑定。
```  
<service 
	android:enabled="true"
	android:name=".MyService"></service>
```
#### provider  
provider标签用来说明应用程序中的每一个内容提供器。内容提供器是用来管理数据库访问以及程序内和程序间共享的。
```  
<provider 
	android:permission="com.paad.MY_PERMISSION"
	android:name=".MyContentProvider"
	android:enabled="true"
	android:authorities="com.paad.myapp.MyContentProvider">
</provider>
```
#### receiver  
通过添加receiver标签，可以注册一个广播接收器(Broadcast Receiver)，而不用事先启动应用程序。广播接收器就像全局事件监听器一样，一旦注册了之后，无论何时，只要与它相匹配的intent被应用程序广播出来，它就会立即执行。通过在声明中注册一个广播接收器，可以使这个进程实现完全自动化。如果一个匹配的Intent被广播了，则应用程序就会自动启动，并且你注册的广播接收器也会开始运行。
```  
<receiver 
	android:enabled="true"
	android:label="My Broadcast Receiver"
	android:name=".MyBroadcastReceiver">
</receiver>
```
#### uses-permission  
作为安全模型的一部分，uses-permission标签声明了那些由你定义的权限，而这些权限是应用程序正常执行所必需的。在安装程序的时候，你设定的所有权限将会告诉给用户，由他们来决定同意与否。对很多本地Android服务来说，权限都是必需的，特别是那些需要付费或者有安全问题的服务(例如，拨号、接收SMS或者使用基于位置的服务)。如下所示，第三方应用程序，包括你自己的应用程序，也可以在提供对共享的程序组件进行访问之前指定权限。
```  
<uses-permission android:name="android.permission.ACCESS_LOCATION"> </uses-permission>
```
#### permission  
在可以限制访问某个应用程序组件之前，需要在清单中定义一个permission。可以使用permission标签来创建这些权限定义。然后，应用程序组件就可以通过添加android:permission属性来要求这些权限。再后，其他的应用程序就需要在它们的清单中包含uses-permission标签(并且通过授权)，之后才能使用这些受保护的组件。
在permission标签内，可以详细指定允许的访问权限的级别(normal、dangerous、signature，signatureOrSystem)、一个 label属性和一个外部资源，这个外部资源应该包含了对授予这种权限的风险的描述。
```  
<permission 
	android:name="com.paad.DETONATE_DEVICE"
	android:protectionLevel="dangerous"
	android:label="Self Destruct"
	android:description="@string/detonate_description">
</permission>
```
#### instrumentation  
instrumentation类提供一个框架，用来在应用程序运行时在活动或者服务上运行测试。它们提供了一些方法来监控应用程序及其与系统资源的交互。对于为自己的应用程序所创建的每一个测试类，都需要创建一个新的节点。
```  
<instrumentation 
	android:label="My Test"
	android:name=".MyTestClass"
	android:targetPackage="com.paad.aPackage">
</instrumentation>
```
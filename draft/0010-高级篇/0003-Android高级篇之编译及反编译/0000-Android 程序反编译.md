#### Apk文件的格式
Android application package文件。每个要安装到android平台的应用都要被编译打包为一个单独的文件，后缀名为.apk，其中包含了应用的二进制代码、资源、配置文件等。
apk文件实际是一个zip压缩包，可以通过解压缩工具解开。可以用zip解开*.apk文件，下面是一个helloword的apk示例文件。
```  
|– AndroidManifest.xml 
|– META-INF 
| |– CERT.RSA 
| |– CERT.SF 
| `– MANIFEST.MF 
|– classes.dex 
|– res 
| |– drawable 
| | `– icon.png 
| `– layout 
| `– main.xml 
`– resources.arsc 
```
Manifest文件：AndroidManifest.xml是每个应用都必须定义和包含的，它描述了应用的名字、版本、权限、引用的库文件等等信息[ ， ]，如要把apk上传到Google Market上，也要对这个xml做一些配置。注意：在apk中的xml文件是经过压缩的，不可以直接打开。
Res文件：res文件夹下为所有的资源文件。
resources.arsc文件：为编译后的二进制资源文件，许多做汉化软件的人都是修改该文件内的资源以实现软件的汉化的。
META-INF目录：META-INF目录下存放的是签名信息，用来保证apk包的完整性和系统的安全。在eclipse编译生成一个api包时，会对所有要打包的文件做一个校验计算，并把计算结果放在META-INF目录下。而在OPhone平台上安装apk包时，应用管理器会按照同样的算法对包里的文件做校验，如果校验结果与META-INF下的内容不一致，系统就不会安装这个apk.这就保证了apk包里的文件不能被随意替换.比如拿到一个apk包后，如果想要替换里面的一幅图片，一段代码，或一段版权信息，想直接解压缩、替换再重新打包，基本是不可能的.如此一来就给病毒感染和恶意修改增加了难度，有助于保护系 统的安全。
classes.dex是java源码编译后生成的java字节码文件。但由于Android使用的dalvik虚拟机与标准的java虚拟机是不兼容的，dex文件与class文件相比，不论是文件结构还是opcode都不一样。
XML文件的反编译
在apk中的xml文件是经过压缩的，可以通过AXMLPrinter2工具解开，具体命令为：
java -jar AXMLPrinter2.jar AndroidManifest.xml
HelloAndroid程序中Manifest文件的实例：
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:Android="http://schemas.android.com/apk/res/android"
    package="name.feisky.Android.test"
    Android:versionCode="1"
    Android:versionName="1.0" >
    <application
        Android:icon="@7F020000"
        Android:label="@7F040001" >
        <activity
            Android:name=".HelloAndroid"
            Android:label="@7F040001" >
            <intent-filter>
                <action Android:name="android.intent.action.MAIN" >
                </action>
                <category Android:name="android.intent.category.LAUNCHER" >
                </category>
            </intent-filter>
        </activity>
    </application>
    <uses-sdk Android:minSdkVersion="6" >
    </uses-sdk>
</manifest>
```
而原文件为：  
```  
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:Android="http://schemas.android.com/apk/res/android"
    package="name.feisky.Android.test"
    Android:versionCode="1"
    Android:versionName="1.0" >
    <application
        Android:icon="@drawable/icon"
        android:label="@string/app_name" >
        <activity
            Android:name=".HelloAndroid"
            Android:label="@string/app_name" >
            <intent-filter>
                <action Android:name="android.intent.action.MAIN" />
                <category Android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
    <uses-sdk Android:minSdkVersion="6" />
</manifest>
```
#### classes.dex文件反编译
classes.dex是java源码编译后生成的java字节码文件。但由于Android使用的dalvik虚拟机与标准的java虚拟机是不兼容的，dex文件与class文件相比，不论是文件结构还是opcode都不一样。目前常见的java反编译工具都不能处理dex文件。
Android模拟器中提供了一个dex文件的反编译工具，dexdump。用法为首先启动Android模拟器，把要查看的dex文件用adb push上传的模拟器中，然后通过adb shell登录，找到要查看的dex文件，执行dexdump xxx.dex。但是这样得到的结果，其可读性是极差的。下面介绍一个可读性比较好的工具。
工具准备：
1、把dex文件反编译为jar文件的工具。（dex2jar）
2、把jar反编译为java的工具。（JD-GUI）
反编译的步骤
1、从APK中提取classes.dex文件，对APK文件解压即可得到。将其放到dex2jar的目录下，打开cmd，运行dex2jar.bat classes.dex，生成classes.dex.dex2jar.jar。
2、运行JD-GUI工具，打开上面的jar文件，即可看到源代码。
HelloAndroid实例：
```  
import Android.app.Activity;
import Android.os.Bundle;
public class HelloAndroid extends Activity {
	public void onCreate(Bundle paramBundle) {
		super.onCreate(paramBundle);
		setContentView(2130903040);
	}
} 
```
其原程序为： 
```  
import Android.app.Activity;
import Android.os.Bundle;
public class HelloAndroid extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
	}
}
```
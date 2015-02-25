因为我们专业，所以我们用Auto Build，所以我们用CI系统。
代码管理使用SVN，自动编译使用Ant，而持续集成使用Hudson，操作系统使用Ubuntu10.04。
#### 1. 安装
#### 1.1 安装JDK
sudo apt-get install sun-java6-jdk
#### 1.2 安装Ant
sudo apt-get install ant-optional
#### 1.3 安装Hudson
sudo apt-get upgrade
wget -O /tmp/key http://hudson-ci.org/debian/hudson-ci.org.key
sudo apt-key add /tmp/key
wget -O /tmp/hudson.deb http://hudson-ci.org/latest/debian/hudson.deb
sudo dpkg --install /tmp/hudson.deb
#### 1.4 安装Android SDK
http://androidappdocs.appspot.com/sdk/index.html
#### 2. Project配置
#### 2.1 build.xml
http://androidappdocs.appspot.com/guide/developing/other-ide.html
按照官方的做法，使用自动生成的build文件就可以了。
```  
<?xml version="1.0" encoding="UTF-8"?>
<project name="test-android">
<!-- The local.properties file is created and updated by the 'android' tool.
It contains the path to the SDK. It should *NOT* be checked in in Version
Control Systems. -->
<property file="local.properties" />
<!-- The build.properties file can be created by you and is never touched
by the 'android' tool. This is the place to change some of the default property values
used by the Ant rules.
Here are some properties you may want to change/update:
application.package
the name of your application package as defined in the manifest. Used by the
'uninstall' rule.
source.dir
the name of the source directory. Default is 'src'.
out.dir
the name of the output directory. Default is 'bin'.
Properties related to the SDK location or the project target should be updated
using the 'android' tool with the 'update' action.
This file is an integral part of the build system for your application and
should be checked in in Version Control Systems.
-->
<property file="build.properties" />
<!-- The default.properties file is created and updated by the 'android' tool, as well
as ADT.
This file is an integral part of the build system for your application and
should be checked in in Version Control Systems. -->
<property file="default.properties" />
<!-- Custom Android task to deal with the project target, and import the proper rules.
This requires ant 1.6.0 or above. -->
<path id="android.antlibs">
<pathelement path="${sdk.dir}/tools/lib/anttasks.jar" />
<pathelement path="${sdk.dir}/tools/lib/sdklib.jar" />
<pathelement path="${sdk.dir}/tools/lib/androidprefs.jar" />
<pathelement path="${sdk.dir}/tools/lib/apkbuilder.jar" />
<pathelement path="${sdk.dir}/tools/lib/jarutils.jar" />
</path>
<taskdef name="setup" classname="com.android.ant.SetupTask" classpathref="android.antlibs" />
<!-- Execute the Android Setup task that will setup some properties specific to the target,
and import the build rules files.
The rules file is imported from
<SDK>/platforms/<target_platform>/templates/android_rules.xml
To customize some build steps for your project:
- copy the content of the main node <project> from android_rules.xml
- paste it in this build.xml below the <setup /> task.
- disable the import by changing the setup task below to <setup import="false" />
This will ensure that the properties are setup correctly but that your customized
build steps are used.
-->
<setup />
</project>
```
#### 2.2 local.properties
这个配置文件定义了Android SDK的位置。
sdk.dir=${tool.android.sdk}
#### 2.3 build.properties
这个文件里面定义了App的Package，以及生成App签名必须用的一些配置。
```  
application.package=test.android
key.store=../test-android.keystore
key.alias=test-android
key.store.password=password
key.alias.password=password
```
#### 3. Hudson配置
3.1 System Configuration
系统配置很简单，只需要配置JDK、Ant的位置就可以了。
比较有用的还有一项：E-mail Notification，如果你需要在build失败发送邮件的话，那么需要配置这一项。
3.2 Job Configuration
3.2.1 Source Code Management
1. 选择Subversion，并且设置好SVN的地址以及用户名、密码。
2. 把Use update和Revert勾选上。
#### 3.2.2 Build Triggers
1. 勾选上Build Periodically，然后设置自动Build的时机，这里语法跟cron的语法是一样的。
例如：0 2 * * 1-6
2. 勾选上Poll SCM，设置每隔多长时间检测SVN的变更。
例如：0,15,30,45 9-23 * * 1-5
#### 3.2.3 Build
Step1：删除上次编译的文件
```  
rm –f test-android.keystore
rm –f –R ./test-android/gen
rm –f –R ./test-android/bin
```
Step2：生成Keystore
http://androidappdocs.appspot.co ... ng/app-signing.html
例如：
```  
keytool -genkey -v -alias test-android -keyalg RSA -keysize 2048 -dname 'CN=xxx, OU=xxx, O=xxx, L=xxx, ST=xxx, C=xx' -validity 10000 -keypass password -storepass password -keystore 'test-android.keystore'
```
Step3：Invoke Ant
设置Targets：release –Dsdk.dir=$your-sdk-dir
例如：
```  
release –Dsdk.dir=/home/build/android-sdk-linux
```
#### 3.2.3 Post-build　Actions
1. 勾选上Archive the artifacts，设置Files to archive：test-android/bin/test-android-release.apk。
2. 勾选上E-mail Notification，可以设置发送邮件的对象和时机。
经过以上步骤的设置，大功告成了。
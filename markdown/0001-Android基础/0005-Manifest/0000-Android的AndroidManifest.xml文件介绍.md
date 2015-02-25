AndroidManifest.xml是每一个应用都需要的文件，位于应用根目录下，它描述了程序包的全局变量，包括暴露的应用组件(activities,services等等)和为每个组件的实现类，什么样的数据可以操作，以及在什么地方运行。 
#### 主要包括以下各个元素。 
A.包名(package):指定本应用内java主程序包的包名。当没有指定apk的文件名时，编译后产生程序包将以此命名。本包名应当在Android系统运行时唯一。 
B.认证(certificate):指定本应用程序所授予的信任级别，目前有的认证级别有platform(system)、shared、media以及应用自定义的认证。不同的认证可以享受不同的权限。 
C.权限组(permission-group):权限组的定义是为了描述一组具有共同特性的权限。 
D.权限(permission):权限用来描述是否拥有做某件事的权力。Android系统中权限是分级的，前分为普通级别(Normal)，危险级别(dangerous)，签名级别(signature)和系统/签名级别(signature or system)。系统中所有预定义的权限根据作用的不同，分别属于不同的级别。对于普通和危险级别的权限，我们称之为低级权限，应用申请即授予。其他两级权限，我们称之为高级权限或系统权限，应用拥有platform级别的认证才能申请。当应用试图在没有权限的情况下做受限操作，应用将被系统杀掉以警示。系统应用可以使用任何权限。权限的声明者可无条件使用该权限。 
E.权限树(permission-tree)权限树的设置是为了统一管理一组权限，声明于该树下的权限所有者归属该应用。系统提供了API，应用可以在运行时动态添加。 PackageManager.addPermission() 
F.使用权限(uses-permission):应用需要的权限应当在此处申请，所申请的权限应当被系统或某个应用所定义，否则视为无效申请。同时，使用权限的申请需要遵循权限授予条件，非platform认证的应用无法申请高级权限。 
G:SDK（uses-sdk）:标识本应用运行的SDK版本。高兼容性的应用可以忽略此项。 
H.application:application是Android应用内最高级别(top level)的模块，每个应用内最多只能有一个application，如果应用没有指定该模块，一个默认的application将被启用。application将在应用启动时最先被加载，并存活在应用的整个运行时生命周期。因此一些初始化的工作适合在本模块完成. Application元素有许多属性，其中：“persistent”表示本应用是否为常驻内存，“enable”表示本应用当前是否应当被加载。 
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
    <array name="icons">
        <item>@drawable/home</item>
        <item>@drawable/settings</item>
        <item>@drawable/logout</item>
    </array>
    <array name="colors">
        <item>FFFF0000</item>
        <item>FF00FF00</item>
        <item>FF0000FF</item>
    </array>
</resources>
```
在AndroidManifest.xml文件中，运行时模块的定义都作为本模块的子元素。当运行时模块被调度时，如果应用没有启动，将首先启动应用进行初始化，然后调度对应模块。 
I.activity:activity是application模块的运行时子元素，标识了一个UI。除了application，一个应用可以声明并实现零至多个其它运行时模块，activity也同样。activity也包含了许多定义它工作状态的属性，其中：“name”是必须的，它指定了该activity所在的文件名，如果该文件所属包不同于该应用的包名（即本描述文件的最开始处），那么名字前面需要加入所在包名。activity通过增加intent-fliter来标识哪些intent可以被处理，同时intent也是调度activity的主要参数。
J.receiver:receiver也是application的运行时子元素。receiver通过增加intent-fliter来标识它需要接受哪些intent。当收到intent后，receiver将根据不同的intent进行不同的处理。当一个Intent发出后，所有注册了该intent的receiver都将会收到，系统会根据receiver在系统中的注册次序顺序发送。当一个receiver处理完该Intent后，系统才会向下一个receiver发送。当一个receiver有多个未接收的intent时，将按照intent发送的次序顺序接收。 
在本实例中，intent-filter如下：
```   
<action android:name="android.intent.action.MAIN" /> 
<category android:name="android.intent.category.LAUNCHER" /> 
```
K.service:service也是application的运行时子元素。Service属于后台模块，启动后将长时间运行，除非停止该service或所在应用进程被杀死。 
L.provider:provider也是application的运行时子元素。它继承于ContentProvider，是对该应用管理的用户数据的结构化接入，是基于数据库操作方式的封装。如果应用允许外部应用访问／管理它的用户数据，provider是Android平台提供的最佳方式。 
M.activity-alias:顾名思义，是已有activity的别名。 
N:uses-library:标识应用启动所必须的共享库。
#### 引言
大部分移动设备平台上的应用程序都运行在他们自己的沙盒中。他们彼此之间互相隔离，并且严格限制应用程序与硬件和原始组件之间的交互。 我们知道交流是多么的重要，作为一个孤岛没有交流的东西，一定毫无意义！Android应用程序也是一个沙盒，但是他们能够使用Intent、Broadcast Receivers、Adapters、Content Providers、Internet去突破他们的边界互相交流。有交流还会和谐，由此可见这些交流手段有多重要。
上篇文章中我们在SMS接收程序和使用Intent发送SMS程序中用到了Intent，并做了简单的回顾和总结：android应用程序的三大组件——Activities、Services、Broadcast Receiver，通过消息触发，这个消息就称作意图（Intent）。然后以活动为例简单介绍了Intent了并说明Intent机制的好处。既然在SMS程序中用到了Intent，这里我就借机顺着这条线，彻底详细地介绍一下Intent。分两篇文章介绍：
1、Android开发之旅: Intents和Intent Filters（理论部分）
2、Android开发之旅: Intents和Intent Filters（实例部分）
本文的主要内容如下：
```  
1、概述
2、Intent对象
2.1、组件名字
2.2、动作
2.3、数据
2.4、种类
2.5、附加信息
2.6、标志
3、Intent解析
3.1、Intent过滤器
3.1.1、动作检测
3.1.2、种类检测
3.1.3、数据检测
3.2、通用情况
3.3、使用intent匹配
```
#### 1、概述
一个应用程序的三个核心组件——activities、services、broadcast receivers，都是通过叫做intents的消息激活。Intent消息是一种同一或不同应用程序中的组件之间延迟运行时绑定的机制。intent本身（是一个Intent对象），是一个被动的数据结构保存一个将要执行的操作的抽象描述，或在广播的情况下，通常是某事已经发生且正在宣告。对于这三种组件，有独立的传送intent的机制：
Activity：一个intent对象传递给Context.startActivity()或Activity.startActivityForRestult()去启动一个活动或使一个已存在的活动去做新的事情。
Service：一个intent对象传递给Context.startService()去初始化一个service或传递一个新的指令给正在运行的service。类似的，一个intent可以传递给Context.bindService()去建立调用组件和目标服务之间的连接。
Broadcast Receiver：一个intent对象传递给任何广播方法（如Context.sendBroadcast()，Context.sendOrderedBroadcast()，Context.sendStickyBroadcast()），都将传递到所有感兴趣的广播接收者。
在每种情况下，Android系统查找合适的activity、service、broadcast receivers来响应意图，如果有必要的话，初始化他们。这些消息系统之间没有重叠，即广播意图仅会传递给广播接收者，而不会传递活动或服务，反之亦然。
下面首先描述intent对象，然后介绍Android将intent映射到相应组件的规则——如何解决哪个组件应该接收intent消息。对于没有指定目标组件名字的intent，这个处理过程包括按照intent filters匹配每个潜在的目标对象。
#### 2、Intent对象
一个Intent对象是一个捆信息，包含对intent有兴趣的组件的信息（如要执行的动作和要作用的数据）、Android系统有兴趣的信息（如处理intent组件的分类信息和如何启动目标活动的指令）。下面列出它的主要信息：
#### 2.1、组件名字
处理intent的组件的名字。这个字段是一个ComponentName对象——是目标组件的完全限定类名（如"com.example.project.app.FreneticActivity"）和应用程序所在的包在清单文件中的名字（如"com.example.project"）的组合。其中组件名字中的包部分不必一定和清单文件中的包名一样。
组件名字是可选的，如果设置了，intent对象传递到指定类的实例；如果没有设置，Android使用intent中的其它信息来定位合适的目标组件（见下面的Intent解析）。组件的名字通过setComponent()，setClass()或setClassName()设置，通过getComponent()读取。
#### 2.2、动作
一个字符串命名的动作将被执行，或在广播intent中，已发生动作且正被报告。Intent类定义了一些动作常量，如下：
```  
Constant	Target component	Action
ACTION_CALL	activity	Initiate a phone call.
ACTION_EDIT	activity	Display data for the user to edit.
ACTION_MAIN	activity	Start up as the initial activity of a task, with no data input and no returned output.
ACTION_SYNC	activity	Synchronize data on a server with data on the mobile device.
ACTION_BATTERY_LOW	broadcast receiver	A warning that the battery is low.
ACTION_HEADSET_PLUG	broadcast receiver	A headset has been plugged into the device, or unplugged from it.
ACTION_SCREEN_ON	broadcast receiver	The screen has been turned on.
ACTION_TIMEZONE_CHANGED	broadcast receiver	The setting for the time zone has changed.
```
查看更多的动作请参考Intent类。其它的动作定义在Android API中，我们还可以定义自己的动作字符串一再我们的应用程序中激活组件。自定义动作字符串应该包含应用程序报名前缀，如"com.example.project.SHOW_COLOR"。
动作很大程度上决定了剩下的intent如何构建，特别是数据（data）和附加（extras）字段，就像一个方法名决定了参数和返回值。正是这个原因，应该尽可能明确指定动作，并紧密关联到其它intent字段。换句话说，应该定义你的组件能够处理的Intent对象的整个协议，而不仅仅是单独地定义一个动作。
一个intent对象的动作通过setAction()方法设置，通过getAction()方法读取。
#### 2.3、数据
数据（data）是将作用于其上的数据的URI和数据的MIME类型。不同的动作有不同的数据规格。例如，如果动作字段是ACTION_EDIT，数据字段将包含将显示用于编辑的文档的URI；如果动作是ACTION_CALL，数据字段将是一个tel:URI和将拨打的号码；如果动作是ACTION_VIEW，数据字段是一个http:URI，接收活动将被调用去下载和显示URI指向的数据。
当匹配一个intent到一个能够处理数据的组件，通常知道数据的类型（它的MIME类型）和它的URI很重要。例如，一个组件能够显示图像数据，不应该被调用去播放一个音频文件。
在许多情况下，数据类型能够从URI中推测，特别是content:URIs，它表示位于设备上的数据且被内容提供者（content provider）控制。但是类型也能够显示地设置，setData()方法指定数据的URI，setType()指定MIME类型，setDataAndType()指定数据的URI和MIME类型。通过getData()读取URI，getType()读取类型。
#### 2.4、种类
此外，还包含关于应该处理intent的组件类型信息。可以在一个Intent对象中指定任意数量的种类描述。Intent类定义的一些种类常量，如下这些：
```  
1) CATEGORY_BROWSABLE
The target activity can be safely invoked by the browser to display data referenced by a link — for example, an image or an e-mail message.
2) CATEGORY_GADGET
The activity can be embedded inside of another activity that hosts gadgets.
3) CATEGORY_HOME
The activity displays the home screen, the first screen the user sees when the device is turned on or when the HOME key is pressed.
4) CATEGORY_LAUNCHER
The activity can be the initial activity of a task and is listed in the top-level application launcher.
5) CATEGORY_PREFERENCE
The target activity is a preference panel.
```
更多的种类常量请参考Intent类。
addCategory()方法添加一个种类到Intent对象中，removeCategory()方法删除一个之前添加的种类，getCategories()方法获取Intent对象中的所有种类。
#### 2.5、附加信息

额外的键值对信息应该传递到组件处理intent。就像动作关联的特定种类的数据URIs，也关联到某些特定的附加信息。例如，一个ACTION_TIMEZONE_CHANGE intent有一个"time-zone"的附加信息，标识新的时区，ACTION_HEADSET_PLUG有一个"state"附加信息，标识头部现在是否塞满或未塞满；有一个"name"附加信息，标识头部的类型。如果你自定义了一个SHOW_COLOR动作，颜色值将可以设置在附加的键值对中。
Intent对象有一系列的put…()方法用于插入各种附加数据和一系列的get…()用于读取数据。这些方法与Bundle对象的方法类似，实际上，附加信息可以作为一个Bundle使用putExtras()和getExtras()安装和读取。
#### 2.6、标志
有各种各样的标志，许多指示Android系统如何去启动一个活动（例如，活动应该属于那个任务）和启动之后如何对待它（例如，它是否属于最近的活动列表）。所有这些标志都定义在Intent类中。
#### 3、Intent解析
Intent可以分为两组：
1) 显式intent：通过名字指定目标组件。因为开发者通常不知道其它应用程序的组件名字，显式intent通常用于应用程序内部消息，如一个活动启动从属的服务或启动一个姐妹活动。
2) 隐式intent：并不指定目标的名字（组件名字字段是空的）。隐式intent经常用于激活其它应用程序中的组件。
Android传递一个显式intent到一个指定目标类的实例。Intent对象中只用组件名字内容去决定哪个组件应该获得这个intent，而不用其他内容。
隐式intent需要另外一种不同的策略。由于缺省指定目标，Android系统必须查找一个最适合的组件（一些组件）去处理intent——一个活动或服务去执行请求动作，或一组广播接收者去响应广播声明。这是通过比较Intent对象的内容和intent过滤器（intent filters）来完成的。intent过滤器关联到潜在的接收intent的组件。过滤器声明组件的能力和界定它能处理的intents，它们打开组件接收声明的intent类型的隐式intents。如果一个组件没有任何intent过滤器，它仅能接收显示的intents，而声明了intent过滤器的组件可以接收显示和隐式的intents。
只有当一个Intent对象的下面三个方面都符合一个intent过滤器：action、data（包括URI和数据类型）、category，才被考虑。附加信息和标志在解析哪个组件接收intent中不起作用。
#### 3.1、Intent过滤器
活动、服务、广播接收者为了告知系统能够处理哪些隐式intent，它们可以有一个或多个intent过滤器。每个过滤器描述组件的一种能力，即乐意接收的一组intent。实际上，它筛掉不想要的intents，也仅仅是不想要的隐式intents。一个显式intent总是能够传递到它的目标组件，不管它包含什么；不考虑过滤器。但是一个隐式intent，仅当它能够通过组件的过滤器之一才能够传递给它。
一个组件的能够做的每一工作有独立的过滤器。例如，记事本中的NoteEditer活动有两个过滤器，一个是启动一个指定的记录，用户可以查看和编辑；另一个是启动一个新的、空的记录，用户能够填充并保存。
一个intent过滤器是一个IntentFilter类的实例。因为Android系统在启动一个组件之前必须知道它的能力，但是intent过滤器通常不在java代码中设置，而是在应用程序的清单文件（AndroidManifest.xml）中以<intent-filter>元素设置。但有一个例外，广播接收者的过滤器通过调用Context.registerReceiver()动态地注册，它直接创建一个IntentFilter对象。
一个过滤器有对应于Intent对象的动作、数据、种类的字段。过滤器要检测隐式intent的所有这三个字段，其中任何一个失败，Android系统都不会传递intent给组件。然而，因为一个组件可以有多个intent过滤器，一个intent通不过组件的过滤器检测，其它的过滤器可能通过检测。
#### 3.1.1、动作检测
清单文件中的<intent-filter>元素以<action>子元素列出动作，例如：
```  
<intent-filter . . . >
    <action android:name="com.example.project.SHOW_CURRENT" />
    <action android:name="com.example.project.SHOW_RECENT" />
    <action android:name="com.example.project.SHOW_PENDING" />
    . . .
</intent-filter>
```
像例子所展示，虽然一个Intent对象仅是单个动作，但是一个过滤器可以列出不止一个。这个列表不能够为空，一个过滤器必须至少包含一个<action>子元素，否则它将阻塞所有的intents。
要通过检测，Intent对象中指定的动作必须匹配过滤器的动作列表中的一个。如果对象或过滤器没有指定一个动作，结果将如下：
如果过滤器没有指定动作，没有一个Intent将匹配，所有的intent将检测失败，即没有intent能够通过过滤器。
如果Intent对象没有指定动作，将自动通过检查（只要过滤器至少有一个过滤器，否则就是上面的情况了）
#### 3.1.2、种类检测
类似的，清单文件中的<intent-filter>元素以<category>子元素列出种类，例如：
```  
<intent-filter . . . >
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    . . .
</intent-filter>
```
注意本文前面两个表格列举的动作和种类常量不在清单文件中使用，而是使用全字符串值。例如，例子中所示的"android.intent.category.BROWSABLE"字符串对应于本文前面提到的BROWSABLE常量。类似的，"android.intent.action.EDIT"字符串对应于ACTION_EDIT常量。
对于一个intent要通过种类检测，intent对象中的每个种类必须匹配过滤器中的一个。即过滤器能够列出额外的种类，但是intent对象中的种类都必须能够在过滤器中找到，只有一个种类在过滤器列表中没有，就算种类检测失败！
因此，原则上如果一个intent对象中没有种类（即种类字段为空）应该总是通过种类测试，而不管过滤器中有什么种类。但是有个例外，Android对待所有传递给Context.startActivity()的隐式intent好像它们至少包含"android.intent.category.DEFAULT"（对应CATEGORY_DEFAULT常量）。因此，活动想要接收隐式intent必须要在intent过滤器中包含"android.intent.category.DEFAULT"。
注意："android.intent.action.MAIN"和"android.intent.category.LAUNCHER"设置，它们分别标记活动开始新的任务和带到启动列表界面。它们可以包含"android.intent.category.DEFAULT"到种类列表，也可以不包含。
#### 3.1.3、数据检测
类似的，清单文件中的<intent-filter>元素以<data>子元素列出数据，例如：
```  
<intent-filter . . . >
    <data android:mimeType="video/mpeg" android:scheme="http" . . . />
    <data android:mimeType="audio/mpeg" android:scheme="http" . . . />
    . . .
</intent-filter>
```
每个<data>元素指定一个URI和数据类型（MIME类型）。它有四个属性scheme、host、port、path对应于URI的每个部分：
```   
scheme://host:port/path 
```
例如，下面的URI： 
```   
content://com.example.project:200/folder/subfolder/etc 
```
scheme是content，host是"com.example.project"，port是200，path是"folder/subfolder/etc"。host和port一起构成URI的凭据（authority），如果host没有指定，port也被忽略。 这四个属性都是可选的，但它们之间并不都是完全独立的。要让authority有意义，scheme必须也要指定。要让path有意义，scheme和authority也都必须要指定。
当比较intent对象和过滤器的URI时，仅仅比较过滤器中出现的URI属性。例如，如果一个过滤器仅指定了scheme，所有有此scheme的URIs都匹配过滤器；如果一个过滤器指定了scheme和authority，但没有指定path，所有匹配scheme和authority的URIs都通过检测，而不管它们的path；如果四个属性都指定了，要都匹配才能算是匹配。然而，过滤器中的path可以包含通配符来要求匹配path中的一部分。
<data>元素的type属性指定数据的MIME类型。Intent对象和过滤器都可以用"*"通配符匹配子类型字段，例如"text/*"，"audio/*"表示任何子类型。
数据检测既要检测URI，也要检测数据类型。规则如下：
1) 一个Intent对象既不包含URI，也不包含数据类型：仅当过滤器也不指定任何URIs和数据类型时，才不能通过检测；否则都能通过。
2) 一个Intent对象包含URI，但不包含数据类型：仅当过滤器也不指定数据类型，同时它们的URI匹配，才能通过检测。例如，mailto:和tel:都不指定实际数据。
3) 一个Intent对象包含数据类型，但不包含URI：仅当过滤也只包含数据类型且与Intent相同，才通过检测。
4) 一个Intent对象既包含URI，也包含数据类型（或数据类型能够从URI推断出）：数据类型部分，只有与过滤器中之一匹配才算通过；URI部分，它的URI要出现在过滤器中，或者它有content:或file: URI，又或者过滤器没有指定URI。换句话说，如果它的过滤器仅列出了数据类型，组件假定支持content:和file: 。
如果一个Intent能够通过不止一个活动或服务的过滤器，用户可能会被问那个组件被激活。如果没有目标找到，会产生一个异常。
#### 3.2、通用情况
上面最后一条规则表明组件能够从文件或内容提供者获取本地数据。因此，它们的过滤器仅列出数据类型且不必明确指出content:和file:scheme的名字。这是一种典型的情况，一个<data>元素像下面这样：
```  
<data android:mimeType="image/*" />
```
告诉Android这个组件能够从内容提供者获取image数据并显示它。因为大部分可用数据由内容提供者（content provider）分发，过滤器指定一个数据类型但没有指定URI或许最通用。
另一种通用配置是过滤器指定一个scheme和一个数据类型。例如，一个<data>元素像下面这样：
<data android:scheme="http" android:type="video/*" />
告诉Android这个组件能够从网络获取视频数据并显示它。考虑，当用户点击一个web页面上的link，浏览器应用程序会做什么？它首先会试图去显示数据（如果link是一个HTML页面，就能显示）。如果它不能显示数据，它将把一个隐式Intent加到scheme和数据类型，去启动一个能够做此工作的活动。如果没有接收者，它将请求下载管理者去下载数据。这将在内容提供者的控制下完成，因此一个潜在的大活动池（他们的过滤器仅有数据类型）能够响应。
大部分应用程序能启动新的活动，而不引用任何特别的数据。活动有指定"android.intent.action.MAIN"的动作的过滤器，能够启动应用程序。如果它们出现在应用程序启动列表中，它们也指定"android.intent.category.LAUNCHER"种类：
```  
<intent-filter>
    <action android:name="code android.intent.action.MAIN" />
    <category android:name="code android.intent.category.LAUNCHER" />
</intent-filter>
```
#### 3.3、使用intent匹配
Intents对照着Intent过滤器匹配，不仅去发现一个目标组件去激活，而且去发现设备上的组件的其他信息。例如，Android系统填充应用程序启动列表，最高层屏幕显示用户能够启动的应用程序：是通过查找所有的包含指定了"android.intent.action.MAIN"的动作和"android.intent.category.LAUNCHER"种类的过滤器的活动，然后在启动列表中显示这些活动的图标和标签。类似的，它通过查找有"android.intent.category.HOME"过滤器的活动发掘主菜单。
我们的应用程序也可以类似的使用这种Intent匹配方式。PackageManager有一组query…()方法返回能够接收特定intent的所有组件，一组resolve…()方法决定最适合的组件响应intent。例如，queryIntentActivities()返回一组能够给执行指定的intent参数的所有活动，类似的queryIntentServices()返回一组服务。这两个方法都不激活组件，它们仅列出所有能够响应的组件。对应广播接收者也有类似的方法——queryBroadcastReceivers()。
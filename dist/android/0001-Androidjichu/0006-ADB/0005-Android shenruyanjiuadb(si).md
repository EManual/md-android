#### 启用logcat日志
Android日志系统提供了记录和查看系统调试信息的功能。日志都是从各种软件和一些系统的缓冲区中记录下来的，缓冲区可以通过 logcat 命令来查看和使用。
#### 使用logcat命令
你可以用 logcat 命令来查看系统日志缓冲区的内容:
```  
[adb] logcat [< option>] ... [< filter-spec>] ...
```
请查看Listing of logcat Command Options ，它对logcat命令有详细的描述。
你也可以在你的电脑或运行在模拟器/设备上的远程adb shell端来使用logcat 命令，也可以在你的电脑上查看日志输出。
```  
$ adb logcat
```
你也这样使用：
```  
 logcat
```
过滤日志输出
每一个输出的Android日志信息都有一个标签和它的优先级。
日志的标签是系统部件原始信息的一个简要的标志。(比如：“View”就是查看系统的标签).
优先级有下列集中，是按照从低到高顺利排列的:
```  
V — Verbose (lowest priority)
D — Debug
I — Info
W — Warning
E — Error
F — Fatal
S — Silent (highest priority, on which nothing is ever printed)
```
在运行logcat的时候在前两列的信息中你就可以看到 logcat 的标签列表和优先级别，它是这样标出的:< priority>/< tag>。
下面是一个logcat输出的例子，它的优先级就似乎I，标签就是ActivityManage:
I/ActivityManager( 585): Starting activity: Intent { action=android.intent.action...}
为了让日志输出能体现管理的级别,你还可以用过滤器来控制日志输出,过滤器可以帮助你描述系统的标签等级。
过滤器语句按照下面的格式描tag:priority ... , tag 表示是标签, priority 是表示标签的报告的最低等级。 
从上面的tag的中可以得到日志的优先级。你可以在过滤器中多次写tag:priority。
这些说明都只到空白结束。下面有一个列子，例子表示支持所有的日志信息，除了那些标签为”ActivityManager”和优先级为”Info”以上的和标签为” MyApp”和优先级为” Debug”以上的。小等级,优先权报告为tag。
```  
adb logcat ActivityManager:I MyApp:D *:S
```
上面表达式的最后的元素 *:S，是设置所有的标签为"silent"，所有日志只显示有"View" and "MyApp"的，用*:S的另一个用处是能够确保日志输出的时候是按照过滤器的说明限制的，也让过滤器也作为一项输出到日志中。 
下面的过滤语句指显示优先级为warning或更高的日志信息:
```  
adb logcat *:W
```
如果你电脑上运行logcat ，相比在远程adbshell端，你还可以为环境变量ANDROID_LOG_TAGS :输入一个参数来设置默认的过滤
```  
export ANDROID_LOG_TAGS="ActivityManager:I MyApp:D *:S"
```
需要注意的是ANDROID_LOG_TAGS 过滤器如果通过远程shell运行logcat 或用adb shell logcat 来运行模拟器/设备不能输出日志。
#### 控制日志输出格式
日志信息包括了许多元数据域包括标签和优先级。可以修改日志的输出格式，所以可以显示出特定的元数据域。可以通过 -v 选项得到格式化输出日志的相关信息.
```  
* brief — Display priority/tag and PID of originating process (the default format).
* process — Display PID only.
* tag — Display the priority/tag only.
* thread — Display process:thread and priority/tag only.
* raw — Display the raw log message, with no other metadata fields.
* time — Display the date, invocation time, priority/tag, and PID of the originating process.
* long — Display all metadata fields and separate messages with a blank lines.
```
当启动了logcat ，你可以通过-v 选项来指定输出格式:
```  
[adb] logcat [-v < format>]
```
下面是用 thread 来产生的日志格式:
```  
adb logcat -v thread
```
需要注意的是你只能-v 选项来规定输出格式 option.
#### 查看可用日志缓冲区
Android日志系统有循环缓冲区，并不是所有的日志系统都有默认循环缓冲区。为了得到日志信息，你需要通过-b选项来启动logcat。如果要使用循环缓冲区，你需要查看剩余的循环缓冲期:
```  
* radio — 查看缓冲区的相关的信息.
* events — 查看和事件相关的的缓冲区.
* main — 查看主要的日志缓冲区
```
-b 选项使用方法:
```  
[adb] logcat [-b < buffer>]
```
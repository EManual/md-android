这些权限就可以放在 AndroidManifest.xml这个文件里声明，书写格式如下：
```  
<uses-permission android:name="android.permission.SEND_SMS"></uses-permission>
```
#### ACCESS_CHECKIN_PROPERTIES 
允许读写访问"properties"表在checkin数据库中，改值可以修改上传 Allows read/write access to the "properties" table in the checkin database, to change values that get uploaded.
#### ACCESS_COARSE_LOCATION 
允许应用程序访问粗糙的位置（例如：Cell-ID,WiFi）。 Allows an application to access coarse (e.g., Cell-ID, WiFi) location
#### ACCESS_FINE_LOCATION 
允许一个程序访问精良位置(如GPS) Allows an application to access fine (e.g., GPS) location
#### ACCESS_LOCATION_EXTRA_COMMANDS 
允许一个程序访问额外的位置来提供命令。 Allows an application to access extra location provider commands
#### ACCESS_MOCK_LOCATION 
允许程序创建模拟位置提供用于测试 Allows an application to create mock location providers for testing
#### ACCESS_NETWORK_STATE 
允许程序访问有关GSM网络信息 Allows applications to access information about networks
#### ACCESS_SURFACE_FLINGER 
允许程序使用SurfaceFlinger底层特性 Allows an application to use SurfaceFlinger's low level features
#### ACCESS_WIFI_STATE 
允许程序访问Wi-Fi网络状态信息 Allows applications to access information about Wi-Fi networks
#### BATTERY_STATS 
允许程序收集手机电池统计信息 Allows an application to collect battery statistics
#### BIND_APPWIDGET 
允许程序通知AppWidget服务，哪个程序可以访问AppWidget数据。 Allows an application to tell the AppWidget service which application can access AppWidget's data.
#### BIND_INPUT_METHOD 
输入法服务必须请求来确保只有唯一的系统可以绑定他们。 Must be required by input method services, to ensure that only the system can bind to them.
#### BLUETOOTH 
允许程序链接匹配的蓝牙设备。 Allows applications to connect to paired bluetooth devices
#### BLUETOOTH_ADMIN 
允许程序发现和配对蓝牙设备 Allows applications to discover and pair bluetooth devices
#### BRICK 
请求能够禁用设备(非常危险) Required to be able to disable the device (very dangerous!).
#### BROADCAST_PACKAGE_REMOVED 
允许程序广播一个提示消息在一个应用程序包已经移除后 Allows an application to broadcast a notification that an application package has been removed.
#### BROADCAST_SMS 
允许程序广播一个SMS接收提示。 Allows an application to broadcast an SMS receipt notification
#### BROADCAST_STICKY 
允许一个程序广播常用 Allows an application to broadcast sticky intents.
#### BROADCAST_WAP_PUSH 
允许程序广播一个WAP PUSH接收提示。 Allows an application to broadcast a WAP PUSH receipt notification
#### CALL_PHONE 
允许一个程序初始化一个电话拨号不需通过拨号用户界面需要用户确认 Allows an application to initiate a phone call without going through the Dialer user interface for the user to confirm the call being placed.
#### CALL_PRIVILEGED 
允许一个程序拨打任何号码，包含紧急号码无需通过拨号用户界面需要用户确认 Allows an application to call any phone number, including emergency numbers, without going through the Dialer user interface for the user to confirm the call being placed.
#### CAMERA 
请求访问使用照相设备 Required to be able to access the camera device.
#### CHANGE_COMPONENT_ENABLED_STATE 
允许一个程序是否改变一个组件或其他的启用或禁用 Allows an application to change whether an application component (other than its own) is enabled or not.
#### CHANGE_CONFIGURATION 
允许一个程序修改当前设置，如本地化 Allows an application to modify the current configuration, such as locale.
#### CHANGE_NETWORK_STATE 
允许程序改变网络连接状态 Allows applications to change network connectivity state
#### CHANGE_WIFI_MULTICAST_STATE 
允许程序进入Wi-Fi连通模式。 Allows applications to enter Wi-Fi Multicast mode
#### CHANGE_WIFI_STATE 
允许程序改变Wi-Fi连接状态。 Allows applications to change Wi-Fi connectivity state
#### CLEAR_APP_CACHE 
允许程序清除设备上所有安装程序的缓存。 Allows an application to clear the caches of all installed applications on the device.
#### CLEAR_APP_USER_DATA 
允许程序清除用户数据。 Allows an application to clear user data
#### CONTROL_LOCATION_UPDATES 
允许启用禁止位置更新提示从无线模块 Allows enabling/disabling location update notifications from the radio.
#### DELETE_CACHE_FILES 
允许程序删除缓存文件 Allows an application to delete cache files.
#### DELETE_PACKAGES 
允许程序删除数据包 Allows an application to delete packages.
#### DEVICE_POWER 
允许访问底层电源管理 Allows low-level access to power management
#### DIAGNOSTIC 
允许程序RW诊断资源 Allows applications to RW to diagnostic resources.
#### DISABLE_KEYGUARD 
允许程序禁用键盘锁 Allows applications to disable the keyguard
#### DUMP 
允许程序恢复状态转存信息通过系统服务 Allows an application to retrieve state dump information from system services.
#### EXPAND_STATUS_BAR 
允许程序扩展或折叠状态栏 Allows an application to expand or collapse the status bar.
#### FACTORY_TEST 
作为一个工厂测试程序，运行在root用户 Run as a manufacturer test application, running as the root user.
#### FLASHLIGHT 
允许访问闪光灯 Allows access to the flashlight
#### FORCE_BACK 
允许程序强行一个返回程序无论什么在顶层活动 Allows an application to force a BACK operation on whatever is the top activity.
#### GET_ACCOUNTS 
允许账户服务中访问账户列表 Allows access to the list of accounts in the Accounts Service
#### GET_PACKAGE_SIZE 
允许一个程序获取任何package占用空间容量 Allows an application to find out the space used by any package.
#### GET_TASKS 
允许一个程序获取信息有关当前或最近运行的任务，一个缩略的任务状态，是否活动等等 Allows an application to get information about the currently or recently running tasks: a thumbnail representation of the tasks, what activities are running in it, etc.
#### GLOBAL_SEARCH 
许可可能被用在内容提供商上，为了允许全局搜索系统访问他们数据 This permission can be used on content providers to allow the global search system to access their data.
#### HARDWARE_TEST 
允许访问硬件设备 Allows access to hardware peripherals.
#### INJECT_EVENTS 
允许一个程序截获用户事件如按键、触摸、轨迹球等变成事件流并发送他们到任何窗口。 Allows an application to inject user events (keys, touch, trackball) into the event stream and deliver them to ANY window.
#### INSTALL_LOCATION_PROVIDER 
允许程序安装一个位置提供商到位置管理处。 Allows an application to install a location provider into the Location Manager
#### INSTALL_PACKAGES 
允许一个程序安装包 Allows an application to install packages.
INTERNAL_SYSTEM_WINDOW 
允许打开窗口使用系统用户界面 Allows an application to open windows that are for use by parts of the system user interface.
#### INTERNET 
允许程序打开网络套接字 Allows applications to open network sockets.
#### MANAGE_APP_TOKENS 
允许程序管理(创建、撤消、z-order默认向z轴推移)程序引用在窗口管理器中 Allows an application to manage (create, destroy, Z-order) application tokens in the window manager.
#### MASTER_CLEAR 
目前还没有明确的解释，Android开发网分析可能是清除一切数据，类似硬格机
#### MODIFY_AUDIO_SETTINGS 
允许程序修改全局音频设置 Allows an application to modify global audio settings
#### MODIFY_PHONE_STATE 
允许修改话机状态，如电源，人机接口等 Allows modification of the telephony state - power on, mmi, etc.
#### MOUNT_FORMAT_FILESYSTEMS 
允许格式化文字系统可移动存储。 Allows formatting file systems for removable storage.
#### MOUNT_UNMOUNT_FILESYSTEMS 
允许挂载和反挂载文件系统可移动存储 Allows mounting and unmounting file systems for removable storage.
#### PERSISTENT_ACTIVITY 
允许一个程序设置他的activities显示 Allow an application to make its activities persistent.
#### PROCESS_OUTGOING_CALLS 
允许程序监视、修改有关播出电话 Allows an application to monitor, modify, or abort outgoing calls.
#### READ_CALENDAR 
允许程序读取用户日历数据 Allows an application to read the user's calendar data.
#### READ_CONTACTS 
允许程序读取用户联系人数据 Allows an application to read the user's contacts data.
#### READ_FRAME_BUFFER 
允许程序屏幕波或和更多常规的访问帧缓冲数据 Allows an application to take screen shots and more generally get access to the frame buffer data
#### READ_HISTORY_BOOKMARKS 
允许程序读取（但不能写入）用户浏览器的历史记录和收藏夹。 Allows an application to read (but not write) the user's browsing history and bookmarks.
#### READ_INPUT_STATE 
允许程序恢复按键的当前状态。 Allows an application to retrieve the current state of keys and switches.
#### READ_LOGS 
允许程序读取底层系统日志文件 Allows an application to read the low-level system log files.
#### READ_OWNER_DATA 
允许程序读取所有者数据 Allows an application to read the owner's data.
#### READ_PHONE_STATE 
允许只读来访问话机状态 Allows read only access to phone state.
#### READ_SMS 
允许程序读取短信息 Allows an application to read SMS messages.
#### READ_SYNC_SETTINGS 
允许程序读取同步设置 Allows applications to read the sync settings
#### READ_SYNC_STATS 
许程序读取同步状态 Allows applications to read the sync stats
#### REBOOT 
请求能够重新启动设备 Required to be able to reboot the device.
#### RECEIVE_BOOT_COMPLETED 
允许一个程序接收到 ACTION_BOOT_COMPLETED，它在系统完成启动后广播 Allows an
application to receive the ACTION_BOOT_COMPLETED that is broadcast after the system finishes booting.
#### RECEIVE_MMS 
允许一个程序监控将收到MMS彩信,记录或处理 Allows an application to monitor incoming MMS messages, to record or perform processing on them.
#### RECEIVE_SMS 
允许程序监控一个将收到短信息，记录或处理 Allows an application to monitor incoming SMS messages, to record or perform processing on them.
#### RECEIVE_WAP_PUSH 
允许程序监控将收到WAP PUSH信息 Allows an application to monitor incoming WAP push messages.
#### RECORD_AUDIO 
允许程序录制音频 Allows an application to record audio
#### REORDER_TASKS 
允许程序改变Z轴排列任务 Allows an application to change the Z-order of tasks
#### RESTART_PACKAGES 
允许程序重新启动其他程序 Allows an application to restart other applications.
#### SEND_SMS 
许程序发送SMS短信 Allows an application to send SMS messages.
#### SET_ACTIVITY_WATCHER 
允许程序监控和控制活动如何在全球范围系统中开始的 Allows an application to watch and control how activities are started globally in the system.
#### SET_ALWAYS_FINISH 
允许程序控制是否活动间接完成当处于后台时 Allows an application to control whether activities are immediately finished when put in the background.
#### SET_ANIMATION_SCALE 
修改全局信息比例 Modify the global animation scaling factor.
#### SET_DEBUG_APP 
配置一个程序用于调试 Configure an application for debugging.
#### SET_ORIENTATION 
允许底层访问设置屏幕方向和实际旋转 Allows low-level access to setting the orientation (actually rotation) of the screen.
#### SET_PREFERRED_APPLICATIONS 
允许一个程序修改列表参数PackageManager.addPackageToPreferred() 和PackageManager.removePackageFromPreferred()方法 Allows an application to modify the list of preferred applications with the PackageManager.addPackageToPreferred() and PackageManager.removePackageFromPreferred() methods.
#### SET_PROCESS_LIMIT 
允许设置最大的运行进程数量（不是必需的） Allows an application to set the maximum number of (not needed) application processes that can be running.
#### SET_TIME_ZONE 
允许程序设置系统时间时区 Allows applications to set the system time zone
#### SET_WALLPAPER 
允许程序设置壁纸 Allows applications to set the wallpaper
#### SET_WALLPAPER_HINTS 
允许程序设置壁纸提示 Allows applications to set the wallpaper hints
#### SIGNAL_PERSISTENT_PROCESSES 
允许程序请求发送信号到所有显示的进程中 Allow an application to request that a
signal be sent to all persistent processes
#### STATUS_BAR 
允许程序打开、关闭或禁用状态栏及图标 Allows an application to open, close, or disable the status bar and its icons.
#### SUBSCRIBED_FEEDS_READ 
允许一个程序访问订阅RSS Feed（内容提供者） Allows an application to allow access the subscribed feeds ContentProvider.
#### SUBSCRIBED_FEEDS_WRITE 
系统暂时保留改设置,Android开发网认为未来版本会加入该功能。
#### SYSTEM_ALERT_WINDOW 
允许程序打开窗口通过使用TYPE_SYSTEM_ALERT，显示在其他所有程序的顶层 Allows an application to open windows using the type TYPE_SYSTEM_ALERT, shown on top of all other applications.
#### UPDATE_DEVICE_STATS 
允许程序更新设备统计信息 Allows an application to update device statistics.
#### VIBRATE 
允许访问振动设备 Allows access to the vibrator
#### WAKE_LOCK 
允许使用PowerManager的WakeLocks保持进程在休眠时从屏幕消失( Allows using PowerManager WakeLocks to keep processor from sleeping or screen from dimming
#### WRITE_APN_SETTINGS 
允许程序写入API设置 Allows applications to write the apn settings
#### WRITE_CALENDAR 
允许程序写入但不读取用户日历数据 Allows an application to write (but not read) the user's calendar data.
#### WRITE_CONTACTS 
允许程序写入但不读取用户联系人数据 Allows an application to write (but not read) the user's contacts data.
#### WRITE_EXTERNAL_STORAGE 
允许程序写入外部存储器 Allows an application to write to external storage
#### WRITE_GSERVICES 
允许程序修改Google服务地图 Allows an application to modify the Google service map.
#### WRITE_HISTORY_BOOKMARKS 
允许程序写入但不读取用户浏览器的历史记录和收藏夹 Allows an application to write (but
not read) the user's browsing history and bookmarks.
#### WRITE_OWNER_DATA 
允许一个程序写入但不读取所有者数据 Allows an application to write (but not read) the owner's data.
#### WRITE_SECURE_SETTINGS 
允许程序读取或写入安全系统设置 Allows an application to read or write the secure system settings.
#### WRITE_SETTINGS 
允许程序读取或写入系统设置 Allows an application to read or write the system settings.
#### WRITE_SMS 
允许程序写短信 Allows an application to write SMS messages.
#### WRITE_SYNC_SETTINGS 
允许程序写入同步设置 Allows applications to write the sync settings
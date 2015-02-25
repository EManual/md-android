Android提供了一个Configuration类，主要用来描述与能让应用程序获取的资源相关的所有硬件配置信息。包含用户指定的信息项(本地和缩放比例)和动态硬件配置(一系列的输入设备)。
Configuration 类中包含了很多种信息，例如系统字体大小，orientation，输入设备类型等等.fontScale -- 来源于system.prop中sys.font.scale配置项，输入设备类型配置：系统加入的任何输入device必须拥有输入属性:现在android中仅支持touchscreen(触摸),keyboard(键盘),navigation(滚动球)
```  
orientation -- 屏幕方位
keyboardHidden -- 如果是划盖或开盖手机并且没有软键盘支持，这个设成true
hardKeyboardHidden -- 如果是划盖或开盖手机,这个设成true
locale -- 用户选择的location信息
theme -- 皮肤
```
有时候，就需要从Configuration中读取相关的数据，比如本地语言等。通过什么方式来实现呢?这里使用ActivityManagerNative.getDefault()获取IActivityManager对象，然后调用IActivityManager对象的getConfiguration方法获得Configuratin。
```  
IActivityManager am = ActivityManagerNative.getDefault();
Configuration conf = am.getConfiguration();
//如果需要修改Configuratin，先改变数据，然后调用IActivityManager对象的updateConfiguration方法。
```
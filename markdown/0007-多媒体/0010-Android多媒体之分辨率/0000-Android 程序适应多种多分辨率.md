关于在不同分辨率中的布局自动适应的问题，在网上找了很多，现在将其分享出来：
默认的加载方式都不能很好地适应不同的分辨率，Android从1.6开始支持多种分辨率的处理，原理简而言之就是根据屏幕参数，动态加载资源文件。在Android项目文件结构中，drawable文件夹下包含三个子文件夹，分别为drawable-hdpi,drawable-mdpi,drawable-ldpi,分别存放hdpi,mdpi,ldip的位图。应用程序运行时，Android系统会根据当前设备的屏幕大小、分辨率、屏幕密度、方向、长宽比等信息，选择相应文件夹进行加载。Android配置修饰符的定义规则如下：
1）在res 文件夹下新建目录，命名为<resources_name>-<qualifier> 这种格式，其中<resources_name> 为标准资源名称，例如drawable 或者layout;<qualifier> 即修饰符，指定对应的屏幕参数，比如normal/small/large,hdpi/mdpi/ldpi,land/port,long/notlong 等。
2）在步骤1 新建的文件夹中存入相应的资源，比如位图资源或者layout 资源，资源文件的名字必须与默认资源文件的名字相同。例如：
```  
file:///C:/Users/zwt/AppData/Local/youdao/ynote/images/DDC395E307E34A9B9858AFA355F8DACC/1331034307_d5aefdc6.jpeg
```
3）Android 系统支持多分辨率的机制离不开Android-Manifest.xml 文件的supports-screen 元素，若应用程序要适应多种分辨率，需要将anyDensity 设置为true.
```  
<supports-screens android:anyDensity="true" android:largeScreens="true"/>
```
想必大家都知道Google官方提供的Android开发环境是Eclipse，仅仅给出了ADT插件。但是在Android SDK Tool文件夹中我们可以找到一个名为activityCreator.bat的批处理文件，它调用的是tools\lib\activityCreator文件夹中的activityCreator.exe程序，其实为一个Python语言解释程序。activityCreator的Activity创建脚本全部参数使用方法如下：
```  
Usage：
activityCreator [--out outdir] [--ide intellij] your.package.name.ActivityName
Creates the structure of a minimal Android application.
The following will be created：
- AndroidManifest.xml： The application manifest file.
- build.xml： An Ant script to build/package the application.
- res ： The resource directory.
- src ： The source directory.
- src/your/package/name/ActivityName.java the Activity java class. packageName
is a fully qualified java Package in the format . … （with
at least two components）.
- bin ： The output folder for the build script.
Options：
--out ： specifies where to create the files/folders.
--ide intellij： creates project files for IntelliJ
```
比如：定位目录到Android SDK的Tools目录中，执行activityCreator -out ReleaseDir cn.com.android.www
其中 -out参数为设置输出文件夹为ReleaseDir，而最后的cn.com.android.www为我们的Package包名称。
注意：由于Android SDK对应开发平台不同，而对应编译文件也不同，Windows下为批处理activityCreator.bat，而Linux系统自带Python解释器，所以为activityCreator.py。
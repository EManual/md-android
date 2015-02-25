看了论坛还没有帖子解决这个问题，特写这篇教学，大家互相学习。
由于在Android源码中，很多方法、成员、类、包都被打上@hide标签，这些成员在SDK中没有公开，以至于在编译Launcher源码时最常遇到的类android.view.View的成员mScrollX无法访问。
下面说说如何解决这个问题。
1，准备好编译后的Android源码。
2，在该源码的out目录下寻找包含你所用隐藏类的jar文件，通常文件名为classes.jar。例如framework的jar文件为out\target\common\obj\JAVA_LIBRARIES\framework_intermediates\classes.jar。
3，在eclipse的Android项目中，选择项目属性->Java Build Path->Libraries->Add Library->User Library->Next-> UserLibraries进入到User Libraries管理界面，点击New新建一个User Library，比如android_framework，点击Add Jars把Jar包加入到建立的User Library中，最后点击OK就可以了。
注意：为了访问因此成员，需要改变类搜索顺序，选择项目属性->Java Build Path->Order and Export，把所建立的User Libraries移到Android SDK的上面。
这个时候你的eclipse中的错误应该已经减少，甚至没有了。
要想在模拟器上马上看效果的话，按照以下方式进行修改：
改掉原始包的名字，切记使用eclipse的重命名机制（在包名上按F2可修改），不仅是类的引用，还有很多xml文件内部的引用（如import com.android.launcher3.R;），只要重命名不错，这些都可以一次性搞定的。最后在AndroidManifest.xml文件里面，找到这句话删除掉（android:sharedUserId="android.uid.shared"）。到现在为止，你就拥有了自己的Launcher了！
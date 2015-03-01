#### 布局优化
使用tools里面的hierarchyviewer.bat来查看layout的层次。在启动模拟器启动所要分析的程序，再启动 hierarchyviewer.bat，选择模拟器以及该程序，点击“Load View Hierarchy”，就会开始分析。可以save as png。
<merge> 减少视图层级结构
从上图可以看到存在两个FrameLayout，红色框住的。如果能在layout文件中把FrameLayout声明去掉就可以进一步优化布局代码了。 但是由于布局代码需要外层容器容纳。
直接删除FrameLayout则该文件就不是合法的布局文件。这种情况下就可以使用<merge> 标签了。
修改为如下代码就可以消除多余的FrameLayout了：
```  
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android" >
    <ImageView
        android:id="@+id/image"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:scaleType="center"
        android:src="@drawable/candle" />
    <TextView
        android:id="@+id/text1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="@string/hello"
        android:textColor="00ff00" />
    <Button
        android:id="@+id/start"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:text="Start" />
</merge>
```
<merge>也有一些使用限制： 只能用于xml layout文件的根元素;在代码中使用LayoutInflater.Inflater()一个以merge为根元素的布局文件时候，需要使用View inflate (int resource, ViewGroup root, boolean attachToRoot)指定一个ViewGroup 作为其容器，并且要设置attachToRoot 为true。
通过inflate在Activity中布局是个有性能消耗的过程。每增加一个嵌套的布局和视图都会对应用的性能造成很大的影响。
总之，好的实践是尽量保持布局尽可能简单，尤其是要避免嵌套inflate操作整个新的布局，这是为更新已经存在布局的小变化。
以下几点是包含在Android最佳实践指导原则里的，当然并不绝对：
1、避免不必要的嵌套：不要把一个布局放置在其他布局里，除非是必要的。
2、避免使用太多视图：在一个布局中每增加一个新的视图，都会在inflate操作时耗时和消耗资源。任何时候都不要在一个布局中包含超过80个视图，否则，消耗在inflate操作上的时间会很大。
3、避免深度嵌套：布局可以任意嵌套，这极易于创建复杂和深度嵌套的布局层次。如果没有硬性限制，将嵌套限制在10层以下是好的实践。
优化布局层次，比如减少没效率的或者不必要的嵌套布局，是十分重要的。
Android SDK包含了layoutopt，一个命令行工具，来辅助这个优化工作。运行该命令，参数是布局文件或者布局文件的目录，将分析并给出改善的建议。
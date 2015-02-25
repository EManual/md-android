#### 一、创建Eclipse工程
按照下列步骤在 Eclipse 中创建 Android 工程：
1.选择 File > New > Project。
2.选择 Android > Android Project，并点击 Next。
3.选择工程的内容：
输入工程的名称，这是你创建的工程所在文件夹的名称。
在 Contents 下面，选择 Create new project in workspace. 选择工程工作区的位置。
在Target下面，选择一个Android target用作工程的Build Target。
Build Target指定你希望应用被创建的Android平台。除非你知道你要使用SDK最近引入的新的APIs，否则你应当选择一个尽可能低版本平台的target，如Android 1.6平台注意：你可以随时改变工程的Build Target：在 Package Explorer中右键点击你的工程，选择Properties，选择Android，然后选择你想要的Project Target
在Properties下面，填写所有需要的字段
输入应用的名称，这个名称将出现在Android设备上，并且是人类可读的
输入组件的名称，这是将要放置源代码组件的命名空间（命名规则遵循 Java 编程语言的组件命名规则）
选择 Create Activity（可选，通常都选择），输入一个主 Activity 类的名称
输入一个Min SDK Version。这是一个整数，用来表示运行你的应用所需要的最低API Level。输入后会在Android Manifest文件的中自动设置minSdkVersion属性。如果你确定不了合适的API Level，复制Build Target列出的API Level
4.点击 Finish。
完成之后，ADT将创建下列文件夹和文件：
```  
src/：包含存根类 Activity Java 文件，所有其他Java文件都在这里：包含你用来创建应用的 android.jar 文件，由你在New Project Wizard中所选择的build target决定。
gen/：这里包含 ADT 生成的 Java 文件，如你的R.java文件和 AIDL文件创建的接口（这是一个自动的类）。
assets/：这是空的，你可以用它来存储原始资产文件。
res/：用于存储应用程序资源的文件夹，如可拖拽文件，构图文件，字符串值等。
AndroidManifest.xml：你工程的 Android Manifest。
default.properties：这个文件包含工程的设置，如 build target，它是工程所必须的，应该在 Source Revision Control系统中维护。不要对其进行人工编辑——要编辑工程的属性，右键点击工程的文件夹，并选择"Properties"
```
在本教程中，我们只使用两个文件夹：src/ 和 res/， 我们现在从普遍问题的解决实例开始
如何通过触碰事件来滑动两个（或更多的）布局 如新闻和天气
现在你已经准备好了，模拟图形效果是我们在Android开发中首先需要做的几件事之一。所以，用的最多、效果最好的动画/事件之一是用手指滑动窗口，从一个活动转向另一个。完成这项工作需要一些java代码和一些XML布局文件中的提示。
从 XML 布局文件开始
从路径 res/layout/ 打开 main.xml （或者其他布局文件）并输入这些行：
```  
<ViewFlipper
    android:id="@+id/layoutswitcher"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:layout_margin="6dip" >
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
        <TextView
            android:id="@+id/firstpanel"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:paddingTop="10dip"
            android:text="my first panel"
            android:textStyle="bold" >
        </TextView>
    </LinearLayout>
    <LinearLayout
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" >
        <TextView
            android:id="@+id/secondpanel"
            android:layout_width="fill_parent"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:paddingTop="10dip"
            android:text="my second panel"
            android:textStyle="bold" >
        </TextView>
    </LinearLayout>
</ViewFlipper>
```
正如你看到的，我加入了一个ViewFlipper，两个LinearLayout，并为每个内部布局加入simpletextview，当你可以定制你想要的两个内部布局的类型时，如果你想加入用来开关内部布局的工具栏，则第一步在创建合适的开关布局时是必需的。
第二步：Java 代码（通过触碰开关布局）
现在从 src/ 打开你的主类，在类的开始声明这两个变量：
```  
private ViewFlipper vf;
private float oldTouchValue; 
```
第一个变量VF用来控制 ViewFlipper，触碰事件需要第二个变量，现在将 viewflipper 附到类中：
```  
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	....
	vf = (ViewFlipper) findViewById(R.id.switchlayout);
} 
```
现在我们有一个声明过的 viewflipper，进入本教程的最后几步。
触碰事件
这款新的移动电话最重要的特征是触摸屏系统，正如iPhone的apps或者其他触摸设备一样，用户喜欢用手指在不同的活动之间进行切换。在Android开发中，你可以用两种方式附加触摸事件，"应用 touchlistener"或者重写主函数声明一个onTouchEvent。
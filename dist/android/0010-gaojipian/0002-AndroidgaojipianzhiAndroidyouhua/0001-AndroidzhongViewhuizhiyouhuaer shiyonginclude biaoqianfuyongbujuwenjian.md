尽管Android通过内置了各种各样的控件提供了微小、可复用的交互性元素，也许你需要复用较大的组件 ---- 某些特定布局文件 。为了更有效率复用的布局文件，你可以使用以及标签将其他的布局文件加入到当前的布局文件中。
复用布局文件是一种特别强大的方法，它允许你创建可复用性的布局文件。例如，一个包含“Yse”or“No”的Button面版，或者是带有文字说明的Progressbar。复用布局文件同样意味着你应用程序里的任何元素都能从繁杂的布局文件提取出来进行单独管理，接着你需要做的只是加入这些独立的布局文件(因为他们都是可复用地)。因此，当你通过自定义View创建独立的UI组件时，你可以复用布局文件让事情变得更简单。
#### 1、创建一个可复用性的布局文件
如果你已经知道复用布局的”面貌”，那么创建、定义布局文件( 命名以”.xml”为后缀)。例如，这里是一个来自G-Kenya codelab的布局文件，定义了在每个Activity中都要使用的一个自定义标题(titlebar.xml):由于这些可复用性布局被添加至其他布局文件中，因此，它的每个根视图(root View)最好是精确(exactly)的。
```  
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@color/titlebar_bg" >
    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/gafricalogo" />
</FrameLayout>
```
#### 2、使用标签
在需要添加这些布局的地方，使用标签。例如，下面是一个来自G-Kenya codelab的布局文件，它复用了上面列出的“title bar”文件， 该布局文件如下：
```  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/app_bg"
    android:gravity="center_horizontal"
    android:orientation="vertical" >
    <include layout="@layout/titlebar" />
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="10dp
        android:text="@string/hello" />
</LinearLayout>
```
你也可以在节点中为被添加的布局文件的root View定义特别标识，重写所有layout参数即可(任何以“android：layout_”为前缀的属性)。例如：
```  
<include android:id="@+id/news_title"  
	 android:layout_width="match_parent"
	 android:layout_height="match_parent"
	 layout="@layout/title"/>
```
#### 3、使用标签
当在布局文件中复用另外的布局时， 标签能够在布局层次消除多余的视图元素。例如，如果你的主布局文件是一个垂直地包含两个View的LinearLayout，该布局能够复用在其他布局中，而对任意包含两个View的布局文件都需要一个root View(否则，编译器会提示错误)。然而，在该可复用性布局中添加一个LinearLayout作为root View，将会导致一个垂直的LinearLayout包含另外的垂直。内嵌地LinearLayout只能减缓UI效率，其他毫无用处可言。该复用性布局利用.xml呈现如下：
```  
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/app_bg"
    android:gravity="horizontal"
    android:orientation="vertical" >
    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/add" />
    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/delete" />
</LinearLayout>
```
为了避免冗余的布局元素，你可以使用作为复用性布局文件地root View 。例如：
```  
<merge xmlns:android="http://schemas.android.com/apk/res/android" >
    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/add" />
    <Button
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/delete" />
</merge> 
```
现在，当你添加该布局文件时(使用标签)，系统忽略< merge />节点并且直接添加两个Button去取代节点。
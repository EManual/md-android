RelativeLayout是一个在相对位置上显示子View元素的VeiwGroup，一个视图的位置,可以指定为相对于兄妹的元素（比如一个给定的与孙的左边或者下边）或者心爱那个对于RelativeLayout区域的位置（比如与底部对齐，剩下的中心）
一个RelativeLayout是一个非常强大使用的为设置用户界面的布局，因为它可以消除嵌套的视图组ViewGroup，如过你发现你用了几个嵌套的LinearLayout组，你可以替换为一个单独的RelativeLayout
1、开始一个新的工程，名字叫做HelloRelativeLayout
2、打开res/layout/main.xml文件并且插入如下信息
```  
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <TextView
        android:id="@+id/label"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="Type here:" />
    <EditText
        android:id="@+id/entry"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/label"
        android:background="@android:drawable/editbox_background" />
    <Button
        android:id="@+id/ok"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true
        android:layout_below="@id/entry"
        android:layout_marginLeft="10dip"
        android:text="OK" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignTop="@id/ok"
        android:layout_toLeftOf="@id/ok"
        android:text="Cancel" />
</RelativeLayout>
```
3、注意到每一个android:layout_*属性，比如layout_below,layout_alignParentRightRight，和layout_toLeftOf，当用一个RelativeLayout的时候，你可以用这些属性来描述你想要的每个视图View的位置，每一个这些属性都定义一个不懂种类的相对位置，一些属性用到同级视图的资源ID来定义自己的相对位置。比如最后一个Button是被定义到位于被定义ID为ok的视图的左边和顶部对齐，所有的layout布局属性都被定义在RelativeLayout.LayoutParams中。
4、现在打开HelloLinearLayout.java并且确定它已经在onCreate()方法中加载了res/layout/main.xml布局文件
```  
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.main);
```
5、你可以看到如下的布局
![img](P)  
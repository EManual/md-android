我们都知道EditText与TextView是Android的文本输入框和文本显示框，但是基于手机屏幕的大小因素，如果在需要输入较多文字或者显示较多内容的时候，手机屏幕是远远不够的，因此让文本框具有滚动条的功能是手机上必备的，下面ATAAW.COM来介绍下如何加上滚动条。
要加上滚动条，其实很简单，只需要在文本输入框或者文本显示框上面加上滚动条控件即可，该控件名字为ScrollView，以下我们对比下(以TextView举例)。
A、未加滚动效果
```  
<TextView
	android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:id="@+id/textView" />
```
B、加上滚动效果
```  
<ScrollView
    android:id="@+id/scrollView"
    android:layout_width="fill_parent"
    android:layout_height="200px"
    android:background="@android:drawable/edit_text"
    android:scrollbarStyle="outsideOverlay" >
    <TextView
        android:id="@+id/textView"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</ScrollView>
```

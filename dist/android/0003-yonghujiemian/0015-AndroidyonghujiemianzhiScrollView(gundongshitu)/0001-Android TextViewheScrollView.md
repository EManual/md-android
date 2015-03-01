在Android上面让TextView过多的文字实现有滚动条，之前想简单了以为设置TextView的属性就可以实现，结果还是需要ScrollView配合使用，才能达到滚动条的效果有两种方式实现。
一种是代码写java的layout
```  
RelativeLayout.LayoutParams param = new RelativeLayout.LayoutParams(80, 80);
// 初始化滚动条控件
ScrollView scrollView = new ScrollView(this);
scrollView.setScrollContainer(true);
scrollView.setFocusable(true);
// 初始化文字控件
TextView textView = new TextView(this);
textView.setHorizontallyScrolling(true);
textView.setText("走情感路线,哈哈哈, 走情感路线,哈哈哈");
scrollView.addView(textView);
main_view.addView(scrollView, param);
```
另一种则用layout.xml来实现
```  
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:fadingEdge="vertical"
    android:scrollbars="vertical" >
    <TextView
        android:id="@+id/text_view"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:paddingTop="5dip"
        android:textColor="071907" >
    </TextView>
</ScrollView>
```
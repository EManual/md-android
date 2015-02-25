#### RatingBar
这个组件主要是用于显示或从用户那里进行数值设定，默认的情况下，进度条里有5个五角星，而且它类似SeekBar，也可以进行拖动进度条
需要注意的是，不同于SeekBar，实现的onRatingChange()方法是在修改完成之后调用的，通常是在用户抬起手指时候，也就是说，当用户在五角星上拖动设置评分的时候，这个方法并不会被调用，而是在用户停止触摸控件的时候获得调用。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:orientation="vertical" >
    <RatingBar
        android:id="@+id/ratebar1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:numStars="5"
        android:stepSize="0.25" />
    <TextView
        android:id="@+id/text"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" />
</LinearLayout>
```
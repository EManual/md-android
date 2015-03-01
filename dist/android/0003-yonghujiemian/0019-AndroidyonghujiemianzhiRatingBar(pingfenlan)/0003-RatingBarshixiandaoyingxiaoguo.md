如何才能实现在倒影效果
![img](P)  
已经搞定（用换图的方法）
自己写的style
```  
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
        android:id="@+android:id/background"
        android:drawable="@drawable/unselected">
    </item>
    <item
        android:id="@+android:id/secondaryProgress"
        android:drawable="@drawable/unselected">
    </item>
    <item
        android:id="@+android:id/progress"
        android:drawable="@drawable/selected">
    </item>
</layer-list>
```
XML中的
```  
<RatingBar android:id="@+id/systemclear_ratingScore" 
    android:layout_height="wrap_content" android:layout_width="wrap_content"
    style="@style/roomRatingBar" android:numStars="5" />
```
string中的
```  
<style name="roomRatingBar" parent="@android:style/Widget.RatingBar.Small">这句很重要实现不可交互用的
	<item name="android:progressDrawable">@drawable/room_rating_bar</item>
	<item name="android:minHeight">70px</item>
	<item name="android:maxHeight">57px</item>
</style>
```
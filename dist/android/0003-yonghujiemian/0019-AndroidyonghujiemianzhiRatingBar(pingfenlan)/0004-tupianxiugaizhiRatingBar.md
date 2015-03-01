RatingBar是我们在系统显示分数的好组件，但是我们一般想把RatingBar装饰的更好看，想把更好看的图片用来替换系统默认的图片，系统默认的样子是这样的：
下图是个不错的选择哦：
想要实现如上效果，首先我们在styles.xml写入一个样式：
```  
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<style name="foodRatingBar" parent="@android：style/Widget.RatingBar">
		<item name="android：progressDrawable">@drawable/food_ratingbar_full</item>
		<item name="android：minHeight">48dip</item>
		<item name="android：maxHeight">48dip</item>
	</style>
</resources>
```
然后在Drawable文件夹下建food_rating_bar_full.xml文件，内容如下：
```  
<?xml version="1.0" encoding="utf-8"?>
<layer-list
    android="http：//schemas.android.com/apk/res/android"
    xmlns="" >
    <item
        id="@+android：id/background"
        android=""
        android=""
        drawable="@drawable/food_ratingbar_full_empty"/>
    <item
        id="@+android：id/secondaryProgress"
        android=""
        android=""
        drawable="@drawable/food_ratingbar_full_empty"/>
    <item
        id="@+android：id/progress"
        android=""
        android=""
        drawable="@drawable/food_ratingbar_full_filled"/>
</layer-list>
```
food_ratingbar_full_empty是代表没有选中图片效果，food_ratingbar_full_filled选中图片效果。
最后将style放入RatingBar中，即可实现你的图片效果：
Xml代码
```  
<RatingBar
    id="@+id/my_rating_bar"
    style="@style/foodRatingBar"
    android="" />
```
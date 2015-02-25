很多开发者在做Android UI时不知道如何设置ScrollView滚动条控件的滑块颜色，其实通过ScrollView的xml布局属性android:scrollbarThumbVertical可以关联一个drawable对象，比如说在ScrollView中我们有
```  
android:scrollbars="vertical"   //滚动条是垂直的
android:scrollbarThumbVertical="@drawable/red"  //垂直滚动条颜色为red，red可以是一个png的图片或用shape组成的xml图形文件组成的drawable对象。
```
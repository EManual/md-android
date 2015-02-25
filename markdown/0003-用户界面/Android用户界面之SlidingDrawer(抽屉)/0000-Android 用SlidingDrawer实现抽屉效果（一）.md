一、SlidingDrawer 这个类，也就是所谓的"抽屉"类。它的用法很简单，要包括handle,和content。
handle 就是当你点击它的时候，content 要么抽抽屉要么关抽屉。
这是上下拉抽屉的效果，将 SlidingDrawer属性设置为android:orientation="vertical"即可
![img](P)  
![img](P)  
这是左右拉抽屉的效果，将 SlidingDrawer属性设置为android:orientation="horizontal"即可。
![img](P)  
![img](P)  
二、重要属性
```  
android:allowSingleTap：指示是否可以通过handle打开或关闭
android:animateOnClick：指示是否当使用者按下手柄打开/关闭时是否该有一个动画。
android:content：隐藏的内容
android:handle：handle(手柄)
```
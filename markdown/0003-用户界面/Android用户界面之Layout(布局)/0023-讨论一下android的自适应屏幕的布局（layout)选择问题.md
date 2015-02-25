权威的文章应该是这个：
http://developer.android.com/guide/practices/screens_support.html
![img](P)  
```  
res/layout/my_layout.xml             // layout for normal screen size ("default")
res/layout-small/my_layout.xml       // layout for small screen size
res/layout-large/my_layout.xml       // layout for large screen size
res/layout-xlarge/my_layout.xml      // layout for extra large screen size
res/layout-xlarge-land/my_layout.xml // layout for extra large in landscape orientation
res/drawable-mdpi/my_icon.png        // bitmap for medium density
res/drawable-hdpi/my_icon.png        // bitmap for high density
res/drawable-xhdpi/my_icon.png       // bitmap for extra high density
xlarge screens are at least 960dp x 720dp
large screens are at least 640dp x 480dp
normal screens are at least 470dp x 320dp
small screens are at least 426dp x 320dp
```
Beginning with Android 3.2 (API level 13), these size groups are deprecated
从androd 3.2开始，这些xlarge,large,normal,small等标识已经过时了。
启用新的标识了。
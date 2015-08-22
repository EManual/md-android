在项目开发的过程中，同一个布局对应不同的手机会显示出不同的效果。导致这个现象产生的原因是不同手机的分辨率不同。在android sdk提供的帮助文档中，我们可以看到各种手机的分辨率和对应的屏大小。QVGA (240x320)，WQVGA400(240x400)，WQVGA432 (240x432)，HVGA (320x480)，WVGA800 (480x800)，WVGA854 (480x854)。

目前android手机的分辨率大致就是帮助文档中描述的几种，我们可以用两种方式进行不同手机的适配。一种是在java代码中，另外一种是在xml文件中。具体使用哪种方式更有效更合适，要看具体的情况而定。

在xml进行手机匹配，主要是针对布局中控件太多，不方便在java代码中修改的情况。在xml中解决不匹配问题很简单，对于不同手机的分辨率，建立对应的layout文件即可。例如：480x800，之间建立layout-800x400,240x320，建立layout-320x240。特别注意：大的写在前面，例如800,320，小的写在后面，例如480,240。建立了相应的layout后，还要在不同的手机上调整布局中的控件大小和位置。

我选择的是xml匹配方式，结果发现按上面的方式做了之后，对应分辨率的手机的显示没有任何的效果，后来，我查看帮助文档后，发现必须要在androidmainfest中进行如下代码的配置：

```
    <supports-screens
        android:anyDensity="true
        android:largeScreens="true
        android:normalScreens="true
        android:smallScreens="true
        android:xlargeScreens="true" />
```


如果没有这几行代码，不管你怎么调整layout中的控件，对应分辨率的手机是没有任何效果的。注意：由于android版本的不同，有些版本不支持xlargeScreens，可以直接将android:xlargeScreens="true"去掉。

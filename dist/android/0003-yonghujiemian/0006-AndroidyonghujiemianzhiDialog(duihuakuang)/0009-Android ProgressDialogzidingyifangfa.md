项目当中遇到这样一个应用场景：执行某个操作需要耗时15秒以上，依照惯例，这就要使用到进度条一类的UI控件，以安抚用户等待的烦躁心情。Android Framework已经提供了ProgressDialog，可以很好的解决这个问题。
ProgressDialog实际上是AlertDialog的子类，其有着两种不同的表现形式。第一种是针对没有明确的进度，不知道当前完成了多少的情况，此时使用一个转动的圆环来展现；第二种是针对有了明确的总进度，并知道当前的完成比例等信息，此时使用的是一个横条来展现。根据项目方案，我们的效果类似第一种情形。
不过我所处的项目情况比较特殊，因为由于设备的特性导致不能频繁刷新屏幕，–刷新一次屏幕的时间大于2s。因此凡是动画之类的效果统统禁用，原生的ProgressDialog也就不能使用了，只能另想解决办法。
解决问题的办法之一就是修改ProgressDialog的默认实现，使其不要频繁刷新。修改之前要先清楚其是怎么实现的。通过源代码可以知道，ProgressDiglog是通过在themes.xml定义progressBarStyle，进一步从styles.xml中找到Widget.ProgressBar的样式定义：
```  
<style name="Widget.ProgressBar">
<item name="android:indeterminateOnly">true</item>
<item name="android:indeterminateDrawable">@android:drawable/progress_medium</item>
<item name="android:indeterminateBehavior">repeat</item>
</style>
```
如果没猜错的话，接下来要做事情就变成了修改android:indeterminateDrawable对应的drawable。
先向drawable文件夹下增加两个图片progress_round_1.png和progress_round_2.png，然后增加一个XML文件progress_round.xml。
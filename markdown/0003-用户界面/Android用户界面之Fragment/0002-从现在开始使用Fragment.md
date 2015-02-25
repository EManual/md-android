Android开发者们应该有所耳闻了，但大多数应该没有使用过。
曾经我也很诧异为啥又弄出来一个跟activity一样有类似生命周期的东东，而且还只有平板可以用。
下面讲讲为啥要用Fragment：
首先，Fragment可以兼容手机和平板，最大减少针对不同平台的工作量。
其次，Fragment可以向下兼容（通过android官方的Support Package），在2.x平台上没有任何问题。
最重要的是，Fragment实质上是一种可以包含控制代码的视图模块，可以非常方便的进行组合。
[另外，如果大家现在去看TabActivity的官方文档，会发现此类已被标记为deprecated，建议使用Fragment代替]
废话少说，给大家展示两个项目，都是github上面开源的。
1.水平分页指示器，google+中有用到这种效果，现已成为android4.0标配。
这个项目只是实现了分页指示。谷歌的Support Package自己内置了一套水平滑动的方案，非常实用，基于Fragment实现。
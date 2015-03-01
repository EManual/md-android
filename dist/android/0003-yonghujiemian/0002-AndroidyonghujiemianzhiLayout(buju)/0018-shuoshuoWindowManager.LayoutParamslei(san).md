接上篇：
```  
8. public int gravity;
gravity 属性。什么是gravity属性呢？简单地说，就是窗口如何停靠。
9. public float horizontalMargin;
水平边距，容器与widget之间的距离，占容器宽度的百分率。
10. public float verticalMargin;
纵向边距。
11. public int format;
期望的位图格式。默认为不透明。参考android.graphics.PixelFormat。
12. public int windowAnimations;
窗口所使用的动画设置。它必须是一个系统资源而不是应用程序资源，因为窗口管理器不能访问应用程序。
13. public float alpha = 1.0f;
整个窗口的半透明值，1.0表示不透明，0.0表示全透明。
14. public float dimAmount = 1.0f;
当FLAG_DIM_BEHIND设置后生效。该变量指示后面的窗口变暗的程度。
1.0表示完全不透明，0.0表示没有变暗。
15. public float screenBrightness = -1.0f;
用来覆盖用户设置的屏幕亮度。表示应用用户设置的屏幕亮度。
从0到1调整亮度从暗到最亮发生变化。
16. public IBinder token = null;
窗口的标示符。( Identifier for this window. This will usually be filled in for you. )
17. public String packageName = null;
此窗口所在的包名。
18. public int screenOrientation =ActivityInfo.SCREEN_ORIENTATION_UNSPECIFIED;
屏幕方向，参见android.content.pm.ActivityInfoscreenOrientation。
19. 在兼容模式下，备份/恢复参数所使用的内部缓冲区。
public int[] mCompatibilityParamsBackup = null;
```
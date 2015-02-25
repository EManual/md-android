1、ID：(android:id="@id/name" android:id="@+id/name")任何一个view对象都有可能有一个int类型的ID与其关联来唯一的确认这个组件，在字符串开头的@符号表示xml解析器应该将ID字符串以ID资源的形式解析和扩展。而+号表示这个名称是新的，我们需要在资源文件（R.java）中添加（这一步是自动的，系统会为我们自动添加），如果我们需要一个在R.java中已经存在了的ID作为其ID的话，我们不需要加上+符号.还有一种是在android系统中定义的ID资源，当我们需要用到这些资源的时候，我们不需要添加+符号，只需要添加上android包命名空间即可如：android:id="@android:id/empty"。
2、android:layout_height and android:layout_width：所有的view对象都会需要这两者，来表示view对象的高度和宽度，可能会通过精确的测量来设置view对象的宽度和高度，但是也可能会更经常的用到wrap_content和fill_parent，其中wrap_content表示view对象是自适应的，根据内容来自适应大小。而fill_parent是让你的view对象尽可能的去填充他的父对象。在api level8中fill_parent被更名为match_parent。
3、android:gravity ：用来设置控件里的子控件的对齐方式，而android:layout_gravity是设置控件本身在其父控件中的对齐方式。
4、android:orientation ：总共有两个属性vertical表示在其内部的子控件是垂直布局，而horizontal表示在其内部的子控件是水平布局。
5、android:layout_weight：用于给线性布局中的视图的重要度赋值的，所有的view对象都有一个android:layout_weight,默认为0，需要多大的空间就占据多大的屏幕空间，若赋个高于零的值，则将父视图中的可用控件分割，分割大小取决于每一个视图的layout_weight的值和其他控件的android:layout_weight的值。下面是个例子，别人给的，比较通俗易懂：现在在水平方向上有一个TextView和两个EditView，如果TextView不指定该值，而两个EditView则指定该值为1，那么后两者就会平分TextView剩余的空间，如果一个EditView的值为1另一个的值为2那么前者将得到TextView剩余空间的三分之一，后者得三分之二。
6、android:layout_margin ：是指该View对象与其父View对象之间的边距。
7、android:padding ：是指该View对象内的内容与该View对象之间的边距。
8、在这里顺便说一下dip、px、dp、sp的意思和区别：
dip是指device independent pixels（设备独立像素点）不同设备有不同的显示效果，这个和设备硬件有关，一般我们为了支持WVGA、HVGA和QVGA推荐使用这个，它不依赖与像素，需要特别注意的是dip与屏幕密度有关，而屏幕密度又与具体的硬件有关，硬件配置不正确，有可能导致dip不能正常显示，在屏幕密度为160的显示屏上，1dip=1px，有时候你的屏幕分辨率很大如480*800，但是屏幕密度没有正确设置比如说还是160，那么这个时候凡是使用dip的都会显示异常，基本上显示都是过小，dip的换算公式如下所示：
dip = int(px/1.5 + 0.5)。dp与dip是一个概念。
px指像素点这是绝对像素。
sp放大像素，主要用于字体显示。
NOTE: 根据google的推荐，像素统一使用dip，字体统一使用sp。
```  
public static int dip2px(Context context, float dipValue) {
	final float scale = context.getResources().getDisplayMetrics().density;
	return (int) (dipValue * scale + 0.5f);
}
public static int px2dip(Context context, float pxValue) {
	final float scale = context.getResources().getDisplayMetrics().density;
	return (int) (pxValue / scale + 0.5f);
}
```
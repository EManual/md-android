要实现一个自定义的View，通常都是重写一些系统框架在所有View上调用的基本方法，如大家都熟悉的onDraw(Convas)方法，没有必要重写View所有的方法。
下面我们一起对View的方法按照View的生命周期事件顺序进行分类，如表view-1所示： 
#### 分类-创建
1、构造方法
View中有两种类型的构造方法，一种是在代码中构建View，另一种是填充布局文件构建View，第二种构造方法要解析并应用布局文件中定义的任何属性。
2、onFinishInflate()
在来自于XML的View和它所有的子节点填充之后被调用。
#### 分类-布局
1、onMeasure
调用该方法来确定view及它所有子节点需要的尺寸。
2、onLayout
当view需要为它的所有子节点指定大小和布局时，此方法被调用。
3、onSizeChanged
当这个view的大小发生变化时，此方法被调用。
#### 分类-绘制
1、onDraw
当view渲染它的内容时被调用。
#### 分类-事件处理
1、onKeyDown
当一个新的按键事件发生时被调用。
2、onKeyUp
当一个按键按起时被调用。
3、onTrackballEvent
当轨迹球动作事件发生时被调用。
4、onTouchEvent
当触屏动作发生时被调用。
#### 分类-焦点
1、onFocusChanged
当view获得或是失去焦点时被调用。
2、onWindowFocusChanged
当包含view的窗口获得焦点或是失去焦点时被调用。
#### 分类-附着
1、onAttachedToWindow()
当view附着到窗口时被调用。
2、onDetachedFromWindow
当view脱离它的窗口时被调用。
3、onWindowVisibilityChanged
当包含此view的窗口的可见性发生变化时被调用。
#### ID
关于View的ID，不做过多说明，只是用于标记View。
#### 位置
View的几何形状是一个矩形，view的位置由矩形的左上角的x,y坐标及矩形的高度和宽度决定，他们的单位都是像素。
通过getLeft和getTop方法可以获得view相对于它的直接父节点的x，y坐标。
此外，还有一些便利的方法可以帮助我们减少计算量，比如getRight和getBottom方法，调用getRight等价于getLeft()+getWidth()方法，同理getBottom等价于getTop()+getHeight()方法。
#### 大小，padding，margin
一个View的大小由它的宽度和高度来表达，一个View实际上拥有两对宽高值。
第一对宽高值是：测量过的宽度和高度，即view想从它的父节点获得的宽高值，可以通过getMeasuredWidth()方法和getMeasuredHeight()方法获得。
第二对宽高值是：绘制的宽度和高度，即View的实际宽度和高度，是在布局之后，绘制View阶段的宽高值，这与上面提到的宽高值可能不同。通过getWidth()方法和getHeight()方法可以获得View的实际宽度和高度。
padding同css中的padding属性。通过setPadding(int,int,int,int)可以设置View的padding，通过getPaddingXXX()方法可以获得View中对应方向的padding，XXX值得是left，right，top，bottom。
view不提供对margin的支持，但是viewGroup提供支持。
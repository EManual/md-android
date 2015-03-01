#### 优化视图
通过前面的学习，现在该设计良好的View能够响应手势以及状态之间进行转换，除此之外你必须确保View运行的流畅快速。为了避免迟缓的UI效果或者运行的停顿，必须确保你的动画一直运行在每秒60帧。
####  越少越好
为了加速视图，从那些调用频繁的活动中减少不必要的代码。在OnDraw()方法中开始绘制，它会给你最大的任何内存分配都可能导致内存回收，这将会时分配内存。
另一方面需要减少onDraw()方法中的开销，只在需要时才调onDraw()方法。通常invalidate()方法会调用调用它的重载版本即带有参数的invalidate()方法而不是无参的invalidate()方法。该带参数的方法invalidate()能使draw过程更有效，以及减少对落在该矩形区域(参数指定的区域)外视图的不必要重绘。
注，invalidate()的三个重载版本为：
```  
1、public void invalidate(Rect dirty)
2、public void invalidate(int l, int t, int r, int b)
3、public void invalidate()
```
另外的一个高代价的操作是布局过程(layout)。任何时刻对View调用requestLayout()方法，Android UI框架都需要遍历整个View树，确定每个视图它们所占用的大小。如果在measure过程中有任何冲突，可能会多次遍历View树。UI设计人员有时为了实现某些效果，创建了较深层次的ViewGroup。但这些深层次View树会引发效率问题。确保你的View树层次尽可能浅。
如果你有的UI设计是复杂地，你应该考虑设计一个自定义ViewGroup来实现layout过程。不同于内置View控件，自定义View能够假定它的每个子View的大小以及形状，同时能够避免为每个子View进行measure过程。 PieChart展示了如何继承ViewGroup类。 PieChart带有子View，但它从来没有measure它们。相反，它根据自己的布局算法去直接设置每个子View的大小。
如下代码所示：
```  
/**
  * Custom view that shows a pie chart and, optionally, a label.
  */
public class PieChart extends ViewGroup {
	//
	// Measurement functions. This example uses a simple heuristic: it assumes
	// that
	// the pie chart should be at least as wide as its label.
	//
	@Override
	protected int getSuggestedMinimumWidth() {
		return (int) mTextWidth * 2;
	}
	@Override
	protected int getSuggestedMinimumHeight() {
		return (int) mTextWidth;
	}
	@Override
	protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
		// Try for a width based on our minimum
		int minw = getPaddingLeft() + getPaddingRight()
				+ getSuggestedMinimumWidth();
		int w = Math.max(minw, MeasureSpec.getSize(widthMeasureSpec));
		// Whatever the width ends up being, ask for a height that would let the
		// pie
		// get as big as it can
		int minh = (w - (int) mTextWidth) + getPaddingBottom()
				+ getPaddingTop();
		int h = Math.min(MeasureSpec.getSize(heightMeasureSpec), minh);
		setMeasuredDimension(w, h);
	}
	@Override
	protected void onLayout(boolean changed, int l, int t, int r, int b) {
		// Do nothing. Do not call the superclass method--that would start a
		// layout pass
		// on this view's children. PieChart lays out its children in
		// onSizeChanged().
	}
	@Override
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		super.onSizeChanged(w, h, oldw, oldh);
		// Set dimensions for text, pie chart, etc
		// Account for padding
		// Lay out the child view that actually draws the pie.
		mPieView.layout((int) mPieBounds.left, (int) mPieBounds.top,
				(int) mPieBounds.right, (int) mPieBounds.bottom);
		mPieView.setPivot(mPieBounds.width() / 2, mPieBounds.height() / 2);
		mPointerView.layout(0, 0, w, h);
		onDataChanged();
	}
} 
```
#### 使用硬件加速
Android 3.0版本后，Android 2D图形库能在大多数Android设备上使用GPU(图形处理单元)加速。GPU硬件加速可以极大的优化多数应用程序，但它并不是每个应用程序的最优选择。Android框架给予你是否在应用程序中使用硬件加速的控制力。
```  
<uses-sdk android:targetSdkVersion="11"/>
```
如何运用硬件加速</a>>>篇展示了如何在Application、Activity、Window级别中使用硬件加速。值得注意的是我们必须手动在配置文件中设置应用程序API级别为11或者更高级别，即在 AndroidManifest.xml进行如下配置：
一旦你开启了硬件加速，你可能看不到效率的提升。Mobile GPUs 善于处理特定的任务，例如：伸缩、旋转、平移图片。它也有一些不擅长处理的任务，例如：绘制直线或曲线。常言道物尽其用，扬长避短，尽可能让GPU处理它擅长的任务，减少让其处理弱势任务的。
在PieChart 示例中，例如，相对来说绘制一个圆形是比较耗费资源的。每次旋转引起的重绘导致UI的迟缓。解决办法就是让View来呈现该圆形，并且设置该View的layer type属性为 LAYER_TYPE_HARDWARE,因此GPU能够缓存静态图片。示例中该View作为 PieChart类的内部类存在，减少了为了实现这个方法的代码开销。
```  
private class PieView extends View {  
    public PieView(Context context) {  
        super(context);  
        if (!isInEditMode()) {  
            setLayerType(View.LAYER_TYPE_HARDWARE, null);
        }  
    }  
    @Override
    protected void onDraw(Canvas canvas) {  
        super.onDraw(canvas);  
  
		for (Item it : mData) {  
            mPiePaint.setShader(it.mShader);
            canvas.drawArc(mBounds,
                    360 - it.mEndAngle,
                    it.mEndAngle - it.mStartAngle,
                    true, mPiePaint);  
        }  
    }  
    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {  
        mBounds = new RectF(0, 0, w, h);
    }  
    RectF mBounds;
}
```
改变之后，只有View第一次显示的时候才会调用PieChart.PieView.onDraw()方法。在应用程序的其他时间，绘制的图像将会作为图片缓存，重绘时GPU将任意旋转图像。
然而这只是一个折中手段。缓存图片作为硬件层导致 video memory开销，video memory却是一种受限制的资源。 出于这个原因，在PieChart.PieView的最终版本上，只有在用户滑动时才设置它的layer type属性为LAYER_TYPE_HARDWARE。在其他时间，仅仅设置它的layer type属性为 LAYER_TYPE_HARDWARE，这允许GPU停止缓存图片。
最后，不要忘记分析你的代码。在一个View上做的优化技术可能会在其他View上产生不好的影响。
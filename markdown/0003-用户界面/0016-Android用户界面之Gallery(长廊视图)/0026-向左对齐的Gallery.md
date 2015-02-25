先上效果图：
![img](P)  
系统自带的Gallery选中的item总是在组件的中间。但是有些时候我们需要把选中的元素放在左边或者是Gallery一出来就要放在左边。
修改Gallery靠左对齐的思路：
1、Gellary总是对center进行锁定的，所以可以考虑修改它的center的位置，把center改成在left的位置就可以了。
Gallery中有个方法：
```  
[Tags]/**
[Tags]*@return The center of this Gallery.
[Tags]*/
private int getCenterOfGallery() {
	return (getWidth() - mPaddingLeft -mPaddingRight) / 2 + mPaddingLeft;
}
```
只可惜这个方法是private，所以没有办法重写。那就把源码拷贝出来进行修改吧，但是当你拷贝处理的时候会发现很多的变量是没有办法用的，这是因为：
a. @ViewDebug.ExportedProperty  
This annotation can be used to markfields and methods to be dumped by the view server. Only non-void methods withno arguments can be annotated by this annotation.
这个很好理解，不作翻译。
b. {@hide}
Views which have been hidden or removedwhich need to be animated on their way out. This field should be made private,so it is hidden from the SDK.
这也很好理解，自己翻译。
总结：Gallery的源码太过复杂所以这个方法只好放弃。
2、重写Gallery。这个问题的关键是如何重写，重写什么。
Gallery其实只是显示了数据，那么我们不必把数据移动位置而把摄像机移动位置不就可以了吗？正是这样完成了Gallery的重写。代码如下：
```  
import android.R.attr;
import android.content.Context;
import android.content.res.TypedArray;
import android.graphics.Camera;
import android.graphics.Matrix;
import android.util.AttributeSet;
import android.view.MotionEvent;
import android.view.View;
import android.view.animation.Transformation;
import android.widget.Gallery;
public class GalleryFlow extends Gallery {
	private Camera mCamera;
	private int mWidth;
	private int mPaddingLeft;
	private boolean flag;
	private static int firstChildWidth;
	private static int firstChildPaddingLeft;
	private int offsetX;
	public GalleryFlow(Context context) {
		super(context);
		mCamera = new Camera();
		this.setStaticTransformationsEnabled(true);
	}
	public GalleryFlow(Context context, AttributeSet attrs) {
		super(context, attrs);
		mCamera = new Camera();
		setAttributesValue(context, attrs);
		this.setStaticTransformationsEnabled(true);
	}
	public GalleryFlow(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		mCamera = new Camera();
		setAttributesValue(context, attrs);
		this.setStaticTransformationsEnabled(true);
	}
	private void setAttributesValue(Context context, AttributeSet attrs) {
		TypedArray typedArray = context.obtainStyledAttributes(attrs,
				new int[] { attr.paddingLeft });
		mPaddingLeft = typedArray.getDimensionPixelSize(0, 0);
		typedArray.recycle();
	}
	protected boolean getChildStaticTransformation(View child, Transformation t) {
		t.clear();
		t.setTransformationType(Transformation.TYPE_MATRIX);
		mCamera.save();
		final Matrix imageMatrix = t.getMatrix();
		if (flag) {
			firstChildWidth = getChildAt(0).getWidth();
			firstChildPaddingLeft = getChildAt(0).getPaddingLeft();
			flag = false;
		}
		offsetX = firstChildWidth / 2 + firstChildPaddingLeft + mPaddingLeft
				- mWidth / 2;
		mCamera.translate(offsetX, 0f, 0f);
		mCamera.getMatrix(imageMatrix);
		mCamera.restore();
		return true;
	}
	@Override
	public boolean onTouchEvent(MotionEvent event) {
		event.offsetLocation(-offsetX, 0);
		return super.onTouchEvent(event);
	}
	protected void onSizeChanged(int w, int h, int oldw, int oldh) {
		if (!flag) {
			mWidth = w * 2;
			getLayoutParams().width = mWidth;
			flag = true;
		}
		super.onSizeChanged(w, h, oldw, oldh);
	}
}
```
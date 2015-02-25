其他的动画机制的代码感兴趣的读者请自己阅读，希望通过原理的讲解以后看起来会轻松点，呵呵。以上就是Android的动画框架的原理，了解了原理对我们的开发来说就可以清晰的把握动画的每一帧是怎样生成的，这样便于开发和调试。它把动画的播放绘制交给父View去处理而不是让子View本身去绘制，这种从更高的层次上去控制的方式便于把动画机制做成一个易用的框架，如果用户要在某个 view 中使用动画，只需要在 xml 描述文件或代码中指定就可以了，从而把动画的实现和View本身内容的绘制（象TextView里面的文字显示）分离开了，起到了减少耦合和提高易用性的效果。
动画实现示例
在这个例子中，将要实现一个绕 Y 轴旋转的动画，这样可以看到 3D 透视投影的效果，代码如下 (清单 4)：
清单 3. 实现一个绕 Y 轴旋转的动画
```  
import android.view.animation.Animation;
import android.view.animation.Transformation;
import android.graphics.Camera;
import android.graphics.Matrix;
[Tags]/**
 [Tags]* An animation that rotates the view on the Y axis between two specified
 [Tags]* angles. This animation also adds a translation on the Z axis (depth) to
 [Tags]* improve the effect.
 [Tags]*/
public class Rotate3dAnimation extends Animation {
	private final float mFromDegrees;
	private final float mToDegrees;
	private final float mCenterX;
	private final float mCenterY;
	private final float mDepthZ;
	private final boolean mReverse;
	private Camera mCamera;
	[Tags]/**
	 [Tags]* Creates a new 3D rotation on the Y axis. The rotation is defined by its
	 [Tags]* start angle and its end angle. Both angles are in degrees. The rotation
	 [Tags]* is performed around a center point on the 2D space, definied by a pair of
	 [Tags]* X and Y coordinates, called centerX and centerY. When the animation
	 [Tags]* starts, a translation on the Z axis (depth) is performed. The length of
	 [Tags]* the translation can be specified, as well as whether the translation
	 [Tags]* should be reversed in time.
	 [Tags]* @param fromDegrees
	 [Tags]*            the start angle of the 3D rotation
	 [Tags]* @param toDegrees
	 [Tags]*            the end angle of the 3D rotation
	 [Tags]* @param centerX
	 [Tags]*            the X center of the 3D rotation
	 [Tags]* @param centerY
	 [Tags]*            the Y center of the 3D rotation
	 [Tags]* @param reverse
	 [Tags]*            true if the translation should be reversed, false otherwise
	 [Tags]*/
	public Rotate3dAnimation(float fromDegrees, float toDegrees, float centerX,
			float centerY, float depthZ, boolean reverse) {
		mFromDegrees = fromDegrees;
		mToDegrees = toDegrees;
		mCenterX = centerX;
		mCenterY = centerY;
		mDepthZ = depthZ;
		mReverse = reverse;
	}
	@Override
	public void initialize(int width, int height, int parentWidth,
			int parentHeight) {
		super.initialize(width, height, parentWidth, parentHeight);
		mCamera = new Camera();
	}
	@Override
	protected void applyTransformation(float interpolatedTime, Transformation t) {
		final float fromDegrees = mFromDegrees;
		float degrees = fromDegrees
				+ ((mToDegrees - fromDegrees) * interpolatedTime);
		final float centerX = mCenterX;
		final float centerY = mCenterY;
		final Camera camera = mCamera;
		final Matrix matrix = t.getMatrix();
		camera.save();
		if (mReverse) {
			camera.translate(0.0f, 0.0f, mDepthZ * interpolatedTime);
		} else {
			camera.translate(0.0f, 0.0f, mDepthZ * (1.0f - interpolatedTime));
		}
		camera.rotateY(degrees);
		camera.getMatrix(matrix);
		camera.restore();
		matrix.preTranslate(-centerX, -centerY);
		matrix.postTranslate(centerX, centerY);
	}
}
```
在这个例子中我们重载了applyTransformation函数，interpolatedTime就是getTransformation函数传下来的差值点，在这里做了一个线性插值算法来生成中间角度：float degrees = fromDegrees + ((mToDegrees - fromDegrees) * interpolatedTime);Camera类是用来实现绕Y轴旋转后透视投影的，我们只需要其返回的Matrix值,这个值会赋给Transformation中的矩阵成员，当ParentView去为ChildView设置画布时，就会用它来设置坐标系，这样ChildView画出来的效果就是一个绕Y轴旋转同时带有透视投影的效果。利用这个动画便可以作出像立体翻页等比较酷的效果。如何使用这个animation请见ApiDemos程序包com.example.android.apis.animation中的Transition3d.java 代码。
Android中显示Gif格式图
有关这一部分，本文将不做详细介绍。 感兴趣的读者请参看 Apidemos 中 com.example.android.apis.graphics 下面的 BitmapDecode.java 中的示例代码。
这里先简单说明一下，它的实现是通过Movie这个类来对Gif文件进行读取和解码的，同时在onDraw函数中不断的绘制每一帧图片完成的，这个示例代码在onDraw中调用invalidate来反复让View失效来让系统不断调用SampleView的onDraw函数；至于选出哪一帧图片进行绘制则是传入系统当前时间给Movie类，然后让它根据时间顺序来选出帧图片。反复让View失效的方式比较耗资源，绘制效果允许的话可以采取延时让View失效的方式来减小CPU消耗。
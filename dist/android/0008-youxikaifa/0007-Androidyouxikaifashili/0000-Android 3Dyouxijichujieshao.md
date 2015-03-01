首先开始介绍OpenGL的术语。 
#### 顶点Vertex 
顶点是3D空间中的一个点，也是许多对象的基础元素。在OpenGL中你可以生命少至二维坐标(X,Y)，多至四维(X,Y,Z,W). 
w轴是可选的，默认的值是1.0.Z轴也是可选的，默认为0.在这个系列中，我们将要用到3个主要的坐标X，Y，Z，因为W一般都是被用来作为占位符。vertex的复数是vertices（这对非英语母语的人来说比较重要，因为这容易产生歧义）。所有的对象都是用vertices作为它们的点，因为点就是vertex。
#### 三角形Triangle 
三角形需要三个点才能创建。因此在OpenGL中，我们使用3个顶点来创建一个三角形。
#### 多边形Polygon 
多边形是至少有3个连接着的点组成的一个对象。三角形也是一个多边形。
#### 图元Primitives 
一个Primitive是一个三维的对象，使用三角形或者多边形创建。形象的说，一个有50000个顶点的非常精细的模型是一个Primitive，同样一个只有500个顶点的低模也叫做一个Primitive。
现在我们可以开始变成了。
创建一个工程交Vortex，activity也是这个名字。我们的工程应该大概是这个样子的：
```  
import android.app.Activity;
import android.os.Bundle;
public class Vortex extends Activity {
	private static final String LOG_TAG = Vortex.class.getSimpleName();
	private VortexView _vortexView;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		_vortexView = new VortexView(this);
		setContentView(_vortexView);
	}
}
```
我们已经添加了View。让我们看一下VortexView类。
```  
import android.content.Context;
import android.opengl.GLSurfaceView;
public class VortexView extends GLSurfaceView {
	private static final String LOG_TAG = VortexView.class.getSimpleName();
	private VortexRenderer _renderer;
	public VortexView(Context context) {
		super(context);
		_renderer = new VortexRenderer();
		setRenderer(_renderer);
	}
}
```
我们继承了GLSurfaceView是因为它会帮助我们画3D图像。接下来看VortexRenderer类。一个Renderer包含画一帧所必需的所有东西。 引用自这儿references 。Renderer负责OpenGL call来render一个帧。
来看一下这个类:
```  
import javax.microedition.khronos.egl.EGLConfig;
import javax.microedition.khronos.opengles.GL10;
import android.opengl.GLSurfaceView;
public class VortexRenderer implements GLSurfaceView.Renderer {
	private static final String LOG_TAG = VortexRenderer.class.getSimpleName();
	private float _red = 0.9f;
	private float _green = 0.2f;
	private float _blue = 0.2f;
	@Override
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
	}
	@Override
	public void onSurfaceChanged(GL10 gl, int w, int h) {
		gl.glViewport(0, 0, w, h);
	}
	@Override
	public void onDrawFrame(GL10 gl) {
		// define the color we want to be displayed as the "clipping wall"
		gl.glClearColor(_red, _green, _blue, 1.0f);
		// clear the color buffer to show the ClearColor we called above...
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	}
}
```
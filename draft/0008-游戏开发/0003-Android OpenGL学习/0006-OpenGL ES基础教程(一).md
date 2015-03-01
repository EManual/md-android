一、设置OpenGL ES视图
设置OpenGL视图并不难，Android上也较简单。我们一般只需要2个步骤。
GLSurfaceView
我们要为GLSurfaceView提供一个专门用于渲染的接口
```  
public void setRenderer(GLSurfaceView.Renderer renderer)
```
GLSurfaceView.Renderer
GLSurfaceView.Renderer是一个通用渲染接口。我们必须实现下面的三个抽象方法：
```  
// 画面创建
public void onSurfaceCreated(GL10 gl, EGLConfig config)
// 画面绘制
public void onDrawFrame(GL10 gl)
// 画面改变
public void onSurfaceChanged(GL10 gl, int width, int height)
```
onSurfaceCreated
在这里我们主要进行一些初始化工作，比如对透视进行修正、设置清屏所用颜色等。
onDrawFrame
绘制当前画面
onSurfaceChanged
当设备水平或者垂直变化时调用此方法，设置新的显示比例
案例代码：
```  
public class OpenGLDemo extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) { 
		GLSurfaceView view = new GLSurfaceView(this);
		view.setRenderer(new OpenGLRenderer());
		setContentView(view);
	}
}
```
实现renderer需要更多的设置
```  
public void onSurfaceCreated(GL10 gl, EGLConfig config) {
		// 黑色背景
		gl.glClearColor(0.0f, 0.0f, 0.0f, 0.5f);
		// 启用阴影平滑（不是必须的）
		gl.glShadeModel(GL10.GL_SMOOTH);
		// 设置深度缓存
		gl.glClearDepthf(1.0f);
		// 启用深度测试
		gl.glEnable(GL10.GL_DEPTH_TEST);
		// 所作深度测试的类型
		gl.glDepthFunc(GL10.GL_LEQUAL);
		// 对透视进行修正
		gl.glHint(GL10.GL_PERSPECTIVE_CORRECTION_HINT, GL10.GL_NICEST);
	}
	public void onDrawFrame(GL10 gl) {
		// 清除屏幕和深度缓存
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
	}
	public void onSurfaceChanged(GL10 gl, int width, int height) {
		// 设置画面的大小
		gl.glViewport(0, 0, width, height);
		// 设置投影矩阵
		gl.glMatrixMode(GL10.GL_PROJECTION);
		// 重置投影矩阵
		gl.glLoadIdentity();
		// 设置画面比例
		GLU.gluPerspective(gl, 45.0f, (float) width / (float) height, 0.1f,100.0f);
		// 选择模型观察矩阵
		gl.glMatrixMode(GL10.GL_MODELVIEW);
		// 重置模型观察矩阵
		gl.glLoadIdentity();
	}
}
```
只要加入这段代码到OpenGLDemo class里就可实现全屏
```  
this.requestWindowFeature(Window.FEATURE_NO_TITLE);
getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN,
WindowManager.LayoutParams.FLAG_FULLSCREEN);
```
设置完视图后，即可编译运行，可以看到一个“漂亮”的黑屏：
![img](P)  
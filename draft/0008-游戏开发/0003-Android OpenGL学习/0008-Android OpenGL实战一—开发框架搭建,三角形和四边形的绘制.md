说起来很惭愧，一直没有接触过OpenGL，以前从来没有这方面的需求，今天决定学习一下！
OpenGL(Open Graphics Library)定义了一个跨编程语言、跨平台的编程接口的规格，是一个性能卓越的三维图形标准！
#### OpenGL ES与OpenGL的区别：
OpenGL ES是专为内嵌和移动设备设计的一个2D/3D轻量级图形库，它基于OpenGL API设计，是OpenGL三维图形API的子集
Android里有三个与OpenGL有关的包：
```  
android.opengl
javax.microedition.khronos.egl
javax.microedition.khronos.opengles
```
今天用到的不多，只有几个类而已
首先，写一个类实现Renderer接口，并实现它的三个抽象方法，要吃饭了，直接上代码吧
```  
import java.nio.IntBuffer;
import javax.microedition.khronos.egl.EGLConfig;
import javax.microedition.khronos.opengles.GL10;
import android.opengl.GLSurfaceView.Renderer;
public class GLReader implements Renderer {
	private int one = 0x10000;
	// 三角形的三个顶点
	private IntBuffer triggerBuffer = IntBuffer.wrap(new int[] { 0, one, 0,
			// 上顶点
			-one, -one, 0,
			// 做顶点
			one, -one, 0
	// 右顶点
			});
	// 四边形的四个顶点
	private IntBuffer quaterBuffer = IntBuffer.wrap(new int[] {
	one, one, 0,
	-one, one, 0,
	one, -one, 0,
	-one, -one, 0 });
	 /**
	  * 
	  * 所有绘图的工作放到此方法里
	  */
	@Override
	public void onDrawFrame(GL10 gl) {
		// 清除屏幕和深度缓存
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
		// 重置当前的模型观察矩阵
		gl.glLoadIdentity();
		// 左移 1.5 单位，并移入屏幕 6.0
		gl.glTranslatef(-1.5f, 0.0f, -6.0f);
		// 开启顶点设置功能
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		 /**
		  * 设置顶点数据
		  * 参数：
		  * 1、顶点尺寸----这里使用的是xyz坐标系，所以是3
		  * 2、顶点类型----这里是固定的，所以用GL_FIXED
		  * 3、步长
		  * 4、顶点缓存----即顶点数组
		  */
		gl.glVertexPointer(3, GL10.GL_FIXED, 0, triggerBuffer);
		 /**
		  * 绘制顶点
		  * 参数：
		  * 1、绘制的模式----我们使用GL_TRIANGLES来表示绘制三角形
		  * 2、开始位置
		  * 3、要绘制的顶点计数
		  */
		gl.glDrawArrays(GL10.GL_TRIANGLES, 0, 3);
		// 重置当前的模型观察矩阵
		gl.glLoadIdentity();
		// 右移1.5，进入6.0
		gl.glTranslatef(1.5f, 0.0f, -6.0f);
		// 设置四边形顶点数据
		gl.glVertexPointer(3, GL10.GL_FIXED, 0, quaterBuffer);
		// 绘制顶点（注意参数一不同）
		gl.glDrawArrays(GL10.GL_TRIANGLE_STRIP, 0, 4);
		// 取消顶点设置
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
	}
	 /**
	  * 当窗口大小发生改变是调用此方法
	  * 不管窗口大小是否已经改变，此方法至少执行一次
	  * 所以在此方法中设置OpenGL场景的大小
	  */
	@Override
	public void onSurfaceChanged(GL10 gl, int width, int height) {
		float ratio = (float) width / height;
		// 设置OpenGL场景大小为屏幕大小
		gl.glViewport(0, 0, width, height);
		/* 为屏幕设置透视图 */
		// 设置投影矩阵
		gl.glMatrixMode(GL10.GL_PROJECTION);
		// 重置投影矩阵
		gl.glLoadIdentity();
		// 设置视口大小
		gl.glFrustumf(-ratio, ratio, -1, 1, 1, 10);
		// 选择模型观察矩阵
		gl.glMatrixMode(GL10.GL_MODELVIEW);
		// 重置模型观察矩阵
		gl.glLoadIdentity();
	}
	 /**
	  * 窗口创建时调用此方法
	  * 此方法内做初始化的操作
	  */
	@Override
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
		// 对透视进行修正
		gl.glHint(GL10.GL_PERSPECTIVE_CORRECTION_HINT, GL10.GL_FASTEST);
		// 背景：黑色
		gl.glClearColor(0, 0, 0, 0);
		// 启动阴影平滑
		gl.glShadeModel(GL10.GL_SMOOTH);
		// 设置深度缓存
		gl.glClearDepthf(1.0f);
		// 启用深度测试
		gl.glEnable(GL10.GL_DEPTH_TEST);
		// 所做深度测试的类型
		gl.glDepthFunc(GL10.GL_LEQUAL);
	}
}
```
代码注释写的很清楚，值得一提的是在onSurfaceChanged()里
```     
// 设置视口的大小
gl.glFrustumf(-ratio, ratio, -1, 1, 1, 10);
// 选择模型观察矩阵
gl.glMatrixMode(GL10.GL_MODELVIEW);
```
顺序不能写反，否则会出问题，至于什么问题，自己试下就知道了，我也是偶然发现的
```       
import android.app.Activity;
import android.opengl.GLSurfaceView;
import android.os.Bundle;
public class MainActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		// setContentView(R.layout.main);
		GLReader reader = new GLReader();
		GLSurfaceView glview = new GLSurfaceView(this);
		glview.setRenderer(reader);
		setContentView(glview);

	}
}
```
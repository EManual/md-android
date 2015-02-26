顶点数组与绘制
```  
import java.nio.ByteBuffer;
import java.nio.ByteOrder;
import java.nio.FloatBuffer;
import javax.microedition.khronos.egl.EGLConfig;
import javax.microedition.khronos.opengles.GL10;
import Android.app.Activity;
import Android.opengl.GLSurfaceView;
import Android.opengl.GLU;
import Android.os.Bundle;
public class DrawDot extends Activity implements GLSurfaceView.Renderer {
	 /** Called when the activity is first created. */
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		GLSurfaceView myView = new GLSurfaceView(this);
		myView.setRenderer(this);
		setContentView(myView);
	}
	public void onDrawFrame(GL10 gl) {
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
		// 设定背景颜色 此处为黑
		gl.glClearColor(0.0f, 0.0f, 0.0f, 0.0f);
		// 重设视图模型变换 ， 用于观测创建的物体
		gl.glLoadIdentity();
		gl.glTranslatef(0.0f, 0.0f, -5.0f);
		// 开始绘制
		Dot myDot = new Dot();
		myDot.draw(gl);
	}
	public void onSurfaceChanged(GL10 gl, int width, int height) {
		// 设置坐标系
		gl.glViewport(0, 0, width, height);
		// 设置投影变换
		gl.glMatrixMode(GL10.GL_PROJECTION);
		gl.glLoadIdentity();
		// Calculate The Aspect Ratio Of The Window
		GLU.gluPerspective(gl, 0f, (float) width / (float) height, 0.1f, 100.0f);
		gl.glMatrixMode(GL10.GL_MODELVIEW);
		// 设定模型视图矩阵
		gl.glLoadIdentity();
	}
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
		// 第一次创建也会调用onSurfaceChanged函数、
	}
}
class Dot {
	private FloatBuffer vertexBuffer;
	// 点的坐标（x, y, z)；
	private float vertices[] = { 0.5f, 0.5f, 0.0f, -0.5f, 0.5f, 0.0f, 0.5f,
			-0.5f, 0.0f, -0.5f, -0.5f, 0.0f, };

	// 准备顶点数据
	private void init() {
		ByteBuffer vertexByteBuffer = ByteBuffer
				.allocateDirect(vertices.length * 4);
		vertexByteBuffer.order(ByteOrder.nativeOrder());
		vertexBuffer = vertexByteBuffer.asFloatBuffer();
		vertexBuffer.put(vertices);
		vertexBuffer.position(0);
	}
	public Dot(){ 66.init(); 
}	public void draw(GL10 gl) {
		// 开始数组功能 GL_VERTEX_ARRAY
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		// 设定颜色值 此处为红 (red, green, blue, alpha)~[0.0f-1.0f]
		gl.glColor4f(1.0f, 0.0f, 0.0f, 1.0f);
		// 指向数组数据
		gl.glVertexPointer(3, GL10.GL_FLOAT, 0, vertexBuffer);
		// 设置点的大小 为 5 像素
		gl.glPointSize(5);
		// 绘制点 GL_POINTS ； vertices.length/3 点的个数
		gl.glDrawArrays(GL10.GL_POINTS, 0, vertices.length / 3);
		// 关闭数组功能 GL_VERTEX_ARRAY
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
	}
}
```
![img](P)  
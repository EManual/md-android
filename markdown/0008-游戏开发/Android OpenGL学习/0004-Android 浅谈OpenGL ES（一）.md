Android用的是OpenGL图形函数库，它可以跨平台。
OpenGL的定义：OpenGL（Open Graphics Library）是个定义了一个跨编程语言、跨平台的编程接口的规格，它用于三维图象（二维的亦可）。OpenGL是个专业的图形程序接口，是一个功能强大，调用方便的底层图形库。
Android所用到的是它的一个子集OpenGL ES（OpenGL for Embedded Systems（嵌入式系统））就是OpenGL的嵌入式版本。
```  
import android.app.Activity;
import android.os.Bundle;
public class OpenGL extends Activity {
	private VortexView vortexView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		vortexView = new VortexView(this);
		setContentView(vortexView);
	}
	@Override
	protected void onPause() {
		super.onPause();
		vortexView.onPause();
	}
	@Override
	protected void onResume() {
		super.onResume();
		vortexView.onResume();
	}
}
```
继承GLSurfaceView类的自己的View 
```  
import android.content.Context;
import android.opengl.GLSurfaceView;
import android.view.MotionEvent;
public class VortexView extends GLSurfaceView {
	private static final String LOG_TAG = VortexView.class.getSimpleName();
	private VortexRenderer renderer;
	public VortexView(Context context) {
		super(context);
		renderer = new VortexRenderer();
		setRenderer(renderer);
	}
	@Override
	public boolean onTouchEvent(final MotionEvent event) {
		queueEvent(new Runnable() {
			@Override
			public void run() {
				renderer.setColor(event.getX() / getWidth(), event.getY()
						/ getHeight(), 1.0f);
				renderer.setAngle(event.getX() / 10);
			}
		});
		return super.onTouchEvent(event);
	}
}
```
实现渲染器renderer类 
```  
import java.nio.ByteBuffer;
import java.nio.ByteOrder;
import java.nio.FloatBuffer;
import java.nio.ShortBuffer;
import javax.microedition.khronos.egl.EGLConfig;
import javax.microedition.khronos.opengles.GL10;
import android.opengl.GLSurfaceView;
public class VortexRenderer implements GLSurfaceView.Renderer {
	private static final String TAG = VortexRenderer.class.getSimpleName();
	private float red = 0f;
	private float green = 0f;
	private float blue = 0f;
	private ShortBuffer indexBuffer;
	private FloatBuffer vertexBuffer;
	private short[] indicesArray = { 0, 1, 2 };
	private int numberOfVertices = 3;
	private float angle;
	private void initTriangle() {
		ByteBuffer vbb = ByteBuffer.allocateDirect(numberOfVertices * 3 * 4);
		vbb.order(ByteOrder.nativeOrder());
		vertexBuffer = vbb.asFloatBuffer();
		ByteBuffer ibb = ByteBuffer.allocateDirect(numberOfVertices * 2);
		ibb.order(ByteOrder.nativeOrder());
		indexBuffer = ibb.asShortBuffer();
		float[] coords = { -0.5f, -0.5f, 0f, 0.5f, -0.5f, 0f, 0f, 0.5f, 0f };
		vertexBuffer.put(coords);
		indexBuffer.put(indicesArray);
		vertexBuffer.position(0);
		indexBuffer.position(0);
	}
	public void setColor(float red, float green, float blue) {
		this.red = red;
		this.green = green;
		this.blue = blue;
	}
	public void setAngle(float angle) {
		this.angle = angle;
	}
	@Override
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		initTriangle();
	}
	@Override
	public void onSurfaceChanged(GL10 gl, int width, int height) {
		gl.glViewport(0, 0, width, height);
	}
	@Override
	public void onDrawFrame(GL10 gl) {
		gl.glClearColor(red, green, blue, 1.0f);
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
		gl.glRotatef(angle, 0f, 1f, 0f);
		gl.glColor4f(0.5f, 0f, 0f, 0.5f);
		gl.glVertexPointer(3, GL10.GL_FLOAT, 0, vertexBuffer);
		gl.glDrawElements(GL10.GL_TRIANGLES, numberOfVertices,
				GL10.GL_UNSIGNED_SHORT, indexBuffer);
	}
}
```
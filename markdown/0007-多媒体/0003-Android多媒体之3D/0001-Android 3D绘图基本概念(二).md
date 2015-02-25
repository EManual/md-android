[clor=blue]Render (渲染）
我们已定义好了多边形，下面就要了解如和使用OpenGL ES的API来绘制（渲染）这个多边形了。OpenGL ES提供了两类方法来绘制一个空间几何图形：
```  
public abstract void glDrawArrays(int mode, int first, int count) 
```
使用VetexBuffer来绘制，顶点的顺序由vertexBuffer中的顺序指定。
```  
public abstract void glDrawElements(int mode, int count, int type, Buffer indices) 
```
可以重新定义顶点的顺序，顶点的顺序由indices Buffer指定。
前面我们已定义里顶点数组，因此我们将采用glDrawElements来绘制多边形。
同样的顶点，可以定义的几何图形可以有所不同，比如三个顶点，可以代表三个独立的点，也可以表示一个三角形，这就需要使用mode来指明所需绘制的几何图形的基本类型。
```  
import java.nio.ByteBuffer;
import java.nio.ByteOrder;
import java.nio.FloatBuffer;
import java.nio.ShortBuffer;
import javax.microedition.khronos.opengles.GL10;
public class Square {
	// Our vertices.
	private float vertices[] = { -1.0f, 1.0f, 0.0f, // 0, Top Left
			-1.0f, -1.0f, 0.0f, // 1, Bottom Left
			1.0f, -1.0f, 0.0f, // 2, Bottom Right
			1.0f, 1.0f, 0.0f, // 3, Top Right
	};
	// The order we like to connect them.
	private short[] indices = { 0, 1, 2, 0, 2, 3 };
	// Our vertex buffer.
	private FloatBuffer vertexBuffer;
	// Our index buffer.
	private ShortBuffer indexBuffer;
	public Square() {
		// a float is 4 bytes, therefore we
		// multiply the number if
		// vertices with 4.
		ByteBuffer vbb = ByteBuffer.allocateDirect(vertices.length * 4);
		vbb.order(ByteOrder.nativeOrder());
		vertexBuffer = vbb.asFloatBuffer();
		vertexBuffer.put(vertices);
		vertexBuffer.position(0);
		// short is 2 bytes, therefore we multiply
		// the number if
		// vertices with 2.
		ByteBuffer ibb = ByteBuffer.allocateDirect(indices.length * 2);
		ibb.order(ByteOrder.nativeOrder());
		indexBuffer = ibb.asShortBuffer();
		indexBuffer.put(indices);
		indexBuffer.position(0);
	}
	[Tags]/**
	 [Tags]* This function draws our square on screen.
	 [Tags]* @param gl
	 [Tags]*/
	public void draw(GL10 gl) {
		// Counter-clockwise winding.
		gl.glFrontFace(GL10.GL_CCW);
		// Enable face culling.
		gl.glEnable(GL10.GL_CULL_FACE);
		// What faces to remove with the face culling.
		gl.glCullFace(GL10.GL_BACK);
		// Enabled the vertices buffer for writing
		// and to be used during
		// rendering.
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		// Specifies the location and data format of
		// an array of vertex
		// coordinates to use when rendering.
		gl.glVertexPointer(3, GL10.GL_FLOAT, 0, vertexBuffer);
		gl.glDrawElements(GL10.GL_TRIANGLES, indices.length,
				GL10.GL_UNSIGNED_SHORT, indexBuffer);
		// Disable the vertices buffer.
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
		// Disable face culling.
		gl.glDisable(GL10.GL_CULL_FACE);
	}
}
```
在OpenGLRenderer 中添加Square成员变量并初始化：
```  
// Initialize our square.
Square square = new Square(); 
```
并在public void onDrawFrame(GL10 gl) 添加
```  
// Draw our square.
square.draw(gl);
```
来绘制这个正方形，编译运行，什么也没显示，这是为什么呢？这是因为OpenGL ES从当前位置开始渲染，缺省坐标为(0,0,0)，和View port 的坐标一样，相当于把画面放在眼前，对应这种情况OpenGL不会渲染离view Port很近的画面，因此我们需要将画面向后退一点距离：
```  
// Translates 4 units into the screen.
gl.glTranslatef(0, 0, -4);
```
在编译运行，这次倒是有显示了，当正方形迅速后移直至看不见，这是因为每次调用onDrawFrame时，每次都再向后移动4个单位，需要加上重置Matrix的代码。
```  
// Replace the current matrix with the identity matrix
gl.glLoadIdentity();
```
最终onDrawFrame的代码如下：
```  
public void onDrawFrame(GL10 gl) {
	// Clears the screen and depth buffer.
	gl.glClear(GL10.GL_COLOR_BUFFER_BIT |
	GL10.GL_DEPTH_BUFFER_BIT);
	gl.glLoadIdentity();
	gl.glTranslatef(0, 0, -4);
	// Draw our square.
	square.draw(gl); // ( NEW )
} 
```
绘制图形
```  
class Shape {
	private FloatBuffer vertexBuffer;
	// 点的坐标（x, y, z)；
	private float vertices1[] = { -0.5f, 0.3f, 0.0f, 0.0f, 0.0f, 0.0f, -0.5f,
			0.8f, 0.0f, 0.5f, 0.3f, 0.0f, 0.5f, 0.8f, 0.0f, 0.0f, 1.0f, 0.0f, };
	// 准备顶点数据
	private void init() {
		ByteBuffer vertexByteBuffer = ByteBuffer
				.allocateDirect(vertices1.length * 4);
		vertexByteBuffer.order(ByteOrder.nativeOrder());
		vertexBuffer = vertexByteBuffer.asFloatBuffer();
		vertexBuffer.put(vertices1);
		vertexBuffer.position(0);
	}
	public Shape() {
		init();
	}
	public void draw(GL10 gl) {
		// 开始数组功能 GL_VERTEX_ARRAY
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		gl.glColor4f(1.0f, 0.0f, 0.0f, 1.0f);
		gl.glVertexPointer(3, GL10.GL_FLOAT, 0, vertexBuffer);
		// 绘制三角形 ； GL10.GL_TRIANGLES 还有其他模式 修改顶点坐标观察结果
		gl.glDrawArrays(GL10.GL_TRIANGLES, 0, vertices1.length / 3);
		// gl.glDrawArrays(GL10.GL_TRIANGLE_STRIP, 0, vertices1.length / 3);
		// gl.glDrawArrays(GL10.GL_TRIANGLE_FAN, 0, vertices1.length / 3);
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
	}
}
```
![img](P)  
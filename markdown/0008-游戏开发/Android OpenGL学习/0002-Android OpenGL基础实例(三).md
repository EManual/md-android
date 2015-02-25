绘制直线
```  
class Line { 
	private FloatBuffer vertexBuffer;
	// 6 lines and 12 vertices
	private float vertices[] = { 1.0f, 0.0f, 0.0f, -1.0f, 0.0f, 0.0f, 1.0f,
			0.4f, 0.0f, -1.0f, 0.4f, 0.0f, 1.0f, -0.4f, 0.0f, -1.0f, -0.4f,
			0.0f, 1.0f, 0.8f, 0.0f, -1.0f, 0.8f, 0.0f, 1.0f, -0.8f, 0.0f,
			-1.0f, -0.8f, 0.0f, 1.0f, 1.2f, 0.0f, -1.0f, 1.2f, 0.0f };
	private byte line1[] = { 0, 1 };
	private byte line2[] = { 2, 3 };
	private byte line3[] = { 4, 5, 9, 8 };
	private byte line4[] = { 8, 9 };
	private byte line5[] = { 10, 11 };
	private void init() {
		ByteBuffer vertexByteBuffer = ByteBuffer
				.allocateDirect(vertices.length * 4);
		vertexByteBuffer.order(ByteOrder.nativeOrder());
		vertexBuffer = vertexByteBuffer.asFloatBuffer();
		vertexBuffer.put(vertices);
		vertexBuffer.position(0);
	}
	public Line() {
		init();
	}
	public void draw(GL10 gl) {
		// 开始数组功能 GL_VERTEX_ARRAY
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		// 设定颜色值 此处为红 (red, green, blue, alpha)~[0.0f-1.0f]
		gl.glColor4f(1.0f, 0.0f, 0.0f, 1.0f);
		// 指向数组数据
		gl.glVertexPointer(3, GL10.GL_FLOAT, 0, vertexBuffer);
		// 实际绘制 6 条线
		drawlines(gl);

		// 关闭数组功能 GL_VERTEX_ARRAY
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
	}
	private void drawlines(GL10 gl) {
		// 设置直线的宽度
		gl.glLineWidth(1.0f);
		gl.glDrawElements(GL10.GL_LINE_LOOP, 2, GL10.GL_UNSIGNED_BYTE,
				ByteBuffer.wrap(line1));
		gl.glLineWidth(5.0f);
		gl.glDrawElements(GL10.GL_LINE_STRIP, 2, GL10.GL_UNSIGNED_BYTE,
				ByteBuffer.wrap(line2));
		gl.glDrawElements(GL10.GL_LINE_LOOP, 4, GL10.GL_UNSIGNED_BYTE,
				ByteBuffer.wrap(line3));
	}
}
keycodes： 
//直线包含的点 
private byte line1[]={0, 1}; 
private byte line2[]={2, 3}; 
private byte line3[]={4, 5, 9, 8}; 
private byte line4[]={8, 9}; 
private byte line5[]={10, 11}; 
gl.glLineWidth(1.0f);
gl.glDrawElements(GL10.GL_LINE_LOOP, 2, GL10.GL_UNSIGNED_BYTE, ByteBuffer.wrap(line1)); gl.glLineWidth(5.0f);
gl.glDrawElements(GL10.GL_LINE_STRIP, 2, GL10.GL_UNSIGNED_BYTE, ByteBuffer.wrap(line2)); gl.glDrawElements(GL10.GL_LINE_LOOP, 4, GL10.GL_UNSIGNED_BYTE, ByteBuffer.wrap(line3));
```
![img](P)  
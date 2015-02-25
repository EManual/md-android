让我们从新的对象变量开始。_vertexBuffer为我们的三角形保存坐标。_indexBuffer保存索引。_nrOfVertices变量定义需要多少个顶点.对于一个三角形来说,一共需要三个顶点。
这个方法首先为这里两个buffer分配必须的内存. 接下来我们定义一些坐标后面的注释对用途给予了说明.
我们将coords数组填充给_vertexBuffer . 同样在31行将indices数组填充给_indexBuffer 。最后将两个buffer都设置position为0.
为了防止每次都对三角形进行初始化，我们仅仅在onDrawFrame()之前的行数调用它一次。一个比较好的选择就是在onSurfaceCreated()函数中。
```  
@Override
public void onSurfaceCreated(GL10 gl, EGLConfig config) {
	// preparation
	gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
	initTriangle();
}
```
glEnableClientState()设置OpenGL使用vertex数组来画。这是很重要的，因为如果不这么设置OpenGL不知道如何处理我们的数据。接下来我们就要初始化我们的三角形。
为什么我们不需使用不同的buffer？ 在新的onDrawFrame()方法中我们必须添加一些新的OpenGL调用。
```  
@Override
public void onDrawFrame(GL10 gl) {
	// define the color we want to be displayed as the "clipping wall"
	gl.glClearColor(_red, _green, _blue, 1.0f);
	// clear the color buffer to show the ClearColor we called above...
	gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	// set the color of our element
	gl.glColor4f(0.5f, 0f, 0f, 0.5f);
	// define the vertices we want to draw
	gl.glVertexPointer(3, GL10.GL_FLOAT, 0, _vertexBuffer);
	// finally draw the vertices
	gl.glDrawElements(GL10.GL_TRIANGLES, _nrOfVertices,
			GL10.GL_UNSIGNED_SHORT, _indexBuffer);
}
```
glClearColor() 和 glClear() 在教程I部分已经提到过。在第10行使用glColor4f(red, green, blue, alpha)设置三角形为暗红色。
我们使用glVertexPointer()初始化Vertex Pointer. 第一个参数是大小，也是顶点的维数。我们使用的是x,y,z三维坐标。第二个参数，GL_FLOAT定义buffer中使用的数据类型。第三个变量是0，是因为我们的坐标是在数组中紧凑的排列的，没有使用offset。最后哦胡第四个参数顶点缓冲。
最后，glDrawElements()将所有这些元素画出来。第一个参数定义了什么样的图元将被画出来。第二个参数定义有多少个元素，第三个是indices使用的数据类型。最后一个是绘制顶点使用的索引缓冲。
当最后测试这个应用的使用，你会看到一个在屏幕中间静止的三角形。当你点击屏幕的时候，屏幕的背景颜色还是会改变。
现在往里面添加对三角形的旋转。下面的代码是写在VortexRenderer类中的.
```  
private float _angle;
public void setAngle(float angle) {
	_angle = angle;
}
```
glRotatef()方法在glColor4f()之前被onDrawFrame()调用.
```  
@Override
public void onDrawFrame(GL10 gl) {
	// set rotation
	gl.glRotatef(_angle, 0f, 1f, 0f);
	gl.glColor4f(0.5f, 0f, 0f, 0.5f);
	// code snipped
}
```
首先我们实现了GLSurfaceView.Renderer这个接口，主要是实现3个方法：onSurfaceCreated(),onSurfaceChanged()和onDrawFrame()。这些方法很容易理解，第一个在surface创建以后调用，第二个是在surface发生改变以后调用，例如从竖屏切换到横屏的时候，最后一个方法是当任何时候调用一个画图方法的时候。
我们通过glClearColor()方法为底色定义了颜色。底色是在我们能看到的所有东西的后面，所以所有在底色后面的东西都是不可见的。可以想象这种东西为浓雾，挡住了所有的东西。然后我们将要为之设置距离来show一下它怎么用的。那时候你就一定会明白它是怎么存在的了。
为了让颜色变化可见，我们必须调用glClear()以及颜色缓冲的Mask来清空buffer，然后为我们的底色使用新的底色。 
为了能看到它在起作用，我们这里为MotionEvent创建一个response，使用它来改变颜色。首先在VortexRenderer中来创建一个设置颜色的函数。
```  
public void setColor(float r, float g, float b) {
	_red = r;
	_green = g;
	_blue = b;
}
```
下面是VortexView类中创建的方法来处理MotionEvent。
```  
public boolean onTouchEvent(final MotionEvent event) {
	queueEvent(new Runnable() {
		public void run() {
			_renderer.setColor(event.getX() / getWidth(), event.getY()
					/ getHeight(), 1.0f);
		}
	});
	return true;
}
```
这个系列的第二部分是关于如何添加一个三角形并可以旋转它。
第一件事情是初始化需要显示的三角形。我们来在VortexRenderer类中添加一个方法initTriangle()。
```  
// new object variables we need
// a raw buffer to hold indices
private ShortBuffer _indexBuffer;
// a raw buffer to hold the vertices
private FloatBuffer _vertexBuffer;
private short[] _indicesArray = { 0, 1, 2 };
private int _nrOfVertices = 3;
// code snipped
private void initTriangle() {
	// float has 4 bytes
	ByteBuffer vbb = ByteBuffer.allocateDirect(_nrOfVertices * 3 * 4);
	vbb.order(ByteOrder.nativeOrder());
	_vertexBuffer = vbb.asFloatBuffer();
	// short has 2 bytes
	ByteBuffer ibb = ByteBuffer.allocateDirect(_nrOfVertices * 2);
	ibb.order(ByteOrder.nativeOrder());
	_indexBuffer = ibb.asShortBuffer();
	float[] coords = { -0.5f, -0.5f, 0f, // (x1, y1, z1)
			0.5f, -0.5f, 0f, // (x2, y2, z2)
			0f, 0.5f, 0f // (x3, y3, z3)
	};
	_vertexBuffer.put(coords);
	_indexBuffer.put(_indicesArray);
	_vertexBuffer.position(0);
	_indexBuffer.position(0);
}
```
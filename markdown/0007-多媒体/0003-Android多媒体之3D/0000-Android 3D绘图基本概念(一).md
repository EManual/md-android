前面介绍了使用Android 编写OpenGL ES应用的程序框架，本篇介绍3D绘图的一些基本构成要素，最终将实现一个多边形的绘制。
一个3D图形通常是由一些小的基本元素（顶点，边，面，多边形）构成，每个基本元素都可以单独来操作。
#### Vertex （顶点）
顶点是3D建模时用到的最小构成元素，顶点定义为两条或是多条边交会的地方。在3D模型中一个顶点可以为多条边，面或是多边形所共享。一个顶点也可以代表一个点光源或是Camera的位置。下图中标识为黄色的点为一个顶点(Vertex)。

![img](http://emanual.github.io/md-android/img/media_3d/01_3d.jpg)

在Android系统中可以使用一个浮点数数组来定义一个顶点，浮点数数组通常放在一个Buffer（java.nio)中来提高性能。
比如：下图中定义了四个顶点和对应的Android 顶点定义：
```  
private float vertices[] = { 
	-1.0f, 1.0f, 0.0f, // 0, Top Left
	-1.0f, -1.0f, 0.0f, // 1, Bottom Left
	1.0f, -1.0f, 0.0f, // 2, Bottom Right
	1.0f, 1.0f, 0.0f, // 3, Top Right
};
```
为了提高性能，通常将这些数组存放到java.io 中定义的Buffer类中：
```  
// a float is 4 bytes, therefore we multiply the
// number if vertices with 4.
ByteBuffer vbb = ByteBuffer.allocateDirect(vertices.length * 4);
vbb.order(ByteOrder.nativeOrder());
FloatBuffer vertexBuffer = vbb.asFloatBuffer();
vertexBuffer.put(vertices);
vertexBuffer.position(0);
```
有了顶点的定义，下面一步就是如何将它们传给OpenGL ES库，OpenGL ES提供一个成为”管道Pipeline”的机制，这个管道定义了一些“开关”来控制OpenGL ES支持的某些功能，缺省情况这些功能是关闭的，如果需要使用OpenGL ES的这些功能，需要明确告知OpenGL“管道”打开所需功能。
因此对于我们的这个示例，需要告诉OpenGL库打开Vertex buffer以便传入顶点坐标Buffer。要注意的使用完某个功能之后，要关闭这个功能以免影响后续操作：
```  
// Enabled the vertex buffer for writing and to be used during rendering.
gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);// OpenGL docs.
// Specifies the location and data format of an array of vertex
// coordinates to use when rendering.
gl.glVertexPointer(3, GL10.GL_FLOAT, 0, vertexBuffer); // OpenGL docs.
When you are done with the buffer don't forget to disable it.
// Disable the vertices buffer.
gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);// OpenGL docs.
```
#### Edge（边）
边定义为两个顶点之间的线段。边是面和多边形的边界线。在3D模型中，边可以被相邻的两个面或是多边形形共享。对一个边做变换将影响边相接的所有顶点，面或多边形。在OpenGL中，通常无需直接来定义一个边，而是通过顶点定义一个面，从而由面定义了其所对应的三条边。可以通过修改边的两个顶点来更改一条边。
#### Face (面）
在OpenGL ES中，面特指一个三角形，由三个顶点和三条边构成，对一个面所做的变化影响到连接面的所有顶点和边，面多边形。
#### Polygon （多边形）
多边形由多个面（三角形）拼接而成，在三维空间上，多边形并一定表示这个Polygon在同一平面上。这里我们使用缺省的逆时针方向代表面的“前面Front)。
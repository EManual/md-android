我们在上次的实例中进行了简单的步骤 ，下面通过opengl 来画个方形。
有两种观察事物的方式：
一、正交投影或者叫平行投影，就是远处的物体和近处的物体看起来一样大。
二、透视投影，人类眼睛正常观察的景象，及越远的物体看起来越小。
```  
public class GLTutorialTwo extends GLTutorialBase {
	// 点是构建3D模型的基础。 OpenGL ES的内部计算是基于点的。 用点也可以表示光源的位置，物体的位置。
	// 一般我们用一组浮点数来表示点。 例如一个正方形的4个顶点可表示为：
	float[] square = new float[] { -0.25f, -0.25f, 0.0f, 0.25f, -0.25f, 0.0f,
			-0.25f, 0.25f, 0.0f, 0.25f, 0.25f, 0.0f };
	// NIO Buffer for the square
	FloatBuffer squareBuff;
	public GLTutorialTwo(Context c) {
		super(c);
		squareBuff = makeFloatBuffer(square);
		// 为了提高性能， 需要将浮点数组存入一个字节缓冲中。
	}
	protected void init(GL10 gl) {
		gl.glClearColor(0.0f, 0.0f, 0.0f, 1.0f);
		gl.glMatrixMode(GL10.GL_PROJECTION);
		/*
		  * glMatrixMode用来转换当前是模型矩阵还是投影矩阵，操作物体时使用模型矩阵，如平移、旋转、缩放等。
		  * 设置观察物体的方式时，选择投影矩阵。
		  * 下面的是定义了个1.2X1.0的矩形视图，该矩形视图的左下角是二维坐标原点。然后重新切换到模型视图模式 在比较复杂的程序中
		  * 往往不能确定当前矩阵的模式，所以为了避免这种情形的发生，我们要及时切换到给定的矩形模式，这是个很好的主意
		  */
		gl.glLoadIdentity();
		GLU.gluOrtho2D(gl, 0.0f, 1.2f, 0.0f, 1.0f);

	}
	protected void drawFrame(GL10 gl) {
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
		gl.glLoadIdentity();
		gl.glMatrixMode(GL10.GL_MODELVIEW);
		gl.glTranslatef(0, 0, -1); //
		gl.glColor4f(1, 0, 0, 0.5f);
		gl.glVertexPointer(3, GL10.GL_FLOAT, 0, squareBuff); // 说明启用数组的类型和字节缓冲，类型为GL_FLOAT
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY); // 指定需要启用定点数组
		/*
		  * 渲染是把物体坐标所指定的图元转化成帧缓冲区中的图像。图像和顶点坐标有着密切的关系。
		  * 这个关系通过绘制模式给出。常用到得绘制模式有GL_POINTS
		  * 、GL_LINE_STRIP、GL_LINE_LOOP、GL_LINES、GL_TRIANGLES
		  * 、GL_TRIANGLE_STRIP、GL_TRIANGLE_FAN。 上边会分别介绍：
		  */
		gl.glDrawArrays(GL10.GL_TRIANGLE_STRIP, 0, 4);
		// 创建一个几何图元序列，使用每个被的数组中从first开始，到first + count – 1结束的数组元素， mode为绘制模式。
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY); // 关闭顶点数组
	}
}
```
由于这个代码不是我写的，我的目的是贴出来我的研究结果，大家一起来研究研究，
第一个问题，为什么四边形的坐标表示成
```  
-0.25f, -0.25f, 0.0f,
0.25f, -0.25f, 0.0f,
-0.25f, 0.25f, 0.0f,
0.25f, 0.25f, 0.0f 
```
第二个问题，GLU.gluOrtho2D(gl, 0.0f,1.2f,0.0f,1.0f); 这个函数中 参数代表着什么？？
渲染
有了以上的概念讲解后，现在要进行最主要的工作—渲染。渲染是把物体坐标所指定的图元转化成帧缓冲区中的图像。图像和顶点坐标有着密切的关系。这个关系通过绘制模式给出。常用到得绘制模式有GL_POINTS、GL_LINE_STRIP、GL_LINE_LOOP、GL_LINES、GL_TRIANGLES、GL_TRIANGLE_STRIP、GL_TRIANGLE_FAN。下面分别介绍：
GL_POINTS：把每一个顶点作为一个点进行处理，顶点n即定义了点n，共绘制n个点。
GL_LINES：把每一个顶点作为一个独立的线段，顶点2n-1和2n之间共定义了n个线段，总共绘制N/2条线段。，如果N为奇数，则忽略最后一个顶点。
GL_LINE_STRIP：绘制从第一个顶点到最后一个顶点依次相连的一组线段，第n和n+1个顶点定义了线段n，总共绘制N-1条线段。
GL_LINE_LOOP：绘制从定义第一个顶点到最后一个顶点依次相连的一组线段，然后最后一个顶点与第一个顶点相连。第n和n+1个顶点定义了线段n，然后最后一个线段是由顶点N和1之间定义，总共绘制N条线段。
GL_TRIANGLES：把每三个顶点作为一个独立的三·角形。顶点3n-2，3n-1和3n定义了第n个三角形，总共绘制N/3个三角形。
GL_TRIANGLE_STRIP：绘制一组相连的三角形。对于奇数点n，顶点n，n+1和n+2定义了第n个三角形；对于偶数n，顶点n+1，n和n+2定义了第n个三角形，总共绘制N-2个三角形。
GL_TRIANGLE_FAN：绘制一组相连的三角形。三角形是由第一个顶点及其后给定的顶点所确定。顶点1，n+1和n+2定义了第n个三角形，总共绘制N-2个三角形。
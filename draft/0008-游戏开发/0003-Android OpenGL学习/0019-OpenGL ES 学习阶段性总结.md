这几天看了OpenGL ES的相关资料和代码，总结了一些经验。 
1. 要画图，得设定画的是什么图形，是点，直线还是三角形，通过gl.glDrawArrays( p1, 0, p2);中的p1来设定。可用的参数有GL10.GL_POINTS, GL10.GL_LINES, GL10.GL_TRIANGLES, GL10.GL_TRANGLES_FAN, GL10.GL_TRIANGLE_STRIP... 
2. 知道了形状，要设定数据源，也就是要画的点有哪些。
通过gl.glVertexPointer(3, GL10.GL_FIXED, 0, vertexBuff );来设定。
vertexBuff就是你想要画的哪些点。它表示为一个一维矩阵，但实际上根据你要画的形状来分成多个部分的。比如你想一个三角形。那么这个矩阵就可以表示为{-1，0，1，0，0，2}，{-1，0}表示第一个点，{1，0}表示第二个点，{0，2}表示第三个点。如果之后还有的话，也应该是6*N个数字，以确定更多的三角形坐标。 
3. 要是gl.glDrawArrays()方法起作用，之前得调用gl.glEnableClientState(int p1); 
p1表示要激活的数组的种类，
比如要gl.glVertexPointer()设置vertex,那么必须先调用gl.glEnableClientState(GL10.GL_VERTEX_ARRAY）;
要用数组设置颜色，先调用gl.glEnableClientState( GL10.GL_COLOR_ARRAY ); 
4. 画三维图像时，要激活深度测试。gl.glEnable( GL10.GL_DEPTH_TEST ); 
5. 使用texture之前必须先调用gl.glEnable( GL10.GL_TEXTURE_2D );
同时调用gl.glEnableClientState( GL10.GL_TEXTURE_COORD_ARRAY );
来激活texture数组画图。 
6. glLoadIdentity():另当前绘图坐标系从新回到世界坐标系的位置，另他们重合。 
glTranslatef(x,y,z):使绘图坐标系相对世界坐标系沿x,y,z轴移动x,y,z个单位。 
glVertex3f(x,y,z):在当前绘图坐标系绘制一个点 
glColor3f(r,g,b):设置以后绘图函数的绘图颜色，如果没有再次碰到glColor3f(),以后任何绘图函数绘制出的图形颜色都是这个颜色。r,g,b的范围从0.0-1.0。r-蓝色，g-绿色，b-蓝色，色彩是这三种颜色分量的混合，比如glColor3f(1.0,1.0,0.0)是黄色，glColor3f(1.0,0.0,0.0)是红色。 
glRotatef(angle,x,y,z):和glTranslatef()属于一类函数，glTranslatef()是平移，glRotatef是旋转，就是使当前绘图坐标系绕世界坐标系的x,y,z旋转angle个角度，x,y,z的值非0既1，比如glRotatef(30,1.0f,0.0f,0.0f)就是绕x轴旋转30度，glRotatef(30,1.0f,1.0f,0.0f)就是绕x,y的夹角线旋转30度。 
7.用glRotatef能是对象本身围绕一个向量旋转，我们也可以改变观测镜头本身，参考点和向上向量来改变所看到的对象，能产生glRotatef同样的旋转效果。 
```  
GLU.gluLookAt(gl, 
0.0f, 0.0f, 3.0f,//观测点在屏幕正中向外3.0f单位距离的地方 
viewX, 0.0f, 0.0f, //参考点在屏幕中间水平位置viewX 
0.0f, 1.0f, 0.0f );//表示Y轴向上 
```
综合这三个点，表示你这个人的眼睛从屏幕正中向外的3.0单位距离的地方,拿着摄像机瞄准viewX的地方再看，看的时候你的头是向上的（如果设置成1.0f, 0.0f, 0.0f，那么表示你的头得向右歪过来，成水平来看）。 
8.当窗口被创建时需要调用 onSurfaceCreate ，我们可以在这里对 OpenGL 做一些初始化工作，例如：
glHint 用于告诉 OpenGL 我们希望进行最好的透视修正，这会轻微地影响性能，但会使得透视图更好看。
glClearColor 设置清除屏幕时所用的颜色，色彩值的范围从 0.0f~1.0f 大小从暗到这的过程。
glShadeModel 用于启用阴影平滑度。阴影平滑通过多边形精细地混合色彩，并对外部光进行平滑。
glDepthFunc 为将深度缓存设想为屏幕后面的层，它不断地对物体进入屏幕内部的深度进行跟踪。
glEnable 启用深度测试。
```  
// 启用阴影平滑
gl.glShadeModel(GL10.GL_SMOOTH);
// 黑色背景
gl.glClearColor(0, 0, 0, 0);
// 设置深度缓存
gl.glClearDepthf(1.0f);
// 启用深度测试
gl.glEnable(GL10.GL_DEPTH_TEST);
// 所作深度测试的类型
gl.glDepthFunc(GL10.GL_LEQUAL);
// 告诉系统对透视进行修正
gl.glHint(GL10.GL_PERSPECTIVE_CORRECTION_HINT, GL10.GL_FASTEST);
```
9、当窗口大小发生改变时系统将调用 onSurfaceChange 方法，可以在该方法中设置 OpenGL 场景大小 ，代码如下：
```  
//设置OpenGL场景的大小
gl.glViewport(0, 0, width, height);
```
10、场景画出来了，接下来我们就要实现场景里面的内容，比如：设置它的透视图，让它有种越远的东西看起来越小的感觉，代码如下：
```  
//设置投影矩阵
gl.glMatrixMode(GL10.GL_PROJECTION);
//重置投影矩阵
gl.glLoadIdentity();
// 设置视口的大小
gl.glFrustumf(-ratio, ratio, -1, 1, 1, 10);
// 选择模型观察矩阵
gl.glMatrixMode(GL10.GL_MODELVIEW);
// 重置模型观察矩阵
gl.glLoadIdentity();  
```
gl.glMatrixMode(GL10.GL_PROJECTION); 指明接下来的代码将影响 projection matrix （投影矩阵），投影矩阵负责为场景增加透视度。
gl.glLoadIdentity(); 此方法相当于我们手机的重置功能，它将所选择的矩阵状态恢复成原始状态，调用  glLoadIdentity(); 之后为场景设置透视图。
gl.glMatrixMode(GL10.GL_MODELVIEW);   指明任何新的变换将会影响 modelview matrix （模型观察矩阵）。
gl.glFrustumf(-ratio, ratio, -1, 1, 1, 10); 
此方法，前面4个参数用于确定窗口的大小，而后面两个参数分别是在场景中所能绘制深度的起点和终点。
7、了解了上面两个重写方法的作用和功能之后，第三个方法 onDrawFrame 从字面上理解就知道此方法做绘制图操作的。嗯，没错。在绘图之前，需要将屏幕清除成前面所指定的颜色，清除尝试缓存并且重置场景，然后就可以绘图了， 代码如下：
```  
// 清除屏幕和深度缓存
gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
// 重置当前的模型观察矩
gl.glLoadIdentity();
```
8、Renderer 类在实现了上面的三个重写之后，在程序入口中只需要调用 
```  
Renderer render=new ThreeDGl(this);
 /** Called when the activity is first created. */
@Override
public void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	GLSurfaceView gview=new GLSurfaceView(this);
	gview.setRenderer(render);
	setContentView(gview);
}
```
下面分享一段使用Renderer类绘制的三角形和四边形的代码：
```  
import java.nio.IntBuffer;
import javax.microedition.khronos.egl.EGLConfig;
import javax.microedition.khronos.opengles.GL10;
import android.opengl.GLSurfaceView.Renderer;
public class GLRender implements Renderer {
	float rotateTri, rotateQuad;
	int one = 0x10000;
	// 三角形的一个顶点
	private IntBuffer triggerBuffer = IntBuffer.wrap(new int[] { 0, one, 0, // 上顶点
			-one, -one, 0,// 左顶点
			one, -one, 0 // 右下点
			});
	// 正方形的四个顶点
	private IntBuffer quateBuffer = IntBuffer.wrap(new int[] { one, one, 0,
			-one, -one, 0, one, -one, 0, -one, -one, 0 });
	private IntBuffer colorBuffer = IntBuffer.wrap(new int[] { one, 0, 0, one,
			0, one, 0, one, 0, 0, one, one });
	@Override
	public void onDrawFrame(GL10 gl) {
		// 清除屏幕和深度缓存
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
		// 重置当前的模型观察矩阵
		gl.glLoadIdentity();
		// 左移 1.5 单位，并移入屏幕 6.0
		gl.glTranslatef(-1.5f, 0.0f, -6.0f);
		// 设置旋转
		gl.glRotatef(rotateTri, 0.0f, 1.0f, 0.0f);
		// 设置定点数组
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		// 设置颜色数组
		gl.glEnableClientState(GL10.GL_COLOR_ARRAY);
		gl.glColorPointer(4, GL10.GL_FIXED, 0, colorBuffer);
		// 设置三角形顶点
		gl.glVertexPointer(3, GL10.GL_FIXED, 0, triggerBuffer);
		// 绘制三角形
		gl.glDrawArrays(GL10.GL_TRIANGLES, 0, 3);
		gl.glDisableClientState(GL10.GL_COLOR_ARRAY);
		// 绘制三角形结束
		gl.glFinish();
		 /***********************/
		/* 渲染正方形 */
		// 重置当前的模型观察矩阵
		gl.glLoadIdentity();
		// 左移 1.5 单位，并移入屏幕 6.0
		gl.glTranslatef(1.5f, 0.0f, -6.0f);
		// 设置当前色为蓝色
		gl.glColor4f(0.5f, 0.5f, 1.0f, 1.0f);
		// 设置旋转
		gl.glRotatef(rotateQuad, 1.0f, 0.0f, 0.0f);
		// 设置和绘制正方形
		gl.glVertexPointer(3, GL10.GL_FIXED, 0, quateBuffer);
		gl.glDrawArrays(GL10.GL_TRIANGLE_STRIP, 0, 4);
		// 绘制正方形结束
		gl.glFinish();
		// 取消顶点数组
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
		// 改变旋转的角度
		rotateTri += 0.5f;
		rotateQuad -= 0.5f;
	}
	@Override
	public void onSurfaceChanged(GL10 gl, int width, int height) {
		float ratio = (float) width / height;
		// 设置OpenGL场景的大小
		gl.glViewport(0, 0, width, height);
		// 设置投影矩阵
		gl.glMatrixMode(GL10.GL_PROJECTION);
		// 重置投影矩阵
		gl.glLoadIdentity();
		// 设置视口的大小
		gl.glFrustumf(-ratio, ratio, -1, 1, 1, 10);
		// 选择模型观察矩阵
		gl.glMatrixMode(GL10.GL_MODELVIEW);
		// 重置模型观察矩阵
		gl.glLoadIdentity();
	}
	@Override
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
		// 启用阴影平滑
		gl.glShadeModel(GL10.GL_SMOOTH);
		// 黑色背景
		gl.glClearColor(0, 0, 0, 0);
		// 设置深度缓存
		gl.glClearDepthf(1.0f);
		// 启用深度测试
		gl.glEnable(GL10.GL_DEPTH_TEST);
		// 所作深度测试的类型
		gl.glDepthFunc(GL10.GL_LEQUAL);
		// 告诉系统对透视进行修正
		gl.glHint(GL10.GL_PERSPECTIVE_CORRECTION_HINT, GL10.GL_FASTEST);
	}
}
```
OpenGL包含几十个选项，通过glEnable()方法和glDisable()方法可以启用和禁用这些选项，启用每个选项都会对性能产生影响。
下面列出最常用的选项：
```  
GL_BLEND :将传入的色值与颜色缓存中的色织进行混合
GL_CULL_FACE :根据窗口坐标中多边形的转动方向（顺时针或逆时针）来忽略多边形的某些面。这是一种消除多边形后面图像绘制的简便方法。
GL_DEPTH_TEST :进行深度对比，并更新深度缓冲内容。如果某些像素距离已经绘制的很远，则这些像素将被忽略。
GL_LIGHTING :进行亮度和材料计算。
GL_LINE_SMOOTH :绘制无锯齿的线。
GL_POINT_SMOOTH :绘制无锯齿的点。
GL_TEXTURE_2D :使用纹理绘制桌面。
```
视点变换：
```  
void gluLookAt(GLdouble eyex,GLdouble eyey,GLdouble eyez,
GLdouble centerx,GLdouble centery,
GLdouble upx,GLdouble upy,GLdouble upz);
```
模型变换:
```  
模型平移glTranslate{fd}(TYPE x,TYPE y,TYPE z);
模型旋转glRotate{fd}(TYPE angle,TYPE x,TYPE,y,TYPE z);
模型缩放glScale{fd}(TYPE x,TYPE y,TYPE z);
```
投影变换:
透视投影函数
```  
void glFrustum(GLdouble left,GLdouble Right,GLdouble bottom,GLdouble top,GLdouble near,GLdouble far);
void gluPerspective(GLdouble fovy,GLdouble aspect,GLdouble zNear, GLdouble zFar);
```
正射投影函数
```  
void glOrtho(GLdouble left,GLdouble right,GLdouble bottom,GLdouble top, GLdouble near,GLdouble far)
void gluOrtho2D(GLdouble left,GLdouble right,GLdouble bottom,GLdouble top)
```
视口变换:
```  
glViewport(GLint x,GLint y,GLsizei width, GLsizei height);
glMatrixMode():指定哪一个矩阵是当前矩阵
模型视景矩阵 GL_MODEVIEW对模型视景矩阵堆栈应用随后的矩阵操作。
模型转换和视点转换共同构成模型视景矩阵
投影矩阵 GL_PROJECTION  对投影矩阵应用随后的矩阵操作。
纹理矩阵 GLTEXTURE  对纹理矩阵堆栈应用随后的矩阵操作。
glLoadIdentity():该函数的功能是重置当前指定的矩阵为单位矩阵。
```
纹理映射
```  
//设置深度缓存 
gl.glClearDepthf(1.0f);
//允许2D贴图,纹理
gl.glEnableClientState(GL10.GL_TEXTURE_COORD_ARRAY);
gl.glEnable(GL10.GL_TEXTURE_2D);
IntBuffer intBuffer = IntBuffer.allocate(1);
// 创建纹理，生成一个纹理的名字
gl.glGenTextures(1, intBuffer);
// 获得纹理名字
texture = intBuffer.get();
// 绑定纹理，设置要使用的纹理
gl.glBindTexture(GL10.GL_TEXTURE_2D, texture);
//生成纹理，1：纹理类型，2：纹理的详细程度，一般设为0，3：图像数据，4：边框
GLUtils.texImage2D(GL10.GL_TEXTURE_2D, 0, GLImage.mBitmap, 0);
//纹理和四边形对应的顶点
gl.glVertexPointer(3, GL10.GL_FIXED, 0, vertices);
gl.glTexCoordPointer(2, GL10.GL_FIXED, 0, texCoords);
//绘制
gl.glDrawElements(GL10.GL_TRIANGLE_STRIP, 24,  GL10.GL_UNSIGNED_BYTE, indices);
```
光照
光的种类：环境光(Ambient )、漫射光(Diffuse)、高光(Specular)。
环境光是无所不在的漫射光，是场景的背景亮度，因此无法确定其来源，平均作用于场景中的所有物体的所有面。
漫射光由特定的光源产生，并在场景中的对象表面产生反射。处于漫射光照射下的任何对象表面都变得很亮，而机会未被照射的区域就显得要暗一些。
```  
//如果不启用GL_LIGHTING光就什么都看不见
gl.glEnable(GL10.GL_LIGHTING);
定义环境光 (r,g,b,a)的颜色(半亮)：
FloatBuffer lightAmbient = FloatBuffer.wrap(new float[]{0.5f,0.5f,0.5f,1.0f}); 
//定义漫射光(最亮)
FloatBuffer lightDiffuse = FloatBuffer.wrap(new float[]{1.0f,1.0f,1.0f,1.0f});
设置好光源组后需要定义光源在场景中所处的位置
//定义光源，把光源放在2.0f的位置上，最后一个参数是1.0f告知OpenGL指定的坐标就是光源的位置，0来定义平行光源 
FloatBuffer lightPosition = FloatBuffer.wrap(new float[]{0.0f,0.0f,2.0f,1.0f});
//设置环境光
gl.glLightfv(GL10.GL_LIGHT1, GL10.GL_AMBIENT, lightAmbient);
//设置漫射光
gl.glLightfv(GL10.GL_LIGHT1, GL10.GL_DIFFUSE, lightDiffuse);
//设置光源的位置
gl.glLightfv(GL10.GL_LIGHT1, GL10.GL_POSITION, lightPosition); 
//启用一号光源
gl.glEnable(GL10.GL_LIGHT1);
```
glfrustumf前四个参数是-1，1，-1.5，1.5，这是规定视窗的大小和纵横位置，前两个参数指以原点为参照，视窗宽度的左值和右值，两值的绝对值相加就是视窗的宽，同理，后两个参数指以原点为参照,视窗高度的上值和下值，两值的绝对值相加就是视窗的高度。视窗决定了三维物体的可视区域。超过这个视窗范围的物体将不会显示出来，GDI的说法就是会被剪切掉.后两个参数是一个距离，2，5，这两个参数只是正值,虽然看上去没有和坐标相关，但实际上它的意义还是坐标上，只是需要自己算一下，第一个参数2是指视窗到视点的距离，第二个参数5是指投影面到视点的距离，第一个参数指示了视窗的前后位置，参数越小，视窗越靠近视点，可视区域越大，反之亦然，投影面就是屏幕了，投影面的远近
纹理对象
除了默认的TEXTURE_2D和TEXTURE_CUBE_MAP，可以创建有名称的纹理对象；
```  
void BindTexture(enum target, uinttexture); //创建纹理对象，连接一个未使用的名称到TEXTURE_2D或TEXTURE_CUBE_MAP，也可以修改已有纹理对象的连接；
```
新创建的纹理对象有3.7.12中所有的state。
```  
void DeleteTextures(sizei n, uint*textures ); //删除已有的n个纹理对象
void GenTextures(sizei n, uint *textures); //返回未使用的纹理对象名称
```
当需要更新纹理时，在OpenGL ES中可以使用glTextSubImage2D函数来代替glText-Image2D函数，这样就可以避免内存分配与回收问题。此外还可以采用纹理压缩技术，在OpenGLES中可以调用glCompressedTextImage2D函数和glCompressedTextSubImage2D函数来压缩纹理。压缩纹理的优点在于使用内存较小，可以提高高速缓存的利用率。
贴上去的纹理必须是512*512的或2^次方的
GLSurfaceView.setRenderMode(RENDERMODE_WHEN_DIRTY);停止持续渲染。
GLSurfaceView.requestRender()；程序再渲染屏幕。默认持续型渲染
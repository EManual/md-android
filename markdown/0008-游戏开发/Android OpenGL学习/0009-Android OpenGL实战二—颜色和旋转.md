经过OpenGl实战一，已经熟悉了OpenGL开发框架的搭建，到目前为止都是比较简单的，我们已经实现了画出三角形和四边形，在OpenGl中绘制的任何模型都会被分解为三角形和四边形两种简单的图形，但是只有图形是不生动的，所以在三角形和多边形的基础上我们着色，并加上简单的旋转动作！
#### 一、颜色
平滑着色Smooth coloring
单调着色Flat coloring
两者的区别在于：平滑着色指定每个顶点颜色，平滑过渡，单调着色相当于填充颜色（单色）
使用起来都很简单，但还是有区别的：
平滑着色：
```  
private int one = 0x10000;  
[Tags]/** 
 [Tags]* 三角形的顶点颜色值 
 [Tags]* 参数： 
 [Tags]* 1、红色分量 
 [Tags]* 2、绿色分量 
 [Tags]* 3、蓝色分量 
 [Tags]* 4、alpha值 
 [Tags]*/  
private IntBuffer colorBuffer=IntBuffer.wrap(new int[]{  
	one,0,0,one,
	0,one,0,one,
	0,0,one,one,
});  
```
上面是定义顶点颜色，着色前需要开启颜色渲染功能，着色后要关闭颜色渲染功能，具体代码如下：
```  
//开启色彩渲染功能  
gl.glEnableClientState(GL10.GL_COLOR_ARRAY);  
//设置三角形颜色  
gl.glColorPointer(4, GL10.GL_FIXED, 0, colorBuffer);  
[Tags]/** 
 [Tags]* 绘制顶点 
 [Tags]* 参数： 
 [Tags]* 1、绘制的模式----我们使用GL_TRIANGLES来表示绘制三角形 
 [Tags]* 2、开始位置 
 [Tags]* 3、要绘制的顶点计数 
 [Tags]*/  
gl.glDrawArrays(GL10.GL_TRIANGLES, 0, 3);  
//关闭色彩渲染功能  
gl.glDisableClientState(GL10.GL_COLOR_ARRAY);  
```
这里要注意绘制的操作一定要放到设置颜色的后面，否则没效果
单调着色：
这个比较简单，不需要开启和关闭渲染功能
```  
//设置四边形颜色-单调着色  
gl.glColor4f(0.5f, 0.5f, 1.0f, 1.0f);  
```
参数和平滑着色一样
#### 二、旋转
旋转也很简单，首先定义旋转角度的float变量
```  
private float rotateTri;
```
然后调用旋转的方法：
```  
[Tags]/** 
 [Tags]* 旋转三角形 
 [Tags]* 参数： 
 [Tags]* 1、旋转角度 
 [Tags]* 234是x、y、z共同决定旋转轴方向的参数 
 [Tags]* 例如:1,0,0 表示经过x坐标的一个单位向右旋转 
 [Tags]*      -1,0,0 表示.................向左旋转 
 [Tags]*/  
gl.glRotatef(rotateTri, 0.0f, 1.0f, 0.0f);  
```
就这一行，就可以旋转了，还是比较好理解的，为了让物体能够不停的旋转，可以在onDrawFrame方法中改变角度常量rotateTri+=0.5f;
简单的着色和旋转就实现了，下面我贴出全部代码，共同学习！
```  
import java.nio.IntBuffer;
import javax.microedition.khronos.egl.EGLConfig;
import javax.microedition.khronos.opengles.GL10;
import android.opengl.GLSurfaceView.Renderer;
public class GLReader implements Renderer {
	private int one = 0x10000;
	// 三角形的三个顶点
	private IntBuffer triggerBuffer = IntBuffer.wrap(new int[] { 0, one, 0,// 上顶点
			-one, -one, 0, // 做顶点
			one, -one, 0 // 右顶点
			});
	// 定义三角形和四边形的旋转变量
	private float rotateTri, rotateQuad;
	[Tags]/**
	 [Tags]* 三角形的顶点颜色值 参数： 1、红色分量 2、绿色分量 3、蓝色分量 4、alpha值
	 [Tags]*/
	private IntBuffer colorBuffer = IntBuffer.wrap(new int[] { one, 0, 0, one,
			0, one, 0, one, 0, 0, one, one, });
	// 四边形的四个顶点
	private IntBuffer quaterBuffer = IntBuffer.wrap(new int[] { one, one, 0,
			-one, one, 0, one, -one, 0, -one, -one, 0 });
	[Tags]/**
	 [Tags]* 所有绘图的工作放到此方法里
	 [Tags]*/
	@Override
	public void onDrawFrame(GL10 gl) {
		// 清除屏幕和深度缓存
		gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
		// 重置当前的模型观察矩阵
		gl.glLoadIdentity();
		// 左移 1.5 单位，并移入屏幕 6.0
		gl.glTranslatef(-1.5f, 0.0f, -6.0f);
		// 开启顶点设置功能
		gl.glEnableClientState(GL10.GL_VERTEX_ARRAY);
		[Tags]/**
		 [Tags]* 旋转三角形 参数： 1、旋转角度 234是x、y、z共同决定旋转轴方向的参数 例如:1,0,0 表示经过x坐标的一个单位向右旋转
		 [Tags]* -1,0,0 表示.................向左旋转
		 [Tags]*/
		gl.glRotatef(rotateTri, 0.0f, 1.0f, 0.0f);
		[Tags]/**
		 [Tags]* 设置顶点数据 参数： 1、顶点尺寸----这里使用的是xyz坐标系，所以是3 2、顶点类型----这里是固定的，所以用GL_FIXED
		 [Tags]* 3、步长 4、顶点缓存----即顶点数组
		 [Tags]*/
		gl.glVertexPointer(3, GL10.GL_FIXED, 0, triggerBuffer);
		// 开启色彩渲染功能
		gl.glEnableClientState(GL10.GL_COLOR_ARRAY);
		// 设置三角形颜色
		gl.glColorPointer(4, GL10.GL_FIXED, 0, colorBuffer);
		[Tags]/**
		 [Tags]* 绘制顶点 参数： 1、绘制的模式----我们使用GL_TRIANGLES来表示绘制三角形 2、开始位置 3、要绘制的顶点计数
		 [Tags]*/
		gl.glDrawArrays(GL10.GL_TRIANGLES, 0, 3);
		// 关闭色彩渲染功能
		gl.glDisableClientState(GL10.GL_COLOR_ARRAY);
		// 重置当前的模型观察矩阵
		gl.glLoadIdentity();
		// 右移1.5，进入6.0
		gl.glTranslatef(1.5f, 0.0f, -6.0f);
		// 设置四边形顶点数据
		gl.glVertexPointer(3, GL10.GL_FIXED, 0, quaterBuffer);
		// 设置四边形颜色-单调着色
		gl.glColor4f(0.5f, 0.5f, 1.0f, 1.0f);
		// 旋转四边形
		gl.glRotatef(rotateQuad, 1.0f, 0.0f, 0.0f);
		// 绘制顶点（注意参数一不同）
		gl.glDrawArrays(GL10.GL_TRIANGLE_STRIP, 0, 4);
		// 取消顶点设置
		gl.glDisableClientState(GL10.GL_VERTEX_ARRAY);
		// 改变旋转角度
		rotateTri += 0.5;
		rotateQuad -= 0.5;
	}
	[Tags]/**
	 [Tags]* 当窗口大小发生改变是调用此方法 不管窗口大小是否已经改变，此方法至少执行一次 所以在此方法中设置OpenGL场景的大小
	 [Tags]*/
	@Override
	public void onSurfaceChanged(GL10 gl, int width, int height) {
		float ratio = (float) width / height;
		// 设置OpenGL场景大小为屏幕大小
		gl.glViewport(0, 0, width, height);
		/* 为屏幕设置透视图 */
		// 设置投影矩阵
		gl.glMatrixMode(GL10.GL_PROJECTION);
		// 重置投影矩阵
		gl.glLoadIdentity();
		// 设置视口大小
		gl.glFrustumf(-ratio, ratio, -1, 1, 1, 10);
		// 选择模型观察矩阵
		gl.glMatrixMode(GL10.GL_MODELVIEW);
		// 重置模型观察矩阵
		gl.glLoadIdentity();
	}
	[Tags]/**
	 [Tags]* 窗口创建时调用此方法 此方法内做初始化的操作
	 [Tags]*/
	@Override
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
		// 对透视进行修正
		gl.glHint(GL10.GL_PERSPECTIVE_CORRECTION_HINT, GL10.GL_FASTEST);
		// 背景：黑色
		gl.glClearColor(0, 0, 0, 0);
		// 启动阴影平滑
		gl.glShadeModel(GL10.GL_SMOOTH);

		// 设置深度缓存
		gl.glClearDepthf(1.0f);
		// 启用深度测试
		gl.glEnable(GL10.GL_DEPTH_TEST);
		// 所做深度测试的类型
		gl.glDepthFunc(GL10.GL_LEQUAL);
	}
}
```
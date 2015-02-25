我们的这一篇是接上一篇的来说的。那就不多说废话了。直接贴上代码：
```  
private void copy(Object src) {
	try {
		// 打印日志
		Logger.log("Copying data from master Activity!");
		// 返回一个数组，其中包含目前这个类的的所有字段的Filed对象
		Field[] fs = src.getClass().getDeclaredFields();
		// 遍历fs数组
		for (Field f : fs) {
			// 尝试设置无障碍标志的值。标志设置为false将使访问检查，设置为true，将其禁用。
			f.setAccessible(true);
			// 将取到的值全部装入当前类中
			f.set(this, f.get(src));
		}
	} catch (Exception e) {
		// 抛出运行时异常
		throw new RuntimeException(e);
	}
}
public boolean onTouchEvent(MotionEvent me) {
	// 按键开始
	if (me.getAction() == MotionEvent.ACTION_DOWN) {
		// 保存按下的初始x,y位置于xpos,ypos中
		xpos = me.getX();
		ypos = me.getY();
		return true;
	}
	// 按键结束
	if (me.getAction() == MotionEvent.ACTION_UP) {
		// 设置x,y及旋转角度为初始值
		xpos = -1;
		ypos = -1;
		touchTurn = 0;
		touchTurnUp = 0;
		return true;
	}
	if (me.getAction() == MotionEvent.ACTION_MOVE) {
		// 计算x,y偏移位置及x,y轴上的旋转角度
		float xd = me.getX() - xpos;
		float yd = me.getY() - ypos;
		// Logger.log("me.getX() - xpos----------->>"
		// + (me.getX() - xpos));
		xpos = me.getX();
		ypos = me.getY();
		Logger.log("xpos------------>>" + xpos);
		// Logger.log("ypos------------>>" + ypos);
		// 以x轴为例，鼠标从左向右拉为正，从右向左拉为负
		touchTurn = xd / -100f;
		touchTurnUp = yd / -100f;
		Logger.log("touchTurn------------>>" + touchTurn);
		// Logger.log("touchTurnUp------------>>" + touchTurnUp);
		return true;
	}
	// 每Move一下休眠毫秒
	try {
		Thread.sleep(15);
	} catch (Exception e) {
		// No need for this...
	}
	return super.onTouchEvent(me);
}

// MyRenderer类实现GLSurfaceView.Renderer接口
class MyRenderer implements GLSurfaceView.Renderer {
	// 当前系统的毫秒数
	private long time = System.currentTimeMillis();
	// 是否停止
	private boolean stop = false;
	// 停止
	public void stop() {
		stop = true;
	}
	// 当屏幕改变时
	public void onSurfaceChanged(GL10 gl, int w, int h) {
		// 如果FrameBuffer不为NULL,释放fb所占资源
		if (fb != null) {
			fb.dispose();
		}
		// 创建一个宽度为w,高为h的FrameBuffer
		fb = new FrameBuffer(gl, w, h);
		Logger.log(master + "");
		// 如果master为空
		if (master == null) {
			// 实例化World对象
			world = new World();
			// 设置了环境光源强度。设置此值是负的整个场景会变暗，而为正将照亮了一切。
			world.setAmbientLight(20, 20, 20);
			// 在World中创建一个新的光源
			sun = new Light(world);
			// 设置光照强度
			sun.setIntensity(250, 250, 250);
			// 创建一个纹理
			// 构造方法Texture(Bitmap image)
			// static Bitmap rescale(Bitmap bitmap, int width, int height)
			// static Bitmap convert(Drawable drawable)
			Texture texture = new Texture(BitmapHelper.rescale(
					BitmapHelper.convert(getResources().getDrawable(
							R.drawable.glass)), 64, 64));
			// TextureManager.getInstance()取得一个Texturemanager对象
			// addTexture("texture",texture)添加一个纹理
			TextureManager.getInstance().addTexture("texture", texture);
			// Object3D对象开始了:-)
			// Primitives提供了一些基本的三维物体，假如你为了测试而生成一些对象或为
			// 其它目的使用这些类将很明智，因为它即快速又简单，不需要载入和编辑。
			// 调用public static Object3D getCube(float scale) scale:角度
			// 返回一个立方体
			cube = Primitives.getCube(10);
			// 以纹理的方式给对象所有面"包装"上纹理
			cube.calcTextureWrapSpherical();
			// 给对象设置纹理
			cube.setTexture("texture");
			// 除非你想在事后再用PolygonManager修改,否则释放那些不再需要数据的内存
			cube.strip();
			// 初始化一些基本的对象是几乎所有进一步处理所需的过程。
			// 如果对象是"准备渲染"(装载，纹理分配，安置，渲染模式设置，
			// 动画和顶点控制器分配),那么build()必须被调用，
			cube.build();
			// 将Object3D对象添加到world集合
			world.addObject(cube);
			// 该Camera代表了Camera/viewer在当前场景的位置和方向，它也包含了当前视野的有关信息
			// 你应该记住Camera的旋转矩阵实际上是应用在World中的对象的一个旋转矩阵。
			// 这一点很重要，当选择了Camera的旋转角度，一个Camera(虚拟)围绕w旋转和通过围绕World围绕w旋转、
			// 将起到相同的效果，因此，考虑到旋转角度，World围绕camera时，camera的视角是静态的。假如你不喜欢
			// 这种习惯，你可以使用rotateCamera()方法
			Camera cam = world.getCamera();
			// 以50有速度向后移动Camera（相对于目前的方向）
			cam.moveCamera(Camera.CAMERA_MOVEOUT, 50);
			// cub.getTransformedCenter()返回对象的中心
			// cam.lookAt(SimpleVector lookAt))
			// 旋转这样camera以至于它看起来是在给定的world-space 的位置
			cam.lookAt(cube.getTransformedCenter());
			// SimpleVector是一个代表三维矢量的基础类，几乎每一个矢量都
			// 是用SimpleVector或者至少是一个SimpleVector变体构成的(有时由于
			// 某些原因比如性能可能会用(float x,float y,float z)之类)。
			SimpleVector sv = new SimpleVector();
			// 将当前SimpleVector的x,y,z值设为给定的SimpleVector(cube.getTransformedCenter())的值
			sv.set(cube.getTransformedCenter());
			// Y方向上减去100
			sv.y -= 100;
			// Z方向上减去100
			sv.z -= 100;
			// 设置光源位置
			sun.setPosition(sv);
			// 强制GC和finalization工作来试图去释放一些内存，同时将当时的内存写入日志，
			// 这样可以避免动画不连贯的情况，然而，它仅仅是减少这种情况发生的机率
			MemoryHelper.compact();
			// 如果master为空,使用日志记录且设master为HelloWorld本身
			if (master == null) {
				Logger.log("Saving master Activity!");
				master = HelloWorld.this;
			}
		}
	}
	// 需实现的onSurfaceCreated(GL10 gl, EGLConfig config)
	public void onSurfaceCreated(GL10 gl, EGLConfig config) {
	}
	// 绘制到当前屏幕哦:-D
	public void onDrawFrame(GL10 gl) {
		try {
			// 如果stop为true
			if (!stop) {
				// 如果touchTurn不为0,向Y轴旋转touchTure角度
				if (touchTurn != 0) {
					// 旋转物体的旋转绕Y由给定矩阵W轴角（弧度顺时针方向为正值）,应用到对象下一次渲染时。
					cube.rotateY(touchTurn);
					// 将touchTurn置0
					touchTurn = 0;
				}
				if (touchTurnUp != 0) {
					// 旋转物体的旋转围绕x由给定角度宽（弧度，逆时针为正值）轴矩阵,应用到对象下一次渲染时。
					cube.rotateX(touchTurnUp);
					// 将touchTureUp置0
					touchTurnUp = 0;
				}
				// 用给定的颜色(back)清除FrameBuffer
				fb.clear(back);
				// 变换和灯光所有多边形
				world.renderScene(fb);
				// 绘制
				world.draw(fb);
				// 渲染图像显示
				fb.display();
				// 记录FPS
				if (System.currentTimeMillis() - time >= 1000) {
					// Logger.log(fps + "fps");
					fps = 0;
					time = System.currentTimeMillis();
				}
				fps++;
				// 如果stop为false,释放FrameBuffer
			} else {
				if (fb != null) {
					fb.dispose();
					fb = null;
				}
			}
			// 当出现异常，打印异常信息
		} catch (Exception e) {
			Logger.log(e, Logger.MESSAGE);
		}
	}
}
```
代码如下：
```  
public void render() {
	// 清屏
	Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
	// 初始化绘图调用
	spriteBatch.begin();
	// 绘制精灵到游戏屏幕
	sprite.draw(spriteBatch);
	// 结束绘图调用
	spriteBatch.end();
}
public void dispose() {
	// 释放占用的资源
	spriteBatch.dispose();
	texture.dispose();
}
public void resume() {
}
public void pause() {
}
public void resize(int width, int height) {
}
public void onCreate(Bundle bundle) {
	super.onCreate(bundle);
	// 初始化游戏屏幕，并设置是否支持GLES 2.0，如果您对向下兼容没什么需要选择true即可(2.1以上)，否则选择false。
	initialize(new TestSprite(), true);
}
```
Pixmap:
Libgdx所提供的像素级图像渲染用类，由于Libgdx目前以JNI方式自带图像解码器，所以我们可以直接将Pixmap理解为一个Android中 Bitmap的替代者，两者间实现细节虽有差别，但具体作用却大同小异。Pixmap支持Alpha、LuminanceAlpha、RGB565、 RGBA4444、RGB888、RGBA8888等五种图像彩色模式，支持png、jpg、bmp等三种图像文件的读取和加载。一般来说，Pixmap 必须和Texture混用才能真正显示画面。不过在事实上，Libgdx的Texture里已经内置有Pixmap了。
```  
// Libgdx的Pixmap使用
public class Main extends AndroidApplication {
	class TestPixmap implements ApplicationListener {
		// 准备绘图用SpriteBatch
		SpriteBatch spriteBatch;
		// Pixmap是Libgdx提供的针对opengl像素操作的上级封装，它可以凭空构建一个像素贴图，
		// 但是它的现实必须通过Texture。
		Pixmap pixmap;
		// 准备Texture
		Texture texture;
		public void create() {
			// 构建SpriteBatch
			spriteBatch = new SpriteBatch();
			// 构建Pixmap(在Android环境使用internal加载模式时，文件必须放置于assets文件夹下)
			pixmap = new Pixmap(Gdx.files.internal("myPixmap.png"));
			// 绘制一个蓝方块到Ball图像之上
			pixmap.setColor(Color.BLUE.r, Color.BLUE.g, Color.BLUE.b,
					Color.BLUE.a);
			pixmap.drawRectangle(15, 15, 40, 40);
			// 以指定Pixmap构建Texture
			texture = new Texture(pixmap);
			// 注入Texture后的pixmap已经没用，可以注销
			pixmap.dispose();
		}
		public void dispose() {
			spriteBatch.dispose();
			texture.dispose();
		}
		public void pause() {
		}
		public void render() {
			// 清屏
			Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
			// 初始化绘图调用
			spriteBatch.begin();
			// 绘制精灵到游戏屏幕
			spriteBatch.draw(texture, 100, 180);
			// 结束绘图调用
			spriteBatch.end();
		}
		public void resize(int width, int height) {
		}
		public void resume() {
		}
	}
	public void onCreate(Bundle bundle) {
		super.onCreate(bundle);
		// 初始化游戏屏幕，并设置是否支持GLES 2.0，如果您对向下兼容没什么需要选择true即可(2.1以上)，否则选择false。
		initialize(new TestPixmap(), true);
	}
}
```
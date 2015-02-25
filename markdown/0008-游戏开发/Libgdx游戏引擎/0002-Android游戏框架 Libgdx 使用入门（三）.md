BitmapFont：
Libgdx所提供的OpenGL文字用类，构造BitmapFont时需要一个描述文字构成的fnt文件，和一个提供文字图片的png文件（PS：在Libgdx的com.badlogic.gdx.utils包下有提供内置字库，目前仅支持英文、数字和常见符号），同SpriteBatch相配合时能够完成一些基础的文字绘图.值得一提的是，我们也可以使用BitmapFontCache类将BitmapFont包装成了一个静态的Font实例，以避免大量贴图时产生的不必要损耗。
```  
//libgdx的文字显示
public class Main extends AndroidApplication {
	class TestFont extends Game {
		// SpriteBatch是libgdx提供的opengl封装，可以在其中执行一些常规的图像渲染，
		// 并且libgdx所提供的大多数图形功能也是围绕它建立的。
		SpriteBatch spriteBatch;
		// BitmapFont是libgdx提供的文字显示用类，内部将图片转化为可供opengl调用的
		// 文字贴图(默认不支持中文)。
		BitmapFont font;
		public void create() {
			// 构建SpriteBatch用于图像处理(内部调用opengl或opengles)
			spriteBatch = new SpriteBatch();
			// 构建BitmapFont，必须有一个fnt文件描述文字构成，一个图片文件提供文字用图
			font = new BitmapFont(Gdx.files.internal("font.fnt"),
					Gdx.files.internal("font.png"), false);
		}
		public void render() {
			// 调用清屏
			Gdx.gl.glClear(GL10.GL_COLOR_BUFFER_BIT);
			// 初始要有begin起始
			spriteBatch.begin();
			// 显示文字到屏幕指定位置
			// PS:Libgdx采用标准笛卡尔坐标系，自左下0,0开始
			font.draw(spriteBatch, "FPS" + Gdx.graphics.getFramesPerSecond(),
					5, 475);
			font.draw(spriteBatch, "Hello Libgdx", 255, 255);
			// 结束要有end结尾
			spriteBatch.end();
		}
		public void resize(int width, int height) {
		}
		public void pause() {
		}
		public void resume() {
		}
		public void dispose() {
			// 释放占用的资源
			spriteBatch.dispose();
			font.dispose();
		}
	}
	public void onCreate(Bundle bundle) {
		super.onCreate(bundle);
		// 初始化游戏屏幕，并设置是否支持GLES 2.0，如果您对向下兼容没什么需要选择true即可(2.1以上)，否则选择false。
		initialize(new TestFont(), true);
	}
}
```
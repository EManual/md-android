SpriteBatch：
Libgdx所提供的纹理渲染器，本质上是OpenGL的简易封装体，具体实现上与XNA中的SpriteBatch类非常近似，每次调用SpriteBatch类都必须以begin函数开头，以end函数结尾.由于Libgdx中SpriteBatch提供的功能还非常有限，所以在完全不懂OpenGL的前提下使用其进行游戏开发或许有一定难度。
ShaderProgram：
Libgdx所提供的着色器，在Android环境使用时需要GLES2.0或以上版本才能完整支持的高级渲染功能之一，内部封装着GLES2.0专用的顶点着色与片断着色Shader Model，它的本质作用是对3D对象表面进行渲染处理，此物性能基本取决于GPU（除了Google Nexus系列手机暂未见能完全跑出速度的机型）。
```  
//libgdx的ShaderProgram使用
public class Main extends AndroidApplication {
	class TestShader implements ApplicationListener {
		ShaderProgram shader;
		Texture texture;
		Texture texture2;
		Mesh mesh;
		public void create() {
			// 以下命令供GPU使用(不支持GLES2.0就不用跑了)
			String vertexShader = "attribute vec4 a_position;
					+ "attribute vec2 a_texCoord" + "varying vec2 v_texCoord;
					+ "void main() " + "{ " + " gl_Position = a_position;
					+ " v_texCoord = a_texCoord; " + "} ";
			String fragmentShader = "ifdef GL_ES
					+ "precision mediump float;
					+ "endif 
					+ "varying vec2 v_texCoord;
					+ "uniform sampler2D s_texture;
					+ "uniform sampler2D s_texture2;
					+ "void main()
					+ "{
					+ " gl_FragColor = texture2D( s_texture, v_texCoord ) * texture2D( s_texture2, v_texCoord);
					+ "} ";
			// 构建ShaderProgram
			shader = new ShaderProgram(vertexShader, fragmentShader);
			// 构建网格对象
			mesh = new Mesh( true , 4 , 6 , new VertexAttribute(Usage.Position, 2 ,"a_position" ), new VertexAttribute(Usage.TextureCoordinates), 2 , "a_texCoord" ));
			float[] vertices = { -0.5f, 0.5f, 0.0f, 0.0f, -0.5f, -0.5f, 0.0f,
					1.0f, 0.5f, -0.5f, 1.0f, 1.0f, 0.5f, 0.5f, 1.0f, 0.0f };
			short[] indices = { 0, 1, 2, 0, 2, 3 };
			// 注入定点坐标
			mesh.setVertices(vertices);
			mesh.setIndices(indices);
			// 以Pixmap生成两个指定内容的Texture
			Pixmap pixmap = new Pixmap(256, 256, Format.RGBA8888);
			pixmap.setColor(1, 1, 1, 1);
			pixmap.fill();
			pixmap.setColor(0, 0, 0, 1);
			pixmap.drawLine(0, 0, 256, 256);
			pixmap.drawLine(256, 0, 0, 256);
			texture = new Texture(pixmap);
			pixmap.dispose();
			pixmap = new Pixmap(256, 256, Format.RGBA8888);
			pixmap.setColor(1, 1, 1, 1);
			pixmap.fill();
			pixmap.setColor(0, 0, 0, 1);
			pixmap.drawLine(128, 0, 128, 256);
			texture2 = new Texture(pixmap);
			pixmap.dispose();
		}
		public void dispose() {
		}
		public void pause() {
		}
		public void render() {
			// PS:由于使用了ShaderProgram，因此必须配合gl20模式(否则缺少关键opengles接口)
			Gdx.gl20.glViewport(0, 0, Gdx.graphics.getWidth(),
					Gdx.graphics.getHeight());
			Gdx.gl20.glClear(GL20.GL_COLOR_BUFFER_BIT);
			Gdx.gl20.glActiveTexture(GL20.GL_TEXTURE0);
			texture.bind();
			Gdx.gl20.glActiveTexture(GL20.GL_TEXTURE1);
			texture2.bind();
			// 开始使用ShaderProgram渲染
			shader.begin();
			shader.setUniformi("s_texture", 0);
			shader.setUniformi("s_texture2", 1);
			mesh.render(shader, GL20.GL_TRIANGLES);
			// 结束ShaderProgram渲染
			shader.end();
		}
		public void resize(int width, int height) {
		}
		public void resume() {
		}
	}
	public void onCreate(Bundle bundle) {
		super.onCreate(bundle);
		// 初始化游戏屏幕，并设置是否支持GLES 2.0，如果您对向下兼容没什么需要选择true即可(2.1以上)，否则选择false。

		initialize(new TestShader(), true);
	}
}
```
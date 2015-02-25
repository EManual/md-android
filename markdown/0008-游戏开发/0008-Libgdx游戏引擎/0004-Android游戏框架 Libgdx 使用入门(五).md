FrameBuffer:
Libgdx所提供的帧缓冲器，在Android环境使用时需要GLES2.0或以上版本才能完整支持的高级渲染功能之一，也就是常说的 FrameBuffer Object(FBO)功能封装(用过JavaSE或JavaME开发游戏的朋友，绘图时大概都接触过双缓存这个概念，虽然有所差别，不过将FrameBuffer理解成起近似作用也未尝不可)此物性能彻底取决于GPU(除了Google Nexus系列手机暂未见能完全跑出速度的机型)。
```  
//libgdx的FrameBuffer使用
public class Main extends AndroidApplication {
	class TestFrameBuffer implements ApplicationListener {
		FrameBuffer frameBuffer;
		Mesh mesh;
		ShaderProgram meshShader;
		Texture texture;
		SpriteBatch spriteBatch;
		// PS:如果不支持GLES2.0就不用试了
		public void create() {
			mesh = new Mesh(true, 3, 0, new VertexAttribute(Usage.Position, 3,
					"a_Position"), new VertexAttribute(Usage.ColorPacked, 4,
					"a_Color"), new VertexAttribute(Usage.TextureCoordinates,
					2, "a_texCoords"));
			float c1 = Color.toFloatBits(255, 0, 0, 255);
			float c2 = Color.toFloatBits(255, 0, 0, 255);
			float c3 = Color.toFloatBits(0, 0, 255, 255);
			mesh.setVertices(new float[] { -0.5f, -0.5f, 0, c1, 0, 0, 0.5f,
					-0.5f, 0, c2, 1, 0, 0, 0.5f, 0, c3, 0.5f, 1 });
			texture = new Texture(Gdx.files.internal("myTest.png"));
			spriteBatch = new SpriteBatch();
			frameBuffer = new FrameBuffer(Format.RGB565, 128, 128, true);
			String vertexShader = "attribute vec4 a_Position;
					+ "attribute vec4 a_Color;
					+ "attribute vec2 a_texCoords; " + "varying vec4 v_Color;
					+ "varying vec2 v_texCoords; " + "void main() " + "{
					+ " v_Color = a_Color;" + " v_texCoords = a_texCoords;
					+ " gl_Position = a_Position; " + "} ";
			String fragmentShader = "precision mediump float;
					+ "varying vec4 v_Color;
					+ "varying vec2 v_texCoords;
					+ "uniform sampler2D u_texture;
					+ "void main()
					+ "{
					+ " gl_FragColor = v_Color * texture2D(u_texture, v_texCoords);
					+ "}";
			meshShader = new ShaderProgram(vertexShader, fragmentShader);
			if (meshShader.isCompiled() == false)
				throw new IllegalStateException(meshShader.getLog());
		}
		public void dispose() {
		}
		public void pause() {
		}
		public void render() {
			frameBuffer.begin();
			Gdx.graphics.getGL20().glViewport(0 , 0 , frameBuffer.getWidth(),
			frameBuffer.getHeight());
			Gdx.graphics.getGL20().glClearColor(0f, 1f, 0f, 1 );
			Gdx.graphics.getGL20().glClear(GL20.GL_COLOR_BUFFER_BIT);
			Gdx.graphics.getGL20().glEnable(GL20.GL_TEXTURE_2D);
			texture.bind();
			meshShader.begin();
			meshShader.setUniformi("u_texture" , 0 );
			mesh.render(meshShader, GL20.GL_TRIANGLES);
			meshShader.end();
			frameBuffer.end();
			Gdx.graphics.getGL20().glViewport(0 , 0 , Gdx.graphics.getWidth(),
			Gdx.graphics.getHeight());
			Gdx.graphics.getGL20().glClearColor(0 .2f, 0 .2f, 0 .2f, 1 );
			Gdx.graphics.getGL20().glClear(GL20.GL_COLOR_BUFFER_BIT);
			spriteBatch.begin();
			spriteBatch.draw(frameBuffer.getColorBufferTexture(), 0 , 0 , 256 );
		}
	}
}
```
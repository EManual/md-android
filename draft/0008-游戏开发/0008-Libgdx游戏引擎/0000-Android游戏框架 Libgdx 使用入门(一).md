Libgdx是一款支持2D与3D游戏开发的游戏类库，兼容大多数微机平台(标准JavaSE实现，能运行在Mac、Linux、Windows等系统)与Android平台(Android1.5以上即可使用，Android2.1以上可满功率发挥)，
Libgdx由audio、files、graphics、math、physics、scenes、utils这些主要类库所组成，它们分别对应了Libgdx中的音频操作，文件读取，2D/3D渲染，Libgdx绘图相关运算，Box2D封装，2D/3D游戏组件(3D部分目前无组件)，以及Libgdx内置工具类。
下面开始，我将就Libgdx的具体实现，开始讲解如何正确使用Libgdx类库。
不过在正式开始之前，我们首先还得讲讲Gdx类。
#### 关于Libgdx中的Gdx类：
单从表面上看，Gdx类占用空间不足2KB，甚至不具备一行可以被直接执行的函数，并没什么重要好说。
然而，真实的Gdx却是Libgdx类库运行的核心所在，没有它你将寸步难行，不单运行Graphics、Input、Files、Audio、AndroidApplication等Libgdx关键部分所必需的实例会在Libgdx初始化时注入Gdx中对应的graphics、input、files、audio、app等静态变量里面，就连Libgdx对OpenGL接口(或OpenGLES，视Libgdx运行平台而定，以下统称OpenGL)的GL10、GL11、GL20、GLCommon等封装类也会在Graphics实例化时分别注入到gl10、gl11、gl20、gl这四个同样位于Gdx的静态变量当中(在Graphics中也会继续保留它们的引用，因此无论你执行Gdx.graphics.getGL10还是Gdx.gl10，其实都在调用同一个静态变量)。事实上，如果你想不使用Gdx而正常运行Libgdx，那么除了重构源码，就再没有任何办法可想了。
PS:如果你不清楚自己究竟在什么环境使用Libgdx，其实也不必强分gl10或gl11，大可以通过Gdx.gl方式调用Libgdx中对于OpenGL接口的默认封装(执行某些非多版本共有接口时，依旧需要使用对应版本专属gl)。
想要使用Libgdx，却不明白Gdx是干什么用的，那么一切就都是空谈。
下面开始，我将具体讲解Libgdx中的图像处理与游戏组件部分：
#### 关于Libgdx的图像处理部分：
#### Mesh:
本质上讲，Libgdx中所有可见的3D物体首先都是一个Mesh(网格，或者说三维网格形式的高级图元)。Mesh是如何生成的呢?众所周知，数学上讲的立体几何由点、线、面三部分组成，无论多么复杂的图像也可以分解为无数细小的这三部分，或者说可以由非常基础的N个这三部分所组合而成;到了3D游戏开发时，当我们要构建复杂的3D图像，首先会以一系列有序的vertices(顶点)构成这些具体的点、线、三角要素，即构成绘图基本图元(Primitives)，再将基本图元组合成更完整的高级图元也就是具体3D对象。因此，如果对Mesh概念进行简单的理解，其实它就是一个象征完整图像的基本图元集合体，Libgdx先让我们把一个个细分的vertices组成基本图元，再由Mesh类将基本图元制成更加复杂的高级图元展示出来。
PS:如果对此类认识不足，可以去玩玩模拟人生，下个修改器尝试编辑角色或物品造型后就懂了
#### Texture:
Libgdx所提供的游戏纹理用类，其实质可理解为保存在显存中的Image，它以贴图的方式通过OpenGL将图片显示到游戏界面之上。Libgdx的纹理可以直接从指定文件路径加载，也可以通过它提供的Pixmap类凭空创建(它的Texture(int width, int height, Format format)构造内部直接调用了Pixmap，不是必须在外部生成Pixmap后注入)。另外在加载Texture时，个人建议通过Libgdx提供的TextureDict.loadTexture函数调用，该方法内部提供了Texture缓存管理，能够避免无意义的资源重复加载。此外，Texture通常会与TextureRegion类配套使用，利用TextureRegion包装Texture后，再利用SpriteBatch进行绘制，可以很方便的修订Texture为我们需要的显示范围。还有，Libgdx中Sprite类为TextureRegion子类，因此能够将Sprite当作TextureRegion来使用，只是Sprite类比TextureRegion有所扩展。不过Libgdx的SpriteCache类并没有继承Sprite或TextureRegion，所以起不到TextureRegion的作用，只能构建一组静态贴图集合罢了，特此说明。
```  
// Libgdx的Texture与Sprite使用
public class Main extends AndroidApplication {
	class TestSprite implements ApplicationListener {
		// 准备绘图用SpriteBatch
		SpriteBatch spriteBatch;
		// 准备游戏精灵
		Sprite sprite;
		// 准备图片加载用Texture
		Texture texture;
		public void create() {
			// 构建SpriteBatch
			spriteBatch = new SpriteBatch();
			// 构建Texture，图像宽与高大小都必须为2的整数次幂，否则提示异常
			// PS:在Android环境使用Libgdx的internal加载时必须文件必须位于assets目录下
			texture = new Texture(Gdx.files.internal("mySprite.png"));
			// 以指定Texture构建Sprite
			sprite = new Sprite(texture);
		}
	}
}
```
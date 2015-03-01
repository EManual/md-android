在libgdx引擎中，有一个专门用于生成粒子特效的小工具，叫做particle-editor，利用该工具可以方便的生成一个适用于libgdx的粒子特效脚本文件。打开工具，如下图所示：
![img](P)  
(注：可以点击open，选择一张图片作为粒子，然后设置各个参数，在此不做详述)
点击左下角Save按钮，将该特效的脚本文件保存在本地中，例如我们保存为particle.p，保存完成后，可以看到该脚本文件的内容。好了，下面我们将把这个粒子特效加入到我们的程序中。
1.将particle.p和你所选择的图片复制到资源的data目录下。（注：需要将particle.p中最后图片的位置修改为当前目录）
2.新建类HelloParticle。代码如下
```  
public class HelloParticle implements ApplicationListener {
	SpriteBatch spriteBatch;
	ParticleEffect particle;
	public void create() {
		spriteBatch = new SpriteBatch();
		particle = new ParticleEffect();
		particle.load(Gdx.files.internal("data/particle.p"),
				Gdx.files.internal("data/"));
		particle.start();
		particle.setPosition(100, 100);
	}
	public void resume() {
	}
	public void render() {
		int centerX = Gdx.graphics.getWidth() / 2;
		int centerY = Gdx.graphics.getHeight() / 2;
		Gdx.graphics.getGL10().glClear(GL10.GL_COLOR_BUFFER_BIT);
		spriteBatch.begin();
		spriteBatch.setColor(Color.WHITE);
		particle.draw(spriteBatch, Gdx.graphics.getDeltaTime());
		spriteBatch.end();
	}
	public void resize(int width, int height) {
		spriteBatch.getProjectionMatrix().setToOrtho2D(0, 0, width, height);
	}
	public void pause() {
	}
	public void dispose() {
	}
}
```
3.在main方法中启动。
```  
public static void main (String[] argv) {
	new JoglApplication(new HelloParticle(), "Hello World", 480, 320, false);
}
```
好了，下面是运行效果：
![img](P)  
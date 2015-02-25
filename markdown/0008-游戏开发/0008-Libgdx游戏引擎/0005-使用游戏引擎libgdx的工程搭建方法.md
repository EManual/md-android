首先当然是下载引擎，网址：http://code.google.com/p/libgdx/
1.新建java工程，如GameEngine
2.在工程下新建libs文件夹，将gdx.jar,gdx-backend-jogl.jar,gdx-sources.jar,gdx.dll,libgdx.so,libgdx-64.so放入
3.右击项目，选择属性->javabuild path->Libraries->Add jars,将libs下的jar文件加入
4.新建一个包，然后建立两个类，代码如下
```  
public class MyFirstTriangle implements ApplicationListener {
	@Override
	public void create() {
	}
	@Override
	public void dispose() {
	}
	@Override
	public void pause() {
	}
	@Override
	public void render() {
	}
	@Override
	public void resize(int width, int height) {
	}
	@Override
	public void resume() {
	}
}
```
5.如果发现异常，打开工程目录下.classpath文件，修改如下：
```  
<?xml version="1.0" encoding="UTF-8"?>
<classpath>
    <classpathentry
        kind="src"
        path="src" />
    <classpathentry
        kind="con"
        path="org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.6" />
    <classpathentry
        kind="lib"
        path="libs/gdx-backend-jogl.jar" />
    <classpathentry
        kind="lib"
        path="libs/gdx.jar"
        sourcepath="libs/gdx-sources.jar" >
        <attributes>
            <attribute
                name="org.eclipse.jdt.launching.CLASSPATH_ATTR_LIBRARY_PATH_ENTRY"
                value="GameEngine/libs" />
        </attributes>
    </classpathentry>
    <classpathentry
        kind="output"
        path="bin" />
</classpath>
```
编译运行，成功……
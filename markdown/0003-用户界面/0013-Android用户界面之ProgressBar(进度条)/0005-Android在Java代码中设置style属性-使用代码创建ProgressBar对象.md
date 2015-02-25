在Andriod开发中，很大一部分都要与资源打交道，比如说：图片，布局文件，字符串，样式等等。这给我们想要开发一些公共的组件带来很大的困难，因为公共的组件可能更愿意以jar包的形式出现。但是java的jar包中只允许出现java代码而不能出现资源。
当我们想要以jar包的形式提供我们自己开发的公共组件时，我们就需要把以代码的形式创建资源。
下面提供一个使用全Java代码的形式创建一个ProgressBar。
ProgressBar默认的样式是一个圈圈，我们要想其显示为进度条的样式可以在布局文件中使用如下代码：
```  
<ProgressBar android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	style="?android:attr/progressBarStyleHorizontal"/>
```
上面的关键代码是红色的部分，这部分的代码就是使得ProgressBar由转圈圈的样式变成进度条的样式。使用这种方式创建的ProgressBar不能包含在jar包中。
同样我们也可以使用纯代码的形式创建ProgressBar对象，如下：
```  
ProgressBar progressBar = new ProgressBar(context);
LineanerLayout layout = new LinearLayout(context);
layout.addView(progressBar, new LayoutParam(LayoutParam.FILL_PARENT, LayoutParam.FILL_PARENT));
```
这样就使用纯代码的方式创建了一个ProgressBar对象，但是他还只是默认的样式一个不停的转的圈圈。
这时我们可能都会想到好像没有设置样式。我们可以把之前的那个样式设进去，但是我们找遍API发现View并没有提供任何给我们设置样式的方法。
其实样式就是通过一种方式给一个View或一组View设置一些共同的属性值，所以不可能能使用代码来设置。
我们可以看下progressBarStyleHorizontal样式中给View设置了哪些属性，我们找到framework下的res目录下的values/Theme.xml文件，搜索progressBarStyleHorizontal会发现如下行：
```  
<item name="progressBarStyleHorizontal">@android:style/Widget.ProgressBar.Horizontal</item>
```
该主题对应的Widget样式是Widget.ProgressBar.Horizontal，我们在同样的的目录下打开style.xml文件，搜索该样式，可以找到如下代码：
```  
<style name="Widget.ProgressBar.Horizontal">
	<item name="android:indeterminateOnly">false</item>
	<item name="android:progressDrawable">@android:drawable/progress_horizontal</item>
	<item name="android:indeterminateDrawable">@android:drawable/progress_indeterminate_horizontal</item>
	<item name="android:minHeight">20dip</item>
	<item name="android:maxHeight">20dip</item>
</style>
```
也就是progressBarStyleHorizontal样式实际上就是设置了如上的属性，我们直接在布局文件中把如上的值设进去，代码看起来如下：
```  
<ProgressBar android:layout_width="fill_parent"
	android:layout_height="wrap_content"
	android:indeterminateOnly="false"
	android:progressDrawable="@android:drawable/progress_horizontal"
	android:indeterminateDrawable="@android:drawable/progress_indeterminate_horizontal"
	android:minHeight="20dip"
	android:maxHeight="20dip" />
```
这时运行我们的程序，发现ProgressBar已从圈圈变成进度条的样式。这时我们可以在代码中把这些属性设成布局文件中的值，纯Java代码看起来应该如下面的那样：
```  
ProgressBar progressBar = new ProgressBar(this);
progressBar.setIndeterminate(false);
progressBar.setProgressDrawable(getResources().getDrawable(android.R.drawable.progress_horizontal));
progressBar.setIndeterminateDrawable(getResources().getDrawable(android.R.drawable.progress_indeterminate_horizontal));
progressBar.setMinimumHeight(20);
LinearLayout layout = new LinearLayout(this);
layout.addView(progressBar, new LayoutParams(LayoutParams.FILL_PARENT, LayoutParams.WRAP_CONTENT));
setContentView(layout);
```
这时我们发现ProgressBar确实变成了横条，但并没有显示成进度条的样子，我们仔细对比一下纯Java代码和xml布局文件之间差异，我们发现android:indeterminateOnly="false"和 progressBar.setIndeterminate(false);并不完全一样布局文件的属性有一个Only结尾但代码中并没有，我们查找Api发现并没有setIndeterminateOnly这样的一个方法。
我们打开ProgressBar的源代码，找到.setIndeterminate(false) 方法，方法的代码如下：
```  
public synchronized void setIndeterminate(boolean indeterminate) {
	if ((!mOnlyIndeterminate || !mIndeterminate)
			&& indeterminate != mIndeterminate) {
		mIndeterminate = indeterminate;
		if (indeterminate) {
			// swap between indeterminate and regular backgrounds
			mCurrentDrawable = mIndeterminateDrawable;
			startAnimation();
		} else {
			mCurrentDrawable = mProgressDrawable;
			stopAnimation();
		}
	}
}
```
我们这时候可以发现Indeterminate和IndeterminateOnly并不是同一个东西，这时我们应该想的到，只要我们把IndeterminateOnly的值变成false就可以使ProgressBar变成进度条的样式，我们查找所有的代码，发现并没有提供相应的公开方法来修改该属性的值。
也就是说，我们讨论了那么久发现根本就无法通过纯代码的形式来创建一个进度条样式的ProgressBar。
但是。。。
不就是改变一个类的私有变量的值嘛，Java的封装性其实并没有我想的那么好，我们完全可以通过反射机制来修改一个对象的私有变量的值，由于该文章并不是讨论反射的的文章，所以这里只给出通过反射来修改私有变量值的代码，但并不作详细的说明：
我们创建一个新的类，叫BeanUtils.java
类得内容看其来如下：
```  
public class BeanUtils {
	private BeanUtils() {
	}
	[Tags]/**
	 [Tags]* 直接设置对象属性值,无视private/protected修饰符,不经过setter函数.
	 [Tags]*/
	public static void setFieldValue(final Object object,
			final String fieldName, final Object value) {
		Field field = getDeclaredField(object, fieldName);
		if (field == null)
			throw new IllegalArgumentException("Could not find field [
					+ fieldName + "] on target [" + object + "]");
		makeAccessible(field);
		try {
			field.set(object, value);
		} catch (IllegalAccessException e) {
			Log.e("zbkc", "", e);
		}
	}
	[Tags]/**
	 [Tags]* 循环向上转型,获取对象的DeclaredField.
	 [Tags]*/
	protected static Field getDeclaredField(final Object object,
			final String fieldName) {
		return getDeclaredField(object.getClass(), fieldName);
	}
	[Tags]/**
	 [Tags]* 循环向上转型,获取类的DeclaredField.
	 [Tags]*/
	@SuppressWarnings("unchecked")
	protected static Field getDeclaredField(final Class clazz,
			final String fieldName) {
		for (Class superClass = clazz; superClass != Object.class; superClass = superClass
				.getSuperclass()) {
			try {
				return superClass.getDeclaredField(fieldName);
			} catch (NoSuchFieldException e) {
				// Field不在当前类定义,继续向上转型
			}
		}
		return null;
	}
	[Tags]/**
	 [Tags]* 强制转换fileld可访问.
	 [Tags]*/
	protected static void makeAccessible(Field field) {
		if (!Modifier.isPublic(field.getModifiers())
				|| !Modifier.isPublic(field.getDeclaringClass()
						.getModifiers())) {
			field.setAccessible(true);
		}
	}
}
```
该工具提供一个共有的方法：public static void setFieldValue(final Object object, final String fieldName, final Object value)来修改一个对象的私有变量的值：
这时我们的ProgressBar代码看起来应该如下：
```  
ProgressBar progressBar = new ProgressBar(this);
BeanUtils.setFieldValue(progressBar, "mIndeterminateOnly", new Boolean(false));
progressBar.setIndeterminate(false);
progressBar.setProgressDrawable(getResources().getDrawable(android.R.drawable.progress_horizontal));
progressBar.setIndeterminateDrawable(getResources().getDrawable(android.R.drawable.progress_indeterminate_horizontal));
progressBar.setMinimumHeight(20);
LinearLayout layout = new LinearLayout(this);
layout.addView(progressBar, new LayoutParams(LayoutParams.FILL_PARENT, LayoutParams.WRAP_CONTENT));
setContentView(layout);
```
到此为止我们终于使用纯java代码的方式创建了一个ProgressBar的进度条样式。
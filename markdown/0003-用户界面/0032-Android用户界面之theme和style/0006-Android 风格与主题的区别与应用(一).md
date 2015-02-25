Android xml风格和主题文件的编写，是涉及到整个程序界面美观的因素之一。较好的应用风格和主题，可以实现美观而统一的界面，这就犹如Web开发中的CSS。
Styles和Themes都是资源，存放在res/values文件夹下。
#### 什么是Style，什么是Theme?
Style：是一个包含一种或者多种格式化属性的集合，我们可以将其用为一个单位用在布局XML单个元素当中。比如，我们可以定义一种风格来定义文本的字号大小和颜色，然后将其用在View元素的一个特定的实例。
Theme：是一个包含一种或者多种格式化属性的集合，我们可以将其为一个单位用在应用中所有的Activity当中或者应用中的某个Activity当 中。比如，我们可以定义一个Theme，它为window frame和panel 的前景和背景定义了一组颜色，并为菜单定义可文字的大小和颜色属性，可以将这个Theme应用在你程序当中所有的Activity里。
Style和Theme的XML文件结构，对每一个Styles和Themes，给<style>元素增加一个全局唯一的名字，也可以选择增加一个父类属性。在后边我们可以用这个名字来应用风格，而父类属性标识了当前风格是继承于哪个风格。在<style>元素内部，申明一个或者多个<item>，每一个<item>定义了一个名字属性，并且在元素内部定义了这个风格的值。
#### 风格
1.在res/values 目录下新建一个名叫style.xml的文件。
2.对每一个风格和主题，给<style>element增加一个全局唯一的名字，也可以选择增加一个父类属性。在后边我们可以用这个名字来应用风格，而父类属性标识了当前风格是继承于哪个风格。
3.在<style>元素内部，申明一个或者多个<item>,每一个<item>定义了一个名字属性，并且在元素内部定义了这个风格的值。
4.你可以应用在其他XML定义的资源。
下面SDK提供的Style的例子：
```  
<?xml version="1.0″ encoding="utf-8″?>
<resources>
	<style name="SpecialText" parent="@style/Text">
		<item name="android:textSize">18sp</item>
		<item name="android:textColor">008</item>
	</style>
</resources>
```
上面的样式可以用在单个view中如：
```  
<EditText
    id="@+id/text1"
    style="@style/mytext"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    World=""
    android:text="Hello," />
```
现在这个EditText组件的所表现出来的风格就为我们在上边的XML文件中所定义的那样。编写一个简单的Style：
```  
<?xml version="1.0″ encoding="utf-8″?>
<resources>
	<style name="SpecialText" >
		<item name="android:textSize">18sp</item>
		<item name="android:textColor">EC9237</item>
	</style>
	<style name="SpecialText2″ >
		<item name="android:textSize">26sp</item>
		<item name="android:textColor">FF7F7C</item>
		<item name="android:fromAlpha">0.0</item>
		<item name="android:toAlpha">0.0</item>
	</style>
</resources>
```
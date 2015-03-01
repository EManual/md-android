在styles.xml文件中加入下面两个样式，然后在需要的地方设置TextView的style
```  
<!-- 文字白色阴影style-->
<style name="text_white_shadow">
	<item name="android:shadowColor">@color/white</item>
	<item name="android:shadowDy">-2</item>
	<item name="android:shadowRadius">1</item>
</style>
<!-- 文字黑色阴影style-->
<style name="text_black_shadow">
	<item name="android:shadowColor">@color/black</item>
	<item name="android:shadowDy">-2</item>
	<item name="android:shadowRadius">1</item>
</style>
```
Android手机操作系统是一个开源的操作系统。其应用方式灵活方便，极受广大编程人员的喜爱。我们在这里就可以先来了解一下Android Theme的具体用法。Android Theme的用法与style的差不多，不过，具体还是有些不一样的。
首先在values下新建一个theme。xml，用来定义需要的theme了，代码如下：
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<style name="theme" parent="android:Theme.Black">
		<item name="android:windowNoTitle">true</item>
		<item name="android:textSize">14sp</item>
		<item name="android:textColor">FFFF0000</item>
	</style>
</resources>
```
Android Theme的用法是不是跟style的很相似？
然后你可以在需要的那个activity上引用，加上
```  
setTheme(R.style.theme);
```
这句代码就ok了。
当然，你也可以在AndroidManifest.xml中定义把主题运用到整个application或者具体的activity中，代码：
```  
<application 
	android:icon="@drawable/icon"
	android:label="@string/app_name"
	android:theme="@style/theme">
```
在application属性加上一句就可以了。如果你需要为特定的activity指定Android Theme，你只要把这句加到AndroidManifest.xml中activity中的属性就。
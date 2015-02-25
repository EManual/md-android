在mainfest.xml中应用主题：
为了在成用当中所有的Activity当中使用主题，你可以打开AndroidManifest.xml文件，编辑<application>标签，让其包含android:theme属性，值是一个主题的名字，如下：
```  
<application android:theme="@style/CustomTheme">
```
如果你只是想让你程序当中的某个Activity拥有这个主题，那么你可以修改<activity>标签。
编写的简单的一个Theme：
```  
<?xml version="1.0″ encoding="utf-8″?>
<resources>
	<style name="CustomTheme" parent="android:Theme.Black">
		<item name="android:windowNoTitle">true</item>
		<item name="android:testSize">14sp</item>
		<item name="android:textColor">FFFF0000</item>
	</style>
</resources>
```
Android中提供了几种内置的资源，有好几种主题你可以切换而不用自己写。比如你可以用对话框主题来让你的Activity看起来像一个对话框。在manifest中定义如下：
```  
<activity android:theme="@android:style/Theme.Dialog">
```
如果你喜欢一个主题，但是想做一些轻微的改变，你只需要将这个主题添加为父主题。比如我们修改Theme.Dialog主题。我们来继承Theme.Dialog来生成一个新的主题。
```  
<style name="CustomDialogTheme" parent="@android:style/Theme.Dialog">
```
继承了Theme.Dialog后，我们可以按照我们的要求来调整主题。我们可以修改在Theme.Dialog中定义的每个item元素的值，然后我们在Android Manifest 文件中使用CustomDialogTheme 而不是Theme.Dialog.
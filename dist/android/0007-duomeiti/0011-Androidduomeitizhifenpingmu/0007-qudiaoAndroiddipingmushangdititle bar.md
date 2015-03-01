我们在开发的时候，都会遇到一个问题，这个问题有的时候我们是必须解决的。那是什么问题那，就是我们每个activity会默认带上一个title bar用来显示程序的名字，有的时候我们为了扩大屏幕的显示区域。我们需要去掉title bar，那么我们有什么办法来实现那，我们一共有三个方法可以去掉title bar，那我们就来看看这三个方法都是这么样来实现去掉title bar。
第一个方法是在代码去掉title bar
在Activity的onCreate中加入如下代码:
```  
this.requestWindowFeature(Window.FEATURE_NO_TITLE);
```
这个方法很简单，但是这个方法有一个不好的地方就是，当在Activity将要显示的时候，仍然会出现title bar，出现title bar了。我们又得从新用到上面的代码，这样的话，翻来覆去的使用，一个是麻烦，一个是给用户视觉上带来了不好的影响，eoe建议开发者还是不要用这种方法。
第二种方法是使用style配置文件，步骤如下:
1.在res/values文件夹下创建一个xml文件，名为mainStyle.xml，内容如下:
```  
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
	<style name="NoTitle" parent="android:Theme">
		<item name="android:windowNoTitle">true</item>
	</style>
</resources>
```
2.然后在AndroidManifest.xml中需要去掉title bar的activities的节点上加上一个样式属性,代码如下:
```  
<activity
    android:name=".view.AutoTaskDemo"
    android:configChanges="keyboardHidden|orientation|locale"
    android:label="@string/app_name"
    android:theme="@style/NoTitle" >
```
这个方法比较好，也没有像第一个方法那样出现很多的问题，第二种方法我们直接在xml里就可以解决去掉title bar的问题，这样我们就会节省很多的时间在main代码当中，这样直观看main代码也不会很乱，本人比较喜欢像这样的方法，能解决问题还能节约很多的时间，大家以后都得这样，不要找那些很繁琐的解决方法，到最后你写在main里，你都不知道这个是干什么用的了，这样是不是很麻烦，无形中给我们增添了很多要解决的问题。下面我们再说说第三种方法吧，这种方法也是很好的，简单而且还很方便。
第三种方法是直接在AndroidManifest.xml中进行修改，
这种方法就是我们要到AndroidManifest.xml中来解决，这个方发比第二个还要简单，但是我们在AndroidManifest.xml中的title bar的activities的节点上加上一个样式属性，我们写上了这个属性的话，title bar就不会出现了，添加的这个属性就是有点像给title bar屏蔽了，这样我们的activity中就不会出现title bar了。这个也是解决的方法。但是当大家在AndroidManifest.xml里定义的权限多了的时候，大家可千万不要加载错了，如果加载错了的话，那可就不得了，因为你可能一下就把你所有的编写程序给整瘫痪了，这样你就得从新的该，这样也会给开发者代码不少麻烦，eoe在这里嘱咐大家千万要细心。那么我们就来看看代码吧。
```  
<activity
    android:name=".view.SettingActivity"
    android:configChanges="keyboardHidden|orientation"
    android:theme="@android:style/Theme.NoTitleBar" />
```
我们也可以在AndroidManifest.xml文件的application节点上修改，对所有的activity都有效，代码如下:
```  
<application
    android:icon="@drawable/icon"
    android:label="@string/app_name"
    android:theme="@android:style/Theme.NoTitleBar" >```
以上就是我们怎么样把android的屏幕上的title bar去掉，大家可一看出，一般的不会用第一种方法了，如果用第一种方法那会给自己代码不少的麻烦，也不建议大家用。大家还是最好用第二种和第三种方法。
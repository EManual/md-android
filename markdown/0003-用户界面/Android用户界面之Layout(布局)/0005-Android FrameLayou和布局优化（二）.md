这里要注意的是，"@layout/relative"不是引用Layout的id，而是引用res/layout/relative.xml，其内容是前面文章介绍RelativeLayout的布局代码。
另外，通过<include>，除了可以覆写id属性值，还可以修改其他属性值，例如android:layout_width，android:height等。
<viewstub> 延迟加载
ViewStub是一个不可见的，大小为0的View，最佳用途就是实现View的延迟加载，在需要的时候再加载View，可Java中常见的性能优化方法延迟加载一样。
当调用ViewStub的setVisibility函数设置为可见或则调用inflate初始化该View的时候，ViewStub引用的资源开始初始化，然后引用的资源替代ViewStub自己的位置填充在ViewStub的位置。因此在没有调用setVisibility(int)或则inflate()函数之前ViewStub一种存在组件树层级结构中，但是由于ViewStub非常轻量级，这对性能影响非常小。 可以通过ViewStub的inflatedId属性来重新定义引用的layout id。 例如：
```  
<ViewStub android:id="@+id/stub" 
	android:inflatedId="@+id/subTree"
	android:layout="@layout/mySubTree"
	android:layout_width="120dip"
	android:layout_height="40dip" />
```
上面定义的ViewStub，可以通过id“stub”来找到，在初始化资源“mySubTree”后，stub从父组件中删除，然后"mySubTree"替代stub的位置。初始资源"mySubTree"得到的组件可以通过inflatedId 指定的id "subTree"引用。 然后初始化后的资源被填充到一个120dip宽、40dip高的地方。
推荐使用下面的方式来初始化ViewStub：
```  
ViewStub stub = (ViewStub) findViewById(R.id.stub); 
View inflated = stub.inflate();
```
当调用inflate()函数的时候，ViewStub 被引用的资源替代，并且返回引用的view。这样程序可以直接得到引用的view而不用再次调用函数 findViewById()来查找了。
ViewStub目前有个缺陷就是还不支持 <merge /> 标签。
layoutopt (Layout Optimization工具)
这工具可以分析所提供的Layout，并提供优化意见。在tools文件夹里面可以找到layoutopt.bat。
用法：
```  
layoutopt  <list of xml files or directories>
```
参数：
一个或多个的Layout xml文件，以空格间隔；或者多个Layout xml文件所在的文件夹路径
例子：
```  
layoutopt G:\StudyAndroid\UIDemo\res\layout\main.xml
layoutopt G:\StudyAndroid\UIDemo\res\layout\main.xml G:\StudyAndroid\UIDemo\res\layout\relative.xml
layoutopt G:\StudyAndroid\UIDemo\res\layout
```
Android操作系统已经被非常多的手机硬件所采用。就目前推出的第一款安装有Android操作系统的T-Mobile G1，在G1上可以体验到旋转手机从而实时的改变屏幕显示模式，比如我们打开硬件键盘，屏幕将会实时的从纵向显示转变为横向显示。
为了达到简化开发的目的，可以在屏幕变化时自动保存现有的资源或者一些必要的信息从而在变化后可以很快的根据新的标准重新恢复Activity所包含的内容。Android基于这方面的考虑预置了一种可以调用特定资源从而匹配屏幕变化的行为，比如说标记横向屏幕所对应的layout或者一些 drawables资源。
对于开发者来讲需要非常重视对这个预置的功能应用，因为它可以使应用程序实时根据屏幕的调整做出最合理的匹配方案。桌面从纵向调整为横向显示需要明确Activity是如何做出反应的，这对于一些刚刚接触Android平台的人来讲在理解上会有一些困难，Activity响应显示模式变化要经过 destroyed和recreated两个过程，通过recreated可以出发程序对最新的显示模式做出反应。
当应用程序在运行时需要表现大量的数据或者需要通过网络加载某些资源时，如果我们在显示模式的变化中不做任何干预，那么同样的应用程序将需要重新加载必要的预置数据，这样每次改变显示模式都将花费同样的时间来重复加载相同的资源。
通过一个更典型的例子来理解这个过程，Photostream通过网络下载6幅图片并显示在屏幕中，并且针对两种显示模式提供相对的layout和drawables匹配方案：
在这个例子中可以清楚的理解，我们不希望用户在改变现实模式时重新加载大量的数据。那么针对这样的问题我们可以借鉴一些主流浏览器所采取的方法，建立一个暂存区来保留临时文件（例如检测到设备提供SD Card，从而把临时文件存放在里边），这样我们可以再重新改变现实模式后可以通过临时文件快速的重新加载数据内容。那么通过什么方式才能优化是否为程序提供暂存文件的功能呢？（可能有些程序并不需要这样必须的行为，所以没有必要额外的利用外界的存储设备）值得庆幸的是Android为我们提供了onRetainNonConfigurationInstance()方法，Activity的基类中声明了这个虚函数，可以让开发者在自己的应用程序中决定是否实现这个函数的函数体，借用上边的例子我们可以用如下代码来保存临时图片：
```  
@Override
public Object onRetainNonConfigurationInstance() {
	final LoadedPhoto[] list = new LoadedPhoto[numberOfPhotos];
	keepPhotos(list);
	return list;
}
```
之后便可以在activity的onCreate()函数中实现重新调用临时文件，在代码中需要判断系统是否需要重新加载临时文件。以下是放在OnCreate()函数中加载临时文件的代码：
```  
private void loadPhotos() {
	final Object data = getLastNonConfigurationInstance();
	if (data == null) {
		mTask = new GetPhotoListTask().execute(mCurrentPage);
	} else {
		final LoadedPhoto[] photos = (LoadedPhoto[]) data;
		for (LoadedPhoto photo : photos) {
			addPhoto(photo);
		}
	}
}
```
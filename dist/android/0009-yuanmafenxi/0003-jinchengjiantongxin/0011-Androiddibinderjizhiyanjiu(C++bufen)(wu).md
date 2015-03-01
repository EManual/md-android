首先是一个宏定义CHECK_INTERFACE()，这个宏定义的作用是检查接口的描述字符串，这个前面也提到过，不需细说。然后就是onTrasact()函数的实现。这个函数的结构也很简单，就是根据参数code的值分别执行不同的功能调用。code的取值就是前面提到过的接口功能代码。函数的参数除了code，还包括Parcel类的两个对象data和reply,分别用于传送输入参数和返回数据，与transact()函数的参数相对应。还有一个参数flag在这里用不上，不讨论。对应我们前面所选择的接口函数的例子create(url)，看看这边对应的实现：
```  
case CREATE_URL: {
	CHECK_INTERFACE(IMediaPlayerService, data, reply);
	pid_t pid = data.readInt32();
	sp<IMediaPlayerClient> client = interface_cast<IMediaPlayerClient>(data.readStrongBinder());
	const char* url = data.readCString();
	sp<IMediaPlayer> player = create(pid, client, url);
	reply->writeStrongBinder(player->asBinder());
	return NO_ERROR;
}
```
首先是从data对象中依次取出各项输入参数，然后调用接口函数create()(将在子类MediaPlayerService中实现)，最后向reply中写入返回数据。这个函数返回后，代理那边的transact()也会跟着返回。
那么onTransact()函数是怎么被调用的呢？通过查看BnInterface模板类的定义可以看到，这个类也是一个多重继承类，另一个父类是BBinder(frameworks\base\include\utils\Binder.h，frameworks\base\libs\utils\Binder.cpp)。BBinder类继承自IBinder，也实现了transact()函数，在这个函数中调用onTransact()函数。而BBinder对象的transact()函数则是在IPCThreadState类的executeCommand()成员函数中调用的。这已经涉及到较底层的实现，在这里不再多说。
上面这部分代码还与前面提到过的BpBinder对象的创建有关系。看其中的红色字体部分，通过create()函数调用会创建一个IMediaPlayer接口类的子类的对象，这个对象其实是MediaPlayerService::Client类(可以看一下MediaPlayerService的定义)的对象实例，而MediaPlayerService::Client类是继承自BnMediaPlayer类的，与BnMediaPlayerService类类似，BnMediaPlayer其实也是一个binder实现类(是BBinder的子类，进而也是IBinder的子类)。在上述代码中，通过Parcel的writeStrongBinder()函数将这个对象写入reply，而在代理侧，通过Parcel的readStrongBinder()函数读取则可以得到一个BpBinder的对象。至于类的具体创建过程已经封装在Parcel类的定义中，这里就不再多说了。
#### (4) 接口功能的真正实现
到这里两个binder类就已经定义完了，下面就是IMediaPlayerService接口函数的真正实现。前面已经说过这些函数在类MediaPlayerService中实现。这个类继承自BnMediaPlayerService，也间接地继承了IMediaPlayerService接口类定义的6个功能函数，只需要按照正常方式实现这6个功能函数即可，当然为了实现这6个函数就需要其它一大堆的东西，不过这些具体的实现方法已经与binder机制无关，不再多说。
在MediaPlayerService类中定义了一个静态函数instantiate()，在这个函数中创建MediaPlayerService的对象实例，并将这个对象注册到服务管理器中。这样需要使用的时候就可以从服务管理器获取IMediaPlayerService的代理对象。这个instantiate()是在MediaServer程序的main()函数中调用的。
```  
void MediaPlayerService::instantiate() {
	defaultServiceManager()->addService(
	String16("media.player"), new MediaPlayerService());
}	
```
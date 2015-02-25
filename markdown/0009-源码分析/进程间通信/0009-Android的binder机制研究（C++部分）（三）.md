这个接口函数的参数指定了一个URL，函数将为这个URL创建一个播放器实例用于播放该URL。
函数首先定义了两个局部变量data和reply，变量的类型都是Parcel。Parcel是一个专为binder通信的数据传送而定义的类，该类提供了对多种类型的数据的封装功能，同时提供多个数据读取和写入函数，用于多种类型的数据的写入和读取，支持的数据类型既包括简单数据类型，也包括对象。这里定义的变量data是用于封装create()函数调用所需要的输入参数，而reply则是用于封装调用的返回数据(包括输出参数的值和函数返回值)。
函数首先向data中写入各种数据。第一个写入的是接口的一个描述字符串，binder的实现类中会用这个字符串来对接口做验证，防止调用错误。这个字符串也可以不写，如果不写，在binder实现类中相应的也就不要做验证了。跟在描述字符串后面写入的是该接口函数所需要的各种的输入参数。需要说明的是，Pacel提供一种先入先出的数据存储方式，即数据的写入顺序和读取顺序必须严格一致，否则将会出错。
完成数据写入后，函数调用remote()->transact()用于完成binder通信。transact()函数的第一个参数就是前面提到过的功能代码。transact()的功能是将data中的数据传给binder的实现类，函数调用结束后，reply中将包含返回数据。首先来看看remote()成员函数。前面讲到过BpMediaPlayerService通过继承BpInterface模板类间接继承了IMediaPlayerService接口类，其实BpInterface类是一个有两个父类的多重继承子类，另一个父类是BpRefbase(frameworks\base\include\utils\Binder.h)。remote()就是继承自BpRefBase类的一个成员函数，该函数返回BpRefBase类中定义的一个私有属性mRemote。mRemote是对IBinder接口类的子类BpBinder的一个对象的引用。transact()函数在IBinder接口类中定义(frameworks\base\include\utils\Binder.h)，并在BpBinder类中实现(frameworks\base\include\utils\BpBinder.h、frameworks\base\libs\utils\BpBinder.cpp)。在transact()函数中将调用IPCThreadState类的transact()函数，并进而通过Lniux内核中的android共享内存驱动来实现进程间通信。不过这些细节这里就不多说了。在这里BpBinder类对象是一个关键，是实现Binder代理的核心之一。BpBinder类可以看成是一个通信handle(类似于网络编程中的socket)，用于实现进程间通信。接下来需要研究的是这个BpBinder类对象(即mRemote成员变量的值)是从哪里来的。
回过头来BpMediaPlayerService的构造函数(看前面的代码)。该构造函数的参数是一个IBinder对象的引用。mRemote的值就是在这里传进来的这个对象。那么这个对象又是怎么来的呢？要搞清楚这一点就需要找到创建BpMediaPlayerService类的实例的代码，这个代码就就跟在该类的定义代码的下面。继续看IMediaPlayerService.cpp文件，在BpMediaPlayerService类定义的后面，是下面这样一行代码：
```  
IMPLEMENT_META_INTERFACE(MediaPlayerService, "android.hardware.IMediaPlayerService");	
```
这行代码调用了一个宏定义IMPLEMENT_META_INTERFACE()。这个宏定义与前面提到过的DECLARE_META_INTERFACE()相呼应。看名字就知道，IMPLEMENT_META_INTERFACE()宏是对DECLARE_META_INTERFACE()所定义的成员函数的具体实现。这个宏的第一个参数与DECLARE_META_INTERFACE()的参数需完全一样，第二参数是接口的描述字符串(这个字符串前面也已经讲到过了)。描述字符串不重要，重要的是宏里面定义的一个静态成员函数asInterface()。BpMediaPlayerService的类实例是在IMediaPlayerService的静态成员函数asInterface()中创建的，在IInterface.h中定义了一个内联函数interface_cast()，对这个成员函数进行了封装。通过看代码容易知道，BpMediaPlayerService的构造函数的参数是通过interface_cast()的参数传进来的。
好，下面就该看看这个interface_cast()是在哪里调用的，它的参数到底是什么。找到frameworks\base\media\libmedia\mediaplayer.cpp文件，其中的MediaPlayer::getMediaPlayerService()的实现代码：
```  
const sp<IMediaPlayerService>& MediaPlayer::getMediaPlayerService()
{
	Mutex::Autolock _l(sServiceLock);
	if (sMediaPlayerService.get() == 0) {
		sp<IServiceManager> sm = defaultServiceManager();
		sp<IBinder> binder;
		do {
			binder = sm->getService(String16("media.player"));
			if (binder != 0)
			break;
			LOGW("MediaPlayerService not published, waiting...");
			usleep(500000); // 0.5 s
		} while(true);
		if (sDeathNotifier == NULL) {
			sDeathNotifier = new DeathNotifier();
		}
		binder->linkToDeath(sDeathNotifier);
		sMediaPlayerService = interface_cast<IMediaPlayerService>(binder);
	}
	LOGE_IF(sMediaPlayerService==0, "no MediaPlayerService!?");
	return sMediaPlayerService;
}
```
看一下上面这段代码中的红色字体部分。结合前面的分析，可知BpBinder类的对象实例是从android的服务管理器的getService()函数中获取，进一步追进去，会发现下面这样一段代码：
```  
{
	Parcel data, reply;
	data.writeInterfaceToken(IServiceManager::getInterfaceDescriptor());
	data.writeString16(name);
	remote()->transact(CHECK_SERVICE_TRANSACTION, data, &reply);
	return reply.readStrongBinder();
}	
```
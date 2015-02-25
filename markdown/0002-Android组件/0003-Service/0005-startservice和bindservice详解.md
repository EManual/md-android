Service的生命周期方法比Activity少一些，只有onCreate, onStart, onDestroy 
我们有两种方式启动一个Service,他们对Service生命周期的影响是不一样的。
#### 1、通过startService 
Service会经历 onCreate --> onStart 
stopService的时候直接onDestroy 
如果是 调用者 直接退出而没有调用stopService的话，Service会一直在后台运行。 
下次调用者再起来仍然可以stopService。
#### 2、通过bindService
Service只会运行onCreate， 这个时候调用者和Service绑定在一起 
调用者退出了，Srevice就会调用onUnbind-->onDestroyed 
所谓绑定在一起就共存亡了。 
注意：Service的onCreate的方法只会被调用一次，就是你无论多少次的startService又bindService，Service只被创建一次。
如果先是bind了，那么start的时候就直接运行Service的onStart方法，如果先是start，那么bind的时候就直接运行onBind方法。如果你先bind上了，就stop不掉了，只能先UnbindService, 再StopService,所以是先start还是先bind行为是有区别的。 
Android中的服务和windows中的服务是类似的东西，服务一般没有用户操作界面，它运行于系统中不容易被用户发觉，可以使用它开发如监控之类的程序。
服务不能自己运行，需要通过调用Context.startService()或Context.bindService()方法启动服务。
这两个方法都可以启动Service，但是它们的使用场合有所不同。使用startService()方法启用服务，调用者与服务之间没有关连，即使调用者退出了，服务仍然运行。使用bindService()方法启用服务，调用者与服务绑定在了一起，调用者一旦退出，服务也就终止，大有“不求同时生，必须同时死”的特点。
如果打算采用Context.startService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方法，接着调用onStart()方法。如果调用startService()方法前服务已经被创建，多次调用startService()方法并不会导致多次创建服务，但会导致多次调用onStart()方法。采用startService()方法启动的服务，只能调用Context.stopService()方法结束服务，服务结束时会调用onDestroy()方法。
如果打算采用Context.bindService()方法启动服务，在服务未被创建时，系统会先调用服务的onCreate()方法，接着调用onBind()方法。这个时候调用者和服务绑定在一起，调用者退出了，系统就会先调用服务的onUnbind()方法，接着调用onDestroy()方法。如果调用bindService()方法前服务已经被绑定，多次调用bindService()方法并不会导致多次创建服务及绑定(也就是说onCreate()和onBind()方法并不会被多次调用)。
如果调用者希望与正在绑定的服务解除绑定，可以调用unbindService()方法，调用该方法也会导致系统调用服务的onUnbind()-->onDestroy()方法。
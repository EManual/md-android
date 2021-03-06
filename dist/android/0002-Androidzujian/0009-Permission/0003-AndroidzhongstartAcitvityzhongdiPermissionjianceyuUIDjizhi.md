我们经常在一个activity中去start另一个activity，或者与另一个acitivity的结果进行交互（startActivityForResult）。但有没有想过可能会出现的permission问题呢？如果你遇到了permission denial的Exception，那么你需要读读这篇文章啦。 
我们在同一个application内部，可以随意的startActivity from Activity A to Activity B，而官方的文档中说startActivity可能会报NotFoundException，表示被start的Activity不存在。因此，我们很容易忽略另一个可能的Exception，Permission Denial。 
当我们在不同的application中，如application A中的Activity去start一个application B中的Activity，也许你什么Exception都不会得到，也可能会直接Force Close掉。因为再Start Activity时，代码是有去检验permission的。 
如下情况，可以成功startActivity而不会得到permission denial 
1、同一个application下 
2、Uid相同 
3、permission匹配 
4、目标Activity的属性Android:exported=”true” 
5、目标Activity具有相应的IntentFilter，存在Action动作或其他过滤器并且没有设置exported=false 
6、启动者的Pid是一个System Server的Pid 
7、启动者的Uid是一个System Uid（Android规定android.system.uid=1000，具有该Uid的application，我们称之为获得Root权限） 
如果上述调节，满足一条，一般即可（与其他几条不发生强制设置冲突），否则，将会得到Permission Denial的Exception而导致Force Close。 
现在，我来解释一下Uid机制 
众所周知，Pid是进程ID，Uid是用户ID，只是Android和计算机不一样，计算机每个用户都具有一个Uid，哪个用户start的程序，这个程序的Uid就是那个那个用户，而Android中每个程序都有一个Uid，默认情况下，Android会给每个程序分配一个普通级别互不相同的Uid，如果用互相调用，只能是Uid相同才行，这就使得共享数据具有了一定安全性，每个软件之间是不能随意获得数据的。而同一个application只有一个Uid，所以application下的Activity之间不存在访问权限的问题。 
如果你需要做一个application，将某些服务service，provider或者activity等的数据，共享出来怎么办，三个办法。 
1、完全暴露，这就是android:exported=”true”的作用，而一旦设置了intentFilter之后，exported就默认被设置为true了，除非再强制设为false。当然，对那些没有intentFilter的程序体，它的exported属性默认仍然是false，也就不能共享出去。 
2、权限提示暴露，这就是为什么经常要设置usePermission的原因，如果人家设置了android:permission=”xxx.xxx.xx”那么，你就必须在你的application的Manufest中usepermission xxx.xxx.xx才能访问人家的东西。 
3、私有暴露，假如说一个公司做了两个产品，只想这两个产品之间可互相调用，那么这个时候就必须使用shareUserID将两个软件的Uid强制设置为一样的。这种情况下必须使用具有该公司签名的签名文档才能，如果使用一个系统自带软件的ShareUID，例如Contact，那么无须第三方签名。 
这种方式保护了第三方软件公司的利益于数据安全。 
当然如果一个activity是又system process跑出来的，那么它就可以横行霸道，任意权限，只是你无法开发一个第三方application具有系统的Pid（系统Pid不固定），但是你完全可以开发一个具有系统Uid的程序，对系统中的所有程序任意访问，只需再Manufest中声明shareUserId为android.system.uid即可，生成的文件也必须经过高权限签名才行，一般不具备这种审核条件的application，google不会提供给你这样的签名文件。当然你是在编译自己的系统的话，想把它作成系统软件程序，只需在Android.mk中声明Certificate:platform则可以了，既采用系统签名。这个系统Uid的获得过程，我们把它叫做获得Root权限的过程。所以很多第三方系统管理软件就是有Root权限的软件，因为他需要对系统有任意访问的权限。那么它的Root签名则需要和编译的系统一致，例如官方的系统得用官方的签名文件，CM的系统就得用CM的签名文件。
windows下，配置好Adroid环境变量后(如将d:android-sdk-windows-1.0_r1 ools加入系统变量PATH)，在命令行窗口输入：
```  
emulator
```
启动Android 模拟器后，输入：
```  
adb shell
```
进入adb shell模式：
将网络连接代理设置写入配置数据库，假如你的上网代理IP是10.193.xx.xx：
```  
sqlite3 /data/data/com.android.providers.settings/databases/settings.db "INSERT INTO system VALUES(99,'http_proxy','10.193.xx.xx:1080')"
```
查询一下是否成功更改了系统设置：
```  
sqlite3 /data/data/com.android.providers.settings/databases/settings.db "SELECT * FROM system"
```
结果中应有：99|http_proxy|10.193.xx.xx:1080
重启模拟器，应该可以使用Browser上 Internet了.
删除刚刚写入的配置信息方法：
```  
sqlite3 /data/data/com.android.providers.settings/databases/settings.db "DELETE FROM system WHERE _id=99"
```
Android模拟器默认的地址是10.0.2.3，默认的DNS也是 10.0.2.3，对于在家里上网学习Android的人(像我)来讲，一般电脑的IP都是192.168.1.100之类的，不在同一个网段。所以就会出现电脑可以上网但是模拟器不能上网的情况。其实设置方法很简单，只要把模拟器的默认DNS设置成电脑的DNS地址即可。
第一步：用系统的命令进入Android开发包的tools目录
```  
cd X:...android-sdk-windows ool
```
第二步：使用adb的shell，确认系统的各项属性
```  
adb shell
getprop
//getprop会列出系统当前的各项属性
```
第三步：得到模拟器的DNS地址
在结果里可以看到：
```  
[net.dns1]: [10.0.2.3]
[net.dns2]: [10.0.2.4]
[net.dns3]: [10.0.2.5]
[net.dns4]: [10.0.2.6]
```
第四步：把dns改成我们自己的DNS 
```  
setprop net.dns1 192.168.1.1
```
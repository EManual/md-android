Android系统默认只能通过IP（10.0.2.2）单向访问PC电脑，而PC电脑不能通过IP来直接访问Android模拟器系统。要想实现PC电脑和Android模拟器系统以及Android模拟器之间相互通信必须借助端口重定向(redir)来实现。
先说说端口重定向所需要的telnet客户端安装：
#### windows：
安装telnet客户端。如果没有安装，可以在windows程序管理中的打开或关闭系统功能下找到telnet客户端菜单项来启用telnet客户端功能。
#### linux:
自行安装telnet客户端。
#### 一、PC电脑不能直接访问Android模拟器系统的原因
Android系统为实现通信将PC电脑IP设置为10.0.2.2，自身为10.0.2.15/127.0.0.1。然而PC电脑并没有为Android模拟器系统指定IP，所以PC只能通过端口重定向来实现和Android模拟器的通信。
#### 二、PC电脑和Android模拟器系统之间通信
1、运行模拟器
2、打开window 命令行，执行：
```  
telnet localhost 5554
```
5554是模拟器的端口（位于Android模拟器窗口标题栏），执行之后会进入android console
3、在console下执行：
```  
格式：redir add < udp/tcp >:< pc端口 >:< 模拟器端口 >
例如：redir add udp:2888:2888 
      redir add tcp:2888:2888
```
执行此命令之后，会把PC 2888 端口接收到的tcp/udp数据转到模拟器的2888端口。
#### 三、多个Android模拟器系统之间通信
1、启动模拟器emulator-5554和emulator-5556
2、打开dos窗口执行telnet localhost 5554连接到模拟器5554
3、成功连接后，继续执行：redir add tcp:5000:6000将PC端口5000绑定到模拟器5554的端口6000上。
4、此时模拟器5556通过向PC电脑端口5000（即地址：10.0.2.2:5000）发送tcp/udp数据包跟模拟器5554通信。
5、同理根据步骤2、3来实现PC电脑对模拟器5556的端口转发。
添加成功后，我们可以用redir list命令来列出已经添加的映射端口，redir del可以进行删除。
相信只要理解了PC电脑和Android模拟器系统之间通信，便知道怎么实现多个模拟器之间通信。
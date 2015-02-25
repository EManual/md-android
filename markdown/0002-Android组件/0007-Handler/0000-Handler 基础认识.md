#### Handler的定义:
主要接受子线程发送的数据, 并用此数据配合主线程更新UI。
解释:当应用程序启动时，Android首先会开启一个主线程(也就是UI线程)，主线程为管理界面中的UI控件，进行事件分发，比如说，你要是点击一个 Button ，Android会分发事件到Button上，来响应你的操作。  
如果此时需要一个耗时的操作，例如:联网读取数据，或者读取本地较大的一个文件的时候，你不能把这些操作放在主线程中，如果你放在主线程中的话，界面会出现假死现象，如果5秒钟还没有完成的话，会收到Android系统的一个错误提示  "强制关闭"。  
这个时候我们需要把这些耗时的操作，放在一个子线程中,因为子线程涉及到UI更新，Android主线程是线程不安全的，也就是说，更新UI只能在主线程中更新，子线程中操作是危险的。 
这个时候，Handler就出现了。来解决这个复杂的问题，由于Handler运行在主线程中(UI线程中)，它与子线程可以通过Message对象来传递数据，这个时候，Handler就承担着接受子线程传过来的(子线程用sedMessage()方法传弟)Message对象，(里面包含数据)，把这些消息放入主线程队列中，配合主线程进行更新UI。
#### Handler 概念解释：
handler类允许你发送消息和处理线程消息队列中的消息及runnable对象。handler实例都是与一个线程和该线程的消息队列一起使用，一旦创建了一个新的handler实例，系统就把该实例与一个线程和该线程的消息队列捆绑起来，这将可以发送消息和runnable对象给该消息队列，并在消息队列出口处处理它们。
handler类有两种主要用途：
1。按照时间计划，在未来某时刻，对处理一个消息或执行某个runnable实例。
2。把一个对另外线程对象的操作请求放入消息队列中，从而避免线程间冲突。
时间类消息通过如下方法使用：
post(Runnable), postAtTime(Runnable, long), postDelayed(Runnable, long), sendEmptyMessage(int), sendMessage(Message), sendMessageAtTime(Message, long), and sendMessageDelayed(Message, long) 
methods.post之类函数可以传输一个runnable对象给消息队列，并在到达消息队列后被调用。sendmessage之类函数可以传送一个包含数据的message对象，该message对象可以被Handler类的handleMessage(Message) 方法所处理。
post之类函数和sendmessage之类的函数都可以指定消息的执行时机，是立即执行、稍后一段时间执行，还是在某个确定时刻执行。这可以用来实现超时、消息或其他时间相关的操作。
当一个进程启动时，主线程独立执行一个消息队列，该队列管理着应用顶层的对象（如：activities、broadcast receivers等等）和所有创建的窗口。你可以创建自己的一个线程，并通过handler来与主线程进行通信。这可以通过在新的线程中调用主线程的handler的post和sendmessage操作来实现。
HandlerThread/Looper & MessageQueue & Message的关系：
handler负责将需要传递的信息封装成Message，通过handler对象的sendMessage()来将消息传递给Looper，由Looper将Message放入MessageQueue中。当Looper对象看到MessageQueue中含有Message，就将其广播出去。该handler对象收到该消息后，调用相应的handler对象的handleMessage()方法对其进行处理。
#### Handler一些特点
handler可以分发Message对象和Runnable对象到主线程中，每个Handler实例,都会绑定到创建他的线程中(一般是位于主线程)，它有两个作用: 
(1)安排消息或Runnable 在某个主线程中某个地方执行。
(2)安排一个动作在不同的线程中执行。
#### Handler中分发消息的一些方法
```  
post(Runnable)
postAtTime(Runnable,long)
postDelayed(Runnable long)
sendEmptyMessage(int)
sendMessage(Message)
sendMessageAtTime(Message,long)
sendMessageDelayed(Message,long)
```
以上post类方法允许你排列一个Runnable对象到主线程队列中，sendMessage类方法，允许你安排一个带数据的Message对象到队列中，等待更新。
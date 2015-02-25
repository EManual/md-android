handler机制的原理。
答：andriod提供了Handler和Looper来满足线程间的通信。Handler先进先出原则。Looper类用来管理特定线程内对象之间的消息交换(Message Exchange)。 
1)Looper: 一个线程可以产生一个Looper对象，由它来管理此线程里的Message Queue(消息队列)。 
2)Handler: 你可以构造Handler对象来与Looper沟通，以便push新消息到Message Queue里;或者接收Looper从Message Queue取出)所送来的消息。 
3)Message Queue(消息队列):用来存放线程放入的消息。 
4)线程：UI thread 通常就是main thread，而Android启动程序时会替它建立一个Message Queue。 


两种创建线程的方式：
第一种方式：使用Runnable接口创建线程。
第二种方式：直接继承Thread类创建对象使用Runnable接口创建线程。
1.可以将CPU，代码和数据分开，形成清晰的模型。
2.线程体run()方法所在的类可以从其它类中继承一些有用的属性和方法。
3.有利于保持程序的设计风格一致。
直接继承Thread类创建对象：
1.Thread子类无法再从其它类继承（java语言单继承）。
2.编写简单，run()方法的当前对象就是线程对象，可直接操作。
在实际应用中，几乎都采取用Thread创建线程的步骤 步骤为：
(1)定义线程类：
```  
public class  线程类名 extends Thread {
    ……
    public void run(){
            …… //编写线程的代码
    }
}
```
(2)创建线程类对象
(3)启动线程： 线程类对象.start();
```  
public static void main(String[] args) {
	线程类名 线程类对象 = new 线程类名();
	线程类对象.start();
}
```
用Runnanble 创建线程的步骤： 
1.定义一个Runnable接口类。
2.在此接口类中定义一个对象作为参数run()方法。
3.在run()方法中定义线程的操作。
4.在其它类的方法中创建此Runnable接口类的实例对象，并以此实例对象作为参数创建线程类对象。
5.用start()类方法启动线程。
使用Runnable接口方法创建线程和启动线程。
```  
//使用Runnable接口方法创建线程和启动线程。
public class MyThread implements Runnable {
	int count = 1, number;
	public MyThread(int num) {
		number = num;
		System.out.println("创建线程" + number);
	}
	public void run() {
		while (true) {
			System.out.println("线程" + number + ":计数" + count);
			if (++count == 3)
				return;
		}
	}
	public static void main(String args[]) {
		for (int i = 0; i < 2; i++) {
			new Thread(new MyThread(i + 1)).start(); // 启动线程
		}
	} 
}
```
#### 1、触摸屏事件
1）pointerDragged(int x, int y)  触摸屏拖拽事件(暂时还没研究)。
2）pointerPressed(int x, int y) 触摸屏按压。
3）pointerReleased(int x, int y) 触摸屏释放。
pointerPressed(int x, int y)当用户按下触摸屏的时候会自动调用这个方法x,y就是当前压下的坐标。
#### 2、按键事件 
1）keyPressed：按键按下。
2）keyReleased:按键释放。
3) keyRepeated:重复按键。
#### 3、定时器处理
1）在Timer 的schedule()方法来设定特定时间或特定时间周期。
2）在TimerTask的run()方法中定义任务。
```  
import java.io.IOException;
import java.util.Timer;
import java.util.TimerTask;
import javax.microedition.lcdui.Canvas;
import javax.microedition.lcdui.Display;
import javax.microedition.lcdui.Displayable;
import javax.microedition.lcdui.Graphics;
import javax.microedition.lcdui.Image;
public class WelcomeCanvas extends Canvas {
	private Display display;
	private Displayable nextUI;
	private Timer timer = new Timer();
	private long displayTime = 3000;
	public WelcomeCanvas(Display dis, Displayable disp) {
		this.display = dis;
		this.nextUI = disp;
	}
	protected void paint(Graphics arg0) {
		int width = this.getWidth();
		int height = this.getHeight();
		Image displayImage = null;
		try {
			displayImage = Image.createImage("/logo20k.png");
		} catch (IOException e) {
			e.printStackTrace();
		}
		arg0.drawImage(displayImage, getWidth() / 2, getHeight() / 2,
				Graphics.HCENTER | Graphics.VCENTER);
	}
	public void setDisplayTime(long dispTime) {
		this.displayTime = dispTime;
	}
	protected void keyPressed(int keyCode) {
		cancelWelcome();
	}
	protected void pointerPressed(int y, int x) {
		cancelWelcome();
	}
	private void cancelWelcome() {
		timer.cancel();
		display.setCurrent(nextUI);
	}
	protected void showNotify() {
		timer.schedule(new TimerTask() {
			public void run() {
				cancelWelcome();
			}
		}, displayTime);
	}
}
```
Thread方式实现自动进入主画面 
```  
import java.io.IOException;
import java.util.Timer;
import java.util.TimerTask;
import javax.microedition.lcdui.Canvas;
import javax.microedition.lcdui.Display;
import javax.microedition.lcdui.Displayable;
import javax.microedition.lcdui.Graphics;
import javax.microedition.lcdui.Image;
public class WelcomeCanvas2 extends Canvas implements Runnable {
	private Display display;
	private Displayable nextUI;
	private long displayTime = 3000;
	public WelcomeCanvas2(Display dis, Displayable disp) {
		this.display = dis;
		this.nextUI = disp;
		// 启动线程
		Thread thread = new Thread(this);
		thread.start();
	}
	protected void paint(Graphics arg0) {
		int width = this.getWidth();
		int height = this.getHeight();
		Image displayImage = null;
		try {
			displayImage = Image.createImage("/logo20k.png");
		} catch (IOException e) {
			e.printStackTrace();
		}
		arg0.drawImage(displayImage, getWidth() / 2, getHeight() / 2,
				Graphics.HCENTER | Graphics.VCENTER);
	}
	public void run() {
		// 等待3秒
		try {
			Thread.sleep(displayTime);
		} catch (Exception e) {
		}
		// 显示需要显示的界面
		display.setCurrent(nextUI);
	}
	public void setDisplayTime(long dispTime) {
		this.displayTime = dispTime;
	}
	protected void keyPressed(int keyCode) {
		cancelWelcome();
	}
	protected void pointerPressed(int y, int x) {
		cancelWelcome();
	}
	private void cancelWelcome() {
		display.setCurrent(nextUI);
	}
}
```
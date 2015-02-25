J2me通过Mobile Media API 支持手机音频，这是在特定类型的设备上支持不同程度的多媒体的类和接口的一个集合。更具体地说，Mobile Media API划分为两个不同的API集合： 
一个是Mobile Media API,主要针对高级声音和多媒体能力；另一个是MIDP2.0 Media API，主要针对支持唯一音频的受限制的设备。很明显，我们主要是讨论后一部分。 
MIDP2.0 Media API 围绕着2个主要的部分进行设计：管理器，播放器，和控制器。MIDP2.0 Media API都位于javax.microedition.media和javax.microedition.media.control包中。其中管理器Manager位于media包中，它通过为各种媒体类型创建播放器，从而使我们能够查询一个手机的媒体能力。播放器Player接口位于同一个包中，提供了一组通用的方法来控制音频的回放。控制器Control接口位于media.control包中，用来进行特定类型的媒体控制，例如，VolumeControl用来控制音量，ToneControl用来控制乐音。
如果要想知道我们的手机对语音支持的能力，我们可以使用下面的代码（只提供Canvas类）：
```  
import javax.microedition.lcdui.*;
import javax.microedition.media.*;
import javax.microedition.media.control.*;
public class SCCanvas extends Canvas {
	private Display display;
	public SCCanvas(Display d) {
		super();
		display = d;
	}
	void start() {
		display.setCurrent(this);
		repaint();
	}
	public void paint(Graphics g) {
		// 清除画布
		g.setColor(0, 0, 0);
		g.fillRect(0, 0, getWidth(), getHeight());
		g.setColor(255, 255, 255);
		// 得到手机支持的声音的类型
		String[] contentTypes = Manager.getSupportedContentTypes(null);
		// 显示所支持的声音的类型
		int y = 0;
		for (int i = 0; i < contentTypes.length; i++) {
			// 显示类型
			g.drawString(contentTypes, 0, y, Graphics.TOP | Graphics.LEFT);
			y += Font.getDefaultFont().getHeight();
			// 如果支持乐音生成器，播放乐音
			if (contentTypes == "audio/x-tone-seq") {
				try {
					Manager.playTone(ToneControl.C4, 2000, 100);
				} catch (MediaException me) {
				}
			}
		}
	}
}
```
下面是MIDP2.0手机所支持的常见的音频MIME类型：
audio/x-tone-seq  表示乐音和乐音序列；audio/x-wav表示声波声音；audio/midi表示midi音乐；audio/mpeg表示MP3音频。
使用MIDP2.0 Media API的步骤：
（1）使用Manager类获得一个针对特定媒体类型的播放器。
（2）使用Player接口在特定的播放器上播放媒体。
（3）如果需要的话，使用Control接口来改变媒体的回放。
下面是一个播放乐音的例子：
```  
import javax.microedition.lcdui.*;
import javax.microedition.media.*;
import javax.microedition.media.control.*;
public class SCCanvas extends Canvas {
	public static void main(String[] args) {
		byte tempo = 50; // 设定声音播放速度
		byte d = 8; // 音调值
		byte C4 = ToneControl.C4; // 基准音调
		byte D4 = (byte) (C4 + 2); // 音调值
		byte E4 = (byte) (C4 + 4);
		byte G4 = (byte) (C4 + 7);
		byte rest = ToneControl.SILENCE; // 无声
		byte[] mySequence = { ToneControl.VERSION, 1, // 设置版本号，当前必须设为1
				ToneControl.TEMPO, tempo, // 设置声音播放速度，值越大，播放越快
				ToneControl.SET_VOLUME, 100, // 设置音量，值越大，音量越大
				ToneControl.BLOCK_START, 0, // 预定义播放块，当前块号为0
				E4, d, D4, d, C4, d, E4, d, E4, d, E4, d, E4, d, rest, d, // 块的内容
				ToneControl.BLOCK_END, 0, // 块定义结束符
				ToneControl.PLAY_BLOCK, 0, // 播放当前块号为0的块，块号必须提前定义
				D4, d, D4, d, D4, d, rest, d, // 不使用块号方式播放的内容，必须位于块定义后面
		};
		// 创建播放器
		Player p = Manager.createPlayer(Manager.TONE_DEVICE_LOCATOR);
		// 准备播放信息
		p.realize();
		// 获取音调控制
		ToneControl c = (ToneControl) p.getControl("ToneControl");
		// 设置音调序列
		c.setSequence(mySequence);
		// 设置播放资源，获取设备
		p.prefetch();
		// 开始播放
		p.start();
	}
}
```
下面是一个播放音乐文件的例子：
```  
try {
	// 从资源中获取声音
	InputStream is = getClass().getResourceAsStream(
			"/" + "Testsound.mid");
	// 创建播放MIDI声音的播放器
	Player player = Manager.createPlayer(is, "audio/midi");
	// 获取播放信息
	player.realize();
	// 获取设备
	player.prefetch();
	// 开始播放声音
	player.start();
	// 获得控制接口，此接口的获得必须在获取播放信息或者获取播放设备后面，如果在它们前面，将会出现无法播放声音的情况
	VolumeControl control = (VolumeControl) player
			.getControl("VolumeControl");
	if (control != null) // 必须检查是否为null，因为有些声音格式可能不支持音量控制
	{
		control.setLevel(5); // 设置音量级别为50
		// control.setMute(true); //设置静音
	}
} catch (Exception e) {
}
```
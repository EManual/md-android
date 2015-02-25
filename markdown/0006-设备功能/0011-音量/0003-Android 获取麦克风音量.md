Android上有一些很有趣的应用，例如《吹裙子》、《吹气球》之类的。利用的是实时获取麦克风输入音量，然后进行相应的处理。录音过程中怎样获得声音的大小呢？网上也不少人问如何处理这个事情，也有一些解答，不过都没有实际的代码。简单摸索了一下，写了个小 Demo 试了试，果然可以。给大家共享一下。
不解释代码了，大家看注释。
```  
import android.media.AudioFormat;
import android.media.AudioRecord;
import android.media.MediaRecorder;
import android.util.Log;
public class RecordThread extends Thread {
	private AudioRecord ar;
	private int bs;
	private static int SAMPLE_RATE_IN_HZ = 8000;
	private boolean isRun = false;
	public RecordThread() {
		super();
		bs = AudioRecord.getMinBufferSize(SAMPLE_RATE_IN_HZ,
				AudioFormat.CHANNEL_CONFIGURATION_MONO,
				AudioFormat.ENCODING_PCM_16BIT);
		ar = new AudioRecord(MediaRecorder.AudioSource.MIC, SAMPLE_RATE_IN_HZ,
				AudioFormat.CHANNEL_CONFIGURATION_MONO,
				AudioFormat.ENCODING_PCM_16BIT, bs);
	}
	public void run() {
		super.run();
		ar.startRecording();
		// 用于读取的 
		Buffer byte[] buffer = new byte[bs];
		isRun = true;
		while (isRun) { 
			int r = ar.read(buffer, 0, bs); 
			int v = 0; 
			// 将 buffer 内容取出，进行平方和运算 
			for (int i = 0; i < buffer.length; i++) { 
				// 这里没有做运算的优化，为了更加清晰的展示代码 
				v += buffer[i] * buffer[i];
			} 
			// 平方和除以数据总长度，得到音量大小。可以获取白噪声值，然后对实际采样进行标准化。 
			// 如果想利用这个数值进行操作，建议用 sendMessage 将其抛出，在 Handler 里进行处理。 
			Log.d("spl", String.valueOf(v / (float) r));
		} 
		ar.stop();
	}
	public void pause() {
		// 在调用本线程的 Activity 的 onPause 里调用，以便 Activity 暂停时释放麦克风
		isRun = false;
	}
	public void start() {
		// 在调用本线程的 Activity 的 onResume 里调用，以便 Activity 恢复后继续获取麦克风输入音量
		if (!isRun) {
			super.start();
		}
	}
}
```
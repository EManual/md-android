1. 引言
大家都应该知道在Android中，默认情况下是不允许播放GIF图片动画的，这时需要自己实现来播放GIF动画，在网上找了找能用的实例，又动手做了一下，最后给大家贴出来与大家共享。
2. 功能实现
(1) 主Activity实现：
```  
import android.app.Activity;
import android.os.Bundle;
public class GIFPlayerActivity extends Activity {
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		GameView gameView = new GameView(this);
		setContentView(gameView);
	}
}
```
(2) GIF图片动画View：
```  
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.InputStream;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.view.View;
public class GameView extends View implements Runnable {
	private GIFFrameManager mGIFFrameManager = null;
	private Paint mPaint = null;
	public GameView(Context context) {
		super(context);
		mGIFFrameManager = GIFFrameManager.CreateGifImage(this.fileConnect(this
				.getResources().openRawResource(R.drawable.cat)));
		new Thread(this).start();
	}
	protected void onDraw(Canvas canvas) {
		super.onDraw(canvas);
		mPaint = new Paint();
		mGIFFrameManager.nextFrame();
		Bitmap bitmap = mGIFFrameManager.getImage();
		if (bitmap != null) {
			canvas.drawBitmap(bitmap, 0, 0, mPaint);
		}
	}
	public byte[] fileConnect(InputStream in) {
		ByteArrayOutputStream out = new ByteArrayOutputStream();
		int ch = 0;
		try {
			while ((ch = in.read()) != -1) {
				out.write(ch);
			}
			byte[] b = out.toByteArray();
			out.close();
			out = null;
			in.close();
			in = null;
			return b;
		} catch (IOException e) {
			e.printStackTrace();
			return null;
		}
	}
	public void run() {
		while (!Thread.interrupted()) {
			try {
				Thread.sleep(100);
				this.postInvalidate();
			} catch (Exception ex) {
				ex.printStackTrace();
				Thread.currentThread().interrupt();
			}
		}
	}
}
```
(3) GIF图片动画播放管理器：
```  
import java.util.Vector;
import android.graphics.Bitmap;
public class GIFFrameManager {
	private Vector<Bitmap> frames;
	private int index;
	public GIFFrameManager() {
		frames = new Vector<Bitmap>(1);
		index = 0;
	}
	public void addImage(Bitmap image) {
		frames.addElement(image);
	}
	public int size() {
		return frames.size();
	}
	public Bitmap getImage() {
		if (size() == 0) {
			return null;
		} else {
			return (Bitmap) frames.elementAt(index);
		}
	}
	public void nextFrame() {
		if (index + 1 < size()) {
			index++;
		} else {
			index = 0;
		}
	}
	public static GIFFrameManager CreateGifImage(byte bytes[]) {
		try {
			GIFFrameManager GF = new GIFFrameManager();
			Bitmap image = null;
			GIFEncoder gifdecoder = new GIFEncoder(bytes);
			for (; gifdecoder.moreFrames(); gifdecoder.nextFrame()) {
				try {
					image = gifdecoder.decodeImage();
					if (GF != null && image != null) {
						GF.addImage(image);
					}
					continue;
				} catch (Exception e) {
					e.printStackTrace();
				}
				break;
			}
			gifdecoder.clear();
			gifdecoder = null;
			return GF;
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}
}
```
(4) GIF图片动画编码：
```  
import android.graphics.Bitmap;
import android.graphics.Bitmap.Config;
public class GIFEncoder {
	private int E0;
	private int E1[];
	private int E2;
	private int E6;
	private boolean E7;
	private int E8[];
	private int width;
	private int height;
	private int ED;
	private boolean EE;
	private boolean EF;
	private int F0[];
	private int F1;
	private boolean F2;
	private int F3;
	private long F4;
	private int F5;
	private static final int F6[] = { 8, 8, 4, 2 };
	private static final int F8[] = { 0, 4, 2, 1 };
	int curFrame;
	int poolsize;
	int FA;
	byte C2[];
	int FB;
	int FC;
	int FD;
	public GIFEncoder(byte abyte0[]) {
		E0 = -1;
		E1 = new int[280];
		E2 = -1;
		E6 = 0;
		E7 = false;
		E8 = null;
		width = 0;
		height = 0;
		ED = 0;
		EE = false;
		EF = false;
		F0 = null;
		F1 = 0;
		F5 = 0;
		curFrame = 0;
		C2 = abyte0;
		poolsize = C2.length;
		FA = 0;
	}
	public boolean moreFrames() {
		return poolsize - FA >= 16;
	}
	public void nextFrame() {
		curFrame++;
	}
	public Bitmap decodeImage() {
		return decodeImage(curFrame);
	}
	public Bitmap decodeImage(int i) {
		if (i <= E0) {
			return null;
		}
		if (E0 < 0) {
			if (!E3()) {
				return null;
			}
			if (!E4()) {
				return null;
			}
		}
		do {
			if (!E9(1)) {
				return null;
			}
			int j = E1[0];
			if (j == 59) {
				return null;
			}
			if (j == 33) {
				if (!E7()) {
					return null;
				}
			} else if (j == 44) {
				if (!E5()) {
					return null;
				}
				Bitmap image = createImage();
				E0++;
				if (E0 < i) {
					image = null;
				} else {
					return image;
				}
			}
		} while (true);
	}
	public void clear() {
		C2 = null;
		E1 = null;
		E8 = null;
		F0 = null;
	}
	private Bitmap createImage() {
		int i = width;
		int j = height;
		int j1 = 0;
		int k1 = 0;
		int ai[] = new int[4096];
		int ai1[] = new int[4096];
		int ai2[] = new int[8192];
		if (!E9(1)) {
			return null;
		}
		int k = E1[0];
		int[] image = new int[width * height];
		int ai3[] = E8;
		if (EE) {
			ai3 = F0;
		}
		if (E2 >= 0) {
			ai3[E2] = 0xffffff;
		}
		int l2 = 1 << k;
		int j3 = l2 + 1;
		int k2 = k + 1;
		int l3 = l2 + 2;
		int k3 = -1;
		int j4 = -1;
		for (int l1 = 0; l1 < l2; l1++) {
			ai1[l1] = l1;
		}
		int j2 = 0;
		E2();
		j1 = 0;
		label0: for (int i2 = 0; i2 < j; i2++) {
			int i1 = 0;
			do {
				if (i1 >= i) {
					break;
				}
				if (j2 == 0) {
					int i4 = E1(k2);
					if (i4 < 0) {
						return Bitmap.createBitmap(image, width, height,
								Config.RGB_565);
					}
					if (i4 > l3 || i4 == j3) {
						return Bitmap.createBitmap(image, width, height,
								Config.RGB_565);
					}
					if (i4 == l2) {
						k2 = k + 1;
						l3 = l2 + 2;
						k3 = -1;
						continue;
					}
					if (k3 == -1) {
						ai2[j2++] = ai1[i4];
						k3 = i4;
						j4 = i4;
						continue;
					}
					int i3 = i4;
					if (i4 == l3) {
						ai2[j2++] = j4;
						i4 = k3;
					}
					for (; i4 > l2; i4 = ai[i4]) {
						ai2[j2++] = ai1[i4];
					}
					j4 = ai1[i4];
					if (l3 >= 4096) {
						return Bitmap.createBitmap(image, width, height,
								Config.RGB_565);
					}
					ai2[j2++] = j4;
					ai[l3] = k3;
					ai1[l3] = j4;
					if (++l3 >= 1 << k2 && l3 < 4096) {
						k2++;
					}
					k3 = i3;
				}
				int l = ai2[--j2];
				if (l < 0) {
					return Bitmap.createBitmap(image, width, height,
							Config.RGB_565);
				}
				if (i1 == 0) {
					FC = 0;
					FB = ai3[l];
					FD = 0;
				} else if (FB != ai3[l]) {
					for (int mm = FD; mm <= FD + FC; mm++) {
						image[j1 * width + mm] = FB;
					}
					FC = 0;
					FB = ai3[l];
					FD = i1;
					if (i1 == i - 1) {
						image[j1 * width + i1] = ai3[l];
					}
				} else {
					FC++;
					if (i1 == i - 1) {
						for (int mm = FD; mm <= FD + FC; mm++) {
							image[j1 * width + mm] = FB;
						}
					}
				}
				i1++;
			} while (true);
			if (EF) {
				j1 += F6[k1];
				do {
					if (j1 < j) {
						continue label0;
					}
					if (++k1 > 3) {
						return Bitmap.createBitmap(image, width, height,
								Config.RGB_565);
					}
					j1 = F8[k1];
				} while (true);
			}
			j1++;
		}
		return Bitmap.createBitmap(image, width, height, Config.RGB_565);
	}
	private int E1(int i) {
		while (F5 < i) {
			if (F2) {
				return -1;
			}
			if (F1 == 0) {
				F1 = E8();
				F3 = 0;
				if (F1 <= 0) {
					F2 = true;
					break;
				}
			}
			F4 += E1[F3] << F5;
			F3++;
			F5 += 8;
			F1--;
		}
		int j = (int) F4 & (1 << i) - 1;
		F4 >>= i;
		F5 -= i;
		return j;
	}
	private void E2() {
		F5 = 0;
		F1 = 0;
		F4 = 0L;
		F2 = false;
		F3 = -1;
	}
	private boolean E3() {
		if (!E9(6)) {
			return false;
		}
		return E1[0] == 71 && E1[1] == 73 && E1[2] == 70 && E1[3] == 56
				&& (E1[4] == 55 || E1[4] == 57) && E1[5] == 97;
	}
	private boolean E4() {
		if (!E9(7)) {
			return false;
		}
		int i = E1[4];
		E6 = 2 << (i & 7);
		E7 = EB(i, 128);
		E8 = null;
		return !E7 || E6(E6, true);
	}
	private boolean E5() {
		if (!E9(9)) {
			return false;
		}
		width = EA(E1[4], E1[5]);
		height = EA(E1[6], E1[7]);
		int i = E1[8];
		EE = EB(i, 128);
		ED = 2 << (i & 7);
		EF = EB(i, 64);
		F0 = null;
		return !EE || E6(ED, false);
	}
	private boolean E6(int i, boolean flag) {
		int ai[] = new int[i];
		for (int j = 0; j < i; j++) {
			if (!E9(3)) {
				return false;
			}
			ai[j] = E1[0] << 16 | E1[1] << 8 | E1[2] | 0xff000000;
		}
		if (flag) {
			E8 = ai;
		} else {
			F0 = ai;
		}
		return true;
	}
	private boolean E7() {
		if (!E9(1)) {
			return false;
		}
		int i = E1[0];
		int j = -1;
		switch (i) {
		case 249:
			j = E8();
			if (j < 0) {
				return true;
			}
			if ((E1[0] & 1) != 0) {
				E2 = E1[3];
			} else {
				E2 = -1;
			}
			break;
		}

		do {
			j = E8();
		} while (j > 0);
		return true;
	}
	private int E8() {
		if (!E9(1)) {
			return -1;
		}
		int i = E1[0];
		if (i != 0 && !E9(i)) {
			return -1;
		} else {
			return i;
		}
	}
	private boolean E9(int i) {
		if (FA + i >= poolsize) {
			return false;
		}
		for (int j = 0; j < i; j++) {
			int k = C2[FA + j];
			if (k < 0) {
				k += 256;
			}
			E1[j] = k;
		}
		FA += i;
		return true;
	}
	private static final int EA(int i, int j) {
		return j << 8 | i;
	}
	private static final boolean EB(int i, int j) {
		return (i & j) == j;
	}
}
```
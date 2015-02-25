自从照相技术诞生以来，我们的生活就和图片照片这些东西紧密联系起来。相应的，无论是电脑上的应用还是手机上的应用，图像处理一直是都是不可或缺的一大应用。这里边包括很多各个方面的应用，这次我就讲一个小的图像特效：素描。素描要体现的其实就是两点，一个就是图像呈黑白效果，另一个就是图像颜色深的存在，颜色浅的被忽略。图像处理的实质是对图像的像素进行处理，对于Android平台，只需要取得被处理图像的像素进行相应的运算就可以了。
原理为：先把原来的图像取均值变成黑白图像，然后对黑白图像的像素取反，对这些取反的像素进行模糊，再把原来黑白图像的像素值和模糊后的图像的像素值进行差值运算，最后就得到素描效果的图像了。下面给出代码，我们在这里采用采用高斯模糊方式进行模糊处理，当然，处理的过程中需要处理像素值越界，即大于255和小于0的情形。在本次处理我们用java语言实现，数据运算不是java的强项，如果为了追求更好的处理速度，可以采用NDK的方式来实现。代码后面给出原图和处理后的效果图，最后为了方便大家我们给出了相应的Android工程 和apk文件。
```  
import android.app.Activity;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Color;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.Window;
import android.widget.ImageView;
public class SketchActivity extends Activity {
	private final int PROGRESS_WAIT_VISIBLE = 1;
	private final int PROGRESS_WAIT_GONE = 2;
	private final int IMAGEVIEW_INVALIDATE = 3;
	private ImageView mImageView;
	private Bitmap mBitmap;
	private Handler mHandler = new Handler() {
		@Override
		public void handleMessage(Message msg) {
			super.handleMessage(msg);
			switch (msg.what) {
			case PROGRESS_WAIT_VISIBLE:
				setProgressBarIndeterminateVisibility(true);
				break;
			case PROGRESS_WAIT_GONE:
				setProgressBarIndeterminateVisibility(false);
				break;
			case IMAGEVIEW_INVALIDATE:
				mImageView.invalidate();
				break;
			default:
				break;
			}
		}
	};
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		requestWindowFeature(Window.FEATURE_INDETERMINATE_PROGRESS);
		setContentView(R.layout.sketch);
		mImageView = (ImageView) findViewById(R.id.sketch_image);
		cancelSketch();
	}
	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		super.onCreateOptionsMenu(menu);
		menu.add(1, R.string.label_sketch, 1, R.string.label_sketch);
		menu.add(1, R.string.label_cancel, 2, R.string.label_cancel);
		return true;
	}
	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		super.onOptionsItemSelected(item);
		switch (item.getItemId()) {
		case R.string.label_sketch:
			doSketch();
			break;
		case R.string.label_cancel:
			cancelSketch();
			break;
		default:
			break;
		}
		return true;
	}
	private void cancelSketch() {
		Bitmap bmp = BitmapFactory.decodeStream(getResources().openRawResource(
				R.drawable.demo));
		int width = bmp.getWidth();
		int height = bmp.getHeight();
		int[] pixel = new int[width * height];
		bmp.getPixels(pixel, 0, width, 0, 0, width, height);
		mBitmap = Bitmap.createBitmap(width, height, bmp.getConfig());
		mBitmap.setPixels(pixel, 0, width, 0, 0, width, height);
		mImageView.setImageBitmap(mBitmap);
		bmp.recycle();
	}
	private void doSketch() {
		new Thread(new Runnable() {
			public void run() {
				try {
					int pos, row, col, clr;
					int width = mBitmap.getWidth();
					int height = mBitmap.getHeight();
					int[] pixSrc = new int[width * height];
					int[] pixNvt = new int[width * height];
					mHandler.sendEmptyMessage(PROGRESS_WAIT_VISIBLE);
					mBitmap.getPixels(pixSrc, 0, width, 0, 0, width, height);
					// 先对图象的像素处理成灰度颜色后再取反
					for (row = 0; row < height; row++) {
						for (col = 0; col < width; col++) {
							pos = row * width + col;
							pixSrc[pos] = (Color.red(pixSrc[pos])
									+ Color.green(pixSrc[pos]) + Color
									.blue(pixSrc[pos])) / 3;
							pixNvt[pos] = 255 - pixSrc[pos];
						}
					}
					// 对取反的像素进行高斯模糊, 强度可以设置，暂定为5.0
					gaussGray(pixNvt, 5.0, 5.0, width, height);
					// 灰度颜色和模糊后像素进行差值运算
					for (row = 0; row < height; row++) {
						for (col = 0; col < width; col++) {
							pos = row * width + col;

							clr = pixSrc[pos] << 8;
							clr /= 256 - pixNvt[pos];
							clr = Math.min(clr, 255);

							pixSrc[pos] = Color.rgb(clr, clr, clr);
						}
					}
					// 将处理好的数据更新到图象中
					mBitmap.setPixels(pixSrc, 0, width, 0, 0, width, height);
					mHandler.sendEmptyMessage(IMAGEVIEW_INVALIDATE);
				} catch (Exception e) {
					Log.e("sketch", e.getMessage());
				} finally {
					mHandler.sendEmptyMessage(PROGRESS_WAIT_GONE);
				}
			}
		}).start();
	}
	private static void transferGaussPixels(double[] src1, double[] src2,
			int[] dest, int bytes, int width) {
		int i, j, k, b;
		int bend = bytes * width;
		double sum;
		i = j = k = 0;
		for (b = 0; b < bend; b++) {
			sum = src1[i++] + src2[j++];
			if (sum > 255)
				sum = 255;
			else if (sum < 0)
				sum = 0;
			dest[k++] = (int) sum;
		}
	}
	private void findConstants(double[] n_p, double[] n_m, double[] d_p,
			double[] d_m, double[] bd_p, double[] bd_m, double std_dev) {
		double div = Math.sqrt(2 * 3.141593) * std_dev;
		double x0 = -1.783 / std_dev;
		double x1 = -1.723 / std_dev;
		double x2 = 0.6318 / std_dev;
		double x3 = 1.997 / std_dev;
		double x4 = 1.6803 / div;
		double x5 = 3.735 / div;
		double x6 = -0.6803 / div;
		double x7 = -0.2598 / div;
		int i;
		n_p[0] = x4 + x6;
		n_p[1] = (Math.exp(x1)
				[Tags]* (x7 [Tags]* Math.sin(x3) - (x6 + 2 [Tags]* x4) [Tags]* Math.cos(x3)) + Math
				.exp(x0) * (x5 * Math.sin(x2) - (2 * x6 + x4) * Math.cos(x2)));
		n_p[2] = (2
				[Tags]* Math.exp(x0 + x1)
				[Tags]* ((x4 + x6) [Tags]* Math.cos(x3) [Tags]* Math.cos(x2) - x5 [Tags]* Math.cos(x3)
						[Tags]* Math.sin(x2) - x7 [Tags]* Math.cos(x2) [Tags]* Math.sin(x3)) + x6
				[Tags]* Math.exp(2 [Tags]* x0) + x4 [Tags]* Math.exp(2 [Tags]* x1));
		n_p[3] = (Math.exp(x1 + 2 * x0)
				[Tags]* (x7 [Tags]* Math.sin(x3) - x6 [Tags]* Math.cos(x3)) + Math.exp(x0 + 2
				[Tags]* x1)
				[Tags]* (x5 [Tags]* Math.sin(x2) - x4 [Tags]* Math.cos(x2)));
		n_p[4] = 0.0;
		d_p[0] = 0.0;
		d_p[1] = -2 * Math.exp(x1) * Math.cos(x3) - 2 * Math.exp(x0)
				[Tags]* Math.cos(x2);
		d_p[2] = 4 * Math.cos(x3) * Math.cos(x2) * Math.exp(x0 + x1)
				+ Math.exp(2 * x1) + Math.exp(2 * x0);
		d_p[3] = -2 * Math.cos(x2) * Math.exp(x0 + 2 * x1) - 2 * Math.cos(x3)
				[Tags]* Math.exp(x1 + 2 [Tags]* x0);
		d_p[4] = Math.exp(2 * x0 + 2 * x1);
		for (i = 0; i <= 4; i++) {
			d_m = d_p;
		}
		n_m[0] = 0.0;
		for (i = 1; i <= 4; i++) {
			n_m = n_p - d_p * n_p[0];
		}
		double sum_n_p, sum_n_m, sum_d;
		double a, b;
		sum_n_p = 0.0;
		sum_n_m = 0.0;
		sum_d = 0.0;
		for (i = 0; i <= 4; i++) {
			sum_n_p += n_p;
			sum_n_m += n_m;
			sum_d += d_p;
		}
		a = sum_n_p / (1.0 + sum_d);
		b = sum_n_m / (1.0 + sum_d);
		for (i = 0; i <= 4; i++) {
			bd_p = d_p * a;
			bd_m = d_m * b;
		}
	}
	// 高斯模糊
	private int gaussGray(int[] psrc, double horz, double vert, int width,
			int height) {
		int[] dst, src;
		double[] n_p, n_m, d_p, d_m, bd_p, bd_m;
		double[] val_p, val_m;
		int i, j, t, k, row, col, terms;
		int[] initial_p, initial_m;
		double std_dev;
		int row_stride = width;
		int max_len = Math.max(width, height);
		int sp_p_idx, sp_m_idx, vp_idx, vm_idx;
		val_p = new double[max_len];
		val_m = new double[max_len];
		n_p = new double[5];
		n_m = new double[5];
		d_p = new double[5];
		d_m = new double[5];
		bd_p = new double[5];
		bd_m = new double[5];
		src = new int[max_len];
		dst = new int[max_len];
		initial_p = new int[4];
		initial_m = new int[4];
		// 垂直方向
		if (vert > 0.0) {
			vert = Math.abs(vert) + 1.0;
			std_dev = Math.sqrt(-(vert * vert) / (2 * Math.log(1.0 / 255.0)));
			// 初试化常量
			findConstants(n_p, n_m, d_p, d_m, bd_p, bd_m, std_dev);
			for (col = 0; col < width; col++) {
				for (k = 0; k < max_len; k++) {
					val_m[k] = val_p[k] = 0;
				}
				for (t = 0; t < height; t++) {
					src[t] = psrc[t * row_stride + col];
				}
				sp_p_idx = 0;
				sp_m_idx = height - 1;
				vp_idx = 0;
				vm_idx = height - 1;
				initial_p[0] = src[0];
				initial_m[0] = src[height - 1];
				for (row = 0; row < height; row++) {
					terms = (row < 4) ? row : 4;
					for (i = 0; i <= terms; i++) {
						val_p[vp_idx] += n_p * src[sp_p_idx - i] - d_p
								[Tags]* val_p[vp_idx - i];
						val_m[vm_idx] += n_m * src[sp_m_idx + i] - d_m
								[Tags]* val_m[vm_idx + i];
					}
					for (j = i; j <= 4; j++) {
						val_p[vp_idx] += (n_p[j] - bd_p[j]) * initial_p[0];
						val_m[vm_idx] += (n_m[j] - bd_m[j]) * initial_m[0];
					}
					sp_p_idx++;
					sp_m_idx--;
					vp_idx++;
					vm_idx--;
				}
				transferGaussPixels(val_p, val_m, dst, 1, height);
				for (t = 0; t < height; t++) {
					psrc[t * row_stride + col] = dst[t];
				}
			}
		}
		// 水平方向
		if (horz > 0.0) {
			horz = Math.abs(horz) + 1.0;
			if (horz != vert) {
				std_dev = Math.sqrt(-(horz * horz)
						/ (2 * Math.log(1.0 / 255.0)));
				// 初试化常量
				findConstants(n_p, n_m, d_p, d_m, bd_p, bd_m, std_dev);
			}
			for (row = 0; row < height; row++) {
				for (k = 0; k < max_len; k++) {
					val_m[k] = val_p[k] = 0;
				}
				for (t = 0; t < width; t++) {
					src[t] = psrc[row * row_stride + t];
				}
				sp_p_idx = 0;
				sp_m_idx = width - 1;
				vp_idx = 0;
				vm_idx = width - 1;
				initial_p[0] = src[0];
				initial_m[0] = src[width - 1];
				for (col = 0; col < width; col++) {
					terms = (col < 4) ? col : 4;
					for (i = 0; i <= terms; i++) {
						val_p[vp_idx] += n_p * src[sp_p_idx - i] - d_p
								[Tags]* val_p[vp_idx - i];
						val_m[vm_idx] += n_m * src[sp_m_idx + i] - d_m
								[Tags]* val_m[vm_idx + i];
					}
					for (j = i; j <= 4; j++) {
						val_p[vp_idx] += (n_p[j] - bd_p[j]) * initial_p[0];
						val_m[vm_idx] += (n_m[j] - bd_m[j]) * initial_m[0];
					}
					sp_p_idx++;
					sp_m_idx--;
					vp_idx++;
					vm_idx--;
				}
				transferGaussPixels(val_p, val_m, dst, 1, width);

				for (t = 0; t < width; t++) {
					psrc[row * row_stride + t] = dst[t];
				}
			}
		}
		return 0;
	}
}
```
左边为原图，右边为处理后的图像，原图偏绿，如果觉得图像有点偏绿，可以在进行灰度处理时进行校正，这里就不啰嗦了。
![img](P)  
XML：
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:rientation="vertical" >
    <Button
        android:id="@+id/btnJAVA"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="使用JAVA转换灰度图" />
    <Button
        android:id="@+id/btnNDK"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="使用NDK转换灰度图" />
    <ImageView
        android:id="@+id/ImageView01"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent" />
</LinearLayout>
```
主程序demo.java的源码如下：
```  
import android.app.Activity;
import android.graphics.Bitmap;
import android.graphics.Bitmap.Config;
import android.graphics.drawable.BitmapDrawable;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.ImageView;
public class testToGray extends Activity {
	Button btnJAVA, btnNDK;
	ImageView imgView;
	@Override
	public void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.main);
		this.setTitle("使用NDK转换灰度图---hellogv");
		btnJAVA = (Button) this.findViewById(R.id.btnJAVA);
		btnJAVA.setOnClickListener(new ClickEvent());

		btnNDK = (Button) this.findViewById(R.id.btnNDK);
		btnNDK.setOnClickListener(new ClickEvent());
		imgView = (ImageView) this.findViewById(R.id.ImageView01);
	}
	class ClickEvent implements View.OnClickListener {
		@Override
		public void onClick(View v) {
			if (v == btnJAVA) {
				long current = System.currentTimeMillis();
				Bitmap img = ConvertGrayImg(R.drawable.cat);
				long performance = System.currentTimeMillis() - current;
				// 显示灰度图
				imgView.setImageBitmap(img);
				testToGray.this.setTitle("w:" + String.valueOf(img.getWidth())
						+ ",h:" + String.valueOf(img.getHeight()) + " JAVA耗时
						+ String.valueOf(performance) + " 毫秒");
			} else if (v == btnNDK) {
				long current = System.currentTimeMillis();
				// 先打开图像并读取像素
				Bitmap img1 = ((BitmapDrawable) getResources().getDrawable(
						R.drawable.cat)).getBitmap();
				int w = img1.getWidth(), h = img1.getHeight();
				int[] pix = new int[w * h];
				img1.getPixels(pix, 0, w, 0, 0, w, h);
				// 通过ImgToGray.so把彩色像素转为灰度像素
				int[] resultInt = LibFuns.ImgToGray(pix, w, h);
				Bitmap resultImg = Bitmap.createBitmap(w, h, Config.RGB_565);
				resultImg.setPixels(resultInt, 0, w, 0, 0, w, h);
				long performance = System.currentTimeMillis() - current;
				// 显示灰度图
				imgView.setImageBitmap(resultImg);
				testToGray.this.setTitle("w:" + String.valueOf(img1.getWidth())
						+ ",h:" + String.valueOf(img1.getHeight()) + " NDK耗时
						+ String.valueOf(performance) + " 毫秒");
			}
		}
	}
	[Tags]/**
	 [Tags]* 把资源图片转为灰度图
	 [Tags]* @param resID
	 [Tags]*            资源ID
	 [Tags]*/
	public Bitmap ConvertGrayImg(int resID) {
		Bitmap img1 = ((BitmapDrawable) getResources().getDrawable(resID)).getBitmap();
		int w = img1.getWidth(), h = img1.getHeight();
		int[] pix = new int[w * h];
		img1.getPixels(pix, 0, w, 0, 0, w, h);
		int alpha = 0xFF << 24;
		for (int i = 0; i < h; i++) {
			for (int j = 0; j < w; j++) {
				// 获得像素的颜色
				int color = pix[w * i + j];
				int red = ((color & 0x00FF0000) >> 16);
				int green = ((color & 0x0000FF00) >> 8);
				int blue = color & 0x000000FF;
				color = (red + green + blue) / 3;
				color = alpha | (color << 16) | (color << 8) | color;
				pix[w * i + j] = color;
			}
		}
		Bitmap result = Bitmap.createBitmap(w, h, Config.RGB_565);
		result.setPixels(pix, 0, w, 0, 0, w, h);
		return result;
	}
}
```
封装NDK函数的JAVA类LibFuns.java的源码如下
java代码： 
```  
public class LibFuns {
	static {
		System.loadLibrary("Imgdemo");
	}
	[Tags]/**
	 [Tags]* @param宽度当前视图的宽度
	 [Tags]* @param高度当前视图的高度
	 [Tags]*/
	public static native int[] ImgToGray(int[] buf, int w, int h);
}
```
C代码
```  ]
include <jni.h>
include <stdio.h>
include <stdlib.h>
extern "C" {
	JNIEXPORT jintArray JNICALL Java_com_testToGray_LibFuns_ImgToGray(
	JNIEnv* env, jobject obj, jintArray buf, int w, int h);
}
;
JNIEXPORT jintArray JNICALL Java_com_testToGray_LibFuns_ImgToGray(
JNIEnv* env, jobject obj, jintArray buf, int w, int h) {
	jint *cbuf;
	cbuf = env->GetIntArrayElements(buf, false);
	if (cbuf == NULL) {
		return 0; /* exception occurred */
	}
	int alpha = 0xFF << 24;
	for (int i = 0; i < h; i++) {
		for (int j = 0; j < w; j++) {
			// 获得像素的颜色
			int color = cbuf[w * i + j];
			int red = ((color & 0x00FF0000) >> 16);
			int green = ((color & 0x0000FF00) >> 8);
			int blue = color & 0x000000FF;
			color = (red + green + blue) / 3;
			color = alpha | (color << 16) | (color << 8) | color;
			cbuf[w * i + j] = color;
		}
	}
	int size=w * h;
	jintArray result = env->NewIntArray(size);
	env->SetIntArrayRegion(result, 0, size, cbuf);
	env->ReleaseIntArrayElements(buf, cbuf, 0);
	return result;
}
```
代码:
```  
LOCAL_PATH:= $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE := ImgToGray
LOCAL_SRC_FILES := ImgToGray.cpp
include $(BUILD_SHARED_LIBRARY)
```
在Android上使用JAVA实现彩图转换为灰度图，跟J2ME上的实现类似，不过遇到频繁地转换或者是大图转换时，就必须使用NDK来提高速度了。本文主要通过JAVA和NDK这两种方式来分别实现彩图转换为灰度图，并给出速度的对比。
从转换灰度图的耗时来说，NDK的确比JAVA所用的时间短不少。
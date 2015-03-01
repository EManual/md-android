在实际的项目中，我们经常要得到当前屏幕的分辨率，进行机型适配，得到分辨率其实很简单，主要有两种方法。
方法一：
```  
Display mDisplay = getWindowManager().getDefaultDisplay();
int W = mDisplay.getWidth();
int H = mDisplay.getHeight();
Log.i("Main", "Width = " + W);
Log.i("Main", "Height = " + H);
```
Display是在android.view.Display包中的。
方法二：
```  
DisplayMetrics mDisplayMetrics = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(mDisplayMetrics);
int W = mDisplayMetrics.widthPixels;
int H = mDisplayMetrics.heightPixels;
Log.i("Main", "Width = " + W);
Log.i("Main", "Height = " + H);
```
DisplayMetrics是在android.util.DisplayMetrics包中的，getWindowManager()是Activity中的方法。
我使用的测试手机是M9，我们看一下Logcat的打印信息：
```  media_display/04_display.png```
显示正确，M9确实是这个分辨率。
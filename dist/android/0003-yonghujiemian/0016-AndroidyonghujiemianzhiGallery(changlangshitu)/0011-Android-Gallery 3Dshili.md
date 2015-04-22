效果如下：
![img](http://emanual.github.io/md-android/img/view_gallery/12_gallery.jpg)
一、主类：
```  
import java.io.File;
import java.util.ArrayList;
import java.util.List;
import android.app.Activity;
import android.net.Uri;
import android.os.Bundle;
import android.os.Environment;
import android.util.Log;
import android.view.View;
import android.view.animation.AnimationUtils;
import android.widget.AdapterView;
import android.widget.Gallery;
import android.widget.ImageSwitcher;
import android.widget.ImageView;
import android.widget.Toast;
import android.widget.ViewSwitcher;
import android.widget.AdapterView.OnItemClickListener;
import android.widget.Gallery.LayoutParams;
 /**
 *  * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 * 文件名称    : ImageActivity.java
 * 文件描述    : 图片浏览器
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 */
public class ACDsee extends Activity implements
        AdapterView.OnItemSelectedListener, ViewSwitcher.ViewFactory {
     /**
      * 手机设备中图像列表
      */
    private List<String> ImageList;
    private ImageSwitcher mSwitcher;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.image);
        ImageList = getImagesFromSD();
         /**
          * xml获取Switcher 
          */
        mSwitcher = (ImageSwitcher) findViewById(R.id.switcher);
        mSwitcher.setFactory(this);
        mSwitcher.setInAnimation(AnimationUtils.loadAnimation(this,
                android.R.anim.slide_in_left));
        mSwitcher.setOutAnimation(AnimationUtils.loadAnimation(this,
                android.R.anim.slide_out_right));
         /**
          * xml获取Gallery
          */
        Gallery g = (Gallery) findViewById(R.id.mygallery);
        if(ImageList == null || ImageList.size() == 0)
            return;
         /**
          * 创建适配器，注册适配器
          */
        g.setAdapter(new ImageAdapter(this, ImageList));
        g.setOnItemSelectedListener(this);
         /**
          * 点击事件监听器
          */
        g.setOnItemClickListener(new OnItemClickListener() {
            public void onItemClick(AdapterView<?> parent, View v,
                    int position, long id) {
            }
        });
    }
    private List<String> getImagesFromSD() {
        List<String> imageList = new ArrayList<String>();

        File f = new File("/sdcard/");
        if(Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED))
        {
            f = new File(Environment.getExternalStorageDirectory().toString());
        }
        else
        {
            Toast.makeText(ACDsee.this, R.string.sdcarderror, 1).show();
            return imageList;
        }
        File[] files = f.listFiles();
        if(files == null || files.length == 0)
            return imageList;
         /**
          * 将所有图像文件的路径存入ArrayList列表
          */
        for (int i = 0; i < files.length; i++) {
            File file = files;
            if (isImageFile(file.getPath()))
                imageList.add(file.getPath());
        }
        return imageList;
    }
     /**
      * @param fName
      */
    private boolean isImageFile(String fName) {
        boolean re;
        String end = fName
                .substring(fName.lastIndexOf(".") + 1, fName.length())
                .toLowerCase();
         /**
          * 依据文件扩展名判断是否为图像文件
          */
        if (end.equals("jpg") || end.equals("gif") || end.equals("png")
                || end.equals("jpeg") || end.equals("bmp")) {
            re = true;
        } else {
            re = false;
        }
        return re;
    }
    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position,
            long id) {
         /**
          * 获取当前要显示的Image的路径
          */
        String photoURL = ImageList.get(position);
        Log.i("A", String.valueOf(position));
         /**
          * 设置当前要显示的Image的路径     
          */
        mSwitcher.setImageURI(Uri.parse(photoURL));
    }
    @Override
    public void onNothingSelected(AdapterView<?> parent) {
    }
    @Override
    public View makeView() {
        ImageView i = new ImageView(this);
        i.setBackgroundColor(0xFF000000);
        i.setScaleType(ImageView.ScaleType.FIT_XY);
        i.setLayoutParams(new ImageSwitcher.LayoutParams(
                LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT));
        return i;
    }
}
```
二、GalleryFlow 类
```  
import android.content.Context;
import android.graphics.Camera;
import android.graphics.Matrix;
import android.util.AttributeSet;
import android.view.View;
import android.view.animation.Transformation;
import android.widget.Gallery;
import android.widget.ImageView;
 /**
 *  * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 * 文件名称    : GalleryFlow.java
 * 文件描述    : 自定义Gallery视图，实现图片旋转效果
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 */
public class GalleryFlow extends Gallery {
     /**
      * 用于ImageView的矩阵变换
      */
    private Camera mCamera = new Camera();//视角

    private int mMaxRotationAngle = 60; //转换最大的角度
     /**
      * The maximum zoom on the centre Child
      */
    private int mMaxZoom = -120;
     /**
      * Coverflow的中心
      */
    private int mCoveflowCenter;
    public GalleryFlow(Context context) {
            super(context);
            this.setStaticTransformationsEnabled(true);
    }
    public GalleryFlow(Context context, AttributeSet attrs) {
            super(context, attrs);
            this.setStaticTransformationsEnabled(true);
    }
    public GalleryFlow(Context context, AttributeSet attrs, int defStyle) {
            super(context, attrs, defStyle);
            this.setStaticTransformationsEnabled(true);
    }
     /**
      * get set 方法
      */
    public int getMaxRotationAngle() {
            return mMaxRotationAngle;
    }
    public void setMaxRotationAngle(int maxRotationAngle) {
            mMaxRotationAngle = maxRotationAngle;
    }
    public int getMaxZoom() {
            return mMaxZoom;
    }
    public void setMaxZoom(int maxZoom) {
            mMaxZoom = maxZoom;
    }
     /**
      * 获取Coverflow的中心位置
      * @return
      */
    private int getCenterOfCoverflow() {
            return (getWidth() - getPaddingLeft() - getPaddingRight()) / 2
                            + getPaddingLeft();
    }
     /**
      * 获取子视图的中心位置
      * @param view
      */
    private static int getCenterOfView(View view) {
            return view.getLeft() + view.getWidth() / 2;
    }
    protected boolean getChildStaticTransformation(View child, Transformation t) {
        //获得子视图的中心位置
        final int childCenter = getCenterOfView(child);
        //获得子视图的宽度
        final int childWidth = child.getWidth();
        //设置旋转的角度
        int rotationAngle = 0;
         /**
          * 重置transformation
          */
        t.clear();
        t.setTransformationType(Transformation.TYPE_MATRIX);

        if (childCenter == mCoveflowCenter) {
            transformImageBitmap((ImageView) child, t, 0);
        } else {
             /**
              * 计算旋转角度
              */
            rotationAngle = (int)
                    (((float) (mCoveflowCenter - childCenter) / childWidth) * mMaxRotationAngle);
            if (Math.abs(rotationAngle) > mMaxRotationAngle) {
                rotationAngle = (rotationAngle < 0) ? -mMaxRotationAngle: mMaxRotationAngle;
            }
            transformImageBitmap((ImageView) child, t, rotationAngle);
        }
        return true;
    }
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
            mCoveflowCenter = getCenterOfCoverflow();
            super.onSizeChanged(w, h, oldw, oldh);
    }
     /**
      * 将子视图旋转相应角度 ？？？？？？？？？？？？？
      * @param imageView
      *            ImageView the ImageView whose bitmap we want to rotate
      * @param t
      *            transformation
      * @param rotationAngle
      *            the Angle by which to rotate the Bitmap
      */
    private void transformImageBitmap(ImageView child, Transformation t,
                    int rotationAngle) {
         /**
          * 保存相机当前状态
          */
            mCamera.save();
             /**
              * 获取变换矩阵
              */
            final Matrix imageMatrix = t.getMatrix();
            final int imageHeight = child.getLayoutParams().height;
            final int imageWidth = child.getLayoutParams().width;
            final int rotation = Math.abs(rotationAngle);
             /**
              * 在Z轴上正向移动camera的视角，实际效果为放大图片。
              * 如果在Y轴上移动，则图片上下移动；X轴上对应图片左右移动。
              */
            mCamera.translate(0.0f, 0.0f, 100.0f);
            // As the angle of the view gets less, zoom in
            if (rotation < mMaxRotationAngle) {
                    float zoomAmount = (float) (mMaxZoom + (rotation * 1.5));
                    mCamera.translate(0.0f, 0.0f, zoomAmount);
            }
             /**
              * 在Y轴上旋转，对应图片竖向向里翻转。如果在X轴上旋转，则对应图片横向向里翻转。
              */
            mCamera.rotateY(rotationAngle);
            mCamera.getMatrix(imageMatrix);
            imageMatrix.preTranslate(-(imageWidth / 2), -(imageHeight / 2));
            imageMatrix.postTranslate((imageWidth / 2), (imageHeight / 2));
             /**
              * 恢复相机原状态
              */
            mCamera.restore();
    }
}
```
三、ImageAdater
```  
import java.util.List;
import com.sky_dreaming.ACDsee.R;
import android.content.Context;
import android.content.res.TypedArray;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.Canvas;
import android.graphics.LinearGradient;
import android.graphics.Matrix;
import android.graphics.Paint;
import android.graphics.PorterDuffXfermode;
import android.graphics.Bitmap.Config;
import android.graphics.PorterDuff.Mode;
import android.graphics.Shader.TileMode;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;
 /**
 *  * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 * 文件名称    : ImageAdapter.java
 * 文件描述    : 自定义Gallery适配器，添加视图阴影
 * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * * *
 */
public class ImageAdapter extends BaseAdapter {
    private int mGalleryItemBackground;
    private Context mContext;
     /**
      * 图像路径列表
      */
    private List<String> lis;
    public ImageAdapter(Context c,  List<String> li) {
        mContext = c;
        lis = li;
        TypedArray typedArray = mContext.obtainStyledAttributes(R.styleable.Gallery);
        mGalleryItemBackground = typedArray.getResourceId(
                R.styleable.Gallery_android_galleryItemBackground, 0);
        //回收机制
        typedArray.recycle();
    }
    public ImageView createReflectedImages(String filePath) {
        final int reflectionGap = 4;
        Bitmap originalImage = BitmapFactory.decodeFile(filePath);
        int width = originalImage.getWidth();
        int height = originalImage.getHeight();
        Matrix matrix = new Matrix();
        matrix.preScale(1, -1);
        Bitmap reflectionImage = Bitmap.createBitmap(originalImage, 0,
                height / 2, width, height / 2, matrix, false);
        Bitmap bitmapWithReflection = Bitmap.createBitmap(width,
                (height + height / 2), Config.ARGB_8888);
         /**
          * 创建一个绘有bitmapWithReflection的画布，画布质量和bitmapWithReflection质量相同
          * 也就相当于把bitmapWithReflection作为画布使用
          * 该画布高度为 原图 + 间隔 + 倒影
          */
        Canvas canvas = new Canvas(bitmapWithReflection);
         /**
          * 在画布左上角（0,0）绘制原始图
          */
        canvas.drawBitmap(originalImage, 0, 0, null);
        Paint deafaultPaint = new Paint();
        canvas.drawRect(0, height, width, height + reflectionGap,
                deafaultPaint);
        canvas.drawBitmap(reflectionImage, 0, height + reflectionGap, null);
        Paint paint = new Paint();
        LinearGradient shader = new LinearGradient(0, originalImage
                .getHeight(), 0, bitmapWithReflection.getHeight()
                + reflectionGap, 0x70ffffff, 0x00ffffff, TileMode.CLAMP);
        paint.setShader(shader);
        paint.setXfermode(new PorterDuffXfermode(Mode.DST_IN));
             /**
              * 在倒影图上用带阴影的画笔绘制矩形
              */
        canvas.drawRect(0, height, width, bitmapWithReflection.getHeight()
                + reflectionGap, paint);
        ImageView imageView = new ImageView(mContext);
             /**
              * BitmapDrawable bd = new BitmapDrawable(bitmapWithReflection);
              * bd.setAntiAlias=true;
              * imageView.setImageDrawable(bd);
              * 替代下面：
              * imageView.setImageBitmap(bitmapWithReflection);
              * 可实现消除锯齿的效果
              */
        imageView.setImageBitmap(bitmapWithReflection);
        imageView.setLayoutParams(new GalleryFlow.LayoutParams(180, 240));
        imageView.setBackgroundResource(mGalleryItemBackground);
             /**
              * 设置图像缩放模式以适应ImageView大小。ScaleType.MATRIX：绘制时使用图像变换矩阵缩放。
              * 
              * 注意：如果执行此行代码，则原始图盛满整个ImageView，无倒影效果
              */
        //imageView.setScaleType(ScaleType.MATRIX);
        return imageView;
    }
    @Override
    public int getCount() {
        return lis.size();
    }
    @Override
    public Object getItem(int position) {
        return position;
    }
    @Override
    public long getItemId(int position) {
        return position;
    }
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        return createReflectedImages(lis.get(position));
    }
//    public float getScale(boolean focused, int offset) {
//        /* Formula: 1 / (2 ^ offset) */
//        return Math.max(0, 1.0f / (float) Math.pow(2, Math.abs(offset)));
//    }
}
```
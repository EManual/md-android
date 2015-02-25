效果如下：
![img](P)  
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
[Tags]/**
[Tags]* [Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*
[Tags]* 文件名称    : ImageActivity.java
[Tags]* 文件描述    : 图片浏览器
[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*
[Tags]*/
public class ACDsee extends Activity implements
        AdapterView.OnItemSelectedListener, ViewSwitcher.ViewFactory {
    [Tags]/**
     [Tags]* 手机设备中图像列表
     [Tags]*/
    private List<String> ImageList;
    private ImageSwitcher mSwitcher;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.image);
        ImageList = getImagesFromSD();
        [Tags]/**
         [Tags]* xml获取Switcher 
         [Tags]*/
        mSwitcher = (ImageSwitcher) findViewById(R.id.switcher);
        mSwitcher.setFactory(this);
        mSwitcher.setInAnimation(AnimationUtils.loadAnimation(this,
                android.R.anim.slide_in_left));
        mSwitcher.setOutAnimation(AnimationUtils.loadAnimation(this,
                android.R.anim.slide_out_right));
        [Tags]/**
         [Tags]* xml获取Gallery
         [Tags]*/
        Gallery g = (Gallery) findViewById(R.id.mygallery);
        if(ImageList == null || ImageList.size() == 0)
            return;
        [Tags]/**
         [Tags]* 创建适配器，注册适配器
         [Tags]*/
        g.setAdapter(new ImageAdapter(this, ImageList));
        g.setOnItemSelectedListener(this);
        [Tags]/**
         [Tags]* 点击事件监听器
         [Tags]*/
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
        [Tags]/**
         [Tags]* 将所有图像文件的路径存入ArrayList列表
         [Tags]*/
        for (int i = 0; i < files.length; i++) {
            File file = files;
            if (isImageFile(file.getPath()))
                imageList.add(file.getPath());
        }
        return imageList;
    }
    [Tags]/**
     [Tags]* @param fName
     [Tags]*/
    private boolean isImageFile(String fName) {
        boolean re;
        String end = fName
                .substring(fName.lastIndexOf(".") + 1, fName.length())
                .toLowerCase();
        [Tags]/**
         [Tags]* 依据文件扩展名判断是否为图像文件
         [Tags]*/
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
        [Tags]/**
         [Tags]* 获取当前要显示的Image的路径
         [Tags]*/
        String photoURL = ImageList.get(position);
        Log.i("A", String.valueOf(position));
        [Tags]/**
         [Tags]* 设置当前要显示的Image的路径     
         [Tags]*/
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
[Tags]/**
[Tags]* [Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*
[Tags]* 文件名称    : GalleryFlow.java
[Tags]* 文件描述    : 自定义Gallery视图，实现图片旋转效果
[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*
[Tags]*/
public class GalleryFlow extends Gallery {
    [Tags]/**
     [Tags]* 用于ImageView的矩阵变换
     [Tags]*/
    private Camera mCamera = new Camera();//视角

    private int mMaxRotationAngle = 60; //转换最大的角度
    [Tags]/**
     [Tags]* The maximum zoom on the centre Child
     [Tags]*/
    private int mMaxZoom = -120;
    [Tags]/**
     [Tags]* Coverflow的中心
     [Tags]*/
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
    [Tags]/**
     [Tags]* get set 方法
     [Tags]*/
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
    [Tags]/**
     [Tags]* 获取Coverflow的中心位置
     [Tags]* @return
     [Tags]*/
    private int getCenterOfCoverflow() {
            return (getWidth() - getPaddingLeft() - getPaddingRight()) / 2
                            + getPaddingLeft();
    }
    [Tags]/**
     [Tags]* 获取子视图的中心位置
     [Tags]* @param view
     [Tags]*/
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
        [Tags]/**
         [Tags]* 重置transformation
         [Tags]*/
        t.clear();
        t.setTransformationType(Transformation.TYPE_MATRIX);

        if (childCenter == mCoveflowCenter) {
            transformImageBitmap((ImageView) child, t, 0);
        } else {
            [Tags]/**
             [Tags]* 计算旋转角度
             [Tags]*/
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
    [Tags]/**
     [Tags]* 将子视图旋转相应角度 ？？？？？？？？？？？？？
     [Tags]* @param imageView
     [Tags]*            ImageView the ImageView whose bitmap we want to rotate
     [Tags]* @param t
     [Tags]*            transformation
     [Tags]* @param rotationAngle
     [Tags]*            the Angle by which to rotate the Bitmap
     [Tags]*/
    private void transformImageBitmap(ImageView child, Transformation t,
                    int rotationAngle) {
        [Tags]/**
         [Tags]* 保存相机当前状态
         [Tags]*/
            mCamera.save();
            [Tags]/**
             [Tags]* 获取变换矩阵
             [Tags]*/
            final Matrix imageMatrix = t.getMatrix();
            final int imageHeight = child.getLayoutParams().height;
            final int imageWidth = child.getLayoutParams().width;
            final int rotation = Math.abs(rotationAngle);
            [Tags]/**
             [Tags]* 在Z轴上正向移动camera的视角，实际效果为放大图片。
             [Tags]* 如果在Y轴上移动，则图片上下移动；X轴上对应图片左右移动。
             [Tags]*/
            mCamera.translate(0.0f, 0.0f, 100.0f);
            // As the angle of the view gets less, zoom in
            if (rotation < mMaxRotationAngle) {
                    float zoomAmount = (float) (mMaxZoom + (rotation * 1.5));
                    mCamera.translate(0.0f, 0.0f, zoomAmount);
            }
            [Tags]/**
             [Tags]* 在Y轴上旋转，对应图片竖向向里翻转。如果在X轴上旋转，则对应图片横向向里翻转。
             [Tags]*/
            mCamera.rotateY(rotationAngle);
            mCamera.getMatrix(imageMatrix);
            imageMatrix.preTranslate(-(imageWidth / 2), -(imageHeight / 2));
            imageMatrix.postTranslate((imageWidth / 2), (imageHeight / 2));
            [Tags]/**
             [Tags]* 恢复相机原状态
             [Tags]*/
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
[Tags]/**
[Tags]* [Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*
[Tags]* 文件名称    : ImageAdapter.java
[Tags]* 文件描述    : 自定义Gallery适配器，添加视图阴影
[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*[Tags]*
[Tags]*/
public class ImageAdapter extends BaseAdapter {
    private int mGalleryItemBackground;
    private Context mContext;
    [Tags]/**
     [Tags]* 图像路径列表
     [Tags]*/
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
        [Tags]/**
         [Tags]* 创建一个绘有bitmapWithReflection的画布，画布质量和bitmapWithReflection质量相同
         [Tags]* 也就相当于把bitmapWithReflection作为画布使用
         [Tags]* 该画布高度为 原图 + 间隔 + 倒影
         [Tags]*/
        Canvas canvas = new Canvas(bitmapWithReflection);
        [Tags]/**
         [Tags]* 在画布左上角（0,0）绘制原始图
         [Tags]*/
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
            [Tags]/**
             [Tags]* 在倒影图上用带阴影的画笔绘制矩形
             [Tags]*/
        canvas.drawRect(0, height, width, bitmapWithReflection.getHeight()
                + reflectionGap, paint);
        ImageView imageView = new ImageView(mContext);
            [Tags]/**
             [Tags]* BitmapDrawable bd = new BitmapDrawable(bitmapWithReflection);
             [Tags]* bd.setAntiAlias=true;
             [Tags]* imageView.setImageDrawable(bd);
             [Tags]* 替代下面：
             [Tags]* imageView.setImageBitmap(bitmapWithReflection);
             [Tags]* 可实现消除锯齿的效果
             [Tags]*/
        imageView.setImageBitmap(bitmapWithReflection);
        imageView.setLayoutParams(new GalleryFlow.LayoutParams(180, 240));
        imageView.setBackgroundResource(mGalleryItemBackground);
            [Tags]/**
             [Tags]* 设置图像缩放模式以适应ImageView大小。ScaleType.MATRIX：绘制时使用图像变换矩阵缩放。
             [Tags]* 
             [Tags]* 注意：如果执行此行代码，则原始图盛满整个ImageView，无倒影效果
             [Tags]*/
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
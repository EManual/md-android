#### 一、布局
gallery3d的界面生成和普通的应用程序不一样。普通程序一般一个界面就是一个activity,布局用xml或代码都可以实现，界面切换是activity的切换方式；而gallery3d没有用android的UI系统，而是用opengl画出来的,即界面是在同一个activity的，如主界面，缩略图界面，单张图片查看界面，标记界面等都属于同一个activity。那么这界面布局不同的界面是如何组合到一起的呢？分析代码，可以把它看成一个状态机：   
1、标记模式
```  
public static final int MODE_SELECT = 1;(HudLayer)
```
包含了主界面标记模式，缩略界面矩阵游览时标记模式、缩略图界面分类游览时标记模式3个界面
2、普通模式  
```  
public static final int MODE_NORMAL = 0;(HudLayer)
```
包含了    
```  
public static final int STATE_MEDIA_SETS = 0;主界面
public static final int STATE_GRID_VIEW = 1;缩略图矩阵浏览
public static final int STATE_FULL_SCREEN = 2;查看界面
public static final int STATE_TIMELINE = 3;缩略图界面分类浏览
```
有了以上状态分类后，在渲染的时候就能根据些界面的组成来定哪些控件譔隐藏，哪些要显示了。
下面是基本控件：
```  
com.cooliris.media.GridLayer
com.cooliris.media.BackgroundLayer
com.cooliris.media.HudLayer
com.cooliris.media.ImageButton
com.cooliris.media.TimeBar
com.cooliris.media.MenuBar
com.cooliris.media.PopupMenu
com.cooliris.media.PathBarLayer
```
在渲染时，每一帧所有界面上的元素都画了，由于根据上面的状态只把特定窗口的特定元素显示出来，其它窗口中的隐藏，所以不会乱。
Layer是上面控件的基类，上面控件的类也就有了下面两个方法来隐藏不譔显示的界面元素。
```  
public boolean isHidden() {
	return mHidden;
}
public void setHidden(boolean hidden) {
	if (mHidden != hidden) {
		mHidden = hidden;
		onHiddenChanged();
	}
}
```
下面是根据上面分类来画不同元素所用的标识:
```  
public static final int PASS_THUMBNAIL_CONTENT = 0;
public static final int PASS_FOCUS_CONTENT = 1;
public static final int PASS_FRAME = 2;
public static final int PASS_PLACEHOLDER = 3;
public static final int PASS_FRAME_PLACEHOLDER = 4;
public static final int PASS_TEXT_LABEL = 5;
public static final int PASS_SELECTION_LABEL = 6;
public static final int PASS_VIDEO_LABEL = 7;
public static final int PASS_LOCATION_LABEL = 8;
public static final int PASS_MEDIASET_SOURCE_LABEL = 9;
drawDisplayItem(view, gl, displayItem, texture, PASS_THUMBNAIL_CONTENT, placeholder,
                                displayItem.mAnimatedPlaceholderFade); 画缩略图的，注掉此句，前两屏只显示框，第三屏OK
drawDisplayItem(view, gl, displayItem, texture, PASS_FOCUS_CONTENT, null, 0.0f);画单张图片的，注掉，第三屏黑屏
drawDisplayItem(view, gl, itemDrawn, textureToUse, PASS_FRAME, previousTexture, ratio);画边框的，注掉，前两屏明显没有边框，巨齿明显
drawDisplayItem(view, gl, displayItem, textureString, PASS_TEXT_LABEL, null, 0);画文本标签的
drawDisplayItem(view, gl, displayItem, textureToUse, PASS_SELECTION_LABEL, null, 0);画选中标记的
drawDisplayItem(view, gl, displayItem, videoTexture, PASS_VIDEO_LABEL, null, 0);画视频标记的
drawDisplayItem(view, gl, displayItem, locationTexture, PASS_LOCATION_LABEL, null, 0);画位置标记的
drawDisplayItem(view, gl, displayItem, locationTexture, PASS_MEDIASET_SOURCE_LABEL,
                                        transparentTexture, 0.85f);画源来源图标的(相机或一般文件夹)
```
#### 二、特效
举如何显示一张图片为例，在图片完全显示出来经过这样一个过程，附近的图片渐小渐出，当前图片渐大渐入，当前图片逐渐变大直到全屏。实现这个特效，要进行很多帧的渲染。就是说并不是只调一次onDrawFrame函数就可以了，要调用多次。可以把这个特效的实现想成一个状态变化的过程，在每一个状态，纹理的显示大小和位置都不同，这也符合动画的基本原理。放大、缩小我们只要改变顶点数据就可以做到，gallery3d也是这样做的，下面是主要代码：
我们知道调用onDrawFrame来渲染，最后调到下面的drawFocusItems函数，
```  
GridQuad quad = GridDrawables.sFullscreenGrid[vboIndex];
float u = texture.getNormalizedWidth();
float v = texture.getNormalizedHeight();
float imageWidth = texture.getWidth();
float imageHeight = texture.getHeight();
boolean portrait = ((theta / 90) % 2 == 1);
if (portrait) {
    viewAspect = 1.0f / viewAspect;
}
quad.resizeQuad(viewAspect, u, v, imageWidth, imageHeight);//改变用来贴图片的长方形的大小
quad.bindArrays(gl);//绑定新数据，为渲染做准备。
```
而位置的改变有两种方式，一种是直接以顶点数据中改变，另一种是计算出在3维3个方向的偏移量，再调用gltranslate来做，从代码可以看出采用的是第二种方式来做的，比第一种方式更方便一些。代码：
gl.glTranslatef(-translateXf, -translateYf, -translateZf);
而这里的3个偏移量的计算是和camera相关的，相关文件为GridCamera.java，GridCameraManager.java，过程很复杂，理清楚后再细化吧。
#### cache管理
下面是cache文件
```  
/sdcard/Android/data/com.cooliris.media/cache/local-album-cache
d---rwxr-x system   sdcard_rw          2010-05-21 09:56 local-album-cache
d---rwxr-x system   sdcard_rw          2010-05-21 09:56 local-meta-cache
----rwxr-x system   sdcard_rw   299877 2010-05-28 07:36 local-album-cachechunk_0
d---rwxr-x system   sdcard_rw          2010-05-21 09:56 geocoder-cache
----rwxr-x system   sdcard_rw      284 2010-05-28 07:36 local-album-cacheindex
d---rwxr-x system   sdcard_rw          2010-05-21 09:56 local-image-thumbs
d---rwxr-x system   sdcard_rw          2010-05-21 09:56 local-video-thumbs
d---rwxr-x system   sdcard_rw          2010-05-21 09:56 picasa-thumbs
----rwxr-x system   sdcard_rw       80 2010-05-28 07:36 local-meta-cachechunk_0
----rwxr-x system   sdcard_rw      164 2010-05-28 07:36 local-meta-cacheindex
d---rwxr-x system   sdcard_rw          2010-05-21 09:56 hires-image-cache
----rwxr-x system   sdcard_rw   627629 2010-05-28 07:37 local-image-thumbschunk_0
----rwxr-x system   sdcard_rw     3914 2010-05-21 09:56 local-image-thumbsindex
----rwxr-x system   sdcard_rw    53343 2010-05-28 07:34 hires-image-cache-4982941342287215583_1024.cache
----rwxr-x system   sdcard_rw   237692 2010-05-28 07:33 hires-image-cache3684568484369117627_1024.cache
----rwxr-x system   sdcard_rw   133182 2010-05-28 07:34 hires-image-cache607542544081226432_1024.cache
----rwxr-x system   sdcard_rw    83223 2010-05-28 07:34 hires-image-cache4275479623210216146_1024.cache
----rwxr-x system   sdcard_rw   292837 2010-05-28 07:34 hires-image-cache-646316556936433937_1024.cache
----rwxr-x system   sdcard_rw   191377 2010-05-28 07:35 hires-image-cache2631364604509958174_1024.cache
----rwxr-x system   sdcard_rw   366905 2010-05-28 07:35 hires-image-cache-3280562009766080884_1024.cache
----rwxr-x system   sdcard_rw   323671 2010-05-28 07:35 hires-image-cache5752471827533329222_1024.cache
```
创建cache的关键代码
LocalDataSource
```  
public static final DiskCache sThumbnailCache = new DiskCache("local-image-thumbs");----------------------local-image-thumbs local-image-thumbschunk_0  local-image-thumbsindex
public static final DiskCache sThumbnailCacheVideo = new DiskCache("local-video-thumbs");--------------------local-video-thumbs
public static final DiskCache sAlbumCache = new DiskCache("local-album-cache");----------------------local-album-cache  local-album-cacheindex
public static final DiskCache sMetaAlbumCache = new DiskCache("local-meta-cache");------------------local-meta-cache  local-meta-cacheindex
getChunkFile --------------local-meta-cachechunk_0  local-album-cachechunk_0
ReverseGeocoder::  private static final DiskCache sGeoCache = new DiskCache("geocoder-cache"); -------------------------geocoder-cache
PicasaDataSource:: public static final DiskCache sThumbnailCache = new DiskCache("picasa-thumbs");-----------------------------picasa-thumbs
UriTexture::writeToCache  --------------------------hires-image-cache-xxx_1024.cache
```
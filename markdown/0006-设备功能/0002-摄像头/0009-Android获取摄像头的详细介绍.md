如何获取Android设备上的详细的摄像头信息呢？ 目前Samsung的Galaxy Tab和Nexus S均有前置摄像头，获取Android摄像头的详细信息，在Android 2.3 SDK中得到了增强：
在android.hardware.Camera类中，API Level 9的SDK中加入了两个比较重要的方法，使用getNumberOfCameras这个static类型方法可以获取当前Android设备上的摄像头数量，比如Nexus S有两个，方法原型如下
```  
public static int getNumberOfCameras()
```
而对于具体的每个摄像头的信息，可以通过Camera类的getCameraInfo()这个静态方法获取，该方法有两个参数，参数一的ID，我们通过getNumberOfCameras获取的值减1即可，类似数组索引从0开始一样，用循环遍历每个摄像头信息，参数二是 android.hardware.Camera.CameraInfo类，有关getCameraInfo方法的原型如下：
public static void getCameraInfo (int cameraId，Camera.CameraInfo cameraInfo)
对于Camera.CameraInfo类而言，比较简单，包含两个字段
public int facing 代表摄像头的方位，目前有定义值两个分别为CAMERA_FACING_FRONT前置和CAMERA_FACING_BACK后置
public int orientation 下面是拍照的旋转方向，一般自然些有0度、90度、180度和270度，这样可以获取我们正确的手握设备是横着还是竖着，有关拍照时的方向设置，可以参考下面的代码设置：
```  
public static void setCameraDisplayOrientation(Activity activity,
		int cameraId, android.hardware.Camera camera) {
	android.hardware.Camera.CameraInfo info = new android.hardware.Camera.CameraInfo();
	android.hardware.Camera.getCameraInfo(cameraId, info);
	int rotation = activity.getWindowManager().getDefaultDisplay()
			.getRotation();
	int degrees = 0;
	switch (rotation) {
	case Surface.ROTATION_0:
		degrees = 0;
		break;
	case Surface.ROTATION_90:
		degrees = 90;
		break;
	case Surface.ROTATION_180:
		degrees = 180;
		break;
	case Surface.ROTATION_270:
		degrees = 270;
		break;
	}
	int result;
	if (info.facing == Camera.CameraInfo.CAMERA_FACING_FRONT) {
		result = (info.orientation + degrees) % 360;
		result = (360 - result) % 360; // compensate the mirror
	} else { // back-facing
		result = (info.orientation - degrees + 360) % 360;
	}
	camera.setDisplayOrientation(result);
}
```
运行时检测OpenGL ES版本
对此Android文档中并没有提及，但是<NDK>/sampels/hello-gl2 示例工程中透露出了一些蛛丝马迹，从中我们可以归纳出，要进行OpenGL ES 2.0的渲染，需要有2个步骤：
（1）创建OpenGL ES 2.0 的 Rendering Context
EGL 创建 Context 的函数是
```  
EGLContext eglCreateContext(EGLDisplay display, EGLConfig config, EGLContext share_context, EGLint const * attrib_list)
```
最后一个参数我们一般会给一个NULL值.根据 EGL 1.0 规范，最后一个参数不使用，但是EGL实现可能扩展此参数用于特定目的.Android 的 EGL 正是在此处引入一个值为 0x3098 的属性，用于指定 OpenGL ES 的版本.OpenGL ES 2.0 的版本值为 2
（2）获取 OpenGL ES 2.0 的 EGL Config
对于
```  
EGLBoolean eglChooseConfig(EGLDisplay display, EGLint const * attrib_list, EGLConfig * configs, EGLint config_size, EGLint * num_config)
```
的attrib_list 参数，Android 引入了一个 EGL10.EGL_RENDERABLE_TYPE=0x3040 的属性，EGL 1.0 规范中并未规定此属性.要进行OpenGL ES 2.0 的渲染，需指定此属性值为 4
根据以上两点，我们可以在运行时首先初始化 EGL 支持 OpenGL ES 2.0，如果失败，则回退到 OpenGL ES 1.x，如下：
```  
public class EGLHelper {
	public static final int OPENGL_ES_VERSION_1x = 1;
	public static final int OPENGL_ES_VERSION_2 = 2;
	private static final int EGL_CONTEXT_CLIENT_VERSION = 0x3098;
	private static final int EGL_OPENGL_ES2_BIT = 4;
	private EGL10 egl;
	private EGLDisplay eglDisplay;
	private EGLConfig eglConfig;
	private EGLContext eglContext;
	private EGLSurface eglSurface;
	public EGLHelper() {
	}
	public boolean initialize(SurfaceHolder holder, int glesVersion) {
		if (OPENGL_ES_VERSION_1x != glesVersion
				&& OPENGL_ES_VERSION_2 != glesVersion) {
			throw new IllegalArgumentException(
					"GL ES version has to be one of " + OPENGL_ES_VERSION_1x
							+ " and " + OPENGL_ES_VERSION_2);
		}
		// Choose an EGLConfig
		int[] attrList;
		if (OPENGL_ES_VERSION_1x == glesVersion) {
			attrList = new int[] {
			// EGL10.EGL_SURFACE_TYPE, EGL10.EGL_WINDOW_BIT,
			// EGL10.EGL_RED_SIZE, 8,
			// EGL10.EGL_GREEN_SIZE, 8,
			// EGL10.EGL_BLUE_SIZE, 8,
			// EGL10.EGL_DEPTH_SIZE, 16,
			// EGL10.EGL_NONE // };
			};
		} else {
			attrList = new int[] {
			// EGL10.EGL_RENDERABLE_TYPE, EGL_OPENGL_ES2_BIT,
			// EGL10.EGL_SURFACE_TYPE, EGL10.EGL_WINDOW_BIT,
			// EGL10.EGL_RED_SIZE, 8,
			// EGL10.EGL_GREEN_SIZE, 8,
			// EGL10.EGL_BLUE_SIZE, 8,
			// EGL10.EGL_DEPTH_SIZE, 16,
			// EGL10.EGL_NONE //
			};
		}
		EGLConfig[] configOut = new EGLConfig[1];
		int[] configNumOut = new int[1];
		if (egl.eglChooseConfig(eglDisplay, attrList, configOut, 1,
				configNumOut) && 1 == configNumOut[0]) {
			eglConfig = configOut[0];
		} else {
			// … return false;
		}
		// Create rendering context int[] contextAttrs;
		if (OPENGL_ES_VERSION_1x == glesVersion) {
			contextAttrs = null;
		} else {
			contextAttrs = new int[] { EGL_CONTEXT_CLIENT_VERSION,
					OPENGL_ES_VERSION_2,
			// EGL10.EGL_NONE
			};
		}
		eglContext = egl.eglCreateContext(eglDisplay, eglConfig,
				EGL10.EGL_NO_CONTEXT, contextAttrs);
		if (null == eglContext || EGL10.EGL_NO_CONTEXT == eglContext) {
			// … return false;
		}// … return true;
	}
	public void destroy() {
		// …
	}
}
```
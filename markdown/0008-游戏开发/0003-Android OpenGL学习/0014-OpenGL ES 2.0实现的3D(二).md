然后
```  
eglHelper = new EGLHelper(); 
if (eglHelper.initialize(getHolder(), EGLHelper.OPENGL_ES_VERSION_2)) { 
	// Create RenderingEngine2 
} else if (eglHelper.initialize(getHolder(), EGLHelper.OPENGL_ES_VERSION_1x)) { 
	// Create RenderingEngine1 
} else { 
	// ... 
}
```
Shader
OpenGL ES 2.0引入了Shader的概念。Shader就是交给OpenGL去执行的一段程序，用OpenGL Shading Language（GLSL）编写。Shader分为 Vertex Shader和Fragement Shader两类，Vertex Shader用于处理通过glDrawArrays()提交给 OpenGL 的顶点数据，Fragement Shader 用于计算顶点的颜色
目前我所知的就是这么多了.本篇 HellowArrow 程序所用的 Vertex Shader 和 Fragement Shader 我直接从原书复制.Vertex Shader 源代码文件 Shade.vert内容为
```  
const char* VERTEX_SHADER =STRINGIFY(attribute vec4 Position;
	attribute vec4 SourceColor;
	varying vec4 DestinationColor;
	uniform mat4 Projection;
	uniform mat4 Modelview;
	void main(void){ 
	DestinationColor = SourceColor;
	gl_Position = Projection * Modelview * Position;
});
```
很怪异的语法，后面再学习。Fragement Shader 源文件 Shader.frag 的内容为：
```  
const char * FRAG_SHADER=STRINGIFY(varying lowp vec4 DestinationColor;
void main(void){ 
	gl_FragColor = DestinationColor;
});
```
这两个Shader源文件其实是两个STRINGIFY 宏的扩展（或者说调用吧），GLSL 作为宏变量 。STRINGIFY 宏定义在 RenderingEngine2.c 中，并且用include 将这两个文件的内容包含进来：
```  
define STRINGIFY(A) Ainclude "Shader.vert"include "Shader.frag
```
注意上面 STRINGIFY 宏定义中“A”前缀的  符号。 是一个 c 预处理符，它将宏变量转换为字符串字面值，例如：
```  
define AS_STRING(A) Aconst char * str = AS_STRING(this is a c string!);
const char * str_qt = AS_STRING("this is a quoted c string!");
```
因此，前面的 RenderingEngine2.c 代码片断定义了 VERTEX_SHADER 和 FRAG_SHADER 两个字符串变量，字符串内容分别为 Vertex Shade 和 Fragement Shader 代码.这两段代码会在运行时提交给 OpenGL 编译并执行
RenderingEngine2.c
RenderingEngine2.c 对应于前一篇中的 RenderingEngine1.c，OpenGL 绘图操作（包括动画）都在这个文件中实现，只不过这次用OpenGL ES 2.0而不是 OpenGL ES 1.x 来实现.下面我只列出RenderingEngine2.c与RenderingEngine1.c中不同的地方
initialize()与RenderingEngine1.c的initialize()函数一样，主要功能还是设置Viewport和正交投影矩阵，代码为：
```  
static GLuint simpleProgram;
void initialize(int width, int height) { 
	glViewport(0, 0, width, height);
	simpleProgram = buildProgram(VERTEX_SHADER, FRAG_SHADER);
	glUseProgram(simpleProgram);
	// Initialize the projection 
	matrix. applyOrtho(2, 3);
}
```
glViewport()函数在前一篇讲过了，它设置OpenGL绘图的范围接下来出现了OpenGL ES 2.0的新东西。OpenGL ES 2.0将几个Shader链接成一个单元，称为 Program（程序），我们这里用一个buildProgram()函数创建一个Program，它由前面定义的Vertex Shader和Fragement Shader构成。buildProgram()函数的代码是：
```  
static GLuint buildProgram(const char* vertexShaderSource, const char* fragmentShaderSource) { 
	GLuint vertexShader = buildShader(vertexShaderSource, GL_VERTEX_SHADER);
	GLuint fragmentShader = buildShader(fragmentShaderSource, GL_FRAGMENT_SHADER);
	GLuint programHandle = glCreateProgram(); glAttachShader(programHandle, vertexShader); glAttachShader(programHandle, fragmentShader);
	glLinkProgram(programHandle);
	GLint linkSuccess;
	glGetProgramiv(programHandle, GL_LINK_STATUS, &linkSuccess);
	if (linkSuccess == GL_FALSE) { 
	// ... 
	} 
	return programHandle;
}
```
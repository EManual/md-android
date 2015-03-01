render()
render() 又是一段看不懂的代码啊：
```  
void render() { 
	glClearColor(0.5f, 0.5f, 0.5f, 1);
	glClear(GL_COLOR_BUFFER_BIT);
	applyRotation(-currentDegree);
	GLuint positionSlot = glGetAttribLocation(simpleProgram, "Position");
	GLuint colorSlot = glGetAttribLocation(simpleProgram, "SourceColor");
	glEnableVertexAttribArray(positionSlot);
	glEnableVertexAttribArray(colorSlot);
	GLsizei stride = sizeof(struct Vertex);
	const GLvoid* pCoords = vertices[0].position; const GLvoid* pColors = vertices[0].color; glVertexAttribPointer(positionSlot, 2, GL_FLOAT, GL_FALSE, stride, pCoords);
	glVertexAttribPointer(colorSlot, 4, GL_FLOAT, GL_FALSE, stride, pColors);
	GLsizei vertexCount = sizeof(vertices) / sizeof(struct Vertex);
	glDrawArrays(GL_TRIANGLES, 0, vertexCount);
	glDisableVertexAttribArray(positionSlot);
	glDisableVertexAttribArray(colorSlot);
}
```
glClearColor()、glClear() 清屏
旋转图形，原来 OpenGL ES 1.0 是 glRotatef()，现在通过自定义函数 applyRotate()：
```  
static void applyRotation(float degrees) { 
	float radians = degrees * 3.14159f / 180.0f; 
	float s = sin(radians); 
	float c = cos(radians); 
	float zRotation[16] = { 
		// c, s, 0, 0, 
		// -s, c, 0, 0,
		// 0, 0, 1, 0,
		// 0, 0, 0, 1
		// }; 
	GLint modelviewUniform = glGetUniformLocation(simpleProgram, "Modelview");
	glUniformMatrix4fv(modelviewUniform, 1, 0, &zRotation[0]);
}
```
跟前面initialize()里面的applyOrtho()一样，直接用矩阵运算...无论如何，applyRotate()将图形旋转了-currentDegree，这个和前一篇最终结果是一样的。
接下来又不同了.OpenGL ES 1.0 通过 glVertexPointer()、glColorPointer() 函数将顶点坐标、颜色分量数组的地址提交给OpenGL，现在对应的函数是 两个 glXxxAttribPointer()，它们的第1个参数似乎和前面的 Vertex Shader 联系上了。大概的流程是一致的。
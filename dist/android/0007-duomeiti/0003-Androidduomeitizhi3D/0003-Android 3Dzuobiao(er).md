在进行平移，旋转，缩放变换时，所有的变换都是针对当前的矩阵（与当前矩阵相乘），如果需要将当前矩阵回复最初的无变换的矩阵，可以使用单位矩阵（无平移，缩放，旋转）。
public abstract void glLoadIdentity()。在栈中保存当前矩阵和从栈中恢复所存矩阵，可以使用public abstract void glPushMatrix()和public abstract void glPopMatrix()。
在进行坐标变换的一个好习惯是在变换前使用glPushMatrix保存当前矩阵，完成坐标变换操作后，再调用glPopMatrix恢复原先的矩阵设置。
最后利用上面介绍的坐标变换知识，来绘制3个正方形A,B,C。进行缩放变换，使的B比A小50%，C比B小50%。 然后以屏幕中心逆时针旋转A，B以A为中心顺时针旋转，C以B为中心顺时针旋转同时以自己中心高速逆时针旋转。修改 onDrawFrame 代码如下：
```  
public void onDrawFrame(GL10 gl) {
	gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
	gl.glLoadIdentity();
	gl.glTranslatef(0, 0, -10);
	gl.glPushMatrix();
	gl.glRotatef(angle, 0, 0, 1);
	square.draw(gl);
	gl.glPopMatrix();
	gl.glPushMatrix();
	gl.glRotatef(-angle, 0, 0, 1);
	gl.glTranslatef(2, 0, 0);
	gl.glScalef(.5f, .5f, .5f);
	square.draw(gl);
	gl.glPushMatrix();
	gl.glRotatef(-angle, 0, 0, 1);
	gl.glTranslatef(2, 0, 0);
	gl.glScalef(.5f, .5f, .5f);
	gl.glRotatef(angle * 10, 0, 0, 1);
	square.draw(gl);
	gl.glPopMatrix();
	gl.glPopMatrix();
	angle++;
}
```
效果图：
![img](P)  
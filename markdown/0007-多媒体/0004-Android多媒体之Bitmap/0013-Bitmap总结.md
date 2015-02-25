加载图片方法总结（注意：getResources()其实是myContext.getResources()）
1.适用于背景
```  
private Bitmap score_bar_bg = BitmapFactory.decodeResource(getResources(), R.drawable.score_bar_bg);// 分数条的背景图片
canvas.drawBitmap(score_bar_bg, X方向位置, Y方向位置, null);// 绘制score_bar_bg
```
2.适用于某边的位置有要求时
```  
private Drawable score_bar_bg = getResources().getDrawable(R.drawable.score_bar_bg);
score_bar_bg.setBounds(
                        距左端距离(left）,
                        距顶端距离（top）,
                        距右端距离（right）,
                        距底端距离（bottom）);
myScoreBarBGImage.draw(canvas);
```
3.其他
```  
private Bitmap score_bar_bg =Bitmap.createScaledBitmap(BitmapFactory.decodeResource(getResources(), R.drawable.score_bar_bg),宽,高, true);
canvas.drawBitmap(score_bar_bg, X方向位置, Y方向位置,null);
```
综上 还是第3种好啊。
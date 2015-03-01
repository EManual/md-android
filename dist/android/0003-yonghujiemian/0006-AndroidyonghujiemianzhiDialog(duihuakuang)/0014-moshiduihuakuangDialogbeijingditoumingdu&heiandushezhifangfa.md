#### 设置透明度（貌似是dialog自身的透明度）
```  
WindowManager.LayoutParams lp=dialog.getWindow().getAttributes();
lp.alpha=1.0f;
dialog.getWindow().setAttributes(lp);
```            
alpha在0.0f到1.0f之间。1.0完全不透明，0.0f完全透明
#### 设置黑暗度
```  
dialog.setContentView(R.layout.dialog);           
WindowManager.LayoutParams lp=dialog.getWindow().getAttributes();
lp.dimAmount=1.0f;
dialog.getWindow().setAttributes(lp);
dialog.getWindow().addFlags(WindowManager.LayoutParams.FLAG_DIM_BEHIND);
```
dimAmount在0.0f和1.0f之间，0.0f完全不暗，1.0f全暗
还有个FLAG用途设置背景模糊，WindowManager.LayoutParams.FLAG_BLUR_BEHIND，却不知道如何用
代码如下：
```  
Dialog d = new Dialog(NavActivity.this);
Window window = d.getWindow();
window.setFlags(WindowManager.LayoutParams.FLAG_BLUR_BEHIND,
WindowManager.LayoutParams.FLAG_BLUR_BEHIND);
d.show();
```
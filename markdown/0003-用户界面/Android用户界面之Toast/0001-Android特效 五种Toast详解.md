Toast是Android中用来显示显示信息的一种机制，和Dialog不一样的是，Toast是没有焦点的，而且Toast显示的时间有限，过一定的时间就会自动消失。
1.默认效果:
![img](P)  
```  
Toast.makeText(getApplicationContext(), "默认Toast样式",
Toast.LENGTH_SHORT).show();
```
2.自定义显示位置效果:
![img](P)  
```  
toast = Toast.makeText(getApplicationContext(), "自定义位置Toast", Toast.LENGTH_LONG);
toast.setGravity(Gravity.CENTER, 0, 0);
toast.show();
```
3.带图片效果:
![img](P)  
```  
toast = Toast.makeText(getApplicationContext(), "带图片的Toast", Toast.LENGTH_LONG);
toast.setGravity(Gravity.CENTER, 0, 0);
LinearLayout toastView = (LinearLayout) toast.getView();
ImageView imageCodeProject = new ImageView(getApplicationContext());
imageCodeProject.setImageResource(R.drawable.icon);
toastView.addView(imageCodeProject, 0);
toast.show();
```
4.完全自定义效果:
![img](P)  
```  
LayoutInflater inflater = getLayoutInflater();
View layout = inflater.inflate(R.layout.custom, (ViewGroup) findViewById(R.id.llToast));
ImageView image = (ImageView) layout.findViewById(R.id.tvImageToast);
image.setImageResource(R.drawable.icon);
TextView title = (TextView) layout.findViewById(R.id.tvTitleToast);
title.setText("Attention");
TextView text = (TextView) layout.findViewById(R.id.tvTextToast);
text.setText("完全自定义Toast");
toast = new Toast(getApplicationContext());
toast.setGravity(Gravity.RIGHT | Gravity.TOP, 12, 40);
toast.setDuration(Toast.LENGTH_LONG);
toast.setView(layout);
toast.show();
```
5.其他线程:
![img](P)  
代码:
```  
new Thread(new Runnable() {
	public void run() {
		showToast();
	}
}).start();
```
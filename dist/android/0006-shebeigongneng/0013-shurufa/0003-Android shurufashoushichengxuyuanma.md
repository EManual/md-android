让手机根据自己的手势写字体，直接给出下载地址
![img](P)  
```  
gov.setGestureStrokeType(GestureOverlayView.GESTURE_STROKE_TYPE_MULTIPLE);//设置笔划类型
  // GestureOverlayView.GESTURE_STROKE_TYPE_MULTIPLE 设置支持多笔划
  // GestureOverlayView.GESTURE_STROKE_TYPE_SINGLE 仅支持单一笔划
  path = new File(Environment.getExternalStorageDirectory(), "gestures").getAbsolutePath();
  //得到默认路径和文件名/sdcard/gestures
  file = new File(path);//实例gestures的文件对象
  gestureLib = GestureLibraries.fromFile(path);//实例手势仓库
  gov.addOnGestureListener(new OnGestureListener() { // 这里是绑定手写绘图区
	 @Override
	 // 以下方法是你刚开始画手势的时候触发
	 public void onGestureStarted(GestureOverlayView overlay, MotionEvent event) {
	  tv.setText("请您在紧凑的时间内用两笔划来完成一个手势！西西~");
	 } 
	 @Override
	 // 以下方法是当手势完整形成的时候触发
	 public void onGestureEnded(GestureOverlayView overlay, MotionEvent event) {
	  gesture = overlay.getGesture();// 从绘图区取出形成的手势
	  if (gesture.getStrokesCount() == 2) {//我判定当用户用了两笔划
	   //(强调：如果一开始设置手势笔画类型是单一笔画，那你这里始终得到的只是1！)
	   if (event.getAction() == MotionEvent.ACTION_UP) {//判定第两笔划离开屏幕
		//if(gesture.getLength()==100){}//这里是判定长度达到100像素
		if (et.getText().toString().equals("")) {
		 tv.setText("由于您没有输入手势名称，so~保存失败啦~");
		} else {
		 tv.setText("正在保存手势...");
		 addMyGesture(et.getText().toString(), gesture);//我自己写的添加手势函数
		}
	   }
	  } else {
	   tv.setText("请您在紧凑的时间内用两笔划来完成一个手势！西西~");
	  }
	 } 
	 @Override
	 public void onGestureCancelled(GestureOverlayView overlay, MotionEvent event) {
	 } 
	 @Override
	 public void onGesture(GestureOverlayView overlay, MotionEvent event) {
	 }
	});
  //----这里是在程序启动的时候进行遍历所有手势!------
  if (!gestureLib.load()) {
   tv.setText("Himi提示：手势超过9个我做了删除所有手势的操作，为了界面整洁一些！
	 + " 输入法手势练习~(*^__^*)~ 嘻嘻！\n操作介绍：(画手势我设置必须画两笔划才行哦~)\n1." +
	   "添加手势：先EditText中输入名称，然后在屏幕上画出手势！\n2.匹配手势：" 
	 + "在EditText输入\"himi\",然后输入手势即可！ ");
  } else {
   Set<String> set = gestureLib.getGestureEntries();//取出所有手势
   Object ob[] = set.toArray();
   loadAllGesture(set, ob);
  }
}
public void addMyGesture(String name, Gesture gesture) { 
  try {
   if (name.equals("himi")) {
	findGesture(gesture);
   } else {
	// 关于两种方式创建模拟器的SDcard在【Android2D游戏开发之十】有详解
	if (Environment.getExternalStorageState() != null) {// 这个方法在试探终端是否有sdcard!
	 if (!file.exists()) {// 判定是否已经存在手势文件
	  // 不存在文件的时候我们去直接把我们的手势文件存入
	  gestureLib.addGesture(name, gesture);
	  if (gestureLib.save()) {////保存到文件中
	   gov.clear(true);//清除笔画
	   // 注意保存的路径默认是/sdcard/gesture ,so~别忘记AndroidMainfest.xml加上读写权限！
	   // 这里抱怨一下，咳咳、其实昨天就应该出这篇博文的，就是因为这里总是异常,今天仔细看了
	   // 才发现不是没写权限,而是我虽然在AndroidMainfest.xml中写了权限，但是写错了位置..哭死！
	   tv.setText("保存手势成功！因为不存在手势文件，" + "所以第一次保存手势成功会默认先创" +
		 "建了一个手势文件！然后将手势保存到文件中.");
	   et.setText("");
	   gestureToImage(gesture);
	  } else {
	   tv.setText("保存手势失败！");
	  }
	 } else {//当存在此文件的时候我们需要先删除此手势然后把新的手势放上
	  //读取已经存在的文件,得到文件中的所有手势
	  if (!gestureLib.load()) {//如果读取失败
	   tv.setText("手势文件读取失败！");
	  } else {//读取成功 
	   Set<String> set = gestureLib.getGestureEntries();//取出所有手势
	   Object ob[] = set.toArray();
	   boolean isHavedGesture = false;
	   for (int i = 0; i < ob.length; i++) {//这里是遍历所有手势的name 
		if (((String) ob[i]).equals(name)) {//和我们新添的手势name做对比
		 isHavedGesture = true;
		}
	   }
	   if (isHavedGesture) {//如果此变量为true说明有相同name的手势
//----备注1-------------------//gestureLib.removeGesture(name, gesture);//删除与当前名字相同的手势
/*----备注2-----------------*/gestureLib.removeEntry(name);
		 gestureLib.addGesture(name, gesture);
	   } else {
		gestureLib.addGesture(name, gesture);
	   }
	   if (gestureLib.save()) {
		gov.clear(true);//清除笔画
		gestureToImage(gesture);
		tv.setText("保存手势成功！当前所有手势一共有：" + ob.length + "个");
		et.setText("");
	   } else {
		tv.setText("保存手势失败！");
	   }
	   ////------- --以下代码是当手势超过9个就全部清空 操作--------
	   if (ob.length > 9) {
		for (int i = 0; i < ob.length; i++) {//这里是遍历删除手势
		 gestureLib.removeEntry((String) ob[i]);
		}
		gestureLib.save();
		if (MySurfaceView.vec_bmp != null) {
		 MySurfaceView.vec_bmp.removeAllElements();//删除放置手势图的容器
		}
		tv.setText("手势超过9个，已全部清空!");
		et.setText("");
	   }
	   ob = null;
	   set = null;
	  }
	 }
	} else {
	 tv.setText("当前模拟器没有SD卡 - -。");
	}
   }
  } catch (Exception e) {
   tv.setText("操作异常！");
  }
}
public void loadAllGesture(Set<String> set, Object ob[]) { //遍历所有的手势 
  if (gestureLib.load()) {//读取最新的手势文件
   set = gestureLib.getGestureEntries();//取出所有手势
   ob = set.toArray();
   for (int i = 0; i < ob.length; i++) {
	//把手势转成Bitmap
	gestureToImage(gestureLib.getGestures((String) ob[i]).get(0));
	//这里是把我们每个手势的名字也保存下来
	MySurfaceView.vec_string.addElement((String) ob[i]);
   }
  }
}
public void gestureToImage(Gesture ges) {//将手势转换成Bitmap
  //把手势转成图片,存到我们Image容器中
  if (MySurfaceView.vec_bmp != null) {
   MySurfaceView.vec_bmp.addElement(ges.toBitmap(100, 100, 12, Color.GREEN));
  }
}
public void findGesture(Gesture gesture) {
  try {
   // 关于两种方式创建模拟器的SDcard在【Android2D游戏开发之十】有详解
   if (Environment.getExternalStorageState() != null) {// 这个方法在试探终端是否有sdcard!
	if (!file.exists()) {// 判定是否已经存在手势文件
	 tv.setText("匹配手势失败，因为手势文件不存在！！");
	} else {//当存在此文件的时候我们需要先删除此手势然后把新的手势放上
	 //读取已经存在的文件,得到文件中的所有手势
	 if (!gestureLib.load()) {//如果读取失败
	  tv.setText("匹配手势失败，手势文件读取失败！");
	 } else {//读取成功 
	  List<Prediction> predictions = gestureLib.recognize(gesture);
	  //recognize()的返回结果是一个prediction集合，
	  //包含了所有与gesture相匹配的结果。
	  //从手势库中查询匹配的内容，匹配的结果可能包括多个相似的结果， 
	  if (!predictions.isEmpty()) {
	   Prediction prediction = predictions.get(0);
	   //prediction的score属性代表了与手势的相似程度
	   //prediction的name代表手势对应的名称 
	   //prediction的score属性代表了与gesture得相似程度（通常情况下不考虑score小于1的结果）。 
	   if (prediction.score >= 1) {
		tv.setText("当前你的手势在手势库中找到最相似的手势：name =" + prediction.name);
	   }
	  }
	 }
	}
   } else {
	tv.setText("匹配手势失败，,当前模拟器没有SD卡 - -。");
   }
  } catch (Exception e) {
   e.printStackTrace();
   tv.setText("由于出现异常，匹配手势失败啦~");
  }
}
}
```
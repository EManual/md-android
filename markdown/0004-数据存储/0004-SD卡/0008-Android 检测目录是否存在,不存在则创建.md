通过这几天的学习，感觉学习是越来越轻松了，但是还是有许多东西，不会，今天在思考，如何把在目录中创建文件，并且能知道创建成功不成功，比如说我做了拍照程序，那么我得知道如何让他选择一个目录进行放置或者是设置一个目录进行设置，而且这个目录，也不一定是存在的，所以就写了这么一个小方法，判断目录是否存在，如果不存在则创建，如果存在就不创建了，感觉现在这些写法都很像是普通甘托克使用的方法了，下面做一个记录，也省得以后会忘记了。学习就是一个记忆的过程，平常记忆能力再强的人，也得需要做一个笔记，这个是我现在学习的一个体会!
方法如下：
我们假设SD卡是存在的，如果要检测SD卡是否存在，请参考前面的关于录音的文章，里面有具体的内容，和使用SD卡需要添加的权限，否则无法操作扩展存储设备
1.只创建一级目录的形式为：
例如：只在SD卡上建立一级目录("/sdcard/audio/")：
```  
boolean isFolderExists(String strFolder) {
	File file = new File(strFolder);
	if (!file.exists()) {
		if (file.mkdirs()) {
			return true;
		} else {
			return false;
		}
	}
	return true;
}
```
2.建立多级目录的形式如下：　　例如：在SD卡上建立多级目录("/sdcard/meido/audio/")：
```  
boolean isFolderExists(String strFolder) {
	File file = new File(strFolder);
	if (!file.exists()) {
		if (file.mkdir()) {
			return true;
		} else
			return false;
	}
	return true;
}
```

代码如下：
```  
 /**
  * 删除文件夹
  * 
  * @param filePathAndName
  *            String 文件夹路径及名称 如c:/fqf
  * @param fileContent
  *            String
  * @return boolean
  */
public void delFolder(String folderPath) {
	try {
		delAllFile(folderPath); // 删除完里面所有内容
		String filePath = folderPath;
		filePath = filePath.toString();
		java.io.File myFilePath = new java.io.File(filePath);
		myFilePath.delete(); // 删除空文件夹
	} catch (Exception e) {
		System.out.println("删除文件夹操作出错");
		e.printStackTrace();
	}
}
 /**
  * 删除文件夹里面的所有文件
  * 
  * @param path
  *            String 文件夹路径 如 c:/fqf
  */
public void delAllFile(String path) {
	File file = new File(path);
	if (!file.exists()) {
		return;
	}
	if (!file.isDirectory()) {
		return;
	}
	String[] tempList = file.list();
	File temp = null;
	for (int i = 0; i < tempList.length; i++) {
		if (path.endsWith(File.separator)) {
			temp = new File(path + tempList[i]);
		} else {
			temp = new File(path + File.separator + tempList[i]);
		}
		if (temp.isFile()) {
			temp.delete();
		}
		if (temp.isDirectory()) {
			delAllFile(path + "/" + tempList[i]);// 先删除文件夹里面的文件
			delFolder(path + "/" + tempList[i]);// 再删除空文件夹
		}
	}
}
```
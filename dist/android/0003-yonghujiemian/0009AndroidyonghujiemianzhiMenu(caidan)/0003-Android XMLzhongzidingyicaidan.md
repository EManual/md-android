和Android UI layout一样，我们也可以在XML中定义应用程序的菜单。通过在菜单的onCreateOptionsMenu方法中膨胀菜单layout。这样做会使我们的程序代码简单多了，而且尽可能的将更多的界面设计部分放到XML，便于浏览。
1. 在工程的/res/文件夹下创建menu文件夹，用来保存你为应用程序定义的菜单XML文件。
在菜单XML layout中，有三个有效的元素：menu、group、item。item和group必须是menu的子元素，且item必须是group的子元素。另外的menu可以是item的子元素（为了创建子菜单）。下面的XML片段显示了菜单的层次定义：
```  
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android" >
    <item
 android:id="@+id/media_play"
 android:icon="@android:drawable/ic_media_play"
 android:title="Play"/>
    <item
 android:id="@+id/media_pause"
 android:icon="@android:drawable/ic_media_pause"
 android:title="Pause"/>
    <item
 android:id="@+id/file"
 android:title="File">
 <menu>
     <item
  android:id="@+id/file_open"
  android:title="Open..."/>
     <item
  android:id="@+id/file_save"
  android:title="Save"/>
     <item
  android:id="@+id/file_saveas"
  android:title="Save as"/>
     <item
  android:id="@+id/file_exit"
  android:title="Exit"/>
 </menu>
    </item>
    <item
 android:id="@+id/edit"
 android:title="Edit">
 <menu>
     <group>
  <item
      android:id="@+id/edit_copy"
      android:title="Copy"/>
  <item
      android:id="@+id/edit_paste"
      android:title="Paste"/>
  <item
      android:id="@+id/edit_cut"
      android:title="Cut"/>
  <item
      android:id="@+id/edit_delete"
      android:title="Delete"/>
     </group>
 </menu>
    </item>
</menu>
```
2. 重写Activity的onCreateOptionsMenu方法，通过MenuInflater.inflate方法来膨胀菜单XML。
```  
MenuInflater inflater = getMenuInflater();
inflater.inflate(R.menu.menu_option, menu);
```
3. 在Activity的onOptionsItemSelected方法中处理每个菜单项的点击事件：
```  
public class Snippet {
	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		super.onOptionsItemSelected(item);
		switch (item.getItemId()) {
		case R.id.media_play:
			break;
		case R.id.media_pause:
			break;
		case R.id.file_open:
			break;
		case R.id.file_save:
		}
		return true;
	}
}
```
在XML可以定义菜单项的图标、快捷键、checkbox等更多特征，了解更多请查阅SDK中关于菜单的主题。
效果图：
![img](P)  
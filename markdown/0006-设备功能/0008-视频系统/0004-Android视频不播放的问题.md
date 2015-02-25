我们在用android有时候要上网播放和本地播放一些视频，那么我们通常是用什么方法来实现的那，我们来看看代码吧：
```  
mMediaPlayer = new MediaPlayer();
mMediaPlayer.setDataSource(path);
mMediaPlayer.setDisplay(holder);
mMediaPlayer.prepareAsync();
mMediaPlayer.setOnBufferingUpdateListener(this);
mMediaPlayer.setOnCompletionListener(this);
mMediaPlayer.setOnPreparedListener(this);
mMediaPlayer.setAudioStreamType(AudioManager.STREAM_MUSIC); 
```
上面的代码主要是，我们先得实例化一下mMediaPlaye这个方法，这个是很主要的，如果这个不实例化的话，就没放实现，视频的播放，这个大家千万可别忘了哦。我们来看看eoe的思路。
在OnPreparedListener的onPrepared(MediaPlayer)方法中回下如下的代码:
```  
Log.d(TAG, "onPrepared called");
mVideoWidth = mMediaPlayer.getVideoWidth();
mVideoHeight = mMediaPlayer.getVideoHeight();
Log.d(TAG, "***********mVideoWidth====="+mVideoWidth+"==mVideoHeight===" + mVideoHeight);
if (mVideoWidth != 0 && mVideoHeight != 0) {
holder.setFixedSize(mVideoWidth, mVideoHeight);
mMediaPlayer.start();
}
//去掉buffer对话框
bufferingDialog.dismiss();
```
看看上面的代码，主要就是用到了log，这个也是android在代码测试的时候非常好用的。log会一步一步的走实行的代码，如果我们那行代码写的不对，log就会不执行这段代码，这样我们就会很好的找到哪一行的代码是错误的，这给我们开发应用大大的节省了时间。上面的也是用到了log，这个代码主要就是写了播放器的宽和高，Log.d(TAG,"***********mVideoWidth====="+mVideoWidth+"==mVideoHeight==="+mVideoHeight);这句话的以上就是用log打出mVideoWidth和mVideoHeight的值，这样就很快的知道宽和高了。下面我们就用if来判断一下，我们在if里写了mVideoWidth和mVideoHeight都不等于0.这样我们就可以正常的播放了。那么如果还使用上面的代码则视频不会播放可以在此处使用如下代码:
```  
if (mVideoWidth != 0 && mVideoHeight != 0) {
	holder.setFixedSize(mVideoWidth, mVideoHeight);
}
mMediaPlayer.start(); 
```
也就是不管获取的长度是否大于0,都将player进行start。我们这个方法是非常的好，这样就可以知道在屏幕没有视频的时候，知道视频是否还在播放，这样的话对我们有很多的帮助。
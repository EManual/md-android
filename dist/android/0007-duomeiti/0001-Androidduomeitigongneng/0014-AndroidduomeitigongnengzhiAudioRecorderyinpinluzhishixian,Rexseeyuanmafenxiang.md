Audio系统在Android中负责音频方面输入/输出层次，一般负责播放PCM声音输出和从外部获取PCM声音，以及管理声音设备和设置。这里主要是谈谈其中录制这块：AudioRecorder。
相对于MediaRecorder来说，AudioRecorder更接近底层，为我们封装的方法也更少。而且音频的采样率、声道等等都可以参数配置，但是问题是在模拟器中AudioRecorder却不怎么好用。
Rexsee实现这个类的方法倒真的很值得学习研究。关于Audio系统中其它几个类，如AudioCapture、AudioPlayer，也可以在rexsee的开源社区里找到，全部开放的：http://www.rexsee.com/CN/helpReference.php
如下是源码。。
```  
/* 
  * Copyright (C) 2011 The Rexsee Open Source Project 
  * 
  * Licensed under the Rexsee License, Version 1.0 (the "License"); 
  * you may not use this file except in compliance with the License. 
  * You may obtain a copy of the License at 
  * 
  *      http://www.rexsee.com/CN/legal/license.html 
  * 
  * Unless required by applicable law or agreed to in writing, software 
  * distributed under the License is distributed on an "AS IS" BASIS, 
  * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
  * See the License for the specific language governing permissions and 
  * limitations under the License. 
  */
import rexsee.core.browser.ActivityResult.ActivityResultListener;
import rexsee.core.browser.JavascriptInterface;
import rexsee.core.browser.RexseeBrowser;
import rexsee.core.browser.UrlListener;
import rexsee.core.lang.RexseeLanguage;
import rexsee.core.transportation.RexseeUpload;
import android.app.Activity;
import android.content.ContentResolver;
import android.content.Context;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.provider.MediaStore.Audio.Media;
public class RexseeAudioRecorder implements JavascriptInterface {
	private static final String INTERFACE_NAME = "AudioRecorder";
	@Override
	public String getInterfaceName() {
		return mBrowser.application.resources.prefix + INTERFACE_NAME;
	}
	@Override
	public JavascriptInterface getInheritInterface(RexseeBrowser childBrowser) {
		return this;
	}
	@Override
	public JavascriptInterface getNewInterface(RexseeBrowser childBrowser) {
		return new RexseeAudioRecorder(childBrowser);
	}
	public static final String EVENT_ONRECORDAUDIOSUCCESSED = "onRecordAudioSuccessed";
	public static final String EVENT_ONRECORDAUDIOFAILED = "onRecordAudioFailed";
	public static final String URI_MEDIA_AUDIO = "content://media/external/audio/media";
	private String mediaFile = null;
	private final RexseeBrowser mBrowser;
	public RexseeAudioRecorder(RexseeBrowser browser) {
		mBrowser = browser;
		browser.eventList.add(EVENT_ONRECORDAUDIOSUCCESSED);
		browser.eventList.add(EVENT_ONRECORDAUDIOFAILED);
		browser.urlListeners.add(new UrlListener(
				mBrowser.application.resources.prefix + ":audio") {
			@Override
			public void run(Context context, final RexseeBrowser browser,
					final String url) {
				new Thread() {
					@Override
					public void run() {
						String html = "<HTML><HEAD><TITLE>" + url
								+ "</TITLE></HEAD>";
						html += "<BODY style='margin:0px;background-color:black;color:white;'>";
						ContentResolver contentResolver = mBrowser.getContext()
								.getContentResolver();
						browser.progressDialog
								.show(RexseeLanguage.PROGRESS_ONGOING);
						Cursor cursor = contentResolver.query(
								Uri.parse(URI_MEDIA_AUDIO), new String[] {
										"_id", "_data" }, null, null,
								"_id desc");
						html += "<table width=100% cellspacing=0 style='color:white;font-size:16px;'>";
						if (cursor.getCount() == 0) {
							html += "<tr><td style='padding:10px;'> audio found.</td></tr>";
						} else {
							for (int i = 0; i < cursor.getCount(); i++) {
								cursor.moveToPosition(i);
								html += "<tr><td onclick=\
										+ browser.function.getInterfaceName()
										+ ".go('
										+ URI_MEDIA_AUDIO
										+ "/
										+ cursor.getInt(0)
										+ "');\" style='border-bottom:1px solid 222222; padding:10 5 10 5;word-break:break-all;'>
										+ cursor.getString(1) + "</td></tr>";
							}
						}
						cursor.close();
						html += "</table>";
						html += "</BODY>";
						html += "</HTML>";
						browser.function.loadHTMLWithoutHistory(html);
					}
				}.start();
			}
			@Override
			public boolean shouldAddToHistory(Context context,
					RexseeBrowser browser, String url) {
				return true;
			}
		});
	}
	public String getLastMediaAudio() {
		ContentResolver contentResolver = mBrowser.getContext()
				.getContentResolver();
		Cursor cursor = contentResolver.query(Uri.parse(URI_MEDIA_AUDIO),
				new String[] { "_data" }, null, null, "_id desc");
		if (cursor == null || cursor.getCount() == 0)
			return "";
		cursor.moveToFirst();
		String rtn = "file://" + cursor.getString(0);
		cursor.close();
		return rtn;
	}
	public void record() {
		try {
			Intent intent = new Intent(Media.RECORD_SOUND_ACTION);
			mBrowser.activityResult.start(intent, new ActivityResultListener() {
				@Override
				public void run(int resultCode, Intent resultIntent) {
					if (resultCode == Activity.RESULT_OK) {
						mediaFile = getLastMediaAudio();
						mBrowser.eventList.run(EVENT_ONRECORDAUDIOSUCCESSED,
								new String[] { mediaFile });
					} else {
						mediaFile = null;
						mBrowser.eventList.run(EVENT_ONRECORDAUDIOFAILED);
					}
				}
			});
		} catch (Exception e) {
			mBrowser.exception(getInterfaceName(), e);
		}
	}
	public void prepareUpload() {
		if (mediaFile == null) {
			mBrowser.exception(getInterfaceName(), "Audio is not ready.");
		} else {
			RexseeUpload uploadObject = (RexseeUpload) mBrowser.interfaceList
					.get(mBrowser.application.resources.prefix
							+ RexseeUpload.INTERFACE_NAME);
			if (uploadObject == null) {
				mBrowser.exception(getInterfaceName(),
						"Upload object is not ready.");
			} else {
				uploadObject.selectedFiles.add(mediaFile);
			}
		}
	}
	public void upload(String action, String name) {
		if (mediaFile == null) {
			mBrowser.exception(getInterfaceName(), "Audio is not ready.");
		} else {
			RexseeUpload uploadObject = (RexseeUpload) mBrowser.interfaceList
					.get(mBrowser.application.resources.prefix
							+ RexseeUpload.INTERFACE_NAME);
			if (uploadObject == null) {
				mBrowser.exception(getInterfaceName(),
						"Upload object is not ready.");
			} else {
				uploadObject.clear();
				uploadObject.selectedFiles.add(mediaFile);
				uploadObject.upload(action, name);
			}
		}
	}
}
```
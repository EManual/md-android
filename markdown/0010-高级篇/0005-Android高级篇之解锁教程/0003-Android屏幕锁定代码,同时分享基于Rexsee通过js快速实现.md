Android锁屏时会先调用onPause()；解锁时调用onResume，读入保存的应用程序的资源。如果运行程序时已经锁屏，应用程序会先调用onCreate(),然后onResume(),再则onPause()。
取消锁屏：
```  
<uses-permission android:name="android.permission.DISABLE_KEYGUARD"/>
KeyguardManager mKeyGuardManager = (KeyguardManager)getSystemService(KEYGUARD_SERVICE);
KeyguardLock mLock = mKeyGuardManager.newKeyguardLock("自己Activity名字");
mLock.disableKeyguard();
```
也是相当的简单了，但基于Rexsee的API，可以通过一句话搞定。
#### 1. 取消锁屏：
```  
window.setTimeout('rexseeKeyguard.disable();alert(\'自动解锁！\');',10000);
```
alert('请按电源键关屏再开屏看到锁屏画面，10秒后自动解锁。')
#### 2. 启动锁屏：rexseeKeyguard.reEnable();
如下是源码：
```  
/* 
 [Tags]* Copyright (C) 2011 The Rexsee Open Source Project 
 [Tags]* 
 [Tags]* Licensed under the Rexsee License, Version 1.0 (the "License"); 
 [Tags]* you may not use this file except in compliance with the License. 
 [Tags]* You may obtain a copy of the License at 
 [Tags]*      http://www.rexsee.com/CN/legal/license.html 
 [Tags]* Unless required by applicable law or agreed to in writing, software 
 [Tags]* distributed under the License is distributed on an "AS IS" BASIS, 
 [Tags]* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
 [Tags]* See the License for the specific language governing permissions and 
 [Tags]* limitations under the License. 
 [Tags]*/
import rexsee.core.browser.JavascriptInterface;
import rexsee.core.browser.RexseeBrowser;
import android.app.KeyguardManager;
import android.app.KeyguardManager.KeyguardLock;
import android.content.Context;
public class RexseeKeyguard implements JavascriptInterface {
	private static final String INTERFACE_NAME = "Keyguard";
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
		return new RexseeKeyguard(childBrowser);
	}
	private final Context mContext;
	private final RexseeBrowser mBrowser;
	private KeyguardLock mKeyguardLock = null;
	public RexseeKeyguard(RexseeBrowser browser) {
		mBrowser = browser;
		mContext = browser.getContext();
	}
	public RexseeKeyguard(Context context) {
		mBrowser = null;
		mContext = context;
	}
	// JavaScript Interface
	public void enable() {
		/*
		 [Tags]* try { DevicePolicyManager dpm = (DevicePolicyManager)
		 [Tags]* mContext.getSystemService(Context.DEVICE_POLICY_SERVICE);
		 [Tags]* dpm.lockNow(); } catch (Exception e) {
		 [Tags]* mBrowser.exception(getInterfaceName(), e); }
		 [Tags]*/
	}
	public void reEnable() {
		if (mKeyguardLock != null) {
			mKeyguardLock.reenableKeyguard();
			mKeyguardLock = null;
		}
	}
	public void disable() {
		KeyguardManager keyguardManager = (KeyguardManager) mContext
				.getSystemService(Context.KEYGUARD_SERVICE);
		mKeyguardLock = keyguardManager.newKeyguardLock("");
		mKeyguardLock.disableKeyguard();
	}
}  
```
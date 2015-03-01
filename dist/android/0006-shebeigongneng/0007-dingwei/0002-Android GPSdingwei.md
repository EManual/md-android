用GPS定位到当前位置
要想定位到当前的位置，需要利用手机中的GPS模块。使用GPS首先需要获得LocationManager服务，代码如下：
```  
LocationManager locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
```
通过LocationManager类的getBestProvider方法可以获得当前的位置，但需要通过Criteria对象指定一些参数，代码如下：
```  
Criteria criteria = new Criteria();
// 获得最好的定位效果 
criteria.setAccuracy(Criteria.ACCURACY_FINE);
criteria.setAltitudeRequired(false);
criteria.setBearingRequired(false);
criteria.setCostAllowed(false);
// 使用省电模式 
criteria.setPowerRequirement(Criteria.POWER_LOW);
// 获得当前的位置提供者 
String provider = locationManager.getBestProvider(criteria, true);
// 获得当前的位置 
Location location = locationManager.getLastKnownLocation(provider);
// 获得当前位置的纬度 
Double latitude = location.getLatitude() * 1E6;
// 获得当前位置的经度 
Double longitude = location.getLongitude() * 1E6;
```
在获得当前位置的经纬度后，剩下的工作就和以前的例子一样了。只是在本例中还输出了与当前位置相关的信息，代码如下：
```  
Geocoder gc = new Geocoder(this);
List<Address> addresses = gc.getFromLocation(location.getLatitude(), location.getLongitude(), 1);
if (addresses.size() > 0) { 
	msg += "AddressLine：" + addresses.get(0).getAddressLine(0)+ "\n";
	msg += "CountryName：" + addresses.get(0).getCountryName()+ "\n";
	msg += "Locality：" + addresses.get(0).getLocality() + "\n";
	msg += "FeatureName：" + addresses.get(0).getFeatureName();
} 
textView.setText(msg);
```
本例需要使用如下代码在AndroidManifest.xml文件中打开相应的权限：
```  
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FIND_LOCATION" />
```
效果图：
![img](P)  
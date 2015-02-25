下面是获取一个apk的application信息的代码
```  
[Tags]/*
 [Tags]* Utility method to get application information for a given packageURI
 [Tags]*/
public static ApplicationInfo getApplicationInfo(Uri packageURI) {
	final String archiveFilePath = packageURI.getPath();
	PackageParser packageParser = new PackageParser(archiveFilePath);
	File sourceFile = new File(archiveFilePath);
	DisplayMetrics metrics = new DisplayMetrics();
	metrics.setToDefaults();
	PackageParser.Package pkg = packageParser.parsePackage(sourceFile,
			archiveFilePath, metrics, 0);
	if (pkg == null) {
		return null;
	}
	return pkg.applicationInfo;
}
[Tags]/*
 [Tags]* Utility method to get application information for a given packageURI
 [Tags]*/
public static ApplicationInfo getApplicationInfo(Uri packageURI) {
	final String archiveFilePath = packageURI.getPath();
	PackageParser packageParser = new PackageParser(archiveFilePath);
	File sourceFile = new File(archiveFilePath);
	DisplayMetrics metrics = new DisplayMetrics();
	metrics.setToDefaults();
	PackageParser.Package pkg = packageParser.parsePackage(sourceFile,
			archiveFilePath, metrics, 0);
	if (pkg == null) {
		return null;
	}
	return pkg.applicationInfo;
}
```
这里的Uri的参数是可以是这样形成的
```  
Creates a Uri from a file. The URI has the form "file://". 
Encodes path characters with the exception of '/'.
Example: "file:///tmp/android.txt
```
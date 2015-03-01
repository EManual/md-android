为了放不同内容的漫画到Android Market上，但是Android Market需要每个应用的不要不同的包名，所以就需要批量打包了；
流程差不多是这样的：
1.给编辑人员图形界面操作资源；
2.利用Ant处理相应的资源；
3.利用Ant打包；
4.以用Ant把apk安装到模拟器，看是否正常运行；
这里主要写利用Ant打包apk文件，弄了我好一阵，写下来为自己备忘，也希望能帮助到刚好在处理相关的朋友；
分如下几步：
1.生成R.java
2.编译Java文件
3.生成classes.dex文件
4.打包res文件
5.把3和4生成的文件打包
6.签名apk
```  
<?xml version="1.0" encoding="GBK"?>
<!-- step 
1.generate R.java by aapt 
2.compile java to class by javac.exe 
3.generate classes.dex by dx.bat
4.package resources by aapt 
5. package resource and classes.dex by apkbuilder.bat 
6.sign apk by jarsinger -->
<project name="项目名" default="debug" basedir=".">
<property file="local.properties"/>

<!-- The intermediates directory -->
<!-- Eclipse uses "bin" for its own output, so we do the same. -->
<property name="outdir" value="bin" />
<property name="out.absolute.dir" location="${outdir}" />

<!-- Create R.java in the source directory -->
<property name="outdir-gen" value="gen"/>

<!-- Input directories -->
<property name="resource-dir" value="res" />
<property name="asset-dir" value="assets" />
<property name="src-dir" value="src" />
<property name="src-dir-ospath" value="${basedir}/${src-dir}" />

<!-- Output directories -->
<property name="outdir-classes" value="${outdir}/classes" />
<property name="out.classes.absolute.dir" location="${outdir-classes}" />

<!-- Intermediate files -->
<property name="dex-file" value="classes.dex" />
<property name="intermediate-dex" value="${out.absolute.dir}/${dex-file}" />

<!-- The final package file to generate -->
<property name="out-package" value="${outdir}/${ant.project.name}.apk" />

<!-- Tools -->
<property name="aapt" value="${sdk-platform-tools}/aapt.exe" />
<property name="aidl" value="${sdk-platform-tools}/aidl.exe" />
<property name="dx" value="${sdk-platform-tools}/dx.bat"/>
<property name="adb" value="${sdk-tools}/adb" />
<property name="apk-builder" value="${sdk-tools}/apkbuilder.bat" />
<property name="android-jar" value="${sdk-platform}/android.jar" />

<!-- The final package file to generate -->
<property name="resources-package" value="${outdir}/${ant.project.name}" />
<property name="resources-package-ospath" value="${basedir}/${resources-package}" />
<property name="out-debug-package" value="${outdir}/${ant.project.name}-debug.apk" />
<property name="out-debug-package-ospath" value="${basedir}/${out-debug-package}" />
<property name="out-unsigned-package" value="${outdir}/${ant.project.name}-unsigned.apk" />
<property name="out-unsigned-package-ospath" value="${basedir}/${out-unsigned-package}" />
<property name="out-signed-package" value="${outdir}/${ant.project.name}-signed.apk" />
<property name="out-signed-package-ospath" value="${basedir}/${out-signed-package}" />
<property name="zipalign-package-ospath" value="${basedir}/${ant.project.name}_release.apk" />
<property name="jarsigner" value="${jdk-home}/bin/jarsigner.exe"/>
<property name="zipalign" value="${sdk-tools}/zipalign.exe"/> 

<!-- init -->
<target name="init">
<echo>Creating all output directories </echo>
<delete dir="${outdir}" />
<delete dir="${outdir-classes}" />
<delete dir="${outdir-gen}" />
<mkdir dir="${outdir}" />
<mkdir dir="${outdir-classes}" />
<mkdir dir="${outdir-gen}" />
</target>

<!-- Step 1. Generate the R.java file for this project's resources. -->
<target name="resource-src" depends="init">
	<echo>Generating R.java...</echo>
	<exec executable="${aapt}" failonerror="true">
		<arg value="package" />
		<arg value="-m" />
		<arg value="-J" />
		<arg value="${outdir-gen}" />
		<arg value="-M" />
		<arg value="AndroidManifest.xml" />
		<arg value="-S" />
		<arg value="${resource-dir}" />
		<arg value="-I" />
		<arg value="${android-jar}" />
	</exec>
</target>

<!-- Generate java classes from .aidl files. -->
<target name="aidl" depends="init">
<echo>Compiling aidl files into Java classes...</echo>
<apply executable="${aidl}" failonerror="true">
	<arg value="-p${android-framework}" />
	<arg value="-I${src-dir}" />
	<fileset dir="${src-dir}">
		<include name="**/*.aidl" />
	</fileset>
</apply>
</target>

<!-- Step 2. Compile this project's .java files into .class files. -->
<target name="compile" depends="init, resource-src, aidl">
<javac encoding="GBK" 
target="1.5" 
debug="true" 
extdirs="" 
srcdir="." 
destdir="${outdir-classes}"
bootclasspath="${android-jar}" />
</target>

<!-- Step 3. Convert this project's .class files into .dex files. -->
<target name="dex" depends="compile">
<echo>Converting compiled files into ${intermediate-dex}... </echo>
	<exec executable="${dx}" failonerror="true">
	<arg value="--dex" />
	<arg value="--output=${intermediate-dex}" />
	<arg path="${out.classes.absolute.dir}" />
</exec>
</target>

<!-- Step 4. Put the project's resources into the output package file. -->
<target name="package-res-and-assets">
	<echo>Packaging resources and assets...</echo>
	<exec executable="${aapt}" failonerror="true">
		<arg value="package" />
		<arg value="-f" />
		<arg value="-M" />
		<arg value="AndroidManifest.xml" />
		<arg value="-S" />
		<arg value="${resource-dir}" />
		<arg value="-A" />
		<arg value="${asset-dir}" />
		<arg value="-I" />
		<arg value="${android-jar}" />
		<arg value="-F" />
		<arg value="${resources-package}"/>
	</exec>
</target>

<!-- Step 4. Same as package-res-and-assets, but without "-A ${asset-dir}" -->
<target name="package-res-no-assets">
	<echo>Packaging resources...</echo>
	<exec executable="${aapt}" failonerror="true">
		<arg value="package" />
		<arg value="-f" />
		<arg value="-M" />
		<arg value="AndroidManifest.xml" />
		<arg value="-S" />
		<arg value="${resource-dir}" />

		<!-- No assets directory -->
		<arg value="-I" />
		<arg value="${android-jar}" />
		<arg value="-F" />
		<arg value="${resources-package}" />
	</exec>
</target>

<!-- Invoke the proper target depending on whether or not an assets directory is present. -->
<!-- TODO: find a nicer way to include the "-A ${asset-dir}" argument only when the assets dir exists. -->
<target name="package-res">
	<available file="${asset-dir}" type="dir" property="res-target" value="and-assets" />
		<property name="res-target" value="no-assets" />
	<antcall target="package-res-${res-target}" />
</target>

<!-- Step 5. Package the application and sign it with a debug key.
This is the default target when building. It is used for debug. -->

<target name="debug" depends="dex, package-res">
	<echo>Packaging ${out-debug-package-ospath}, and signing it with a debug key...</echo>
	<exec executable="${apk-builder}" failonerror="true">
		<arg value="${out-debug-package-ospath}" />
		<arg value="-z" />
		<arg value="${resources-package-ospath}" />
		<arg value="-f" />
		<arg value="${intermediate-dex}" />
		<arg value="-rf" />
		<arg value="${src-dir-ospath}" />
	</exec>
</target>

<!-- Step 5. Package the application without signing it. -->
<target name="release" depends="dex, package-res">
	<echo>Packaging ${out-debug-package-ospath}, and signing it with a debug key...</echo>
	<exec executable="${apk-builder}" failonerror="true">
		<arg value="${out-unsigned-package-ospath}" />
		<arg value="-u" />
		<arg value="-z" />
		<arg value="${resources-package-ospath}" />
		<arg value="-f" />
		<arg value="${intermediate-dex}" />
		<arg value="-rf" />
		<arg value="${src-dir-ospath}" />
	</exec>
</target>

<!--Step 6. signer apk with private key -->
<target name="jarsigner" depends="release">
	<echo> jarsigner ${out-signed-package-ospath}</echo>
	<exec executable="${jarsigner}" failonerror="true">
		<arg value="-verbose" />
		<arg value="-storepass" />
		<arg value="这里是keystore的密码" />
		<arg value="-keystore" />
		<arg value="你的keystore的文件" />
		<arg value="-signedjar" />
		<arg value="${out-signed-package-ospath}" />
		<arg value="${out-unsigned-package-ospath}" />
		<arg value="你的keystore的文件" />
	</exec>
</target>

<!--zipalign-->
<target name="zipalign" depends="jarsigner">
	<echo> zipalign ${zipalign-package-ospath}</echo>
	<exec executable="${zipalign}" failonerror="true">
		<arg value="-v" />
		<arg value="-f" />
		<arg value="4" />
		<arg value="${out-signed-package-ospath}" />
		<arg value="${zipalign-package-ospath}" />
	</exec>
</target>
<!-- run -->

<target name="run" depends="zipalign, debug">
<echo>run... Package debug apk and un sign apk</echo>
</target>

<!-- Install the package on the default emulator -->
<target name="install" depends="debug">
	<echo>Installing ${out-debug-package} onto default emulator...</echo>
	<exec executable="${adb}" failonerror="true">
		<arg value="install" />
		<arg value="${out-debug-package-ospath}" />
	</exec>
</target>
</project>
```
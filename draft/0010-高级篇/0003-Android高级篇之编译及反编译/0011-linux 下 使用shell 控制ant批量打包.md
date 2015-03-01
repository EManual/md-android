附件中提供了三个文件
1、build.xml 为 ant 生成android apk的脚本，里面包含了编译，生成dex文件，资源打包，生成release包，添加签名等步骤，看代码就知道了。
2、buildProjectHome.properties 主要是于项目相关的一些路径配置，告诉ant在去哪找资源文件。
3、build.properties 文件主要是android sdk、jdk，以及使用的debug.kestore( release.kestore)的一些配置。
前提条件：linux下要配置sdk，jdk，ant环境。
其中：
build.properties中主要修改   
1、android.sdk.home （你的android linux sdk 根路径）
2、external-map-libs（当前是用的googlemap包）
3、jdk.home         （jdk）  
如果有需求的话，应该更改你的debug.keystore密码，确定你的jdk，sdk路径下都有配置里面的几个文件。
build.xml 如果使用debug.kestore基本上不用改。
buildProjectHome.properties 文件内主要修改：
1、project.home   当前工程所在路径。
2、project.name   工程名。
tips：如果使用了第三方包，请放在工程根目录下的libs文件夹里面（不是lib哦）。
基本上就ok完事了。
至于批量打包，在shell下使用如下代码：
```  
for  (( i=0; i<$excuteTimes; i++ ))
do
如果这里有svn更新操作的话，也可以加上更新操作代码
build
ant
copy
echo "copy file "
if [ -d /home/evans/thinking/ ]; then
echo ""
else
$(mkdir -p /home/evans/thinking/ )
fi 
打完包以后将当前目录下的后缀为.apk文件复制到thinking目录下
$(cp $projectName/bin/*.apk /home/evans/thinking/)
echo "copy success!"
done
```
将这三个文件修改好以后，复制到你的工程目录下面，在命令行输入ant运行。
如果build.xml里面有变量未使用到也没关系，会以字符原样输出。
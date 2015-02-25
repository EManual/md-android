一直都以为软件的设置界面都是作者自己写的，今天才发现有个现成的PreferenceActivity可以使用，非常方便。这里介绍如何在Activity中取到PreferenceActivity设置的值，分享给大家。代码如下：
```  
SharedPreferences pre = PreferenceManager.getDefaultSharedPreferences(this);
String dir=pre.getString("down_savedir", "");//两个参数,一个是key，就是在PreferenceActivity的xml中设置的,另一个是取不到值时的默认值
```
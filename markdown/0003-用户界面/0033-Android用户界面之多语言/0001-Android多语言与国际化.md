internationalization （国际化）简称 i18n,因为在i和n之间还有18个字符，localization（本地化 ），简称i10n。
一般用 语言_地区的形式表示一种语言，如  zh_CN, zh_TW.
各国语言缩写  http://www.loc.gov/standards/iso639-2/php/code_list.php
国家和地区简写 http://www.iso.org/iso/en/prods- ... lists/list-en1.html
在Android工程的res目录下，通过定义特殊的文件夹名称就可以实现多语言支持。比如我们的程序兼容简体中文、日文、英文、法文和德文，在values文件夹中建立默认strings.xml，再建立values-zh-rCN（zh表示中文rCN表示简体，类似还有美式英语，奥式英语）、values-ja、values、values-fr和values-de文件夹。
在每个文件夹里放置一个strings.xml，strings.xml里是各种语言字符串。如果涉及到参数配置类xml文件夹也要改成xml-zh、xml-ja、xml、xml-fr和xml-de。这样在android的系统中进行语言切换，所开发的程序也会跟着切换语言。
在代码中切换语言：
```  
Resources resources = getResources();//获得res资源对象
Configuration config = resources.getConfiguration();//获得设置对象
DisplayMetrics dm = resources .getDisplayMetrics();//获得屏幕参数：主要是分辨率，像素等。
config.locale = Locale.SIMPLIFIED_CHINESE; //简体中文
resources.updateConfiguration(config, dm);
```
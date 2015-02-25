#### 一、管理Fragment
管理Fragment在你的Activity你需要使用一个名为FragmentManager的类，通过调用getFragmentManager()方法来实例化该管理类在你的Activity种。 
FragmentManager类一些主要的方法有通过findFragmentById()来获取一个Activity中有关Fragment布局。当然还有类似findFragmentByTag()方法，以及唐Fragment中出栈的popBackStack()同时可以注册addOnBackStackChangedListener()管理.具体的可以在android.app.FragmentManager类中了解。
#### 二、优化Fragment事物处理
一个很好的特性在添加，删除，替换fragment在Activity时可以使用FragmentTransaction类来提高批量处理的效率，这点和SQLite的数据库更新原理类似。
```  
FragmentManager fragmentManager = getFragmentManager(); //实例化fragmentmanager类
FragmentTransaction transaction = fragmentManager.beginTransaction(); //通过begintransaction方法获取一个事物处理实例。
```
在这期间可以使用add(), remove(), 以及 replace()。最终需要改变时执行commit()即可，接下来我们写代码
```  
transaction.replace(R.id.fragment_container, newFragment);
transaction.addToBackStack(null);
transaction.commit();
```
#### 三、Fragment和Activity互相通讯
通常Fragment中我们放入平时标准的控件或自定义的控件，基本上和Activity一样，但是如何Fragment中的View布局也是放到Activity中的，这里Android开发网提示大家有两种方法来实现
```  
View listView = getActivity().findViewById(R.id.cwj); //通过getActivity方法可以获取一个Activity中的fragment，这里的cwj是一个fragment
```
在activity中的布局如下:
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal" >
    <fragment
        android:id="@+id/cwj"
        android:name="com.eoeandroid.cwj.ArticleListFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1" />
    <fragment
        android:id="@+id/smart"
        android:name="com.eoeandroid.cwj.ArticleReaderFragment"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="2" />
</LinearLayout>
```
当然还有一种通过getFragmentManager方法获取实例，
```  
ExampleFragment fragment = (ExampleFragment) getFragmentManager().findFragmentById(R.id.cwj);
```
Android里面自带有多选的布局，文件名是：simple_list_item_multiple_choice.xml.
```  
<?xml version="1.0" encoding="utf-8"?>
<!--
     Copyright (C) 2008 The Android Open Source Project
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
          [url]http://www.apache.org/licenses/LICENSE-2.0[/url]
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
-->
<CheckedTextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@android:id/text1"
    android:layout_width="match_parent"
    android:layout_height="?android:attr/listPreferredItemHeight"
    android:checkMark="?android:attr/listChoiceIndicatorMultiple"
    android:gravity="center_vertical"
    android:paddingLeft="6dip"
    android:paddingRight="6dip"
    android:textAppearance="?android:attr/textAppearanceLarge" />
```
是一个CheckedTextView的组件。只能实现一个TextView和一个CheckBox的组件。在开发中肯定是不能满足我们的需求的。貌似与它绑定的好像只有ArrayAdapter。什么SimpleAdapter，SimpleCursorAdapter这些Adapter不能与之绑定，看看构造函数就知道了。怎么才能更佳美化我们的ListView的UI呢？只有一个办法，重写一个Adapter，来适配我们的自己的 ListView。
```  
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/row"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal" >
    <RelativeLayout
        android:layout_width="fill_parent"
        android:layout_height="wrap_content" >
        <ImageView
            android:id="@+id/tag"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:background="@drawable/icon" />
        <LinearLayout
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginLeft="5dip"
            android:layout_marginTop="7dip"
            android:layout_toRightOf="@id/tag"
            android:orientation="vertical" >
            <TextView
                android:id="@+id/multiple_title"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:layout_marginLeft="5dip"
                android:gravity="center_vertical"
                android:textSize="20dip" />
            <TextView
                android:id="@+id/multiple_summary"
                android:layout_width="fill_parent"
                android:layout_height="wrap_content"
                android:layout_marginLeft="5dip"
                android:gravity="center_vertical" />
        </LinearLayout>
        <!--
			这三个很重要
			android:focusable="false"
			android:focusableInTouchMode="false"
			android:clickable="false"
        -->
        <CheckBox
            android:id="@+id/multiple_checkbox"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true"
            android:layout_gravity="center_vertical"
            android:layout_marginTop="6dip"
            android:clickable="false"
            android:focusable="false"
            android:focusableInTouchMode="false" />
    </RelativeLayout>
</LinearLayout>
```
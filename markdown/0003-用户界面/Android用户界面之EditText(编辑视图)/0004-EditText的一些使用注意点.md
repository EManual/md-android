1.如何对EditText进行setText()的时候使其自动换行
```  
<EditText android:layout_width="200dp" android:layout_height="wrap_content"  
	android:id="@+id/input" android:singleLine="false"
	/>  
```
我们只要确保singleLine为false的话,并且设置宽度一定,就可以自动换行,注意在这里不要设置
```  
input.setInputType(0);  
```
不然就不会自动换行 
2.在TableLayout中布局一行,设置EditText的xml属性: 
```  
<!-- android:shrinkColumns="1" shrinks the 2nd column to fit the window -->  
<!-- android:stretchColumns="1" stretches the 2nd column -->  
<TableLayout xmlns:android="http://schemas.android.com/apk/res/android"  
    android:layout_width="fill_parent" android:layout_height="fill_parent"
    android:orientation="vertical" android:paddingLeft="5dp"
    android:paddingRight="5dp" android:stretchColumns="1">
    <TableRow android:layout_width="fill_parent"
        android:layout_height="wrap_content" android:orientation="horizontal">
        <TextView android:layout_width="wrap_content"
            android:layout_height="wrap_content" android:text="Email"
            android:paddingRight="5dp">
        </TextView>
        <EditText android:id="@+id/txtEmail" android:layout_width="200dp"
            android:layout_height="wrap_content" android:textSize="18sp"
            android:singleLine="false" android:inputType="textEmailAddress">
        </EditText>
    </TableRow>
</TableLayout> 
```
3.如何设置EditText隐藏键盘 
```  
(EditText)mMarket.setInputType(0);  
```
4.如何设置EditText不被输入法遮盖 
```  
getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE);
```
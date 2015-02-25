最近有个项目需要在ListView加上CheckBox，如果把ListView的选择模式设为CHOICE_MODE_MULTIPLE多选，在Item的XML里用CheckedTextView，是可以达到加CheckBox的效果，但是CheckedTextView样式太单一，左边的文本只有一行，而我的要求是两行，一行标题，一行描述，只好通过自己加CheckBox解决。
根据前面的经验CheckBox加入ListView会导致OnItemClick不可用，在给CheckBox加上android:focusable=”false”属性，实现了需要的功能，但是发现了新问题，无法控制CheckBox的点击事件，ListView只能控制对Item的点击，不能控制对item内的控件的点击，而且在CheckBox上点击也不会触发ListView的OnItemClick事件，因此在点击CheckBox时无法取拿到CheckBox的值.最终通过另一方法解决，就是禁用CheckBox的点击功能，让它只有显示是否选中的功能，通过监听ListView的OnItemClick事件，来切换CheckBox的显示，效果很完美，几乎跟CHOICE_MODE_MULTIPLE一样。
禁用CheckBox的点击功能:
```  
<CheckBox android:id="@+id/isRun"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    style="@style/MyCheckBox"
    android:focusable="false"
    android:clickable="false"
    />
```
这里我用了自己写的style，MyCheckBox
监听点击事件:
这里说明一下，为了方便，我自已写了Adapter，CheckBox传入的是int值，而不是Boolean值，另外在适配器里，因为CheckBox继承自TextView，所以会被识别成TextView，再根据传入的值类型判断即可。
不能用上面注释掉的语句更新视图，因为ListView的Item在隐藏后重新显示(比如说item很多，超出一屏，再拖动ListView)，会重新读取传入的HashMap，所以拖动ListView后就又恢复原样了。
该filter声明了改activity可以对一个笔记目录做的事情。它允许用户查看或编辑该目录(使用VIEW和EDIT action)，或者选取特定的笔记(使用PICK action)。
<data>元素的mimeType指定了这些action可以操作的数据类型。它表明该activity可以从一个持有记事本数据的content provider(vnd.google.note)取得一个或多个数据项的Cursor(vnd.android.cursor.dir)。
注意该filter提供了一个DEFAULT category。这是因为Context.startActivity()和Activity.startActivityForResult()方法将所有的intent都作为作为包含了DEFAULT category来处理，只有两个例外:
显式指明目标activity名称的intent。
包含MAIN action 和LAUNCHER category的intent。
因此，除了MAIN和LAUNCHER的filter之外，DEFAULT category是必须的。
```  
<intent-filter>
	<action android:name="android.intent.action.GET_CONTENT" />
	<category android:name="android.intent.category.DEFAULT" />
	<data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
</intent-filter> 
```
这个filter描述了该activity能够在不需要知道目录的情况下返回用户选择的一个笔记的能力。GET_CONTENT action和PICK action相类似。在这两者中，activity都返回用户选择的笔记的URI。(返回给调用startActivityForResult()来启动NoteList activity的activity。) 在这里，调用者指定了用户选择的数据类型而不是数据的目录。
这个数据类型，vnd.android.cursor.item/vnd.google.note，表示了该activity可以返回的数据类型--一个笔记的URI。从返回的URI，调用者可以从持有笔记数据的content provider(vnd.google.note)得到一个项目(vnd.android.cursor.item)的Cursor。
也就是说, 对于PICK来说，数据类型表示activity可以给用户显式的数据类型。对于GET_CONTENT filter，它表示activity可以返回给调用者的数据类型。
下列intent可以被NoteList activity接受:
```  
action: android.intent.action.MAIN
```
不指定任何数据直接启动activity。
```  
action: android.intent.action.MAIN
category: android.intent.category.LAUNCHER
```
不指定任何数据直接启动activity。这是程序启动器使用的intent。所有使用该组合的filter的activity被加到启动器中。
```  
action: android.intent.action.VIEW
data: content://com.google.provider.NotePad/notes
```
要求activity显示一个笔记列表，这个列表位于content://com.google.provider.NotePad/notes。用户可以浏览这个列表并获取列表项的信息。
```  
action: android.intent.action.PICK
data: content://com.google.provider.NotePad/notes
```
请求activity显示content://com.google.provider.NotePad/notes下的笔记列表。用户可以选取一个笔记，activity将返回笔记的URI给启动NoteList的activity。
```  
action: android.intent.action.GET_CONTENT
data type: vnd.android.cursor.item/vnd.google.note
```
请求activity提供记事本数据的一项。
第二个activity，NoteEditor，为用户显示一个笔记并允许他们编辑它。它可以做以下两件事:
```  
<intent-filter android:label="@string/resolve_edit">
	<action android:name="android.intent.action.VIEW" />
	<action android:name="android.intent.action.EDIT" />
	<action android:name="com.android.notepad.action.EDIT_NOTE" />
	<category android:name="android.intent.category.DEFAULT" />
	<data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
</intent-filter> 
```
这个activity的主要目的是使用户编辑一个笔记--VIEW或者EDIT一个笔记。(在category中,EDIT_NOTE是EDIT的同义词。) intent包含匹配MIME类型vnd.android.cursor.item/vnd.google.note的URI--也就是某一个特定的笔记的URI。它一般来说是NoteList activity中的PICK或者GET_CONTENT action返回的。像以前一样，该filter列出了DEFAULT category。
```  
<intent-filter>
	<action android:name="android.intent.action.INSERT" />
	<category android:name="android.intent.category.DEFAULT" />
	<data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
</intent-filter> 
```
该activity的第二个目的是使用户能够创建一个新的笔记，并插入到已存在的笔记目录中。该intent包含了匹配vnd.android.cursor.dir/vnd.google.note的URI。
有了这些能力, NoteEditor就可以接受以下intent:
```  
action: android.intent.action.VIEW
data: content://com.google.provider.NotePad/notes/ID
```
要求activity显示给定ID的笔记。
```  
action: android.intent.action.EDIT
data: content://com.google.provider.NotePad/notes/ID
```
要求activity显示指定ID的笔记，然后让用户来编辑它。如果用户保存了更改，则activity更新该content provider的数据。
```  
action: android.intent.action.INSERT
data: content://com.google.provider.NotePad/notes
```
要求activity创建一个新的空笔记在content://com.google.provider.NotePad/notes，并允许用户编辑它，如果用户保存了更改，则该URI被返回给调用者。
最后一个activity，TitleEditor，允许用户编辑笔记的标题。
这可以通过直接调用activity(在intent中设置组件名称)的方式来实现。但是这里我们用这个机会来展示如何在已有数据上进行另外的操作
```  
<intent-filter android:label="@string/resolve_title">
	<action android:name="com.android.notepad.action.EDIT_TITLE" />
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.ALTERNATIVE" />
	<category android:name="android.intent.category.SELECTED_ALTERNATIVE" />
	<data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
</intent-filter> 
```
一个intent对象只能指定一个action，而一个intent filter可以指定多个action。action列表不能为空，否则它将组织所有的intent。
一个intent对象的action必须和intent filter中的某一个action匹配，才能通过。如果intent filter的action列表为空，则不通过。如果intent对象不指定action，并且intent filter的action列表不为空，则通过。
Category 测试
```  
<intent-filter >
	<category android:name="android.intent.category.DEFAULT" />
	<category android:name="android.intent.category.BROWSABLE" />
</intent-filter>
```
注意前面说到的对于action和category的常数是在代码中用的，而不是manifest文件中用的。例如，CATEGORY_BROWSABLE常数对应xml中的表示为"android.intent.category.BROWSABLE"。
一个intent要通过category测试，那么该intent对象中的每个category都必须和filter中的某一个匹配。
理论上来说，一个intent对象如果没有指定category的话，它应该能通过任意的category 测试。有一个例外: android把所有的传给startActivity()的隐式intent看做至少有一个category:"android.intent.category.DEFAULT"。因此，想要接受隐式intent的activity必须在intent filter中加入"android.intent.category.DEFAULT"。
#### Data test
```  
<intent-filter . . . >
	<data android:mimeType="video/mpeg" android:scheme="http" . . . />
	<data android:mimeType="audio/mpeg" android:scheme="http" . . . />
	. . .
</intent-filter>
```
每个<data>元素指定了一个URI和一个数据类型。URI每个部分为不同的属性 -- scheme, host, port, path:
```  
scheme://host:port/path
```
例如，在如下的URI中:content://com.example.project:200/folder/subfolder/etc 
scheme为"content"，host为"com.example.project"，port为"200"，path为"folder/subfolder/etc"。host和port一起组成了URI authority。如果host未指定，则port被忽略。
这些属性都是可选的，但它们并非相互独立: 要使一个authority有意义，必须指定一个scheme。要使一个path有意义，必须指定一个scheme和一个authority。
当intent对象中的URI和intent filter中相比较时，它只和filter中定义了的部分比较。例如，如果filter中之定义了scheme，那么所有包含该scheme的URI的intent对象都通过测试。对于path来说，可以使用通配符来进行部分匹配。
<data>元素的type属性指定了数据类型。它在filter中比在URI中更常见。intent对象和filter都可以使用"*"通配符作为子类型。例如"text/*" "audio/*"表示所有的子类型都匹配。
data测试的规则如下:
一个不含uri也不含数据类型的intent对象只通过两者都不包含的filter。
一个含uri但不含数据类型的intent对象(并且不能从uri推断数据类型的)只能通过这样的filter: uri匹配，并且不指定类型。 这种情况限于类似mailto:和tel:这样的不指定实际数据的uri。
一个只包含数据类型但不包含URI的intent只通过这样的filter: 该filter只列出相同的数据类型，并且不指定uri。
一个既包含uri又包含数据类型的intent对象只通过这样的filter: intent对象的数据类型和filter中的一个类型匹配，intent对象的uri要么和filter的uri匹配，要么intent对象的uri为content:或者file:，并且filter不指定uri。
如果一个intent可以通过多于一个activity或者service的filter，那么用户可能会被询问需要启动哪一个。如果一个都没有的话, 那么会抛出异常。
#### Common cases 常见情况
上述的最后一个规则(d)说明了组件通常可以从文件和content provider中获取数据。因此，它们的filter可以只列出数据类型不列scheme。这是个特殊情况。
下列<data>元素告诉android该组件可以从一个content provider取得图像数据并显示之:
```  
<data android:mimeType="image/*" />
```
由于大部分可用的数据由content provider提供，指定数据类型但不指定uri的filter是最常见的情况。
另外一个常见的配置是filter具有一个scheme和一个数据类型。例如，下列<data>元素告诉android该component可以从网络获取图像数据并显示之:
```  
<data android:scheme="http" android:type="video/*" />
```
考虑用户点击一个网页时浏览器的动作。它首先试图显示这个数据(当做一个html页来处理)。如果无法显示，则创建一个隐式intent，并启动一个可以处理它的activity。如果没有这样的activity，那么它请求下载管理器来下载该数据。然后它将数据置于一个content provider的控制之下，这样有很多activity(拥有只有数据类型的filter)可以处理这些数据。
大部分应用程序还有一种方法来单独启动, 不需要引用任何特定的数据. 这些能启动应用程序的activity具有action为"android.intent.action.MAIN" 的filter. 如果它们需要在应用程序启动器中显示, 它们必须指定"android.intent.category.LAUNCHER" 的category。
```  
<intent-filter . . . >
	<action android:name="code android.intent.action.MAIN" />
	<category android:name="code android.intent.category.LAUNCHER" />
</intent-filter>
```
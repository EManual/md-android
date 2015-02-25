#### 一、结构
```  
public interface SharedPreferences
android.content.SharedPreferences
```
#### 二、概述
用于访问和修改getSharedPreferences(String, int)返回偏好设置数据(preference data)的一个接口。对于任何一组特殊的preferences，所有的客户端共享一个此类单独的实例。
修改Preferences必须通过一个SharedPreferences.Editor对象，以确保当他们提交存储数据的操作时，preference值保持一致的状态。
注意：当前此类不支持多线程访问。后续将添加。
（译者注：这里译为” 偏好设定”，类似于ini文件，用于保存应用程序的属性设置）
#### 三、内部类
```  
interface SharedPreferences.Editor
```
用于修改SharedPreferences对象设定值的接口。
interface SharedPreferences.OnSharedPreferenceChangeListener
接口定义一个用于在偏好设定(shared preference)改变时调用的回调函数。
#### 四、公共方法
```  
public abstract boolean contains (String key)
```
判断preferences是否包含一个preference。
参数：
key 想要判断的preference的名称
返回值：
如果preferences中存在preference，则返回true，否则返回false。
```  
public abstract SharedPreferences.Editor edit ()
```
针对preferences创建一个新的Editor对象，通过它你可以修改preferences里的数据，并且原子化的将这些数据提交回SharedPreferences对象。（译者注：原子化——作为一个整体提交，原子性）
注意：如果你想要在SharedPreferences中实时显示，刚通过Editor对象进行的修改，那么你必须调用commit()方法。
返回值：
返回一个SharedPreferences.Editor的新实例，允许你修改SharedPreferences对象里的值。
```  
public abstract Map getAll ()
```
取得preferences里面的所有值
返回值：
返回一个map，其中包含一列preferences中的键值对
异常：
空指针异常(NullPointerException)
```  
public abstract boolean getBoolean (String key, boolean defValue)
```
从preferences中获取一个boolean类型的值。
参数：
key 获取的preference的名称
defValue 当此preference不存在时返回的默认值
返回值：
如果preference存在，则返回preference的值，否则返回defValue。如果使用此名称的preference不是一个boolean类型，则抛出ClassCastException。
异常：
ClassCastException
```  
public abstract float getFloat (String key, float defValue)
```
从preferences中获取一个float类型的值。
参数：
key 获取的preference的名称
defValue 当此preference不存在时返回的默认值
返回值：
如果preference存在，则返回preference的值，否则返回defValue。如果使用此名称的preference不是一个float类型，则抛出ClassCastException。
异常：
ClassCastException 
```  
public abstract int getInt (String key, int defValue)
```
从preferences中获取一个int类型的值。
参数：
key 获取的preference的名称
defValue 当此preference不存在时返回的默认值
返回值：
如果preference存在，则返回preference的值，否则返回defValue。如果使用此名称的preference不是一个int类型，则抛出ClassCastException。
异常
ClassCastException 
```  
public abstract long getLong (String key, long defValue)
```
从preferences中获取一个long类型的值。
参数：
key 获取的preference的名称
defValue 当此preference不存在时返回的默认值
返回值：
如果preference存在，则返回preference的值，否则返回defValue。如果使用此名称的preference不是一个long类型，则抛出ClassCastException。
异常：
ClassCastException
```  
public abstract String getString (String key, String defValue)
```
从preferences中获取一个String类型的值。
参数：
key 获取的preference的名称
defValue 当此preference不存在时返回的默认值
返回值：
如果preference存在，则返回preference的值，否则返回defValue。如果使用此名称的preference不是一个String类型，则抛出ClassCastException。
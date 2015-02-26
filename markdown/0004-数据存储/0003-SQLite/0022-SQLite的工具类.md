在写DAO层时，觉得从Cursor里一个一个的取出字段值再装到VO(值对象)里太麻烦了，就写了一个工具类，用到了反射，可以把查询记录的值装到对应的VO里，也可以生成该VO的List。
使用时需要注意：
考虑到Android的性能问题，VO没有使用Setter和Getter，而是直接用public的属性。
表中的字段名需要和VO的属性名一样，要是不一样就得在查询的SQL中使用字段别名让字段别名和VO属性名一样。
下面是高清有码：
```  
import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.util.HashMap;
import java.util.LinkedList;
import java.util.List;
import java.util.Map;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
 /**
  * 通过SQL语句查询出结果并封闭到VO里
  */
public class HappySQL {
	 /**
	  * 通过SQL语句获得对应的VO。注意：Cursor的字段名或者别名一定要和VO的成员名一样
	  * 
	  * @param db
	  * @param sql
	  * @param clazz
	  * @return
	  */
	@SuppressWarnings("rawtypes")
	public static Object sql2VO(SQLiteDatabase db, String sql, Class clazz) {
		Cursor c = db.rawQuery(sql, null);
		return cursor2VO(c, clazz);
	}
	 /**
	  * 通过SQL语句获得对应的VO。注意：Cursor的字段名或者别名一定要和VO的成员名一样
	  * 
	  * @param db
	  * @param sql
	  * @param selectionArgs
	  * @param clazz
	  * @return
	  */
	@SuppressWarnings("rawtypes")
	public static Object sql2VO(SQLiteDatabase db, String sql,
			String[] selectionArgs, Class clazz) {
		Cursor c = db.rawQuery(sql, selectionArgs);
		return cursor2VO(c, clazz);
	}
	 /**
	  * 通过SQL语句获得对应的VO的List。注意：Cursor的字段名或者别名一定要和VO的成员名一样
	  * 
	  * @param db
	  * @param sql
	  * @param clazz
	  * @return
	  */
	@SuppressWarnings("rawtypes")
	public static List sql2VOList(SQLiteDatabase db, String sql, Class clazz) {
		Cursor c = db.rawQuery(sql, null);
		return cursor2VOList(c, clazz);
	}
	 /**
	  * 通过SQL语句获得对应的VO的List。注意：Cursor的字段名或者别名一定要和VO的成员名一样
	  * 
	  * @param db
	  * @param sql
	  * @param selectionArgs
	  * @param clazz
	  * @return
	  */
	@SuppressWarnings("rawtypes")
	public static List sql2VOList(SQLiteDatabase db, String sql,
			String[] selectionArgs, Class clazz) {
		Cursor c = db.rawQuery(sql, selectionArgs);
		return cursor2VOList(c, clazz);
	}
	 /**
	  * 通过Cursor转换成对应的VO。注意：Cursor里的字段名（可用别名）必须要和VO的属性名一致
	  * 
	  * @param c
	  * @param clazz
	  * @return
	  */
	@SuppressWarnings({ "rawtypes", "unused" })
	private static Object cursor2VO(Cursor c, Class clazz) {
		if (c == null) {
			return null;
		}
		Object obj;
		int i = 1;
		try {
			c.moveToNext();
			obj = setValues2Fields(c, clazz);
			return obj;
		} catch (Exception e) {
			System.out.println(e);
			System.out.println("ERROR @：cursor2VO");
			return null;
		} finally {
			c.close();
		}
	}
	 /**
	  * 通过Cursor转换成对应的VO集合。注意：Cursor里的字段名（可用别名）必须要和VO的属性名一致
	  * 
	  * @param c
	  * @param clazz
	  * @return
	  */
	@SuppressWarnings({ "rawtypes", "unchecked" })
	private static List cursor2VOList(Cursor c, Class clazz) {
		if (c == null) {
			return null;
		}
		List list = new LinkedList();
		Object obj;
		try {
			while (c.moveToNext()) {
				obj = setValues2Fields(c, clazz);
				list.add(obj);
			}
			return list;
		} catch (Exception e) {
			e.printStackTrace();
			System.out.println("ERROR @：cursor2VOList");
			return null;
		} finally {
			c.close();
		}
	}
	 /**
	  * 把值设置进类属性里
	  * 
	  * @param columnNames
	  * @param fields
	  * @param c
	  * @param obj
	  * @throws Exception
	  */
	@SuppressWarnings("rawtypes")
	private static Object setValues2Fields(Cursor c, Class clazz)
			throws Exception {
		String[] columnNames = c.getColumnNames();// 字段数组
		Object obj = clazz.newInstance();
		Field[] fields = clazz.getFields();
		for (Field _field : fields) {
			Class<? extends Object> typeClass = _field.getType();// 属性类型
			for (int j = 0; j < columnNames.length; j++) {
				String columnName = columnNames[j];
				typeClass = getBasicClass(typeClass);
				boolean isBasicType = isBasicType(typeClass);
				if (isBasicType) {
					if (columnName.equalsIgnoreCase(_field.getName())) {// 是基本类型
						String _str = c.getString(c.getColumnIndex(columnName));
						if (_str == null) {
							break;
						}
						_str = _str == null ? "" : _str;
						Constructor<? extends Object> cons = typeClass
								.getConstructor(String.class);
						Object attribute = cons.newInstance(_str);
						_field.setAccessible(true);
						_field.set(obj, attribute);
						break;
					}
				} else {
					Object obj2 = setValues2Fields(c, typeClass);// 递归
					_field.set(obj, obj2);
					break;
				}
			}
		}
		return obj;
	}
	 /**
	  * 判断是不是基本类型
	  * 
	  * @param typeClass
	  * @return
	  */
	@SuppressWarnings("rawtypes")
	private static boolean isBasicType(Class typeClass) {
		if (typeClass.equals(Integer.class) || typeClass.equals(Long.class)
				|| typeClass.equals(Float.class)
				|| typeClass.equals(Double.class)
				|| typeClass.equals(Boolean.class)
				|| typeClass.equals(Byte.class)
				|| typeClass.equals(Short.class)
				|| typeClass.equals(String.class)) {
			return true;
		} else {
			return false;
		}
	}
	 /**
	  * 获得包装类
	  * 
	  * @param typeClass
	  * @return
	  */
	@SuppressWarnings("all")
	public static Class<? extends Object> getBasicClass(Class typeClass) {
		Class _class = basicMap.get(typeClass);
		if (_class == null)
			_class = typeClass;
		return _class;
	}
	@SuppressWarnings("rawtypes")
	private static Map<Class, Class> basicMap = new HashMap<Class, Class>();
	static {
		basicMap.put(int.class, Integer.class);
		basicMap.put(long.class, Long.class);
		basicMap.put(float.class, Float.class);
		basicMap.put(double.class, Double.class);
		basicMap.put(boolean.class, Boolean.class);
		basicMap.put(byte.class, Byte.class);
		basicMap.put(short.class, Short.class);
	}
}
```
调用例子：
获得单个VO：
```  
String sql = "select * from tb_info_review where info_id = " + info_id;  
return (Info_re) HappySQL.sql2VO(db, sql, Info_re.class);
```
获得List：
```  
String sql = "select * from tb_info m, tb_sub2 f where m.send_state = 1 and review_state = 0 and m.info_id = f.info_id";  
List<Info> infos = HappySQL.sql2VOList(db, sql, Info.class);   
```
带变参获得List：
```  
String sql = "select * from tb_info m,tb_ttf f where m.info_id = ? and m.info_id = f.info_id";  
return (SbMan) HappySQL.sql2VO(db, sql, new String[] { info_id + "" }, SbMan.class);  
```
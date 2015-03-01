7 视图界面主文件 2
```  
<xmlns:android="http://schemas.android.com/apk/res/android"> 
	<LinearLayout
		android:orientation="vertical"
		android:layout_width="fill_parent"
		android:layout_height="fill_parent"
		>
		<ListView
			android:layout_width="fill_parent"
			android:layout_height="wrap_contentv
			android:id="@+id/listview"
			/>
```
8 业务bean
```  
 /**
  * 数据库的实体类
  */
public class Person {
	private Integer personId;
	private String name;
	private Short age;
	public Person() {
	}
	public Person(Integer personId, String name, Short age) {
		this.personId = personId;
		this.name = name;
		this.age = age;
	}
	public Person(String name, Short age) {
		this.name = name;
		this.age = age;
	}
	public Integer getPersonId() {
		return personId;
	}
	public void setPersonId(Integer personId) {
		this.personId = personId;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public Short getAge() {
		return age;
	}
	public void setAge(Short age) {
		this.age = age;
	}
	@Override
	public String toString() {
		return "Person [personId=" + personId + ", name=" + name + ", age= + age + "]";
	}
}
```
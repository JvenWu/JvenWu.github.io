## for Android: Use Gson | Gson 库的使用
gson是google提供的一个处理json的项目，使用它可以方便地处理java对象与json的转化，须下载gson库
> 一般转换方式有如下
- 对象转json
- 集合转json
- json转对象
- json转集合
- map转json
- JsonParser解析json

#### 对象转json、集合转json
当进行对象转json时，json的键为对象的字段名
若Pserson为
```java
class Person{
    public String name;
    public int age;
}
```
则转换后的String为{"name":"nameValue","age":ageValue}  
示例
```java
Gson gs = new Gson();
//把Person对象转为JSON格式的字符串
String objectStr = gs.toJson(person);
//把Persionw集合转为JSON格式字符串
String listStr = gs.toJson(persons);
```
#### json转对象、json转集合
示例
```java
//把JSON字符串转为对象
Person jsonObject = gs.fromJson(objectStr, Person.class);
//把JSON格式的字符串转为集合
List<Person> jsonListObject =
	(List<Person>)gs.fromJson(listStr, new TypeToken<List<Person>>(){}.getType());
```
#### map转json、JsonParser解析json
示例
```java
//把map对象转为JSON
Map map = new HashMap();
map.put("appID", "a123");
map.put("userID", "u456");
String state = gs.toJson(map);
//用JsonParser来解析JSON
JsonParser jsonParser = new JsonParser();
String appId = jsonParser.parse(state).getAsJsonObject().get("appID").getAsString();
```

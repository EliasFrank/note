JSON

1json是什么

	* 他是js提供的一种数据交互格式

2.json语法

	* {}：是对象！
	* 属性名必须使用双引号括起来！不能用单引号！！！
 * 属性值：
   	* null
      	* 数值
      	* 字符串
   	* 数值：使用[]括起来
   	* boolean值：true和false

3.应用

	* var person = {"name" : "hl", "age" : 20, "isSingle": true}

4.json与xml比较

	* 可读性：xml胜出
	* 解析难度：json本身就是js对象（主场作战），所以简单很多
	* 流行度：xml已经流行了好多年，但在ajax领域，json更受欢迎

-------------

json-lib

* 它是可以吧Javabean转换成json串
* 核心类
  * JSONObject -- > map
    * toString();
    * JSONObject map = JSONObject.fromObject(person);吧对象转换成JSONObject对象
  * JSONArray -- >  List
    * toString();
    * JSONArray list = JSONArray.fromObject(list1);吧list对象转换成JSONArray对象

```
JSON.stringify();可以直接将json转换为字符串
```


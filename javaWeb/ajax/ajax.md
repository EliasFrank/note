1.ajax是什么

	* asynchronous javascript and xml : 异步的js和xml
	* 他能使用JS访问服务器，而且是异步访问
 * 服务器给客户端的响应一般是整个界面，一个HTML完整界面！但是在ajax中因为是局部刷新，那么服务器就不用再响应整个界面！而只是数据！
   * text: 纯文本
   * xml：
   * json：他是js提供的数据交互格式，它在ajax中最受欢迎

2.异步交互和同步交互

 * 同步：
   * 发送一个请求，就要等待服务器的响应结束，然后才能发第二个请求！中间这段时间就是一个字“卡”
   * 刷新的是整个界面
* 异步
  * 发送一个请求后，无需等待服务器的响应，然后就可以发第二个请求
  * 可以使用JS接受服务器的响应，然后使用JS来局部刷新

3.ajax应用场景

	* 百度的搜索框
	* 用户注册时（校验用户名是否被注册过）

4.ajax的优缺点

 * 优点
   * 异步交互，增强了用户的体验
   * 性能：因为服务器无需再响应整个界面，只需要响应部分内容，所以服务器的压力减轻了
* 缺点
  * ajax不能应用在所有场景
  * ajax无的增多了对武器的访问次数，给服务器带来了压力

--------------------------

ajax四步操作

1.第一步（得到XMLHttpRequest）

 * ajax其实只需要学习一个对象：XMLHttpReuqest，如果掌握了他，就掌握了ajax
 * 得到XMLHttpRequest

   * 大多数浏览器都支持：var xmlHttp = new XMLHttpRequest();
   * IE6.0: var xmlHttp = new ActiveXObject("Msxml2.XMLHTTP");
   * IE5.5以及更早版本的IE：var xmlHttp = new ActiviteXObject("Microsoft.XMLHTTP");



编写创建XMLHttpRequest对象的函数



```javascript
function createXMLHttpRequest(){
    try{
		return new XMLHttpRequest();
    }catch(e){
        try{
			return new ActiviteXObject("Msxml2.XMLHTTP")
        }catch(e){
            try{
				return new ActiveXObject("Microsoft.XMLHTTP")
            }catch(e){
				alert("what the fuck! ")
            	throw e;
            }
        }
    }
}
```



2.第二步（打开与服务器的连接）

 * xmlHttp.open()：用来打开与服务器的连接，他需要三个参数
   	* 请求方式：可以是GET或者POST
   * 请求的URL：指定服务器端资源，例如：/day23_1/AServlet
   * 请求是否为异步：如果为true表示发送异步请求，否则同步请求
* xmlHttp.open("GET", "/day23/AServlet", true)

3.第三步（发送请求）

 * xmlHttp.send(null)：如果不给可能会造成部分浏览器无法发送

   参数就是请求体内容！如果是GET请求，必须给出null		

4.第四步： 

	* 在xmlHttp对象的一个事件上注册监听器：onreadystatechange
 * xmlHttp对象一共有五个状态：
   	* 0：刚创建，还没有调用open方法
    * 1：请求开始，调用 了open方法，但还没有调用send方法
       * 2：调用完了send方法
      *  3：服务器已经开始响应，当不表示响应结束
      	* 4：服务器响应结束（通常我们只关心这个状态）
 * 得到xmlHttp对象的状态码
   	* var state = xmlHttp.readyState//可能是0,1,2,3,4
* 得到服务器响应的状态码
  * var status = xmlHttp.status;//例如为200,404,500
* 得到服务器响应的内容
  * var content = xmlHttp.responseText;//得到服务器的响应文本格式内容
  * var content = xmlHttp.responseXML;//得到服务器的响应xml响应的内容，他是Document

```javascript\
xmlHttp.onreadystatechange = function(){
//xmlHttp的五种状态都会调用本方法
//双重判断：判断是否为4状态，而且还要判断是否为200
				if(xmlHttp.readyState == 4 && xmlHttp.status == 200){
					var text = xmlHttp.responseText;
					
				}
			}
```



发送POST请求（如果发送请求时，需要带有参数，一般都会用POST请求

* open: xmlHttp.open("POST".............)

* 添加一步：设置Content-Type请求头

  ​	xmlHttp.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");

* send：xmlHttp.send("username=张三&password=123");//发送请求时指定请求体

```java
window.onload = function(){

	var id = document.getElementById("idId");
	id.onblur = function(){
		var info = id.value;
		
		if(info == ""){
			alert("请填写编号");
			return;
		}
		if(isNaN(info)){
			alert("编号不是纯数字");
			return;
		}
		
		//创建XMLHttpRequest对象
		var xmlHttp = createXMLHttpRequest();
		
		//打开与服务器的连接
		xmlHttp.open("POST", "/JXAU_Check_System/admin/FindAttIdServlet", true);
		xmlHttp.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
		
		//发送请求
		xmlHttp.send("id=" + info);
		
		//监听状态改变
		xmlHttp.onreadystatechange = function(){
			
			//双重判断
			if(xmlHttp.readyState == 4 && xmlHttp.status == 200){
				var root = document.getElementById("error");
				
				var result = xmlHttp.responseText;
				
				root.innerHTML = result;
				
				if(result != "")
					document.getElementById("submitName").style.display="none";
				else 
					document.getElementById("submitName").style.display="block";
			}
		}
	}
}
```


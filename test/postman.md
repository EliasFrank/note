### 接口测试的简介和分类

接口测试就是针对软件对外提供服务的接口的输入输出进行测试，以及接口间相互逻辑的测试，验证接口功能与接口描述文档的一致性

分类：

* 测试外部接口：测试被测系统和外部系统之间的接口（只需要测试正例即可）
* 测试内部接口
  * 内部接口只提供给内部系统使用（只需要测试正例即可）
  * 内部接口提供给外部系统使用（测试必须非常全面，需要考虑各种异常场景，权限控制）

postman发起接口请求需要关注的四大信息：接口URL地址、请求方法、请求头和请求参数

获取响应返回信息，重点在于响应的正文主题，同时可能对响应头和状态码进行校验

Post接口请求实战

请求体与Content-Type头域

之前的GET方法接口都是通过在URL中添加请求参数的方式完成参数传递。在对使用POST方法的接口进行测试时，需要特别注意**请求参数在body中的格式**以及请求头中的**Content-Type头域**，该头域规定了接口接受的请求参数传值格式。

在Postman请求体中传递参数时，在body菜单中设置请求体的格式

* **x-www-form-urlencoded**对应于Content-Type头域为x-www-form-urlencoded的类型，是以键值对形式发送的表单参数，同时参数会携带在url中

* **form-data**对应于Content-Type的multipart/form-data类型，既可以发送键值对也可以进行文件参数传递

* **raw**选项中可以使用亲求提原始格式编辑各Content-Type类型对应的参数格式，直接按请求体的格式来进行内容发送

* **binary**选项用于发送文件内容请求

### 环境变量和全局变量

接口测试过程中，经常需要使用已经设定好的参数或者其他接口的返回中获取变化的参数。Postman提供了变量的机制供这种场景使用

Postman变量分为环境变量和全局变量两种，在接口请求中可以选择一组环境变量使用。而全局变量可供所有请求使用。

使用变量的格式为{{变量名}}调用变量的值

环境变量优先级高于全局变量

### 测试沙箱

Postman测试沙箱其实是结合JS脚本完成测试中的功能，在请求发起前后实现部分测试操作

常用功能：

* 请求前脚本（pre-request scripts）设置请求前置操作如设置变量等
* 请求后对状态码、响应头、响应正文等信息进行断言操作
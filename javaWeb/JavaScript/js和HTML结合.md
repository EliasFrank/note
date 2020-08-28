1)	JavaScript和HTML有两种结合方式

```shell
<!--html和CSS 的第一种结合方式
<html>
	<head>
		<title>www.xxxx.com</title>
	</head>
	<body>
		 <script type="text/javascript">
			alert("4554454");
		 </script>
	</body>
</html>-->

<!--HTML和js的第二种结合方式-->
<html>
	<head>
		<title>www.xxxx.com</title>
	</head>
	<body>
		 <script type="text/javascript" src = "1.js">
			alert("526526");//这一句语句将不会执行，使用第二种结合方式时，script里面的语							//句将不会执行
		 </script>
	</body>
</html>
```

2)	js里面只有五个属性，分别是string，number，boolean，null，undefined。null表示对一个类的引用。而undefined表示定义了一个变量但没有赋值

3)	js中，number属性不区分小数和整数，123/1000=0.123而不是0，string类型的“123”可以进行和数字相减的操作，但是“adv”等不能转换为数字的就不行。boolean类型的true值和数字相加时，代表1，false代表0

4)	js中，==只判断变量的值是否相同，而===不仅判断变量的值，而且会判断变量的类型

5)	js中没有函数重载，只会调用最近的函数。并且多余的参数会自动忽略

6)	js的script中直接定义的变量，是全局变量。可在本页面中的所有js代码片中使用


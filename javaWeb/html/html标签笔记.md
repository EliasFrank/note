HTML中，超链接的打开默认是在当前界面打开的，如果需要在其他窗口打开，则需要加target

```shell
<a href = "" target = "_blank"> not touch me</a>
<!--疑惑 为什么target = “” 双引号里面加任何东西都会新建界面 _self除外-->
```

当HTML中,超链接不需要打开任何文件时

```shell
<!-- 这个格式的超链接会打开当前文件目录的资源管理器
	在Firefox和Google浏览器中则不会
-->
<a href = ""> 别点我 </a>

<br/>

<!--当此超链接不需进行任何跳转时，则需要一个占位符#-->
<a href = "#">点我也没用 </a>

```

回到顶部时，要加上#

```shell
<a name = "top"> top </a>
<a href = "#top"> return to the top </a>
```

表格标签中，可以再td标签内添加align以控制字符位置

```shell
<tr>
<td align = "right"> 
<!--laign的属性可以设置为right，left, center-->
</td>
</tr>
```

加载图片的标签是

```shell
<img src = "path">
```


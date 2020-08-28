| IP          | count |
| ----------- | ----- |
| 192.168.1.1 | 2     |
| 192.168.2.2 | 3     |

统计工作需要在所有资源之前都执行，那么就可以放到Filter中。

我们这个过滤器不打算做拦截操作！应为我们只是用来做统计的。

用什么东西来装载统计的数据。Map<String, Integer>

整个网站只需要一个Map即可！

Map什么会后创建（使用ServletContextListener，在服务器启动完成创建，并只在ServletContext中）Map保存到ServletContext中

&gt;Map需要在filter中用来保存数据

&gt;Map需要在页面使用，打印Map中的数据
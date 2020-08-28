1. 概述

   * 注解替代web.xml配置文件
   * 异步处理
   * 对上传的支持

   -----------------------

2. 注解替代配置文件

   1. 删除web.xml
   2. 在servlet类上添加@WebServlet(urlPatterns=("/AServlet"))

   总结：

    * 注解好处： 配置信息少
    * 注解缺点： 不方便修改
      3. 在filter类上添加@WebFilter(urlPatterns="/AFilter")
      4. 在Listener类上添加@WebListener

-----------------

3. 异步处理

   1. 什么是异步处理

      1. 原来，在服务器没有结束响应之前，浏览器是看不到响应内容的。只有响应结束时，浏览器才能显示结果。现在异步处理的作用，在服务器开始响应之后，浏览器就可以看到响应内容， 不用等待服务器响应结束

      2. 实现异步的步骤：

         1. 得到AsyncContext, 他异步上下文对象

            AsyncContext ac = request.startAsync(request, response);

         2. 给上下文一个Runnable对象，启动它！(给上下文一个任务，让他完成)

            ```java
            ac.start(new Runnable(){
            	public void run(){
            	
            	}
            });
            ```

            

         3. @WebServlet(urlPatterns="/AServlet", asyncSupported=true)

         4. response.setContentType("text/html;charset=utf-8");

         5. IE如果不能正常输出，这说明响应体的大小不足512B，那你学要多输出点对象

         6. AsyncContext.complete()；通知Tomcat异步线程已经执行结束了，这样Tomcat才会即使的断开与浏览器的连接

4. 上传支持
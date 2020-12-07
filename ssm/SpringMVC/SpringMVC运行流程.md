1. 所有请求，前端控制器（dispatcherServlet）收到请求，调用doDispatch进行处理
2. 根据HandlerMapping中保存的请求映射信息找到，处理当前请求的处理器执行链（包含拦截器）
3. 根据当前处理器找到他的HandlerAdapter（适配器）
4. 拦截器的preHandle先执行
5. 适配器执行目标方法，并返回ModelAndView
   1. ModelAttribute注解标注的方法提前运行
   2. 执行目标方法的时候（确定目标方法的参数）
      1.  有注解
      2. 没注解：
         1. 看是否Model、Map以及其他的类型
         2. 如果是自定义类型
            1. 从隐含模型中看有没有，如果有，就从隐含模型拿
            2. 如果没有，在看看是否SessionAttribute标注的属性，如果是，从session中拿，如果拿不到会抛异常
            3. 都不是，就利用反射创建对象
6. 拦截器的postHandle执行
7. 处理结果（页面渲染流程）
   1. 如果有异常使用异常解析器处理异常，处理完后返回modelAndview
   2. 调用reader进行页面渲染
      1. 视图解析器根据视图名得到视图对象
      2. 视图对象调用render方法
   3. 执行拦截器的afterCompletion

![1597832175563](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1597832175563.png)

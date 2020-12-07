整合目的：分工明确

SpringMVC的配置文件就用来配置和网站转发逻辑以及网站功能有关（视图解析器，文件上传解析器，支持ajax）

spring的配置文件来配置和业务有关的（事务控制、数据源）

```
<import resource="SpringMVC.xml"/> 可以合并配置文件
```

---

SpringMVC和spring分容器


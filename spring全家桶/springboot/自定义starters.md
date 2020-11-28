## starter

1. 这个场景需要使用到的依赖是什么

2. 如何编写自动配置

    ```
    @Configuration //指定这个类是一个配置类
    @ConditionalOn*** //在指定条件成立的情况下自动配置类生效
    @AtuoConfigureAfter //指定自动配置类的顺序
    @Bean //给容器中添加组件
    
    @ConfigurationProperties //结合相关***Properties类来绑定相关的配置
    @EnableConfigurationProperties //让***Properties生效加入到容器中
    
    自动配置类要能加载
    将需要启动就加载的配置类，配置在META-INF/spring.factories
    org.springframework.context.ApplicationContextInitializer=\
    org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
    org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener
    ```

3. 模式

    启动器只用来做依赖导入

    专门写一个自动配置模块

    启动器依赖自动配置；别人只需要引入启动器（starter）

    -mybatis-spring-boot-starter；自定义启动器名-spring-boot-starter
Maven的核心概念

1. 约定的目录结构

 	2. POM
 	3. 坐标
 	4. 依赖
 	5. 仓库
 	6. 生命周期、插件、目标
 	7. 继承
 	8. 聚合



----------

创建约定的目录结构

![1585573535833](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1585573535833.png)

1. 跟目录：工程名
2. SRC目录：源码
3. pom.xml文件：Maven工程的核心配置文件
4. main目录：存放主程序
5. test目录：存放测试程序
6. java目录：存放java源文件
7. resources目录：存放框架或其他工具的配置文件

-------------

为什么要遵守约定的目录结构呢

 * Maven要负责我们这个项目的自动化构建，以编译为例，Maven要想自动进行编译，那么他必须知道java源文件保存在哪里

 * 如果我们自己定义的东西想要让框架或工具知道，有两种办法。

    * 以配置的方式明确告诉框架

      ```
      <param-value>classpath:spring-context.xml<param-value>
      ```

   * 遵守框架内部已经存在的约定

     ```
     log4j.properties
     log4j.xml
     ```

     约定 > 配置 > 编码



-------------------------

常用Maven命令

1. mvn clean: 清理

 	2. mvn compile: 编译主程序
 	3. mvn test-compile: 编译测试程序
 	4. mvn test: 执行测试
 	5. mvn package:  打包

![1585646705118](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1585646705118.png)

-------------------------------

POM

 1. 含义：Project Object Model 项目对象模型

    DOM ：Document Object Model 文档对象模型

 2. pom.xml对于Maven工程师核心配置文件，与构建过程相关的一切设置都在这个文件中进行配置

    重要程度相当于web.xml对于动态web工程

--------------

坐标

 1. 数学中的坐标：

     	1. 在平面空间上，使用X, Y两个向量可以唯一的定位平面中的任何一个点
     	2. 在空间中，使用X, Y , Z三个向量可以唯一的定位空间中的任何一个点

	2. Maven中的坐标

    	1. 使用下面三个向量在仓库中唯一定位一个maven工程

        groupId：公司或组织域名倒序+项目名

        ```xml
        <groupId>com.jxau.maven</groupId>
        ```

        artifactId: 模块名

        ```xml
        <artifactId>hello</artifictId>
        ```

        version : 版本

        ```xml
        <version>1.0.0</version>
        ```

    	2. Maven工程的坐标与仓库中路径的对应关系

        ```xml
        <groupId>org.springframework</groupid>
        <artifactId>spring-core</artifactId>
        <version>4.0.0.RELEASE</version>
        ```

        ```
        对应的路径是
        org/springframework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE
        ```



-----------------

仓库

 1. 仓库的分类

     	1. 本地仓库：当前电脑上部署的仓库目录，为当前电脑上所有maven工程服务
     	2.  远程仓库： 
          	1.  私服：搭建在局域网环境中，为局域网范围内的所有Maven工程服务
          	2. 中央仓库：架设在Internet上，为全世界所有maven工程服务
          	3. 中央仓库的镜像：为了分担中央仓库的流量，提升用户访问速度

	2. 仓库中保存的内容：Maven工程

    	1. Maven自身所需要的插件
    	2. 第三方框架或工具的jar包
    	3. 我们自己开发的Maven工程

	3. 依赖

    	1. Maven解析依赖信息时会到本地仓库中查找被依赖的jar包

        对于我们自己开发的maven工程，mvn install命令安装后就可以进入仓库
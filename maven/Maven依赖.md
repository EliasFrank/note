依赖

 1. maven解析依赖信息时会到本地仓库中查找被依赖的jar包

    对于我们自己开发的Maven工程，使用mvn install命令安装后就可以进入仓库

	2. 依赖的范围

    	1. compile范围依赖
        * 对主程序是否有效：有效
        * 对测试程序是否有效：有效
        * 是否参与打包：参与
    	2. test范围依赖：
        - 对主程序是否有效：无效
        - 对测试程序是否有效：有效
        - 是否参与打包：不参与
    	3. provided
        - 对主程序是否有效：有效
        - 对测试程序是否有效：有效
        - 是否参与打包：不参与
        - 是否参与部署：不参与

![1585814575841](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1585814575841.png)

![1585814593342](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1585814593342.png)

------------------------------

生命周期

	1. 各个构建环节执行的顺序，不能打乱顺序，必须按照既定的正确顺序来执行
 	2. Maven的核心程序中定义了抽象的生命周期，生命周期中各个阶段的具体任务是由插件来完成的
 	3. Maven核心程序为了更好的实现自动化构建，按照这一特点执行生命周期中的各个阶段，不论现在要执行生命周期中的哪一个阶段，都是从这个生命周期最初的位置开始执行



-------------------



依赖（高级）

 1. 依赖的传递性

    ![1586003708499](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1586003708499.png)

    好处：可以传递的依赖不必在每个模块工程中都重复申明，在”最下面“的工程中依赖一次即可

    注意：非compile范围的依赖不能够传递。所以在各个工程模块中，如果哟需要就得重复声明依赖

	2. 依赖排除的设置方式

    ```xml
    <exclusions>
    	<exclusion>
        	<groupId>comms-logging</groupId>
            <artifactId>commons-logging</artifactId>
        </exclusion>
    </exclusions>
    ```

	3. 依赖的原则：

    	1. 作用：解决模块工程之间的jar包冲突问题
    	2. ![1586005352456](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1586005352456.png)

	4. 同一管理依赖的版本

    	1. 情景举例：

        如果对spring各个jar包的依赖版本都是4.0.0，如果需要同一升级为4.1.1，怎么办？手动逐一修改不可靠

    	2. 建议配置方式

        	1. 使用properties标签内使用自定义标签同一声明版本号

            ```xml
            <properties>
            	<jxau.spring.version>4.0.0.RELEASE</jxau.spring.version>
            </properties>
            ```

        	2. 在需要同一版本的位置，使用${自定义标签名}引用声明的版本号

            ```xml
            <version>${jxau.spring.version}</version>
            ```

--------------------

继承

 1. 现状 

    hello依赖的junit：4.0

    hellofriend依赖的junit：4.0

    makefriedns依赖的junit：4.9

     

    由于test范围的依赖不能传递，所以必然会分散在各个模块的工程中，很容易造成版本不一致

	2. 需求：同意管理各个模块工程中对junit依赖的版本
	3. 解决思路：将junit依赖统一提取到“父”工程中，在子工程中声明junit依赖时不指定版本，以父工程中统一设定 的为准，同时也便于修改。
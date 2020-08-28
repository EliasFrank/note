输入命令后回车

​	提请shell程序找到键入命令做对应的可执行程序或代码，并由其分析后提交给内核分配资源将其运行起来

在shell中可执行的命令有两类

  1. 内部命令：由shell自带的，而且通过某命令形式提供。

     * help 内部命令列表
     * enable cmd 启用内部命令
     * enable -n cmd 禁用内部命令
     * enable -n 查看所有禁用的内部命令

	 2. 外部命令：在文件系统路径下有对应的可执行程序文件

       查看路径：which -a \ --skip-alias; whereis

	 3. 区别指定的命令是内部或外部指令

       type COMMAND

	 4. hash缓存表

     ​		系统初始hash表为空，当外部命令执行时，默认会从PATH路径下寻找该命令，找到后会将这条命令的路径记录到hash表中，当再次使用该命令时，shell解释器首先回去查看hash表，存在将执行之，如果不存在，降回去PATH路径下寻找，利用hash缓存表可大大提高命令的调用速率

	 5. hash常见用法

     * hash 显示hash缓存

     * hash -l 显示hash缓存，可作为输入使用

     * hash -p path name 将命令全路径path起别名为name

     * hash -t name 打印缓存中name的路径

     * hash -d name 清除name缓存

     * hash -r 清除缓存

       缓存cache：将刚用硬盘的数据放在内存中，下次用此数据，不需要从银盘找，直接内存取出
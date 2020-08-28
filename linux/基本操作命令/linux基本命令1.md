执行linux命令的过程

```
alias ------ 内部      ---  hash表  -------------$PATH   ------命令找不到
```

-----



命令别名

 * 显示当前shell进程所有可用的命令别名

   alias

 *  定义别名NAME, 其相当于执行命令VALUE

   alias NAME='VALUE'

 * 在命令行中定义的别名，仅对当前shell进程有效 

 * 如果向永久有效，要定义在配置文件中

   仅当前用户：~/.bashrc

   对所有用户有效  /etc/bashrc 

--------



编辑配置给出的新配置不会立即生效

bash进程重新读取配置文件

```shell
. /path/to/config_file
source /path/to/config_file
```

-------

撤销别名：unalias

​		unalias [-a] name [name ...]

​		-a 取消所有别名

如果别名同原命令同名，如果要执行原命令，可使用

```shell
\ALIASNAME
"ALIASNAME"
'ALIASNAME'
command ALIASNAME
/path/command(只适用于外部命令)
```

---------

命令格式

​	COMMAND [OPTIONS...] [ARGUMENTS...]

 * 选项：用于启用或关闭命令的某个或某些功能

   - 短选项： -c 例如 ：-l, -h
   - 长选项： --word 例如： --all, --human

 * 参数：命令的作用对象，比如文件名，用户名等。

   ​		

   注意：

   	* 多个选项以及多参数和命令之间使用空白子字符分隔
   	* 取消和结束命令执行： Ctrl + c, Ctrl + d
   	* 多个命令可以用；符号隔开
   	* 一个命令可以用\分成多行

------------

日期和时间

 * linux的两种时钟：

   	* 系统时钟: 由linux内核通过CPU的工作评率进行的
   	* 硬件时钟：主板

* 相关命令：

  * date 显示和设置系统时间

    ```shell
    date MMDDHHmmYYYY.ss
    ```

    ```shell
    date + %s
    date -d @1509536033
    ```

  * hwclock, clock: 显示硬件时钟

    ```shell
    -s, --hctosys 以硬件时钟为准，校正系统时钟
    -w, --systohc 以系统时钟为准，校正硬件时钟
    ```

* 时区：/etc/localtime

* 显示日历：cal -y

----------------

简单命令

 * 关机：halt, poweroff

 * 重启: reboot

   	* -f : 强制，不调用shutdown
   	* -p: 切断电源

 * 关机或重启 : shutdown

   	* shutdown [OPTION] ... [TIME][MESSAGE]
   	* -r : reboot
   	* -h halt
   	* -c : cancel
    * TIME : 无指定，默认相当于+1 (CentOS7)
      	* now : 立刻，相当于+0
      	* +m：相对时间表示法，几分钟之后，例如+3
      	* hh:mm: 绝对时间表示，指明具体时间

* 用户登录信息查看命令

  whoami：显示当前登录有效用户

  who：系统当前所有的登录会话

  w：系统当前所有的登录回话及所做的操作

* nano文本编辑

* screen命令

  * 创建新screen会话

    screen -S [SESSION]

  * 加入screen会话

    screen -X [SESSION]

  * 退出并关闭screen会话

    exit

  * 玻璃当前screen会话

    Ctrl + a, d

  * 显示所有已经打开的screen会话

    screen -ls

  * 恢复某screen会话

    screen -r [SESSION]
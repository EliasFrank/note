![1587274908686](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1587274908686.png)

![1587298564669](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1587298564669.png)

----------

man命令

 * 提供命令帮助的文件

 * 手册页存放在/user/share/man

 * 几乎每个命令都有man的“页面”

 * man页面分组为不同的“章节”

 * 统称为linux手册

 * man命令的配置文件：/etc/man.config | man_db.conf

   MANPATH /PATH/TO/SOMEWHERE: 指明man文件搜索位置

* man -M /PATH/TO/SOMEWHERE COMMAND: 到指定位置下搜索COMMAND命令的手册页并显示

* 中文man需安装包man-pages-zh-CN

---------

man帮助

 * 查看man手册页

   man [章节] keyword

* 列出所有帮助

  man -a keyword

* 搜索man 手册

  man -k keyword 列出所有匹配的界面，使用whatis数据库

* 相当于whatis

  man -f keyword

* 打印man帮助文件的路径

  man -w [章节] keyword

----------

bash的快捷键

	* Ctrl + l 清屏，相当于clear命令
	* Ctrl + o执行当前命令，并重新显示本命令
	* Ctrl + s 阻止屏幕输出，锁定
	* Ctrl + q 允许屏幕输出
	* Ctrl + c 终止命令
	* Ctrl + z 挂起命令
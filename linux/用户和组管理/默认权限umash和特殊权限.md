**新建文件和目录的默认权限**

* umask值 可以用来保留在创建文件权限

* 新建file权限：666-umask

* 如果所得结果某位存在执行

* 新建DIR 权限：777-umask

* 非特权用户umask是002

* root的umask是022

* umask: 查看

* umask # ：设定

  umask 002

* umask -S 模式方式显示

* umask -p 输出可被调用

* 全局设置：/etc/bashrc    

* 用户设置：/.bashrc

  使文件生效，可以 . ~/.bashrc



---

**linux文件系统上 的特殊权限**

* SUID, SGID, Sticky

* 三种常用权限：r, w, x  user, group, other

* 安全上下文

* 前提：进程有属主和属组；文件有属主和属组

  * 任何一个可执行程序文件能不能启动为进程，取决于发起者对程序文件是否拥有执行权限
  * 启动为进程之后，其进程的属主为发起者，进程的数组为发起者所属的组
  * 进程访问文件时的权限，取决于进程的发起者
    * 进程的发起者，同文件的属主：则应用文件属主权限
    * 进程的发起者，属于文件属组；则应用文件属组权限
    * 应用文件“其他”权限

* suid: 4  作用于二进制可执行文件上， 功能：当用户执行此文件，会继承此文件所有者的权限

* sgid: 2   作用域二进制可执行文件上，功能：当用户执行此文件，会继承此文件所属组的权限

  ​				作用在目录上，当用户在此目录新建文件时，此新文件的所属组继承目录的所属组

* sticky：1  作用域目录上， 功能：对目录的文件，只能删除自己的文件

---

**设定文件特定属性**

* chattr +i 不能删除，改名，更改 
* chattr +a 只能追加内容
* lsattr 显示特定属性


















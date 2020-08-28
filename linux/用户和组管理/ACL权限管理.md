**访问控制列表**

* ACL：Access Control List, 实现灵活的权限管理

* 除了文件的所有者，所属组和其他人，可以对更多的用户设置权限

* centos7默认创建xfs和ext4文件系统具有ACL功能

* centos7之前版本，默认手工创建的ext4文件系统无ACL 功能，需手动增加

  ```shell
  tune2fs -o acl /dev/sdb1
  mount -o acl /dev/sdb1 /mnt/test
  ```

* ACL 生效顺序：所有者，自定义用户，自定义组，其他人

* 为多用户或者组的文件和目录赋予访问权限rwx

  * mount -o acl /direcotry
  * getfacl file  | directory
  * setfacl -m u:hl:rwx file | directory
  * setfacl -Rm g:sales:rwX directory
  * setfacl -M file.acl file | directory
  * setfacl -m g:salesgroup:rw file | directory
  * setfacl -x u:hl file | directory
  * setfacl -X file.cal directory

* ACL文件上的group权限是mask值（自定义用户，自定义组，拥有组的最大权限），而非传统的组权限

* getfacl 可看到特殊权限：flags

* 通过ACL赋予目录默认x权限，目录内文件也不会继承x权限

* base ACL 不能删除

* setfacl -k dir 删除默认ACL权限

* setfacl -b file1 清除所有ACL权限

* getfacl file1 | setfacl --set-file=-

* mask只影响除所有者和other的之外的人和组的最大权限

  mask需要与用户的权限进行逻辑与运算后，才能变成有限的权限（Effective Permission）

  用户或组的设置必须存在于mask权限设定范围内才会生效 setfacl -m mask:::rw file

* --set 选项会把原有的ACL项都删除，用新的替代，需要注意的时一定要包含UGO的设置，不能像-m一样知识添加ACL就可以

  * 示例：setfacl --set u::rw,u:wang:rw,g::r,o::- file1

* 备份和恢复ACL

* 主要的文件操作命令cp和mv都支持ACL，知识cp命令需要加上-p参数。但是tar等常见的备份工具是不会保留目录和文件的ACL信息

  * getfacl -R /tmp.dir > acl.txt
  * setfacl -R -b /tmp/dir
  * setfacl -R --set-file=acl.txt /tmp/dir
  * setfacl --restore acl.txt
  * getfacl -R /tmp/dir


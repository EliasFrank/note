**命令模式操作**

* 字符编辑：

  x：删除光标处的字符

  #x：删除光标处起始的#个字符

  xp：交换光标所在处的字符及其后面字符的位置

  ~：转换大小写

  j: 删除当前行后的换行符

* 替换命令（t, replace)

  r: 替换光标所在处的字符

  R：切换成REPLACE模式

* 删除命令：

  d: 删除命令，可结合光标跳转字符，实现范围删除

  d$:删除到行尾

  d^: 删除到非空行首

  d0:删除到行首

  dw:

  de:

  db:

  #COMMAND

  dd: 删除光标所在的行

  ​	#dd： 多行删除

  D：从当前光标位置一直删除到行尾，等同于d$

* 复制命令(y, yank)

  y: 复制，行为相似于d命令

  y$

  y0

  y^

  ye

  yw

  yb

  #COMMAND

  yy： 复制行

  #yy：复制多行

  Y：复制整行

* 粘贴命令（p，paste）

  p：缓冲区存的如果为整行，则粘贴当前光标所在行的下方；否则，则粘贴至当前光标所在处的后面

  p：缓冲区存的如果为整行，则粘贴当前光标所在行的上方；否则，则粘贴至当前光标所在处的前面

* 改变命令：（c， change）

  c：修改后切换成插入模式

* 命令模式--》插入模式

  c$

  c^

  c0

  cb

  ce

  cw

  #COMMAND

  cc: 删除当前行并输入新内容，相当于S

  C：删除当前光标到行尾，并切换成插入模式

* ![1591949016699](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1591949016699.png)

* 100ixxx [ESC] 粘贴“xxx” 100次

* \<start position> \<command>\<end position>

* Command:

  y 复制， d删除  gU 变大写  gu 变小写

  例如0y$ 命令意味着：

  0->先到行头

  y-> 从这里开始拷贝

  $->拷贝到本行最后一个字符

  ​		ye从当前位置拷贝到本单词的最后一个字符

* di“ 光标在” “之间，则删除” “之间的内容

* yi（ 光标在（）之间，则复制（）之间的内容

* vi【 光标在【】之间，则选中【】之间的内容

* dtx 删除字符知道遇见光标之后的第一个x字符

* ytx复制字符知道遇见光标之后的第一个x字符

* 地址定界

  ：start_pos, end_pos

  #具体第#行，例如2表示第2 行

  #，#从左侧#表示起始行，到右侧#表示结尾行

  #，+#从左侧#表示的起始行，加上右侧#表示的行数

  ​					：2，+3 表示2到5行

  . 当前行

  $ 最后一行

     ., $-1 当前行到倒数第二行

  % 全文，相当于1，$

* 查找

  /PATTERN: 从当前光标所在处向文件尾部查找

  ?PATTERN：从当前光标所在处向文件首部查找

  n： 与命令同方面

  N：与命令反方向

* 查找并替换

  * s：在扩展模式下完成查找替换操作

    格式：s/要查找的内容/替换为的内容/修饰符

    要查找的内容：可使用模式

    替换为的内容：不能使用模式，但可以使用\1,\2等后向引用符号；还可以使用“&”引用前面查找时查找到的整个内容

    修饰符：

    ​	i：忽略大小写

    ​	g：全局替换：默认情况下，每一行只替换第一次出现

    ​	gc：全局替换，每次替换前询问

  * 查找替换中的分隔符/可替换为其他字符，例如：

    s@/etc@/var@g

    s#/boot#/#i

* 撤销更改

  * u撤销最近的更改
  * #u撤销之前多次更改
  * U撤销光标落在这行后所有此行的更改
  * 按Ctrl -r重做最后的“撤销”更改
  * . 重复前一个操作
  * n. 重复前一个操作n次

---

**标记和宏（macro）**

* ma 将当前位置标记位a，26个字母均可做标记
* 'a 跳转到a标记的位置；实用的文档内标记方法，文档中跳跃编辑时很有用
* qa 录制宏a，a为宏的名称
* q  停止录制宏
* @a  执行宏a
* @@ 重新执行上次执行的宏

---

**编辑二进制文件**

* 以二进制方式打开文件

  vim -b binaryfile

* 扩展命令模式下，利用xxd命令转换为可读的十六进制

  ：%！xxd

* 编辑二进制文件

* 扩展命令模式下，利用xxd命令转换回二进制

  ：%!xxd -r

* 保存退出

---

**可视化模式**

* 允许选择的文本块

  v面向字符

  V面向行

  ctrl -v 面向块

* 可视化键可用于与移动键结合使用：

  w)}箭头等

* 突出显示的文字可被删除，复制，变更，过滤，搜索，替换等

---

**多文件模式**

* vim file1 file2 file3...

  :next 下一个

  ：prev前一个

  ：first 第一个

  ：last 最后一个

  ： wall 保存所有

  ： qall 退出所有

  ： wqall

---

**使用多个“窗口”**

* 多文件分割
  vim -o | -O file1 file2 ...

  -o：水平分割

  -O：垂直分割

  在窗口间切换:ctrl +w,Arrow

* 单文件窗口分割：

   ctrl + w，s：split，水平分割

  crtl + w，v：vertical，垂直分割

  ctrl + w，q：取消相邻窗口

  ctrl+w，o：取消全部窗口

  ：wqall 退出

---

**定制vim的工作特性**

* 配置文件：永久有效

  全局：/etc/vimrc

  个人：~/.vimrc

* 扩展模式：当前vim进程有效

* 行号：

  显示：set number，简写为set number

  取消显示：set nonumber，简写为set nonu

* 忽略字符的大小写

  启动：set ic

  不忽略：set noic

* 自动缩进

  启用：set ai

  禁用：set noai

* 文件格式

  启用windows格式：set fileformat=dos

  启用Unix格式：set fileformat=unix

  简写：set ff=dos|unix

* 设置文本宽度

  set textwidth = 65(vim only)

  set wrapmargin = 15

* 设置光标所在行的标识线

  启用：set c1ursorline，简写cul

  禁用：set no cursorline

* 复制保留格式

  启用：set paste

  禁用：set nopaste

* 智能缩进

  启用：smartindent 简写 set si

  禁用：set nosi

* 高亮搜索

  启用：set hlsearch

  禁用：set nohlsearch

* 语法高亮

  启用：syntax on

  禁用：syntax off

* 显示tab和换行符 ^| 和 $ 显示

  启用：set list

  禁用：set nolist

---

**了解更多**

* set帮助

  \>：help option-list

  \>：set or ：set all

* vi/vim内置帮助

  ：help

  ：help topic

  use：q to exit help

* vimtutor

---

![1592042102578](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1592042102578.png)

![1592042140029](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1592042140029.png)

![1592042170925](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1592042170925.png)
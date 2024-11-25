# 前言:compose && link
C语言: file.c->file.o(Unix)/file.obj(windows)->file.exe
前一个过程即编译*compose*,后一个过程即链接*link*
- compose涉及语言的转换,要求.c语法正确,变量和函数声明正确.
- link链接各个.o的函数和全局变量,需要找到有且只有一个同名函数的实现
>[!info] compose和link命令分别是:
>	gcc -c file1.o file1.c
>	gcc -o file file1.o file2.o file3.o
  
# makefile规则
make执行时需要一个makefile
makefile规则:
	 target ... : prerequisites ...
	 recipe
- target:可以是个.o,.exe,label
- prerequisites:依赖项
- recipe:任意shell命令
功能表述:依赖项中任意一个文件改变,recipe执行
>[!info] clean作为target无依赖项
# makefile工作流
1. make 会在当前目录下找名字叫“Makefile”或“makefile”的文件。 
2. 找到“可执行”的文件，并把这个文件作为最先执行的target。
3. 如果 可执行文件不存在，或是其依赖项的 date 要比 edit 这个文件新， 就recipe
4.  make 会在当前文件中可执行文件的依赖项作为target,，如果找到则再根据上一条规则生成 .o 文件。（递归）
5. 于是 make 会生成 .o 文件，然后再用 .o 文件生成 make 的终极任务，也就是可执行文件。
由上面的工作原理可知,makefile只靠起始点(可执行文件)和其依赖项,通过递归的方式构建项目.
因此,没有任何依赖项,也不是make起始点的clean在make过程中不会执行
#  makefile简化写法
- 前提:compose过程一一对应,且.o名字与.c相同
- 自动推导:makefile遍历到一个whatever.o target,就可以自动推导出它的一个依赖项whatever.c,并且自动推导出recipe:cc -c whatever.c
- 这两项因此是可省略的,我们因此简化了makefile的写法
- **注意:如果我们这么写了,实际上启用了隐式规则,记得写一行 .PHONY : clean来表示clean是个伪文件**
# makefile的约定习惯
- 最开头的规则单元会被make当成默认目标
- 必须要写clean,以便维持文件清洁
- 开头写可执行文件规则
- 结尾写clean
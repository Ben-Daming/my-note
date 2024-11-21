# 本note整理了一些好用的基础命令
#shell [[MS L2]] 
*ls只能査指定目录的文件.如果我想査一个目录下所有包括子目录中的wps文件,怎么办?
- find是更强大的命令
	使用find . -name "\*.wps",就可以查看当前目录底下,包括所有子目录中,所有的wps文件
	find之后也可以紧接着execute一些命令,比如我想找到home目录下所有wps文件并删除,就用
	find home -name "\*.wps" -exec rm {} \
	这样就删除了home下的所有文件
- 新命令fd:更方便的find.
	什么参数都没有时,相当于find(有更简洁方便的显示),查看当前目录下属所有文件
	fd \<字符串\> 搜索包含某串字符的文件名
	fd -e md 搜索所有特定格式(markdown)的文件
	-e选项可以与搜索模式一起使用
	fd -e md ME
	输出README.md
- 每一行中以;分隔,就可连续执行两个命令了!
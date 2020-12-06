# MAKE

make是一种规则，主要用于方便C项目的管理，基本的规则如果下。

```makefile
<target> : <prerequisites>
[tab]	<commands>
```

Target 必须存在，但是prerequisites以及commands都是可选的存在。



make命令的含义：构建目标`target`前，需要什么前提条件`prerequisites`，执行什么样的命令`commands`。

e.g.：

```makefile
a.txt: b.txt c.txt
	cat b.txt c.txt > a.txt
```

构建a.txt, 前提是需要b.txt, c.txt存在， 并且执行命令`cat b.txt c.txt > a.txt`来构建a.txt





## taregt

一个目标对应一个构建规则，一般目标为文件名。也可以是命令名：clean 伪目标。





## prerequisites

是否构建目标的标准： 如果有有一个前置文件不存在，或者有一个前置文件出现更新，就需要重新构建新的目标。



## 其他

- .PHONY: 表示伪目标，不需要判断当前目录下是否存在该文件。

- $()用于表示变量， $$()用于引用变量。

- CC内置表达C的编译器
---
title: Markfile语法详解
date: 2022-01-05 15:50
categories: 
- markfile
---
# Markfile语法

## **规则的格式：**

	<target>  :  <prerequisites>
	[tab]    <commands>
	
**`<target>`**: 目标
**`<prerequisites>`**: 前置条件，或者通俗的称之为依赖。
**`<commands>`**: 命令，由单独各行或多行<font style = "background : rgb(200,100,100)">shell命令</font>组成，如果某规则有n个shell命令行构成，而相互之间没有用';'和'\\'连接起来的话，就是相互之间没有关联的shell命令，相互之间也不能变量共享。

**多行命令的格式**：
- <font color = red>.ONESHELL:</font> 
- 在同一行用';'分割
- 在不同行用';\\'分割

*每条规则就明确两件事：构建目标的前置条件是什么，以及如何构建。*
因此其中"目标"是必需的，不可省略；"前置条件"和"命令"都是可选的，但是两者之中必须至少存在一个。

## 何为伪目标？
伪目标不生成文件，只是充当一个标签的作用。但可以其本身可以充当依赖。
声明all、clean 和 target 这三个伪目标:  **`.PHONY:  all clean target`**


## 静态模式
makefile中，一个规则中可以有多个目标，规则所定义的命令对所有的目标有效。一个具有多目标的规则相当于多个规则。使用多目标可以使makefile文件变得简洁。

静态模式规则是这样一个规则：规则存在多个目标，并且不同的目标可以根据目标文件的名字来自动构造出依赖文件。

静态模式可以更加容易地定义多目标的规则，可以让我们的规则变得更加的有弹性和灵活。

语法：

	 <targets ...>: <target-pattern>: <prereq-patterns ...>
	 [tab]    <commands> 

**`<targets>`**: 定义了一系列的目标文件，可以有通配符。是目标的一个集合。
**`<target-parrtern>`**: 是指明了targets的模式，也就是的目标集模式。
**`<prereq-parrterns>`**: 是目标的依赖模式，它对target-parrtern形成的模式再进行一次依赖目标的定义。

## 语法
- <font color = "#5a93a2"># </font>: 表示注释

- <font color = "#5a93a2">赋值 </font>: 可以有空格 四种赋值运算符( = 在执行时扩展，允许递归扩展、:= 在定义时扩展、?= 只有变量为空时才赋值、+= 将值追加到变量的尾端)

- <font color = "#5a93a2">$</font> : 展开makefile中定义的变量，对变量的扩展高优先

- <font color = "#5a93a2">\$\$</font> : 展开makefile命令行中的shell变量

- <font color = "#5a93a2">@</font> : 取消回显echoing，通常配合echo使用，@echo

- <font color = "#5a93a2">-</font> : 忽略删除创建文件时的出错。比如 rm 一个并不存在时的文件时，不会因为出错而终止。也可以通过 rm -f 的方式避免。

- <font color = "#5a93a2">模式匹配</font> ，模式字符为%，指匹配任何非空字符串。静态模式规则，和

- <font color = "#5a93a2">内置变量</font>，如$(cc): 代表环境变量中的编译器、 $(MAKE): 代表环境变量中的make版本

- <font color = "#5a93a2">自动变量</font> :

	- $@: 代指当前目标

	- $<: 指代第一个前置条件

	- $?: 指代比目标更新的所有前置条件

	- $^: 指代所有前置条件

	- $*: 指代匹配符 % 匹配的部分

- <font color = "#5a93a2">$(@D) 和 $(@F)</font> : 分别指向 \$@ 的目录名和文件名。比如，\$@是 src/input.c，那么\$(@D) 的值为 src，\$(@F) 的值为 input.c。$(<D) 和 $(<F) : 同上，分别指向 $< 的目录名和文件名
																 

## 常见函数
[大佬整理]([https://zhuanlan.zhihu.com/p/362917196](https://zhuanlan.zhihu.com/p/362917196))

通配符wildcard，Makefile的通配符与Bash的一致，主要由  * 代表匹配0或者任意个字符、 ? 代表匹配任意一个字符、 [] 代表。
通配符的转义：\\\*...
即使没有.o文件，也强制使用通配符: OBJ = ${wildcard \*.o}

### subst
函数用来文本替换，格式如下。

```$(subst from,to,text)```

### patsubst
patsubst 函数用于模式匹配的替换，格式如下。

```$(patsubst pattern,replacement,text)```

### notdir
说明：去除文件名中的路径信息 示例：

```SRC = ( notdir ./src/a.c )```

### addsuffix
加后缀函数—addsuffix。 语法：

```$(addsuffix SUFFIX,NAMES…)```

### foreach
foreach函数和别的函数非常的不一样。因为这个函数是用来做循环用的 语法是：

```$(foreach <var>,<list>,<text> )```

这个函数的意思是，把参数中的单词逐一取出放到参数所指定的变量中，然后再执行所包含的表达式。

### filter 与 filter-out
```$(filter pattern…,text) 和 $(filter-out pattern…,text)```

函数名称 ：反过滤函数—filter-out “filter-out”函数也可以用来去除一个变量中的某些字符串（实现和“filter”函数相反）

```$(filter-out PATTERN…,TEXT) ```

### strip
```$(strip string)```
文本精简，将文本中前导和尾随的空格删除，在文本中存在多个连续空格时，使用一个空格替换。

### findstring
```$(findstring find,in) ```
在主文本中寻找子文本
顾名思义，这是文本查找函数，在in中寻找是否有find文本。参数：
find : 子文本
in ： 主文本
如果in中存在find，返回find，否则返回""(空文本)。  
  
### call
```$(call variable,param,param,…)```
call函数在makefile中是一个特殊的函数，因为它的作用就是创建一个新的函数。你可以使用call函数创建各种实现复杂功能的函数。

## 判断与循环
|关键词|功能|
|-|-|
|ifeq|判断参数是否相等，相等为true，不相等为false|
|ifnef|判断参数是否不相等，不相等为true，相等为false|
|ifdef|判断参数是否有值，有值为true，没有值为false|
|ifndef|判断参数是否没有值，没有值为true，有值为false|

使用方式如下：
```
ifeq (ARG1,ARG2)
ifeq "ARG1" "ARG2"
ifeq 'ARG1' 'ARG2'
ifeq "ARG1" 'ARG2'
ifeq 'ARG1' "ARG2"
```
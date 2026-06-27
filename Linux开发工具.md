# 一、软件包管理器



## 1.1 Linux软件包



### 1.1.1 什么是软件包？



在 Linux 中，软件不是像 Windows 那样装一个 .exe 完事。而是把软件拆成一个包（Package），里面包含了：

> 📄 程序文件（二进制、库文件）
> 📄 配置文件 
> 📄 文档和手册 
> 📄 安装/卸载脚本 
> 📄 依赖关系说明
> 📄 版权和描述信息     



### 1.1.2 两大包格式



① DEB 包（.deb） 用于 Debian / Ubuntu 系列



② RPM 包（.rpm）用于 CentOS / RHEL / Fedora 系列



### 1.1.3 依赖问题



安装 A → A 需要 B → B 需要 C → ...

  - 底层工具（dpkg / rpm）：只装你指定的包，依赖要手动一个个装，很痛苦
  - 高层工具（apt / yum）：自动分析并安装所有依赖，这就是为什么推荐用它们



### 1.1.4 软件源



核心概念：软件源就是一个在线服务器，存放了大量预编译好的软件包。



```bash
# Ubuntu 软件源配置文件
/etc/apt/sources.list

# CentOS 软件源配置目录
/etc/yum.repos.d/
```





## 1.2  包管理器



### **1.2.1 什么是包管理器？**



包管理器就是帮你自动处理软件安装、升级、卸载和依赖关系的工具，可以理解为一个"应用商店"的命令行版本。



### 1.2.2 两大包管理器体系



#### A、Ubuntu



|   工具   |                     说明                     |
| :------: | :------------------------------------------: |
| **dpkg** | **底层工具，直接操作 .deb 文件，不处理依赖** |
| **apt**  | **高层工具，自动解决依赖，从软件源下载安装** |





**apt 常用操作（推荐）**



```bash
常用命令：

# apt 常用操作（推荐）
sudo apt update              # 更新软件源索引

sudo apt upgrade             # 升级所有已安装软件

sudo apt install 软件包       # 安装软件

sudo apt remove 软件包        # 卸载（保留配置文件）

sudo apt purge 软件包          # 卸载（连配置文件一起删）

sudo apt autoremove          # 清理不再需要的依赖
```



  **dpkg 底层操作**



```bash
常用命令：

sudo dpkg -i package.deb     # 安装本地 .deb 文件

sudo dpkg -r package_name    # 卸载

dpkg -l                      # 列出已安装的包
```





#### B、CentOS



|  工具   |                       说明                       |
| :-----: | :----------------------------------------------: |
| **rpm** |   **底层工具，直接操作 .rpm 文件，不处理依赖**   |
| **yum** |  **高层工具（CentOS 7 及以前），自动解决依赖**   |
| **dnf** | **yum 的升级版（CentOS 8+ / Fedora），更快更好** |





 **yum/dnf 常用操作（推荐）**



```bash
常用命令：


sudo yum install nginx       # 安装

sudo yum remove nginx        # 卸载

sudo yum update              # 更新所有软件

sudo yum search nginx        # 搜索

sudo yum info nginx          # 查看包信息
```





**rpm 底层操作**



```bash
sudo rpm -ivh package.rpm    # 安装本地 .rpm 文件

sudo rpm -e package_name     # 卸载

rpm -qa                      # 列出已安装的包
```







# 二、编辑器Vim



## 2.1 vim 模式





vim模式简介图：

![image-20260531165606340](C:\Users\30398\AppData\Roaming\Typora\typora-user-images\image-20260531165606340.png)



### 2.1.1 Normal 模式（默认）



**进入Normal模式：**启动 Vim 后默认进入此模式



**Normal模式的功能：**用于移动光标、删除、复制、粘贴等操作



**返回Normal模式**：按 Esc 随时回到此模式，状态栏显示 -- Normal --





### 2.1.2 Insert 模式



**Insert模式的功能：**用于输入文本



**进入Insert模式：**



①按 i 进入：在光标前插入



②按 I 进入：在行首插入



③按 a 进入：在光标后插入



④按 A 进入：在行尾插入



⑤按 o 进入：在下方新建一行并插入



⑥按 O 进入：在上方新建一行并插入



**退出Insert模式：**输入Esc退出 Insert 模式，回到 Normal 模式，状态栏显示 -- Normal --



### 2.1.3 Command 模式



**进入Command（命令行）模式：**按  **:**  （shift + ;）进入，用于执行命令，状态栏显示 -- Command --



**退出命令行模式**：输入Esc退出Command 模式，回到 Normal 模式，状态栏显示 -- Normal --



### 2.1.4 Visual 模式（选择文本）



**进入Visual 模式**：



①按 v 进入：字符可视模式 ，可以逐字符选择，状态栏显示  -- VISUAL --  



②按V进入：行可视模式 ，逐行选择，状态栏显示 -- VISUAL LINE --



③按ctrl + v 进入：块可视模式，矩形块选择，状态栏显示 -- VISUAL BLOCK --



**Visual 模式的功能**：Visual 模式让你先选中文本，再决定对它做什么，比普通模式的"操作符+动作"更直观。



**退出 Visual 模式**：输入Esc退出 Visual 模式，回到 Normal模式，状态栏显示 -- Normal --



### 2.1.5 Replace 模式



**Repalce 模式的功能：** Replace 模式让你输入的每个字符都会覆盖（替换）光标下的字符，类似 Windows 下按 Insert 键的效果。



**进入Replace模式：**输入R进入Replace 模式，状态栏显示 -- REPLACE --



**退出Replace模式：**输入Esc退出 Replace 模式，回到 Normal 模式，状态栏显示 -- Normal --





## 2.2 vim 指令操作 



### 2.2.1 启动与退出



启动操作：`vim 文件名`



```bash
vim 文件名         # 打开文件（不存在则新建）

vim +5 文件名      # 打开文件并跳到第5行

vim +/关键词 文件名 # 打开文件并跳到第一个匹配处
```



退出操作：（在命令行模式下）



```bash
w    			   #保存

q                  #退出

wq / x             #保存并退出

q!                 #强制退出（不保存）

ZZ                 #普通模式下，等同于 wq

ZQ                 #普通模式下，等同于 q!
```





### 2.2.2 光标移动



#### A. 基本移动



```bash
光标向左移动： h ←  

光标向下移动： j ↓ 

光标向上移动： k ↑ 

光标向右移动： l →
```





#### B. 单词级移动 



```bash
w        #跳到下一个单词开头     

示例文本：Hello world

初始位置：[H]ello world → 按 w → 最终位置： Hello [w]orld（从 H 到 w）

示例文本：Hello, world（含标点）

初始位置：[H]ello, world → 按 w →最终位置： Hello[,] world（逗号作为独立单词）


W     	 #跳到下一个空格分隔的单词开头 

示例文本：foo.bar baz

初始位置：[f]oo.bar baz → 按 W → 最终位置： foo.bar [b]az（跳过整个 foo.bar）

示例文本：Hello, world

初始位置：[H]ello, world → 按 W → 最终位置：Hello, [w]orld（Hello, 整体被视为一个单词）


b     	 #跳到上一个单词开头，与w的作用相反          

示例文本：foo bar

初始位置：foo [b]ar → 按 b → 最终位置： [f]oo bar（回到 foo 开头）

初始位置：fo[o] bar → 按 b → 最终位置： [f]oo bar（直接跳到当前单词开头，再按则上一个）


B     	 #跳到上一个空格分隔的单词开头，与W的作用相反 

示例文本：foo.bar baz

初始位置：foo.bar [b]az → 按 B → 最终位置：[f]oo.bar baz（直接跳到行首，因为 foo.bar 是一个 WORD）


示例文本：hello world（多个空格）

初始位置：hello [w]orld → 按 B → 最终位置： [h]ello world


e   	 #跳到当前/下一个单词末尾     

示例文本：foo bar

光标在开头：[f]oo bar → 按 e → fo[o] bar（foo 的末尾）

光标在末尾：foo[ ]bar（空格上）→ 按 e → foo ba[r]（bar 的末尾，跳过空格）


示例文本：foo, bar

光标在 foo 的 o：fo[o], bar → 按 e → foo[,] bar（逗号作为单词，末尾即逗号本身）


E    	 #移动到当前或下一个空格分隔的单词的末尾       

示例文本：foo.bar baz

光标在开头：[f]oo.bar baz → 按 E → foo.ba[r] baz（foo.bar 的末尾）

光标在 foo.bar 的末尾 r：foo.ba[r] baz → 按 E → foo.bar ba[z]（baz 的末尾）


示例文本：Hello, world

光标在 H：[H]ello, world → 按 E → Hello[,] world（Hello, 的末尾，逗号）

再按 E → Hello, worl[d]


注：单词包含：字母、数字、下划线组成的序列，其中标点符号视为独立单词
```





#### C. 行内移动



```bash
0 			#将光标移动到行首（第一个字符）


^           #将光标移动到行首（第一个非空字符）


$           #将光标移动到行尾（最后一个字符）
```



#### D.文件级移动



```bash
gg					 #将光标移动到文件的最开头位置


shift+g / G          #将光标移动到文件的最末尾位置


{n}G 或 :{n}         #将光标移动到文件的指定行位置


Ctrl+f				 #向下翻一页


Ctrl+b				 #向上翻一页


Ctrl+d				 #向下翻半页


Ctrl+u				 #向上翻半页
```





### 2.2.3 编辑操作



#### A. 删除操作 -d



```txt
dw — 从光标当前位置，删除直到下一个单词开头


示例1：

文本：Hello world

光标在 H：[H]ello world → 执行命令dw → 删除 "Hello " → 结果 [w]orld


示例2：

文本：Hello world

光标在 l：Hel[l]o world → 执行命令dw → 删除 "lo " → 结果 Helworld


#注意：如果光标在单词中间，dw 只删除该单词剩余部分 + 空格。
```





```bash
d$ – 从光标位置（包括光标下的字符）删除到行尾。


示例：

文本：This is a line.

光标在 i 处：Th[i]s is a line. → 执行命令d$ → 删除 "is is a line." → 结果 Th
```





```bash
dd – 删除光标所在的整行（包括换行符），之后下一行上移。

示例：

文本两行：
Line one
Line two

光标在第一行任意位置 → 执行命令dd → 第一行消失，光标移到第二行开头。
```



> **数字用法 {n} dd**：例如，`3dd` 删除当前行及下面 2 行（共 3 行）。





#### B. 修改操作  -c



```bash
cw – 从光标位置删除到下一个单词开头，然后进入插入模式。


示例：

文本：Hello world

光标在 H：[H]ello world → 执行命令cw → 删除 "Hello" -> 结果 wordld
```



```bash
shift+c / c$ – 删除从光标到行尾，并进入插入模式。


示例：

文本：This is a test

光标在 i 处：Th[i]s is a test → 执行命令c$ 或 shift+c → 删除 "is is a test"，光标在 i 后，输入 "at" → 结果 That
```



```bash
cc – 删除整行（包括换行符），并在该行原来位置进入插入模式（相当于 dd + i，但更高效）

示例：

文本：old line

光标任意位置 → 执行命令 cc → 整行清空，光标在行首，输入 "new line" → 结果 new line
```



#### C. 复制操作 -y



```bash
yy – 复制整行

示例：

文本：hello linux

光标任意位置 → 执行命令 cc → 剪切板中包含文本 "hello linux"
```



#### D. 粘贴操作 -p



```bash
p – 粘贴复制的整行

示例：

文本：hello linux

光标任意位置 → 执行命令 cc → 剪切板中包含文本 "hello linux" -> 执行命令p -> 光标下一行出现文本 hello linux
```



#### E. 撤销/重做



```bash
u – 撤销上一次修改,可连续按，逐步撤销。


ctrl+r - 重做（撤销的撤销）


#1. u 只撤销修改（包括输入、删除、替换等），不撤销光标移动。
#2. . 命令重复的是上一次修改，不受 u 影响。
```



#### F. 重复



```bash
.  - 重复上一次的修改操作
```



#### G. 缩进



```bash
>>  - 增加缩进

<<  - 减少缩进
```



#### H. 大小写



```bash
~	   反转光标字符，将字符从大写转为小写，从小写转为大写	

gUiw	单词转大写	

guiw	单词转小写
```



# 三、编译器gcc/g++ （待完成）



## 3.1 背景知识



## 3.2 gcc编译选项



### 3.2.1 预处理



### 3.2.2 编译



### 3.2.3 汇编



### 3.2.4 链接





## 3.3 静态库和动态库



### 3.3.1 静态库



### 3.3.2 动态库





## 3.4 静态链接和动态链接



### 3.4.1 静态链接



### 3.4.2 动态链接





# 四、自动化构建make/Makefile





## 4.1 make 和 Makefile 的详解



**make的本质**： 是一个自动化构建工具，根据文件依赖关系和时间戳，决定哪些命令需要执行。



> **核心功能**：
>
> - 自动判断哪些文件需要重新编译
> - 只执行必要的命令
> - 避免重复工作





**为什么需要make自动化工具？ ** 



最简单的例子：传统编译代码在编译多个文件时过于麻烦。



```bash
$ gcc -c main.c
$ gcc -c utils.c
$ gcc -o app main.o utils.o
$ ./app


#传统手动编译和链接
#从 main.c  -> main.o
#从 utils.c -> utils.o
#将 main.o 和 utils.o 链接成为 app.exe
```





### 4.1.1 Makefile 基础



Makefile的本质：makefile是⼀个文件，用来告诉 make 工具 “用什么东西(依赖)、通过什么操作(命令)、生成什么文件(依赖)”。



> Makefile文件描述了：
>
> 目标（Target）：要生成的文件
>
> 依赖（Prerequisites）：目标依赖的文件
>
> 命令（Commands）：生成目标的命令



```makefile
#基本语法格式：

目标:依赖
	命令


#相关术语的解释：

1.目标：想要生成的文件名（例如一个可执行文件 app，或者一个中间文件 main.o）
       可以是动作的名称（即“伪目标”，比如 clean 用于清理临时文件）。


2.依赖：这是生成“目标”所必须依赖的源文件或其他目标。

例如：你要生成可执行文件.exe，你的依赖就是 .o 或 .c 源文件。 

例如：如果依赖项本身也是另一个目标，make 会顺藤摸瓜先去处理那个依赖。

注：一个目标可以有多个依赖，中间用空格隔开。


3.命令：为了把“依赖”转化成“目标”而需要执行的 Shell 命令

例如：编译命令 gcc -o app main.c

注1：一个目标下可以有多行命令

注2：每行命令的开头必须且只能是一个 Tab 键（制表符），绝对不能用空格代替。
```





具体示例：编译一个 C 程序



```makefile
# 规则 1：生成最终可执行文件 hello
hello: hello.o
	gcc -o hello hello.o

# 规则 2：生成中间文件 hello.o
hello.o: hello.c
	gcc -c hello.c
```





### 4.1.2 make的执行逻辑



`make`的执行逻辑：是一个**先自顶向下找依赖，再自底向上搞建设**”的过程。





#### A. 阶段一：make 命令，定终极目标



**一、`make` 会在当前目录下寻找名为 `Makefile` 或 `makefile` 的文件。**



① make默认查找

```bash
make 按以下顺序查找配置文件：

1. GNUmakefile    ← GNU 特有，不推荐
2. makefile       ← 小写，常见
3. Makefile       ← 首字母大写，最常见

找到第一个就停止，不再继续查找。
```



② make指定查找

```bash
指定 Makefile 文件

-f 参数

$ make -f MyMakefile
$ make -f config.mk
$ make -f build/Makefile
```



③总结：make 查找 Makefile 的流程

```bash
检查make是否有 -f 参数
     │
     ├─ 有 → 使用指定文件
     │
     └─ 无 → 按顺序查找
                │
                ├─ GNUmakefile
                ├─ makefile
                └─ Makefile
                    │
                    ├─ 找到 → 使用
                    │
                    └─ 未找到 → 报错
```





**二、若找到后，如果你只输入了 `make`（没有带具体目标名），把遇到的第一个“明确的目标”作为默认目标（终极目标）**



make 的默认原则：它会从上到下读取 Makefile，把遇到的第一个“明确的目标”作为默认目标（终极目标）。



代码示例：

```makefile
# 此时敲下 make，会执行 test，因为它排在第一
test:
	@echo "Running tests..."

build: main.o
	gcc -o app main.o
```



注意：make 在找“第一个目标”时，会自动跳过以下这些“非实体”或“模板”定义

> ①**以点 `.` 开头的特殊目标**：比如 `.PHONY`、`.SECONDEXPANSION`。它们是配置项，不是执行入口。
>
> ②**模式规则 (Pattern Rules)**：比如 `%.o: %.c`。这只是个通用公式，不代表具体要生成哪个文件。
>
> ③**变量定义**：比如 `CC = gcc`。





**三、如果指定输入了 `make 目标名  `，它就会直接把 `目标名` 当作终极目标。**



示例：make 目标名

```bash
$ make              # 使用默认目标
$ make all          # 使用指定目标 "all"
$ make clean        # 使用指定目标 "clean"
$ make test install # 使用多个目标
```





#### B.阶段二：自顶向下，找依赖



在这个阶段，Make 绝对不会执行任何一行真正的 Shell 命令（比如 `gcc` 或 `rm`），**它只做一件事：在内存中画出一张严密的“依赖关系树（DAG，有向无环图）”。**



假设你有这样一个 Makefile：

```bash
# 规则 1
app: main.o utils.o
	gcc -o app main.o utils.o

# 规则 2
main.o: main.c 
	gcc -c main.c

# 规则 3
utils.o: utils.c 
	gcc -c utils.c
```





当你输入 `make app` 时，“推导过程”如下：



**1.锁定根节点：**寻找终极目标。

​	make 首先把目标锁定为你指定的 `app`。它在文件里找到了【规则 1】，发现想要得到 `app`，必须先拥有 `main.o` 和 `utils.o`。

于是它在心里画下第一笔：`app` 依赖 `main.o` 和 `utils.o`。此时它不知道这两个东西存不存在，它只管记下需求。



**2.递归探寻左分支：**解析 main.o。

​	make 顺着刚刚的线索，把 `main.o` 当作新的目标去搜索整个 Makefile。它找到了【规则 2】，发现 `main.o` 依赖 `main.c` ，线索继续往下延伸。



**3.触底（找到叶子节点）：**探底物理文件。

​	make 把 `main.c` 当作目标去搜索。它翻遍了整个 Makefile 也没找到生成 `main.c` 的规则，这时候 make 会去看一眼你的目录：发现当前目录下确实有这源文件，停止向下寻找。



**4.回溯探寻右分支：**解析 utils.o。

​	左边理清后，make 退回到最初的目标，去查另一个人质 `utils.o`，它找到【规则 3】，发现 `utils.o` 依赖 `utils.c` ，同理make 把 `utils.c` 当作目标去搜索，最后在目录中找到源文件，停止向下寻找。



经此时 Make 虽然什么文件都没编译，但在它的内存里，已经建好了一棵这样的树：

```Plaintext
       [ app ]
       /     \
      /       \
 [main.o]   [utils.o]
   /            \
  /              \
main.c          utils.c
```







#### C.阶段三：自底向上，看时间戳办事 



```
make执行逻辑

核心规则：从依赖树的底部开始，比较时间戳，只重建"过时"的目标。

1. 执行顺序
    └─ 从叶子节点开始，向根节点推进

2. 判断规则
    ├─ 伪目标 → 总是执行
    ├─ 文件不存在 → 执行
    ├─ 依赖更新 → 执行
    └─ 依赖未更新 → 跳过

3. 时间戳比较
	└─ 根据 Modify time 的时间前后决定（修改时间）
```





 实验演示：



1.编写makefile中的内容



```makefile
makefile文件内容：

app: main.o utils.o
	gcc -o app main.o utils.o

main.o: main.c
	gcc -c main.c

utils.o: utils.c
	gcc -c utils.c
```



2.makefile的依赖图



```
依赖图：
       [ app ]
       /     \
      /       \
 [main.o]   [utils.o]
   /            \
  /              \
main.c          utils.c
```



3.xhsell执行指令



```bash
xshell执行指令：

# 首次构建
$ make
gcc -c main.c
gcc -c utils.c
gcc -o app main.o utils.o


# 再次执行
$ make
make: 'app' is up to date.

# 修改 main.c
$ touch main.c
$ make
gcc -c main.c
gcc -o app main.o utils.o


#时间戳比较

main.c (10:05) > main.o (10:03)?
→ 是 → 重新编译 main.o

utils.c (10:01) < utils.o (10:03)?
→ 否 → 跳过 utils.o

main.o (10:06) > app (10:04)?
→ 是 → 重新链接 app
```





### 4.1.3 变量系统



#### A. 变量定义



递归展开变量  `=`



```bash
CC = gcc
CFLAGS = -Wall -O2
CMD = $(CC) $(CFLAGS)    # CMD 不会立即展开，每次使用时才展开

# 此时如果后面修改了 CC，CMD 会跟着变
CC = clang
# 现在 $(CMD) 展开为 clang -Wall -O2

特点：延迟求值，引用时才展开。缺点是可以被后续赋值覆盖，且不能自引用（会导致无限递归）。
```



简单展开变量 `:=`



```bash
CC := gcc
CFLAGS := -Wall -O2
CMD := $(CC) $(CFLAGS)   # 立即展开，CMD = "gcc -Wall -O2"

CC = clang
# 此时 $(CMD) 仍然是 "gcc -Wall -O2"，不会变

特点：立即求值（immediate evaluation），定义时就确定值。推荐大多数场景使用，行为更可预测。
```



自引用技巧（仅限 :=）



```bash
# 常见场景：往已有变量前面追加内容
CFLAGS := -Wall
CFLAGS := -Werror $(CFLAGS)    # CFLAGS = "-Werror -Wall"

# 用 = 的话会无限递归：
BAD = -o
BAD = $(BAD) -x               #  错误
```





示例演示：变量的使用



```makefile
CC = gcc
CFLAGS = -Wall -g
OBJS = main.o utils.o
TARGET = app

$(TARGET): $(OBJS)
	$(CC) $(CFLAGS) -o $(TARGET) $(OBJS)

main.o: main.c
	$(CC) $(CFLAGS) -c main.c

utils.o: utils.c
	$(CC) $(CFLAGS) -c utils.c
```



> 引用变量时，必须使用 `$()` 或 `${}`。
>
> 如果变量名只有一个字符（比如 `$@`），可以不加括号





#### B. 自动变量



`$@` ： 目标文件名



```bash
$@ — 目标文件名

规则中 : 左侧的目标。

app: main.o utils.o
	gcc main.o utils.o -o $@        #等价于 gcc main.o utils.o -o app   $@ 展开为 app
```



`$<` ： 第一个依赖文件



```makefile
$< — 第一个依赖文件

main.o: main.c
	gcc -c $< -o $@  	 #等价于 gcc -c main.c -o main.o

#     $< = main.c
#     $@ = main.o
```



`$^` ：所有依赖文件（自动去重）



```makefile
$^ — 所有依赖文件（自动去重）

app: main.o utils.o main.o
	echo $^		#等价于 echo main.o utils.o
    
#输出: main.o utils.o  （重复的 main.o 被去掉）
```



`$+` ：所有依赖文件（保留重复，顺序不变）



```makefile
$+ — 所有依赖文件（保留重复，顺序不变）

app: main.o utils.o main.o
	echo $+				#等价于 echo main.o utils.o main.o
	
#输出: main.o utils.o main.o
```



`$*` ：目标的词干（stem）



```bash
$* — 目标的词干（stem）

匹配 % 模式时，% 匹配到的部分：

%.o: %.c
	echo $*     #如果目标是 main.o，则 $* = main
```



`$?` ：比目标更新的依赖



```makefile
$? — 比目标更新的依赖

只列出修改时间晚于目标的依赖文件，常用于增量编译判断：

lib.a: a.o b.o c.o
	ar rcs $@ $?  
	
# 只打包有更新的 .o 文件
```



#### C. 变量替换 （待完成）



### 4.1.4 模式规则



#### A. 什么是模式规则



核心理解：使用 % 作为通配符，定义一类文件的通用构建方式，避免为每个文件重复写规则。





普通规则：一条规则只处理一个文件



```bash
# 普通规则：

main.o: main.c
	gcc -c main.c -o main.o
	
player.o: player.c
	gcc -c player.c -o player.o

enemy.o: enemy.c
	gcc -c enemy.c -o enemy.o
```



模式规则：一条规则处理所有 .c → .o 的转换



```makefile
# 模式规则：一条规则处理所有 .c → .o 的转换

%.o: %.c
	gcc -c $< -o $@


#注：% 匹配任意非空字符串，称为词干（stem）。目标和依赖中的 % 匹配相同的词干。

#例如：main.o: main.c 
```





#### B. 基本语法



```bash
目标模式: 依赖模式
        命令
```



```bash
示例

%.o: %.c
	gcc -c $< -o $@

当 make 需要构建 main.o 时：
1. 匹配目标模式 %.o，%匹配对应的词干 * = main
2. 代入依赖模式 %.c，得到依赖 main.c
3. 执行命令：gcc -c main.c -o main.o
```



#### C. 模式规则 vs 普通规则



```makefile
普通规则：每加一个文件就要写一条规则

main.o: main.c
	gcc -c main.c -o main.o
utils.o: utils.c
	gcc -c utils.c -o utils.o
math.o: math.c
	gcc -c math.c -o math.o
```



```makefile
模式规则：一条搞定所有
	
%.o: %.c
	gcc -c $< -o $@
```



```
 匹配过程图解

  目标: math.o
          ↓
  模式: %.o  → 词干 = math
          ↓
  依赖: %.c  → math.c
          ↓
  命令展开: gcc -c math.c -o math.o
```





#### D.  通配细节



`%` 匹配范围



```makefile
 # % 匹配一个或多个字符（非空字符串）

%.o: %.c          #  main.o → main.c
%.o: src/%.c      #  main.o → src/main.c
%test: %test.c    #  unittest → unittest.c
```



`%` 只能出现一次



```makefile
#  错误：% 在目标中只能出现一次
%_%.o: %.c
```





### 4.1.5 伪目标



#### A. 什么是伪目标



伪目标(Phony)：并不代表一个真正的文件名，它只是一个动作或命令的“代号”。





**问题引入：为什么需要 .PHONY？****



场景演示：若在当前目录下创建了clean文件，make clean 就会因为clean文件已经存在，而不执行清除命令



错误写法的makefile文件：

```makefile
# 没有名为 "clean" 的文件时，执行命令

clean:
	rm -f *.o app
```



shell执行命令

```bash
# 如果当前目录下碰巧存在一个叫 clean 的文件
$ touch clean
$ make clean
make: 'clean' is up to date.    # ← Make 认为 clean 已经是最新的，不执行！
```



正确写法的makefile文件：

```makefile
正确写法

.PHONY: clean
clean:
	rm -f *.o app

#加上.PHONY 声明后，make会无条件执行该目标的命令，不检查文件是否存在。
```



shell执行命令

```bash
# 如果当前目录下碰巧存在一个叫 clean 的文件,也不影响清除命令
$ touch clean
$ make clean
```





#### B.基本语法



```makefile
#集中声明
.PHONY: all clean install test

#规则定义

all:app

clean:
	rm -r *.o app
	
test:
	echo "测试"
```



#### C. 常见伪目标



`all`：默认目标



```makefile
all — 默认目标

.PHONY: all
	all: app libfoo.a utils    # 构建所有产物

app: main.o utils.o
	gcc $^ -o $@

libfoo.a: foo.o bar.o
	ar rcs $@ $^

utils: utils.c
	gcc $< -o $@
```



`clean`：清理



```makefile
clean — 清理

.PHONY: clean
clean:
    rm -f *.o app
    rm -rf build/
```



`install`：安装



```makefile
install — 安装

PREFIX = /usr/local

.PHONY: install
install: app
    install -d $(DESTDIR)$(PREFIX)/bin
    install -m 755 app $(DESTDIR)$(PREFIX)/bin/

.PHONY: uninstall
uninstall:
	rm -f $(DESTDIR)$(PREFIX)/bin/app
```



`test`：运行测试



```makefile
test — 运行测试

.PHONY: test
test: app
    @echo "All tests passed!"
```



`help`：显示帮助



```makefile
help — 显示帮助

.PHONY: help
help:
    @echo "可用目标:"
    @echo "  make all      - 构建所有"
    @echo "  make clean    - 清理"
    @echo "  make install  - 安装"
    @echo "  make test     - 运行测试"
```





# 五、版本控制器git



##   5.1 什么是版本控制？



核心概念：Git 是一个分布式版本控制系统，用于跟踪文件的变化，支持多人协作开发。



场景演示：



```bash
想象你在写论文：

论文v1.doc       ← 初稿
论文v2.doc       ← 修改了第一章
论文v3.doc       ← 加了参考文献
论文最终版.doc    ← 终于定稿
论文最终版2.doc   ← 又改了...
论文打死不改版.doc ← ...

版本控制系统就是帮你自动管理这些"版本"的工具。
```





## 5.2 常用操作



操作流程



```bash
工作区域

工作区 
  ↓     <----- git add
暂存区 
  ↓     <----- git commit
本地仓库 
  ↓     <----- git push
远程仓库 


简单理解：
- 工作区：你正在编辑的文件
- 暂存区：准备提交的文件清单
- 本地仓库：已经保存的历史记录
- 远程仓库：服务器上的备份
```





### 5.2.1 初始化仓库



`git init` ：初始化仓库



```bash
# 方式1：在现有目录初始化
cd my-project
git init

# 方式2：克隆远程仓库 （推荐）
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git my-folder  # 指定文件夹名

初始化后的目录结构：
  my-project/
  ├── .git/              ← Git 仓库数据（不要手动修改）
  │   ├── HEAD           ← 当前分支指针
  │   ├── objects/       ← 所有数据对象
  │   ├── refs/          ← 分支和标签引用
  │   └── ...
  └── 你的项目文件...
```



### 5.2.2 查看状态



`git status` :查看状态



```bash
git status


输出示例：

# 暂存区的修改
On branch main
Changes to be committed:          
    (use "git restore --staged <file>..." to unstage)
          modified:   file1.txt
         
# 工作区的修改（已跟踪）
Changes not staged for commit:    
   (use "git add <file>..." to update what will be committed)
          modified:   file2.txt

# 未跟踪的文件
Untracked files:                  
    (use "git add <file>..." to include in what will be committed)
          new-file.txt
```





### 5.2.3 添加到暂存区



`git add` ：添加到暂存区



```bash
git add：添加到暂存区

git add file.txt          # 添加指定文件
git add .                 # 添加所有修改
git add *.txt             # 通配符添加
git add src/              # 添加整个目录
git add -p                # 交互式添加（选择部分修改）
```





### 5.2.4 提交到本地仓库



`git commit` :提交



```bash
git commit :提交

git commit -m "提交信息"             # 基本提交
git commit -am "信息"               # add + commit（仅限已跟踪文件）
git commit --amend                 # 修改上次提交
git commit --amend -m "新信息"      # 修改上次提交信息
```





### 5.2.5 提交到远端仓库



`git push` ：提交到远端仓库



```bash
git push ：提交到远端仓库


# 推送
git push origin main          # 推送到远程 main 分支
git push -u origin main       # 推送并设置上游分支
git push --force              # 强制推送（危险！）
git push --force-with-lease   # 安全的强制推送
```



### 5.2.6 拉取远端仓库



`git pull` ：拉取远端仓库



```bash
git pull : 拉取


git fetch origin              # 获取远程更新（不合并）
git pull origin main          # fetch + merge
git pull --rebase origin main # fetch + rebase
```



### 5.2.7 查看历史



`git log` ：查看历史提交信息



```bash
git log : 查看历史提交信息


git log                          # 完整日志
git log --oneline                # 单行显示
git log --oneline -10            # 最近10条
git log --graph --oneline        # 图形化显示
git log --author="John"          # 按作者筛选
git log --since="2024-01-01"     # 按日期筛选
git log --grep="fix"             # 按关键词筛选



输出示例：

commit 7ee6f1b3d05fcf9bb327a49364ca501ef0552f82
Author: hamber <3039816615@qq.com>
Date:   Sun Jun 7 19:43:55 2026 +0800

    新增code.c文件

commit 70af76cdabf59b36401b29d68b9c265308d55abc
Author: hanbao <3039816615@qq.com>
Date:   Sun Jun 7 11:22:41 2026 +0000
```





# 六、调试器gdb





## 6.1 gdb操作详解



核心概念：`gdb` 是 GNU 项目中的标准调试器，用于调试 C、C++、Rust 等编译型语言程序。





### 6.1.1 编译时开启调试信息



`gdb`：只能调试可执行文件，且在编译时需带上 `-g` 选项



```bash
# 使用 -g 选项编译，否则 gdB 无法显示源码和变量名：
gcc -g program.c -o program


# 优化级别建议关闭或降低，方便调试
gcc -g -O0 program.c -o program
```



### 6.1.2 启动 GDB



`gdb ` ：调试可执行文件



```bash
gdb ./program           # 调试可执行文件

gdb ./program core     	# 调试 core dump

gdb -p 1234           	# 附加到正在运行的进程 (PID 1234)
```



### 6.1.3 退出 GDB



`gdb` ：退出相关指令



```bash
quit          # 退出（简写 q）

run           # 运行程序（简写 r）

kill          # 终止当前运行的程序
```



### 6.1.4 核心命令 



#### A.  运行控制



`run [args]`：启动程序，可传命令行参数



```bash
run [args] 

简写:r

功能：从头运行程序
```





`continue`：继续运行到下一个断点



```bash
continue 

简写：c

功能：继续运行到下一个断点
```





`next`：单步执行（不进入函数内部）



```bash
next 

缩写：n

简写：单步执行，遇到函数调用会跳过，相当于vs中的F11
```





`step` ：单步执行（进入函数内部）



```bash
step

简写：s

功能：单步执行，遇到函数调用会进入，相当于vs中的F10
```





`finish` ：运行到当前函数返回



```bash
finish 

简写： fin  

功能：运行到当前函数返回
```





`until 行号` ：运行到指定行 



```bash
until 行号 

简写：u

功能：运行到指定行，用于跳出循环
```





#### B. 断点



**一、 设置断点**



`break` ：设置断点



```bash
break 缩写：b

常见使用方式：

b main        ：在main函数设置断点

b 文件名:行号   ：在指定文件位置设置断点

b 函数名       ：在指定函数入口设置断点

b 行号 if      ：条件断点
```



**二、 管理断点**



`info b`：查看所有断点



```bash
info b

输出调试信息：

Num     Type           Disp Enb Address            What
1	breakpoint     keep y   0x00000000004005cd in main at code.c:18
```



`delete` ：删除断点



```bash
delete 缩写：d

常见使用方式： 

info b          ：查看断点编号

d 断点编号       ：删除指定编号位置的断点
```



`disable/enable 断点编号` ：禁用/启用断点 



```bash
diable 缩写 ：dis

enable 缩写 ：en

常见使用方式：

dis  1       ：禁用编号1处的断点

en   1       ：启用编号1处的断点
```



**三、 观察断点**



`watch 变量名`



```bash
watch myVar                  # 当 myVar 的值改变时停下来
  
watch *(int*)0x7fff1234      # 监控某个内存地址

rwatch myVar                 # 当 myVar 被读取时停下来
  
awatch myVar                 # 读或写都停下来
```



`display 变量`



#### C. 查看数据



`print` ：查看数据



```bash
print 

缩写 p
```





`print 变量`：查看变量与指针



```bash
p var：打印变量 var 的当前值。

p &var：打印变量 var 的内存地址。

p ptr：打印指针 ptr 存储的地址。

p *ptr：解引用，打印指针 ptr 指向的内存中的实际值。
```





`print 格式化输出`：以不同进制形式进行输出变量值



```bash
p/x var：以十六进制（Hex）打印（排查内存地址或位掩码时最常用）。

p/t var：以二进制（Two / Binary）打印（查看标志位或位操作时必备）。

p/c var：以字符（Character）形式打印。

p/f var：以浮点数（Float）形式打印。


说明：
- 格式：x(十六进制), d(十进制), s(字符串), i(指令)
- 单位：b(byte), h(halfword), w(word), g(giant/8字节)
```





`print 数组`：查看数组中的内容



```bash
p arr ： 查看静态一维数组中的所有元素


p *数组名@查看长度： 查看动态一维数组中从起始位置，指定长度大小的数组元素


p *(数组名 + 起始偏移)@查看长度 ：查看动态一维数组中从指定位置开始，指定长度大小的数组元素
```





`print 结构体与对象`：查看复杂的结构体与对象



```bash
p node：会打印该节点的所有成员变量（包括内部的指针变量）。

p node->left：直接打印对象的特定成员。

#注：自动将对象的成员变量换行并缩进对齐显示
set print pretty on
```





`print 动态计算与执行`



```bash
p a + b：直接打印两个变量相加的结果。

p arr[i]：结合当前的索引 i，查看特定数组元素。

p func(5)：在 gdb 中调用你代码里的函数 func，传入参数 5 并打印返回值，不需要修改源码重新编译。

p var = 10：直接在调试中强行修改变量的值，看看程序接下来的走向。
```
















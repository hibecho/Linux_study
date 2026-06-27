## 1.操作指令



### ls 指令



**ls表示：list的缩写，列出目录下的文件**



**`ls`** : 仅列出当前目录下**文件和文件夹的名称**



```bash
ls

#有如下输出结果：
Desktop  Documents  Downloads  Music  Pictures  example.txt
```





**`ls -l`**：展示每个文件或目录的**详细属性**



```bash
ls -l  

( ls表示：命令  -l表示：命令行选项) 
	
#输入指令
ls -l example.txt
	
#有如下输出结果：
-rw-r--r-- 1 user group 1024  5月 12 18:21 example.txt


#解释：以上述输出为例 

1. -rw-r--r--   文件类型和权限
	
2.  1           硬链接数：指向该文件的硬链接数量（如果是目录，代表其内部子目录的数量）。

3. user         所有者：创建或拥有该文件的用户名称。

4. group        所属用户组：该文件所属的用户组名称。
	
5. 1024		    文件大小：单位是字节 (Bytes)。

6. 5月 12 18:21 最后修改时间：文件内容上一次被修改的日期和时间。
	
7. example.txt  文件或目录名称


#解释第一列输出的10 个字符 (-rw-r--r--)
	
#注1：首字符（文件类型）：首字符一般由如下三个构成
	
-：普通文件 (文本、压缩包、图片、视频、可执行文件)   
	
d：目录 (directory，即文件夹)
	
l：软链接 (类似 Windows 的快捷方式)

c：字符文件（键盘，显示器，终端）

b：块设备文件 或 磁盘

p：管道文件

#温馨提示：系统不关心文件的后缀，但是工具(如gcc)关心文件的后缀，系统认为后缀是名的一部分
	
	
#注2：后 9 个字符（权限）： 分为3组，每组3个字符，分别代表：所有者、同组用户 和 其他用户 的权限。
	
r (read)：可读
	
w (write)：可写
	
x (execute)：可执行
	
-：没有该权限
	
#例如： rw- 代表可读可写但不可执行，r-- 代表只能读
```

​	



**`ls -l 知识点补充1`**



```bash
#知识点补充1：
	
A. ls -l : 默认查看当前目录下的详细属性
	

B. ls -l [指定目录]: 查看指定目录下的详细属性  
	
例如：假设所在目录路径 /root/mydir ，输入指令 ls -l /home，就可以查看目录home下的所有文件的详细属性

输出信息如下所示：
total 4
drwxr-xr-x 2 root root 4096 May 12 19:24 mydir


C. ls -l -d 或 ls -ld ：看当前目录本身的详细属性 

例如：假设当前用户在目录路径 /root/mydir , 输入指令 ls -l -d 或 ls -ld ，就可以查看目录/home/mydir的详细属性

输出信息如下所示：
drwxr-xr-x 2 root root 4096 May 12 19:24 .


D. ls -l -d [指定目录] / ls -ld [指定目录] ：查看指定目录本身的详细属性

#例如：存在目录路径 /root/mydir ，输入指令 ls -l -d /root/mydir 或 ls -ld /root/mydir，
#     可以指定查看目录/home/mydir的详细属性

#输出信息如下所示：
drwxr-xr-x 2 root root 4096 May 12 19:24 /root/mydir
```





**`ls -a`** : 列出目录下的所有文件，包括以.开头的隐含文件



``` bash
ls -a 
	
核心作用：显示隐藏文件

在 Linux 和 macOS 系统中，只要文件或文件夹的名称是以点 . 开头的，它就是隐藏文件（例如 .bashrc、.gitignore、.config/）。
```



```bash
#ls 和 ls -a 的对比
	
1.普通的 ls，只显示常规文件和目录

2.使用 ls -a，所有隐藏的配置文件都出来了

ls:

Desktop  Documents  Downloads  Music  Pictures  Videos


ls -a:

.  ..  .bash_history  .bashrc  .cache  .config  Desktop  Documents  Downloads  Music  Pictures  Videos


#备注：

1. . (一个点)：代表当前目录本身。

2. .. (两个点)：代表上一级目录（父目录）

3. 任何一个目录(包括根目录)，即使该目录是空的，系统也要默认给该目录自动生成 " . " 和 " .. " 

4. 对于根目录而言 . 和 .. 都代表当前目录本身
```





**`ls -d`**： 只看目录（文件夹）本身，不看目录（文件夹）里面的内容



```bash
ls -d 

假设存在路径 /root/mydir

使用命令 ls -d /root/mydir ，即可输出目录本身 /root/mydir
```



```bash
A. 使用场景1：查看目录本身的详细信息
	
通常我们会用 ls -l 来查看“当前目录下”的详细信息（权限、所有者、大小等）,配合 -d 即可查看一个目录本身的详细信息。
	
假设存在路径 /root/mydir

输入：

ls -l -d /root/mydir 或 ls -ld /root/mydir 

输出结果：

drwxr-xr-x 3 root root 4096 May 13 19:14 /root/mydir
```



```bash
B. 使用场景 2：只列出目录，不列出文件（配合通配符）
	
假设你当前目录下杂乱无章，既有文件又有文件夹，你只想挑出所有的文件夹来看看。

#错误做法：

如果你直接输入 ls *，系统会把当前目录下的文件列出来，并且把每一个子文件夹都打开，把里面的东西也全列出来，屏幕直接爆炸。
	
#正确做法：

利用 -d 和通配符的组合，使用命令：ls -d */

#解释：

*/ 专门匹配所有的目录，加上 -d 后，ls 就会乖乖地只把这些目录的名字打印出来，而不会去翻它们的底朝天。
```



```bash
C. 场景 3：确认某个含有海量文件的目录是否存在
	
假设服务器上有一个存放了 100 万张图片的目录叫 images_backup，你想确认一下这个文件夹在不在当前目录。

#错误做法：

使用命令 ls images_backup，系统会去读取images_backup目录下，那 100 万个文件名然后打印在屏幕上，你的终端可能会卡死好几分钟。

#正确做法

使用命令 ls -d images_backup

系统会回答 images_backup（表示存在），或者报错 No such file or directory（表示不存在），绝对不会读取里面那 100 万个文件。
```





### pwd 指令



**pwd表示：Print Working Directory的缩写，显示当前工作路径。**



**`pwd`：显示用户当前所在的目录**



```bash
pwd

#输出结果

/home/user/Documents/project
```





### cd 指令



**`cd`** ： **Change Directory** 的缩写，意思是“**切换目录**”或“**改变目录**”。



**`cd .`**  ：留在当前目录不动



```bash
cd . 

核心作用：留在当前目录不动 （无效指令）
```



**`cd ..`**：退回到上一级目录（也就是当前目录的“父目录”）



```bash
cd .. 

核心作用：退回到上一级目录（也就是当前目录的“父目录”）。
```



```bash
A.场景 1：普通退步

假设你当前在目录：/var/log/nginx

输入：cd ..
	
结果：你从 nginx 文件夹退了出来，来到了它的上一级 /var/log。
```



```bash
B.场景 2：连续退步（高阶用法）

假设你当前在目录：/home/user1/projects/webapp

输入：cd ../..     #连续退两步

结果：你直接跳过了projects，回到了 /home/user1
```



```bash
C.场景 3：退步并进入旁边的房间

假设你在 /var/log 目录下，你想去 /var/www 目录，你可以先退一步，再进一步：

输入：cd ../www

结果：先从/var/log 目录下退出到 /var，然后进入到 /var/www中
```



**`cd ~`** ：回到自己的 "主目录"（家目录）。



```bash
cd ~

~ 是一个非常聪明的符号，它会根据你当前登录的账号，把你送回属于你自己的地盘：

A. 如果你是 root（超级管理员）

无论你在哪，输入 cd ~，你都会立刻回到 /root 目录。

B. 如果你是普通用户（例如名为 hamber 的用户）

无论你在哪，输入 cd ~，你都会立刻回到 /home/hamber 目录。


#技巧1

不需要敲那个波浪号 ~，直接敲 cd 然后回车，效果和 cd ~ 是一模一样

cd       # 等同于 cd ~


#技巧2

如果你在 ~ 后面直接跟上其他用户的用户名（中间不加空格），你就可以直接跳到他的主目录去（前提是你有权限）。

cd ~zhangsan    # 直接跳转到名为 zhangsan 这个用户的主目录 (/home/zhangsan)
```





**`cd 绝对路径`**：无视当前位置，直接空降到指定的确切坐标。



```bash
cd  /.../目标目录


示例：

cd /c/Users/30398/Documents    # 切换到 Documents 目录
cd /c/Users/30398/Downloads    # 切换到 Downloads 目录
cd /                           # 回到根目录

注：绝对路径是从根目录 / 开始的完整路径，不依赖当前位置。
```



**`cd 相对路径`**：



```bash
cd 相对路径
	
	
示例：

假设当前位置是 /root/Users/hamber：

cd Desktop              # 进入当前目录下的 Desktop
cd Documents/Work       # 进入当前目录下的 Documents/Work
cd ..                   # 回到上一级 /root/Users
cd ../..                # 回到上两级 /root
cd .                    # 不动，还在当前位置（无实际意义）
cd ~                    # 回到家目录 /root/Users/hamber
cd -                    # 切换到上一次所在的目录
	
注：相对路径是基于当前位置的路径，不从根目录 / 开始
```





**`cd -`**



```bash
cd - :回到上一个目录

示例

假设当前位置在 /root/Users/hamber

cd /root/Users/hamber/Desktop    # 切到 Desktop
cd -                          	 # 切回 /root/Users/hamber
cd -                             # 又切到 Desktop
```



常用符号

| **符号** | **含义**   | **示例**         |
| -------- | ---------- | ---------------- |
| `cd .`   | 当前目录   | `cd ./Documents` |
| `cd ..`  | 上一级目录 | `cd ..`          |
| `cd ~`   | 家目录     | `cd ~`           |
| `cd `    | 回家目录   | `cd `            |





### touch 指令



`touch`： 用于创建空文件，如果文件已存在则更新修改时间（不改变内容）。



```bash
touch 文件名

示例1: 文件不存在，创建文件

touch test.txt              # 创建空文件 test.txt
touch a.py b.py c.py        # 一次创建多个文件
touch dir/file.txt          # 在指定目录下创建文件

示例2：文件存在，则更新时间戳（包括 Access 时间戳、 Modify 时间戳、Change 时间戳）

touch temp.txt 				#创建空文件 temp.txt
touch temp.txt 				#文件已存在，只更新修改时间，内容不变
```

​	

文件时间戳： Access、Modify、Change



```bash
文件时间戳：Access、Modify、Change

1. Access Time (atime)

  - 读取文件内容时更新
  - 包括：cat、less、grep、head、编译等


2. Modify Time (mtime)

  - 修改文件内容时更新
  - 包括：编辑、重定向、追加等


3. Change Time (ctime)

  - 修改文件属性数据时更新
  - 包括：权限、所有者、链接数、内容修改


4.文件操作对应文件时间戳的改变

文件操作
   │
   ├─ 读取内容 ─────────→ Access Time 更新
   │
   ├─ 修改内容 ─────────→ Modify Time 更新
   │                     Change Time 更新
   │
   └─ 修改元数据 ───────→ Change Time 更新
     (权限/所有者)
```





`touch -a`：只修改访问时间 



```bash
touch -a 文件名


示例：假设现在系统时间是 10:00:00，我们按顺序执行以下命令：

touch demo.txt
stat demo.txt

# 输出结果片段：
# Access: 2026-05-27 10:00:00  (访问时间)
# Modify: 2026-05-27 10:00:00  (修改时间)
# Change: 2026-05-27 10:00:00  (状态更改时间)

等待几分钟，假设现在时间是 10:05:00，只更新访问时间 (-a)

touch -a demo.txt
stat demo.txt

# 输出结果片段：
# Access: 2026-05-27 10:05:00  (访问时间)
# Modify: 2026-05-27 10:00:00  (修改时间)
# Change: 2026-05-27 10:05:00  (状态更改时间)
```





`touch -c`: 只更改修改时间 



```bash
touch -c filename


示例：假设现在系统时间是 11:00:00，我们按顺序执行以下命令：

touch demo.txt
stat demo.txt

# 输出结果片段：
# Access: 2026-05-27 11:00:00  (访问时间)
# Modify: 2026-05-27 11:00:00  (修改时间)
# Change: 2026-05-27 11:00:00  (状态更改时间)

等待几分钟，假设现在时间是 11:05:00，只更新修改时间 (-c)

touch -c demo.txt
stat demo.txt

# 输出结果片段：
# Access: 2026-05-27 11:00:00  (访问时间)
# Modify: 2026-05-27 11:05:00  (修改时间)
# Change: 2026-05-27 11:05:00  (状态更改时间)
```









### mkdir 指令



`mkdir`：用于创建目录（文件夹）。



```bash
mkdir 目录(文件夹)

示例

mkdir test              # 创建单个目录 test
mkdir a b c             # 一次创建多个目录
mkdir dir/sub           # 在 dir 目录下创建 sub（要求 dir 已存在）
```



`mkdir -p`: 自动创建路径中所有不存在的父目录，以递归的方式创建



```bash
mkdir -p 目录 


示例1：

#1.没有-p 参数的情况下

mkdir a/b/c		 #报错：mkdir: cannot create directory 'a/b/c': No such file or directory

因为 a 和 b 都不存在，mkdir 默认只创建最后一级。


#2.有-p 参数的情况下

mkdir -p a/b/c   #系统会自动按顺序创建：a → a/b → a/b/c，全部不存在就全部创建。


示例2：

# 创建项目结构
mkdir -p myapp/{src/main,src/test,bin,doc}

# 结果：
# myapp
# ├── src
# │   ├── main
# │   └── test
# ├── bin
# └── doc
```





### rmdir 指令



`rmdir` 用于删除目录，但只能删除空目录。



```bash
rmdir 目录名


示例1：

mkdir test
rmdir test      # 成功，test 是空目录


示例2：目录非空会失败

mkdir test
touch test/file.txt
rmdir test      # 报错：Directory not empty
```



`rmdir -p`：递归删除空的父目录



```bash
rmdir -p 目录名/目录路径


示例1：

mkdir -p a/b/c
rmdir -p a/b/c    # c 删了，b 也空了删掉，a 也空了删掉，全部清掉

示例2：

存在如下路径：
 path1/
  ├── myfile.txt
  └── path2/
      ├── myfile.txt
      └── path3/
          └── path4/

rmdir -p path1/path2/path3/path4 

#出现如下报错
#rmdir: failed to remove directory ‘path1/path2’: Directory not empty
```





### rm 指令



`rm` : 用于**删除文件**，会进行提示是否删除文件



```bash
rm 文件名 


示例：删除单个普通文件

touch temp.txt
rm temp.txt

示例：删除多个普通文件

touch a.txt b.txt c.txt
rm a.txt b.txt c.txt
```





`rm -r`：用于**删除目录**（包括目录内所有内容），会进行提示是否删除文件



```bash
rm -r 目录

示例：

假设存在如下路径：

path1
  ├── myfile.txt
  └── path2
      ├── myfile.txt
      └── path3
          └── path4
          
rm -r path1 	#将path1目录，以及 “目录的文件” 和 “子目录及文件” 的内容均进行删除
```





`rm -f` : 只能**强制删除文件**，删除不进行提示、删除不存在的文件也不报错



```bash
rm -f 文件

示例：

touch temp.txt
rm -f temp.txt

#不会出现提示信息，强制删除temp.txt文件
```





`rm -i`: 只能删除文件，且每删一个文件都问你确认



```bash
rm -i 文件

示例：

#指令：
touch a.txt b.txt c.txt
rm -i a.txt b.txt c.txt

# 输出：
# remove a.txt? y     ← 每个文件都问
# remove b.txt? y
# remove c.txt? n     ← 输入 n 就不删
```





`rm -I`: 只能删除文件，删超过3个文件时只问一次是否删除



```bash
rm -I 文件

示例：

#指令输入：
touch a.txt b.txt c.txt d.txt
rm -I a.txt b.txt c.txt d.txt

#指令输出：
rm: remove 4 arguments? y
```





`rm -v` ：只能删除文件，且每删一个文件都问你确认，并输出删出信息



```bash
rm -v 文件
	
示例：

#指令
touch a.txt b.txt c.txt
rm -v a.txt b.txt c.txt

#输出
#rm: remove regular empty file ‘a.txt’? y
#removed ‘a.txt’
#rm: remove regular empty file ‘b.txt’? y
#removed ‘b.txt’
#rm: remove regular empty file ‘c.txt’? y
#removed ‘c.txt’
```





`rm -rf` : 强制 **删除目录 和 删除文件**，删除不进行提示、删除不存在的文件也不报错



```bash
rm -rf 目录/文件


示例1：

touch temp.txt
rm -rf temp.txt

#不会出现提示信息，强制删除temp.txt文件


示例2：

假设存在如下路径：

path1
  ├── myfile.txt
  └── path2
      ├── myfile.txt
      └── path3
          └── path4

rm -rf path1

#不会出现提示信息，强制删除path1（包括目录内所有内容）

```



> 其他常见组合：
>
> rm -r mydir              # 删目录
>
> rm -rf mydir             # 删目录，不提示
>
> rm -rfv mydir            # 删目录，不提示，显示过程
>
> rm -ri mydir             # 删目录，逐个确认

​	



> 通配符删除
>
> rm *.txt                    # 删除所有 .txt 文件
>
> rm test *                    # 删除所有 test 开头的文件
>
> rm -r dir */              # 删除所有 dir 开头的目录





>  危险操作 ⚠️
>
>   rm -rf  /                 # 删除整个系统 — 绝对不要执行
>
>   rm -rf  ~                 # 删除家目录 — 绝对不要执行
>
>   rm -rf  *                 # 删除当前目录下所有内容
>
>   rm -rf  .                  # 同上





### cp 指令



`cp`：复制文件，并会提示是否将源文件覆盖目标文件



```bash
cp 源文件 目标文件/⽬录

#注：
#1.源文件必须存在
#2.若目标文件不存在则生成目标文件，并将源文件中的内容拷贝到目标文件中。若目标文件存在，则提示是否将源文件的内容覆盖目标文件。
#3.若cp 源文件 目录，此时目录必须存在，才能将源文件复制到指定目录下


示例1：

cp a.txt b.txt              # 复制 a.txt 为 b.txt
cp a.txt dir/               # 复制 a.txt 到 dir 目录下 (dir目录必须存在)
cp a.txt dir/b.txt          # 复制 a.txt 到 dir 下并复制 a.txt 为 b.txt


示例2：

cp a.txt b.txt c.txt dir/    # 复制多个文件到 dir 目录
```





`cp -f`：强制覆盖文件，不会进行提示是否将源文件覆盖目标文件



```bash
cp -f 源文件 目标文件/⽬录


示例1：

cp -f a.txt b.txt              # 复制 a.txt 为 b.txt ，不会进行提示是否覆盖
cp -f a.txt dir/               # 复制 a.txt 到 dir 目录下 (dir目录必须存在)
cp -f a.txt dir/b.txt          # 复制 a.txt 到 dir 下并复制 a.txt 为 b.txt

示例2：

cp -f a.txt b.txt c.txt dir/    # 复制多个文件到 dir 目录
```





`cp -r`:  将整个源目录及其内部包含的所有子目录和文件，将其复制到目标目录



```bash
cp -r 源目录 目标目录

#注：当然cp -r 也拥有cp指令的功能，复制源文件到目标文件/目标目录。

工作方式：

它会完整遍历源目录的整个树结构，包括：
- 所有子目录
- 所有文件
- 嵌套的子目录和文件
```



```bash
示例1：

假设你当前目录下有一个文件夹叫 source_folder，你想把它复制一份并命名为 new_folder，且 new_folder 目前不存在。

source_folder 文件目录结构

|-- source_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test


cp -r source_folder new_folder

结果：

系统会自动创建 new_folder，然后把 source_folder 里面的所有内容一模一样地复制到 new_folder 中。

文件结构图如下：

|-- source_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test
|-- new_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test
```



```bash
示例2：

假设你想把 source_folder 复制到 new_folder 中，但 source_folder 和 new_folder 文件夹均存在。

source_folder 文件目录结构如下：

|-- source_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test


指令： cp -r source_folder new_folder

结果：系统会将 source_folder 作为一个完整的文件夹，放入 new_folder 的内部。

文件结构图如下：

|-- source_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test
|-- new_folder
|   `-- source_folder
|       `-- myapp
|           |-- doc
|           `-- src
|               |-- main
|               `-- test
```



```bash
示例3：

假设 source_folder 和 new_folder 文件夹均存在，你想把 source_folder 的文件内容复制到 new_folder 中

source_folder 和 new_folder 文件目录结构如下：

|-- source_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test
|-- new_folder


指令： cp -r source_folder/* new_folder

结果：系统会把 source_folder 里面的所有内容一模一样地复制到 new_folder 中。

|-- source_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test
|-- new_folder
|   `-- myapp
|       |-- doc
|       `-- src
|           |-- main
|           `-- test
```



```bash
示例4：复制多个目录到一个目标 （可以一次性复制多个目录，前提是最后一个参数必须是一个已存在的目录。）

指令：

cp -r folder1 folder2 folder3 target_folder（必须存在）

结果：

folder1、folder2 和 folder3 都会被完整地装进 target_folder 中。
```





`cp -rf`:强制地将整个源目录及其内部包含的所有子目录和文件，将其复制到目标目录，不进行交互。



```bash
cp -rf 源目录 目标目录

#注：使用方式与cp -r 没有本质区别。
```





### mv 指令



`mv`：用于**移动文件/目录**（相当于windows系统中的剪切） 或 **重命名**。 

```bash
mv 源文件 目标路径或目标文件名

#源文件：你想要移动或重命名的文件/目录（可以是一个或多个）。
#目标路径/文件：你想要将文件移动到的位置，或者你想给文件起的新名字。
```



```bash
重命名：同目录

示例：

# 文件名变了，但还在同一个目录下 → 重命名
mv oldname.txt newname.txt


# 目录也可以重命名
mv old_dir/ new_dir/
```



```bash
移动：跨目录

示例1：

假设file.txt在目录 /home/user/Lesson1/ 

# 文件名不变，但换到了另一个目录 → 移动
mv file.txt /home/user/Lesson2/  # <- 将file.txt 移动到Lesson2中

# 同时移动+重命名
mv file.txt /home/user/Lesson2/newname.txt #<- 将file.txt 移动到Lesson2中,并将file.txt重命名为newname.txt

# 移动多个文件
mv file1.txt file2.txt /path/user/Lesson2/
```





`mv -i`:覆盖前会提示确认（interactive交互模式）



```bash
mv -i


示例：

#1.输入指令
mv -i file.txt destination/

#2.如果目标目录下已有同名文件source.txt，终端会显示：
mv: overwrite 'destination/source.txt'?
```



`mv -f`： 不提示直接覆盖（force 强制）



```bash
mv -f


示例：

#如果目标目录下已有同名文件source.txt，终端不会显示：mv: overwrite 'destination/source.txt'?
mv -f source.txt destination/
```



>  -i vs -f 对比
>
>  
>
> 1.有同名文件时
>
>  mv -i file.txt dir/     提示：overwrite? (需要输入 y/n)
>
>  mv -f file.txt dir/     直接覆盖，不提示
>
> 
>
>  2.都不加时（默认行为）
>
>  mv file.txt dir/        直接覆盖（和 -f 效果一样）





### man 指令



`man`： manual的缩写（手册），用于查看 Linux 命令、函数、配置文件的详细帮助文档。



```bash
man 命令名


示例1：

man ls

man cp

man printf

示例2：

# 同名内容存在于多个章节时，指定章节

man 2 open       # 查看 open 系统调用（第2章）

man 3 printf     # 查看 printf 库函数（第3章）
```



>  man 手册分为 9 个章节，按类别划分：
>
> 
>
> 1. 用户命令（ls, cp, grep）
> 2. 系统调用（open, fork, read）
> 3.  C 库函数（printf, malloc, strcpy）
> 4. 设备文件（/dev 下的特殊文件）
> 5. 配置文件格式（/etc/passwd, crontab）
> 6. 游戏和娱乐
> 7. 杂项（协议、文件系统、惯例）
> 8. 系统管理命令（fdisk, iptables, reboot）
> 9. 内核例程





### echo 指令



`echo` ：将你指定的文本、字符串或变量的值，原样输出到屏幕上（标准输出）



```bash
echo 字符串
	

输入：echo "Hello, Linux!"

输出：Hello, Linux!
	
	
注意：echo 默认会在输出的文本末尾自动添加一个“换行符”，所以每次执行完光标都会换到下一行。
```



`echo -n`：取消自动换行



```bash
echo -n

输入：echo -n "Please enter your name: "
	
输出：Please enter your name: _  <--光标会留在这里，而不是跳到下一行
```



`echo -e`：开启转义字符解析



```bash
echo -e
	
输入：echo -e "First Line\nSecond Line"
	
输出：

First Line
Second Line
	

输入：echo -e "Name\tAge\tCity"

输出：Name    Age     City
```



> `\n`：换行 
>
> `\t`：水平制表符 (Tab，相当于按一下 Tab 键)
>
> `\\`：输出一个真正的反斜杠
>
> `\c`：取消输出末尾的换行符（注意：必须加 `-e` 选项才能让 `\c` 生效），并忽略 `\c` 后面的所有字符。





`echo`：结合重定向（`>` 和 `>>`）操作文件



**覆盖写入 (`>`)**：如果文件存在，会清空原有内容并写入新内容；如果不存在则创建文件。



```bash
基本语法：

echo 字符串 > 文件


例如：echo "This is a new file" > config.txt

解释：上述指令目的是将字符串"This is a new file" 写入到文件 config.txt

注：若文件config.txt不存在则新建文件，若config.txt存在则先清空，然后再写入
```



**追加写入 (`>>`)**：将内容追加到文件末尾，不破坏原有内容。



```bash
基本语法：

echo 字符串 >> 文件


例如：echo "Add this line to the end." >> config.txt

解释：上述指令目的是将字符串"This is a new file" 追加到文件 config.txt
	
注：若文件config.txt不存在则新建文件，若config.txt存在则将内容添加到文件末尾
```





### cat 指令



`cat`  : 查看**目标文件的内容**，是英文concatenate（连接）的缩写



```bash
cat 文件 

示例：

# 查看单个文件
cat file1.txt

# 查看多个文件（依次显示）
cat file1.txt file2.txt

# 查看所有 txt 文件
cat *.txt
```



`cat -n`: 查看目标文件的内容，**所有行显示行号**



```bash
cat -n


示例

cat -n file.txt

# 显示行号
# 1  hello
# 2  world
# 3
# 4  test
```





`cat -b` : 查看目标文件的内容，**非空行显示行号**



```bash
cat -b

示例：

cat -b file.txt

# 非空行显示行号
# 1  hello
# 2  world
# 
# 3  test
```





`cat >` :  1.主要用于**快速创建文件**并**从键盘输入内容**   2.主要用于**合并文件**



```bash
cat > 文件名


示例1：

# 1. 输入命令并指定要创建（或覆盖）的文件名
cat > note.txt

# 2. 此时光标会换行，你可以开始输入任意内容：
Hello, Linux!
This is a test file.

# 3. 输入完成后，按一下回车键（确保在独立的新行）
# 然后按下键盘上的 Ctrl + D （表示 EOF，即 End Of File 文件结束）。


示例2：

# 合并两个文件
cat file1.txt file2.txt > merged.txt

#1.系统会严格按照你书写的顺序，先读取 file1.txt 的全部内容，紧接着读取 file2.txt 的全部内容。
#2.如果 merged.txt 不存在：系统会自动为你创建一个新文件，并写入合并后的内容。
#3.如果 merged.txt 已经存在：系统会毫不留情地清空它原有的所有内容，然后才把 file1.txt 和 file2.txt 的内容写进去。
```





`cat >>`: 在不破坏原文件内容的前提下，将新内容追加到文件的最末尾。



```bash
cat >> 文件名


示例：若todo.txt中包含字符串“Hello ”

#1.启动追加模式
cat >> todo.txt

#2.光标跳到下一行，你输入以下内容：
Linux

#3.输入完成后，按下回车（换到新行），然后按 Ctrl + D 保存并退出。

#4.此时todo.txt 中的内容为：Hello Linux
```





### tac 指令



`tac`：**倒序显示文件内容**（从最后一行到第一行）



```bash
tac 文件


示例1：

#1.假设原文件 file.txt：
1
2
3

#2.输入指令
tac file.txt

#3.出现如下输出：
3
2
1


示例2：查看最新日志（倒序）

tac /var/log/messages 

#从最新的日志开始找，找到前 20 个信息
tac /var/log/messages | head -20
```





### more 指令



`more`：在屏幕上加载部分文件内容，在屏幕底部提示 `--More--`，**只能让你向后移动，按页或按行看**。



```bash
more 文件

示例

more file.txt #只能逐行向后查看file.txt文件内容

more /var/log/syslog  #只能逐行向后查看syslog文件内容
```



> 不推荐使用：由于只能向后查看不能向前查看
>
> 推荐使用 ：less，功能更强，more 有的功能 less 全都有





### less 指令



`less`：在屏幕上加载部分文件内容，让你**既能向前移动又能向后移动，按页或按行看**。



```bash
less 文件

示例：

#1.向temp.txt写入字符串（从hello 0到 hello 2000）
cnt=0; while [ $cnt -le 2000 ]; do echo "hello $cnt"; let cnt++; done > temp.txt

#2.输入指令，阅读文件temp.txt
less temp.txt

#3.输出
#hello 0
#hello 1
#hello 2
#hello 3
#hello 4
:
```



`less -N`：在屏幕上加载部分文件内容（带行号的加载），让你**既能向前移动又能向后移动，按页或按行看**。



```bash
less -N 文件


示例：

#1.向temp.txt写入字符串（从hello 0到 hello 2000）
cnt=0; while [ $cnt -le 2000 ]; do echo "hello $cnt"; let cnt++; done > temp.txt

#2.输入指令，阅读文件temp.txt
less temp.txt

#3.输出
#1 hello 0
#2 hello 1
#3 hello 2
#4 hello 3
#5 hello 4
:
```



less 四大核心键



`Space` : 向下翻一页



```bash
Space — 向下翻一页

# 按 Space 后：
# ─────────────
# 第 1~30 行    ← 原来显示的
# ─────────────
# 按 Space
# ─────────────
# 第 31~60 行   ← 翻到下一页
# ─────────────

从当前页往下翻一页（通常一屏约 20~30 行）。
```



  `b` :向上翻一页



```bash
b — 向上翻一页

# 当前显示第 31~60 行
# ─────────────
# 第 31~60 行
# ─────────────
# 按 b
# ─────────────
# 第 1~30 行    ← 翻回上一页
# ─────────────

与 Space 方向相反，往回翻一页。
```



`/`:从当前浏览位置，向下搜索“字符串”的功能



```bash
/ 字符串 - 向下搜索字符串

示例

假设file.txt中含有“hello world”

#1.浏览file.txt文件
less file.txt

#2.搜索字符串
/ “world”

#3.跳转到字符串“world”的位置，并进行高亮字符串
```



`q`:退出



```bash
q — 退出

示例：

#1.输入指令，进行浏览文件file.txt
less file.txt

#2.浏览文件中 ...

#3.输入q进行退出浏览
q         ← 按 q 立刻退出，回到命令行
```





### head 指令



`head`：用于**查看文件开头的内容**，常用于查看日志（默认前 10 行）



```bash
head 文件


示例

# 默认查看前 10 行
head file.txt
```





`head -n`:用于**查看文件开头前n行的内容**，常用于查看日志



```bash
head -n 文件


示例

# 查看前 5 行
head -n 5 file.txt

# 简写形式
head -5 file.txt
```





### tail 指令



`tail`：用于**查看文件末尾的内容**，常用于查看日志（默认后 10 行）



```bash
tail 文件


示例：

# 默认查看后 10 行
tail file.txt
```



`tail -n`:用于**查看文件文件末尾n行的内容**，常用于查看日志



```bash
tail -n 文件

示例1：

# 查看前 5 行
head -n 5 file.txt

# 简写形式
head -5 file.txt


示例2：模拟动态⽇志查看功能

#1.向temp.txt写入字符串（从hello 0到 hello 2000）
cnt=0; while [ $cnt -le 2000 ]; do echo "hello $cnt"; let cnt++; done > temp.txt

#2.输入指令查看180行 - 200行的信息 （结合管道重定向操作）
head -200 temp.txt | tail -20

#3.输出180行 - 200行的信息
hello 180
hello 181
hello 182
...
hello 199
```





### date 指令



`date`：用于**显示或设置系统日期和时间**



```bash
date

示例

#1.输入指令
date

#2.显示当前日期时间（默认格式）
Thu May 28 19:36:33 CST 2026
```



`date -u`:显示 UTC 时间



```bash
date -u

示例：

#1.输入指令
date -u

#2.输出UTC时间
Thu May 28 11:38:59 UTC 2026
```



> 1.Unix 时间戳
>
> 它是从“Unix 纪元”（即协调世界时 UTC 1970年1月1日 00:00:00）开始，到现在所经过的总秒数。
>
> 
>
> 2.UTC时间：是整个人类世界的“基准时间”或“零点时间”。
>
> 
>
> 3.时间分区
>
> 如果你在零时区以东，时间就比 UTC 早，用**加号（+）**。
>
> 如果你在零时区以西，时间就比 UTC 晚，用**减号（-）**。
>
> ​	
>
> 例如：北京时间（UTC+8）
>
> 例如：纽约时间   (UTC-5)





`date +`:格式化输出时间



```bash
date +

常见格式示例

#1. 年-月-日
date +"%Y-%m-%d"
# 2026-05-28

#2. 时:分:秒
date +"%H:%M:%S"
# 15:30:45

#3. 年/月/日 时:分:秒
date +"%Y/%m/%d %H:%M:%S"
# 2026/05/28 15:30:45

#4. 中文格式
date +"%Y年%m月%d日 %H时%M分%S秒"
# 2026年05月28日 15时30分45秒

#5. 带星期
date +"%Y-%m-%d %A"
# 2026-05-28 Wednesday

#6. 时间戳
date +"%s"
# 1780000000
```



常见格式符

| **格式符** | **含义**      | **示例**     |
| ---------- | ------------- | ------------ |
| **`%Y`**   | 四位年份      | `2026`       |
| **`%m`**   | 月份（01-12） | `01`         |
| **`%d`**   | 日期（01-31） | `28`         |
| **`%H`**   | 小时（00-23） | `15`         |
| **`%M`**   | 分钟（00-59） | `30`         |
| **`%S`**   | 秒（00-59）   | `45`         |
| **`%A`**   | 星期全称      | `Wednesday`  |
| **`%a`**   | 星期简称      | `Wed`        |
| **`%B`**   | 月份全称      | `January`    |
| **`%b`**   | 月份简称      | `Jan`        |
| **`%F`**   | 完整日期      | `2026-01-28` |
| **`%T`**   | 完整时间      | `15:30:45`   |
| **`%s`**   | 时间戳（秒）  | `1780000000` |





### find 指令



`find`: 用于在目录树中按条件搜索文件和目录。



```bash
find [搜索路径] [条件] [动作]


示例1：按名称搜索

find . -name "*.txt"          # 精确匹配（区分大小写）
find . -iname "*.txt"         # 不区分大小写
find . -name "report*"        # 通配符
find . -name "*.log" -o -name "*.tmp"   # 多个条件（或）

#1. . 为当前目录路径
#2. -name / -iname 为条件，其中-name区分大小写，-iname不区分大小写
#3. "*.txt" 字符串，搜索后缀为.txt的文件

示例2：  按类型

find . -type f                # 只找文件
find . -type d                # 只找目录
find . -type l                # 只找符号链接


示例3：  按时间

find . -mtime -7              # 最近7天内修改过
find . -mtime +30             # 超过30天没修改
find . -newer file.txt        # 比 file.txt 新
find . -mmin -60              # 最近60分钟内修改过


示例4：按大小
find . -size +100M            # 大于100MB
find . -size -1K              # 小于1KB
find . -size +1G -size -10G   # 大于1G且小于10G


示例5：按权限/用户
find . -perm 644              # 权限为644
find . -user root             # 属于root用户
find . -nouser                # 没有有效属主的文件

#温馨提示：未指定路径，按当前文件所处的目录进行搜索。
```





### which 指令



`which` :用于查找命令的可执行文件路径。



```bash
which 命令


示例：

which python
# → /usr/bin/python

which ls
# → /usr/bin/ls
```





### whereis 指令



`whereis` :用于查找命令的二进制文件、源码文件、man手册位置。



```bash
whereis 命令


示例：

whereis python
# → python: /usr/bin/python3 /usr/lib/python3.11 /usr/share/man/man1/python.1.gz

whereis ls
# → ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz /usr/share/man/man1p/ls.1p.gz
```



### alias 指令



`alias`: 用于查看命令 或 给命令创建别名（快捷方式）。



```bash
alias 命令


#示例1：查看所有已定义的别名
alias

#示例2：查看指定别名
alias ll
# → alias ll='ls -alF'
```



```bash
alias 命令 = '别名'


示例：定义别名

alias ll='ls -alF'
alias gs='git status'
alias ..='cd ..'
alias cls='clear'
alias rm='rm -i'        # 删除前确认
alias cp='cp -i'        # 覆盖前确认
alias mv='mv -i'        # 覆盖前确认
```



### unlias 指令



`unlias`: 用于删除已定义的别名。



```bash
unlias


#示例1：删除单个别名
unalias ll
unalias gs

#示例2：删除所有别名
unalias -a

#示例3：指令定义与指令删除

# 先定义
alias gs='git status'

# 使用
gs        # → 执行 git status

# 删除
unalias gs

# 再用就报错
gs        # → command not found
```



### grep 指令



`grep`：在文件中搜索字符串，将找到的行打印出来（Global Regular Expression Print）。



```bash
grep  "关键词" 文件

示例1：

假设 file1.txt 中包含内容：hello Linux

#1.输入指令
grep "hello" file1.txt

#2.输出结果
hello Linux 


示例2：

假设 file1.txt 中包含内容：hello Linux
假设 file2.txt 中包含内容：hello file
假设 file3.txt 中包含内容：hello world

#1.输入指令
grep "hello" *.txt

#2.输出结果
file1.txt:hello Linux 
file2.txt:hello file
file3.txt:hello world
```





`grep -i`：忽略大小写匹配，字母大写、小写视为完全一致。



```bash
grep -i


示例：

假设 file1.txt 中包含内容：Hello Linux
假设 file2.txt 中包含内容：hello File
假设 file3.txt 中包含内容：Hello World

#1.输入指令
grep -i "hello" *.txt

#2.输出结果
file1.txt:Hello Linux
file2.txt:hello File
file3.txt:Hello World
```





`grep -n` : 显示行号



```bash
grep -n


示例：

假设 file1.txt 中包含一下内容：
Hello Linux1
hello Linux2
Hello Linux3

#1.输入指令
grep -n "hello" file1.txt

#输出结果
2:hello Linux2
```





`grep -v`:  反选（显示不含关键词的行)



```bash
grep -v


示例：

假设 file1.txt 中包含一下内容：
Hello Linux1
Hello Linux2
Hello C++
Hello World

#1.输入指令
grep -v "Linux" file1.txt 

#2.输出结果
Hello C++
Hello World
```



### zip 指令



`zip`：用于压缩文件为 .zip 格式



```bash
zip  压缩包名.zip 要压缩的文件/目录


示例1：打包单个文件

#1.压缩包名为：file1.zip
zip file1.zip file1.txt

#2.输出
adding: file1.txt (deflated 35%)


示例2：打包多个文件

#1.压缩包名为：file.zip   要压缩的文件名为：file1.txt file2.txt file3.txt
zip file.zip file1.txt file2.txt file3.txt

#2.输出
adding: file1.txt (deflated 35%)
adding: file2.txt (stored 0%)
adding: file3.txt (stored 0%)


示例3：只打包一个空目录，内容丢失

#1.压缩包名为：project.zip  空目录project/
zip project.zip project/

#2.输出
adding: project/ (stored 0%)
```



`zip -r`：压缩目录时包含所有子目录和文件（recursive 递归 ）



```bash
zip -r 压缩包名.zip 目录/

#注：压缩目录必须加 -r，否则只打包一个空壳。

示例1：

project
|-- file1.txt
|-- file2.txt
`-- file3.txt

# 加 -r：完整打包目录及所有内容
zip -r project.zip project/


示例2：

# 压缩当前目录中的所有内容
zip -r archive.zip .
```



### unzip 指令



`unzip`：用于解压 .zip 压缩包



```bash
unzip 压缩包名.zip


示例：

#假设有压缩包archive.zip 
unzip archive.zip                    # 将archive.zip的内容 解压到当前目录
```





`unzip -d`：用于解压 .zip 压缩包到指定路径位置



```bash
unzip 压缩包名.zip -d 指定路径目标目录


示例1：

# 解压到当前目录下的 output 文件夹
unzip archive.zip -d output/


示例2：

# 解压到绝对路径
unzip archive.zip -d /home/user/documents/

示例3：

# 解压到上一级目录
unzip archive.zip -d ../
```



### sz 指令



`sz`: 用于从远程服务器下载文件到本地电脑



```bash
sz 文件


示例1：

sz filename.txt #执行后文件自动下载到本地电脑的默认下载目录


示例2：

sz file1.txt file2.txt file3.txt #下载多个文件到本地电脑
```



### rz 指令



`rz`:用于从本地电脑上传文件到远程服务器。



```bash
rz


示例：

#1.输入指令rz
rz

#2.执行任务
执行后弹出文件选择窗口 → 选择文件 → 自动上传到服务器当前目录。
```





### tar 指令



`tar`:用于打包和压缩文件/目录，是 Linux 下最常用的归档工具（tape archive）。



```bash
tar [选项] 归档包名 要打包的文件/目录


示例1：压缩

tar czf archive.tar.gz /home/project    

#命令：tar  选项：czf     压缩包名：archive.tar.gz     打包的目录：/home/project

#大白话： 把/home/project 打包成 archive.tar.gz 这个文件。

#解释1：选项czf
#1. c → create     创建一个新归档包
#2. z → gzip       用 gzip 算法压缩
#3. f → file       指定输出文件名（后面必须紧跟文件名）

#解释2：archive.tar.gz 
#1. archive为自定义名字
#2. tar为归档模式
#3. gz为压缩算法


示例2：解压

tar xzf archive.tar.gz

#命令：tar  选项：xzf     压缩包名：archive.tar.gz     

#大白话：用 gzip 解压 archive.tar.gz，提取所有文件到当前目录

#解释：选项czf
#1. x → extract     从归档包中提取文件
#2. z → gzip        用 gzip 算法解压
#3. f → file        指定输出文件名（后面必须紧跟文件名）
```



| **选项** |                 **含义**                 | **记忆**  |
| :------: | :--------------------------------------: | :-------: |
|   `-c`   |               create 创建                |  create   |
|   `-x`   |               extract 解压               |  extract  |
|   `-z`   | zip 用 gzip 压缩/解压 （后缀为.tar.gz ） |   gzip    |
|   `-j`   | zip 用 bzip2 压缩/解压（后缀为.tar.bz2） |   bzip2   |
|   `-f`   |             file 指定文件名              |   file    |
|   `-v`   |             verbose 显示过程             |  verbose  |
|   `-t`   |              test 列出内容               |   list    |
|   `-C`   |              解压到指定目录              | directory |



### bc 指令



`bc`:用于浮点数计算



### uname 指令



`uname`:用来获取系统和内核信息 (Unix Name)



> uname            # 输出内核名称
>
> uname -a         # 输出所有信息（最常用）



## 2.Linux 常用热键



### 2.1 终端控制



| **热键**     | **功能**                               |
| ------------ | -------------------------------------- |
| **Ctrl + C** | 强制终止当前进程                       |
| **Ctrl + Z** | 将当前进程挂起到后台（可用 `fg` 恢复） |
| **Ctrl + D** | 发送 EOF，退出当前 shell / 关闭输入    |
| **Ctrl + L** | 清屏（等同 `clear`）                   |
| **Ctrl + S** | 暂停终端输出                           |
| **Ctrl + Q** | 恢复终端输出                           |



### 2.2 命令行编辑



| **热键**     | **功能**                       |
| ------------ | ------------------------------ |
| **Ctrl + A** | 光标跳到行首                   |
| **Ctrl + E** | 光标跳到行尾                   |
| **Ctrl + U** | 删除光标前所有字符             |
| **Ctrl + K** | 删除光标后所有字符             |
| **Ctrl + W** | 删除光标前一个单词             |
| **Ctrl + Y** | 粘贴 **Ctrl+U/K/W** 删除的内容 |
| **Ctrl + R** | 反向搜索命令历史               |
| **Ctrl + T** | 交换光标前两个字符             |
| **Alt + B**  | 光标向后移动一个单词           |
| **Alt + F**  | 光标向前移动一个单词           |



### 2.3 进程管理



| **热键**                 | **功能**                     |
| ------------------------ | ---------------------------- |
| **Ctrl + C**             | 终止前台进程                 |
| **Ctrl + Z 接着输入 bg** | 挂起并放到后台运行           |
| **Ctrl + Z 接着输入 fg** | 挂起后恢复前台运行           |
| **Ctrl + \\**            | 发送 SIGQUIT，生成 core dump |



### 2.4 常用系统操作



| **热键**    | **功能**                   |
| ----------- | -------------------------- |
| **Tab**     | 命令/路径自动补全          |
| **Tab Tab** | 列出所有可补全项           |
| **↑ / ↓**   | 浏览命令历史               |
| **!!**      | 重复执行上一条命令         |
| **!$**      | 引用上条命令的最后一个参数 |
| **history** | 查看命令历史               |



### 2.5 虚拟终端（TTY）



| **热键**               | **功能**     |
| ---------------------- | ------------ |
| **Ctrl + Alt + F1~F6** | 切换虚拟终端 |
| **Ctrl + Alt + F7**    | 返回图形界面 |
| **Alt + F4**           | 关闭当前窗口 |
| **Alt + Tab**          | 切换窗口     |





## 3.Linux 权限



### 3.1 权限的简介



#### 3.1.1 基本权限模型



Linux 中每个文件和目录都有三组权限，分别对应三类用户：



|  用户类别  |                   含义                   |
| :--------: | :--------------------------------------: |
| Owner (u)  |                文件所有者                |
| Group (g)  |                文件所属组                |
| Others (o) | 其他用户（非文件所有者，且非文件所属组） |



 每组权限包含三种操作：



|    权限     |  对文件的含义  |              对目录的含义               |
| :---------: | :------------: | :-------------------------------------: |
|  r (read)   | 可读取文件内容 |        可列出目录内容 (ls、cat)         |
|  w (write)  | 可修改文件内容 | 可在目录中创建/删除文件（mkdir、touch） |
| x (execute) | 可作为程序执行 |             可进入目录 (cd)             |



文件类型包含如下：



① `-` (普通文件 Regular File)

不管是 C++/Java 源代码、编译出来的可执行程序、还是压缩包、图片、纯文本，在 Linux 眼里都是普通文件。



②`d` (目录 Directory)

目录即文件夹，在 Linux 的底层逻辑中，目录本质上也是一种文件。



③`l` (软链接 Symbolic Link)

类似于 Windows 下的快捷方式。



④`b` (块设备 Block Device)

代表提供缓冲访问的存储介质，数据是以“数据块”（通常是 512 字节或 4KB）为单位进行读写的。

典型例子：硬盘、U 盘



⑤`c` (字符设备 Character Device)

代表提供无缓冲直接访问的外设，数据是按照“字符”（字节）顺序，一个接一个地进行传输的。

典型例子：键盘、鼠标、屏幕终端



⑥`p` (管道文件 Named Pipe / FIFO)

主要用于本地程序之间的数据传递。

可以认为是一根水管：一个进程往管道的一端写数据，另一个进程从管道的另一端读数据，数据遵循“先进先出”（FIFO）的原则。



⑦`s` (套接字文件 Socket)

这是一种非常强大的通信机制，通常用于网络通信或本地高速进程间通信。



#### 3.1.2 查看权限



```bash
#1.输入指令
ls -l example.txt
	
#2.输出结果：
-rw-r--r-- 1 alice developers 1024  5月 12 18:21 example.txt

#3.解释第一列输出的10 个字符 (-rw-r--r--)

A.第一个字符 - ： 代表文件类型

B.后面9个字符每3个字符分为一组

  第一组：rw-  #代表文件所有者的权限
  第二组：r-x  #代表文件所属组的权限
  第三组：r--  #代表其他用户的权限
  
#注：
#文件/目录 可读用r表示 ，不可读用-表示
#文件/目录 可读用w表示 ，不可写用-表示
#文件/目录 可读用x表示 ，不可执行用-表示

#4. 解释 alice developers

A.拥有者： alice 代表文件的所有者（文件的创建者）
B.所属组： developers 代表文件的所属组（类似于管理文件的组长）
```



#### 3.1.3  权限数字表示法 



##### A. 数字表示法原理



`r / -`  中 r 可以被认为是二进制中的 1，- 可以被认为是二进制中的 0，所以权限可以用数字来表示



每组权限用三个字符来表示，所以可以用3个二进制数字来表示。



> 例如：rwx 可以被认为是 111
>
> 因为 r = 4, w = 2, x = 1 ，所以rwx可以被认为是4+2+1 = 7 。





##### B.**权限缺省**



默认文件权限数字表示为：666

```bash
[hamber@VM-0-14-centos lesson4]$ touch test.txt
[hamber@VM-0-14-centos lesson4]$ ls -l
total 0
-rw-rw-r-- 1 hamber hamber 0 Jun  1 17:10 test.txt
```



默认目录权限数字表示为：775

```bash
[hamber@VM-0-14-centos lesson4]$ mkdir mydir
[hamber@VM-0-14-centos lesson4]$ ls -l
total 4
drwxrwxr-x 2 hamber hamber 4096 Jun  1 17:12 mydir
```



**为什么默认文件权限 和 默认目录权限是这样？**



① 对普通文件而言：起始权限为 666（rw-rw-rw-）,默认不带可执行

② 对目录文件而言：起始权限为 777 (rwxrwxrwx) , 默认携带x

③ 系统默认会出现权限掩码umask，最终权限 = 起始权限 & (~ umask)



##### C.权限掩码



权限掩码的查询：



```bash
[hamber@VM-0-14-centos lesson4]$ umask
0002
```



验证公式： `最终权限 = 起始权限 & (~ umask)`



```bash
验证普通文件的默认权限：rw-rw-rw-

  110110110
& 111111101  -> ~(000 000 010)
-------------------
  110110110 -> 666(rw-rw-rw-)



验证目录文件的默认权限：rwxrwxr-x

  111111111
& 111111101  -> ~(000 000 010)
-------------------
  111111101 -> 775(rwxrwxr-x)


#注：计算时，需忽视umask掩码0002 的第一项0
```





**问题一：umask的目的是什么？**



根据公式：最终权限 = 起始权限 & (~ umask) ，告诉系统创建文件/目录时，哪些权限要被"屏蔽掉"。



**问题二：为什么不直接硬编码"文件默认 644，目录默认 755"?**



原因是不同场景需要不同的默认策略，umask 让这个策略可配置，而不是一刀切。





### 3.2 文件访问权限的更改



#### 3.2.1 chmod指令



`chmod`:设置文件的访问权限



```bash
chmod [who][op][perm] file


who  取值:  u / g / o / a  （所有者 / 组 / 其他人 / 全部）

op   取值： + / - / =  （添加 / 移除 / 设置为 ）

perm 取值： r / w / x （读 / 写 / 执行）
```



```bash
#实操演示1：

#操作前：
[hamber@VM-0-14-centos lesson4]$ ll
-r--rw-r-- 1 hamber hamber    0 Jun  1 17:10 test.txt

#输入指令：让u（文件所有者）添加w（写）权限
[hamber@VM-0-14-centos lesson4]$ chmod u+w test.txt 

#操作后：
[hamber@VM-0-14-centos lesson4]$ ll
-rw-rw-r-- 1 hamber hamber    0 Jun  1 17:10 test.txt
```



```bash
#实操演示2：

#操作前：
[hamber@VM-0-14-centos lesson4]$ ll
-rw-rw-r-- 1 hamber hamber    0 Jun  1 17:10 test.txt

#输入指令：让g（文件所属组）去掉w（写）权限
[hamber@VM-0-14-centos lesson4]$ chmod g-w test.txt 

#操作后：
[hamber@VM-0-14-centos lesson4]$ ll
-rw-r--r-- 1 hamber hamber    0 Jun  1 17:10 test.txt
```



```bash
#实操演示3：

#操作前：
[hamber@VM-0-14-centos lesson4]$ ll
-rw-rw-r-- 1 hamber hamber    0 Jun  1 18:48 test.txt

#输入指令：让u赋予空权限
[hamber@VM-0-14-centos lesson4]$ chmod u= test.txt 

#操作后：
[hamber@VM-0-14-centos lesson4]$ ll
----rw-r-- 1 hamber hamber    0 Jun  1 18:48 test.txt

#注：u= 默认让权限为空
```



```bash
#实操演示4：

#操作前
[hamber@VM-0-14-centos lesson4]$ ll
----rw-r-- 1 hamber hamber    0 Jun  1 18:48 test.txt

#输入指令：让u赋予rw权限
[hamber@VM-0-14-centos lesson4]$ chmod u=rw test.txt 

#操作后：
[hamber@VM-0-14-centos lesson4]$ ll
-rw-rw-r-- 1 hamber hamber    0 Jun  1 18:48 test.txt
```



#### 3.2.2 chown指令



`chown`:修改所有者



```bash
chown [选项] [所有者][:组] 文件


注意事项

1. 只有 root 能改所有者：普通用户不能把文件的所有者改成别人
2. 普通用户能改组：前提是你是文件所有者，且目标组是你所属的组


[常见用法]：

# 只改所有者
chown alice file.txt

# 只改组（冒号前留空）
chown :developers file.txt

# 同时改所有者和组
chown alice:developers file.txt

# 递归修改目录及内容
chown -R alice:developers /project
```



```bash
实例演示：

# 查看当前文件信息
ls -l file.txt
-rw-r--r-- 1 root root 1024 Jun 1 file.txt

# 改所有者为 alice
chown alice file.txt
-rw-r--r-- 1 alice root 1024 Jun 1 file.txt

# 改组为 developers
chown :developers file.txt
-rw-r--r-- 1 alice developers 1024 Jun 1 file.txt
```



#### 3.2.3 chgrp指令



`chgrp`:修改文件所属组



```bash
chgrp [选项] 组 文件


常见用法

# 修改文件所属组
chgrp developers file.txt

# 递归修改目录
chgrp -R developers /project

# 显示修改过程
chgrp -v developers file.txt

# 只在有更改时显示
chgrp -c developers file.txt
```



### 3.3 用户的切换



#### 3.3.1 su指令 



`su`:切换用户



```bash
su — 用户名


# 切换到 root（需要 root 密码）
su

# 切换到 root 并加载其环境变量
su -

# 切换到指定用户
su alice

# 切换到指定用户并加载环境变量
su - alice
```



#### 3.3.2 sudo指令 



`sudo`:以 root 身份执行命令



```bash
sudo 指令

常见用法

# 以 root 身份执行命令
sudo apt update

# 以 root 身份编辑文件
sudo vim /etc/hosts

# 查看当前 sudo 权限
sudo -l
```



```bash
实例演示

# 安装软件
sudo apt install vim

# 重启服务
sudo systemctl restart nginx

# 修改系统配置
sudo vim /etc/fstab

# 查看受保护的日志
sudo tail -f /var/log/auth.log

# 以其他用户身份执行
sudo -u postgres psql
```



sudoers 配置



```bash
# 安全编辑 sudoers 文件
sudo visudo

配置格式

# 语法
用户 主机=(身份) 命令选项


1.用户 ：指定谁可以使用 sudo。

#单个用户
Alice ALL=(ALL) ALL

# 用户组（加 %）
%developers ALL=(ALL) ALL

#所有用户
ALL ALL=(ALL) ALL


2.主机：指定在哪些机器上生效。

# 本机生效
alice localhost=(ALL) ALL

# 指定主机名
alice webserver=(ALL) ALL

# 所有主机（最常用）
alice ALL=(ALL) ALL


3.身份：指定以谁的身份执行命令。

# 以 root 身份执行（最常见）
alice ALL=(root) ALL

# 以任意用户身份执行
alice ALL=(ALL) ALL

# 以指定用户身份执行
alice ALL=(postgres) ALL

# 以指定用户和组身份执行
alice ALL=(postgres:postgres) ALL


4.命令选项：指定能执行哪些命令。

# 所有命令
alice ALL=(ALL) ALL

# 只能执行 systemctl
alice ALL=(ALL) /usr/bin/systemctl

# 多个命令用逗号分隔
alice ALL=(ALL) /usr/bin/systemctl, /usr/bin/journalctl

# 只能执行特定子命令
alice ALL=(ALL) /usr/bin/systemctl restart nginx, /usr/bin/systemctl status nginx

# 命令别名
Cmnd_Alias SERVICES = /usr/bin/systemctl restart nginx, /usr/bin/systemctl restart php
alice ALL=(ALL) SERVICES

# 禁止执行某个命令（前面加 !）
alice ALL=(ALL) ALL, !/usr/bin/passwd root
```



```bash
常见配置示例

# 用户 alice 可以执行所有命令
alice ALL=(ALL) ALL

# 用户 bob 只能执行 systemctl
bob ALL=(ALL) /usr/bin/systemctl

# 用户 carol 执行所有命令不需要密码
carol ALL=(ALL) NOPASSWD: ALL

# 开发组可以重启应用服务，免密
%developers ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart app

# 允许用户在指定主机上执行
alice webserver=(ALL) /usr/bin/systemctl restart nginx

字段解释

alice   ALL=(ALL:ALL)   ALL
│       │   │           │
│       │   │           └── 允许执行的命令
│       │   └── 以什么用户:组身份执行
│       └── 在哪些主机上生效
└── 目标用户
```





sudo 密码缓存机制



```bash
# 第一次执行需要输密码
sudo whoami
[sudo] password for alice: ****
root

# 15分钟内再次执行不需要密码
sudo whoami
root（无需输入密码）

# 手动清除缓存
sudo -k

# 下次执行又要输密码
sudo whoami
[sudo] password for alice: ****
```





### 3.4 目录权限的理解



rwx 对目录权限的含义



① r  ：可以列出目录内容  (若失去r权限，无法 ls)

② w ：可以在目录中创建、删除、重命名文件 （若失去w权限，无法 touch、rm、mv）        

③ x ： 可以进入目录、访问目录下文件的元信息 （若失去x权限，无法 cd、无法查看文件属性)



> 由此可知，对于一个文件是否能够删除，不取决于文件的权限，反而取决于文件所处目录的权限。



### 3.5 多用户隔离的理解



核心思想： Linux 从设计之初就是多用户系统。一台机器可以同时被多人使用，但每个人只能看到和操作自己的东西。



文件系统隔离




```bash
家目录隔离

# 每个用户有自己的家目录

/home/alice  → 只有 alice 能访问

/home/bob   → 只有 bob 能访问

/root     → 只有 root 能访问


核心原因：权限实现隔离

# 查看家目录权限

ls -ld /home/alice /home/bob

# drwx------ alice alice /home/alice    ← 只有自己能访问

# drwx------ bob   bob   /home/bob
```





### 3.6 Linux 权限匹配原则



核心原则： 当用户访问文件时，Linux 按固定顺序检查权限，只使用第一个匹配的规则。



匹配顺序：访问请求 → 是 root？→ 是所有者？→ 在组里？→ 其他人？→ 允许/拒绝



> 注：若用户既是文件的所有者，又是文件的所属组
>
> ​        Linux系统也只会匹配一次权限，给予用户文件所有者的权限，不会向下继续匹配。



特殊用户：root 用户不受权限约束

`root`拥有绝对的特权，无论文件权限怎么设置（哪怕是 `----------`），`root` 都可以随意读写。



```bash
实战演示：

# 文件信息
ls -l file.txt
# -rw-r----- 1 alice developers 4096 Jun 1 file.txt

#按照匹配顺序进行匹配
当 bob 访问这个文件时，Linux 依次检查：

1. bob 是 root 吗？          → 不是，跳过
2. bob 是文件所有者吗？       → 不是（alice 才是），跳过
3. bob 在 developers 组里吗？ → 如果在，使用组权限 (r--)，匹配结束
4. 以上都不是                → 使用其他人权限 (---)

关键：一旦匹配到某个分类，就不再往下检查。
```



### 3.7 粘滞位



**粘滞位**：是一种特殊权限位，用于目录，使目录中的文件只有文件所有者、目录所有者或 root 才能删除。



**粘滞位权限的标志位**：t

```bash
drwxrwxrwt.   4 root root  4096 Jun  1 20:07 tmp
```



**粘滞位设计的背景**: 



若需要进行团队合作进行共享目录，不能将目录放于任何一个人的私人账号下，需要使用根目录下的文件进行共享。

对于共享目录而言，任何用户都有rwx的权限，所有人均可删除共享文件，但如果设置为rw-，则用户不能够创建文件

这就导致了"目录可写"和"文件不可删"之间的矛盾。为了只能够让目录中文件的所有者删除，从而设计了粘滞位。





```
场景1：临时文件目录

# /tmp 需要：
#  所有程序都能写入临时文件
#  不能删别人的临时文件
#  程序崩溃时文件还在，方便调试

drwxrwxrwt /tmp
```



```
场景2：团队共享目录

# 团队共享上传目录
# - 每个人都能上传文件
# - 不能删别人的成果
drwxrwxrwt /shared/uploads
```



## 4.Linux相关知识点



### 4.1 Linux文件



```bash
文件本质：a

文件 = 内容 + 属性

内容包括：文本内容
属性包括：文件名称，文件的大小，文件的类型，文件的权限，文件修改的时间...

学习文件：

1.对文件内容操作

2.对文件属性操作
```



```bash
Linux系统中，一切皆文件

普通文件    → 文件
硬盘       → /dev/sda      → 文件
键盘       → /dev/tty      → 文件（读）
显示器     → /dev/tty      → 文件（写）
进程信息   → /proc/pid/*   → 文件
网络       → socket        → 文件描述符
```





### 4.2 Linux下的路径信息



```bash
#知识点1

在windows操作系统中：\表示路径分隔符

在Linux操作系统中：/表示路径分隔符

举例说明：

对于Linux的路径：/home/user/Documents/project

例如：/home/被操作符/ /所包裹的一定表示目录（文件夹） ，而最后一个project 则不能确定其为目录（文件夹）还是为文件
	
#解释：

/ (第一个斜杠): 代表 Linux 系统的根目录 

home/: 这是根目录下的一个标准文件夹。
	
user/: 这是 home 目录下的一个子文件夹，代表一个具体的用户主目录。	
	
Documents/: 这是 user 目录下的一个常见默认文件夹

project: 这是路径的终点。
```



```bash
#知识点2

在Linux中的路径，对于只有单个/，表示Linux的根目录 
```



```bash
#知识点3

ls + 路径：能够查看指定目录下的文件和文件夹的名称

例如，如下命令所示：ls /home/user/Documents/project
```



```bash
#知识点4

A.绝对路径：绝对路径是指从文件系统的根目录（/）开始，完整描述到达目标文件或目录所需要经过的所有层级的路径。

1.核心特征： 永远以正斜杠 /（代表根目录）开头。

2.生活类比：中国 -> 北京市 -> 朝阳区 -> 某某路 -> 某某号

3.使用场景：在配置文件中使用


B.相对路径：相对路径是指从你当前所在的工作目录出发，到达目标文件或目录的路径。

1.核心特征： 不以斜杠 / 开头。它的起点是你当前的位置。

2.生活类比：基于当前位置指路：“出门左转，第二个路口右转”。

3.使用场景：在日常中使用
```



```bash
#知识点5

对于任何一个Linux用户，用特定的用户名登录系统，默认在 "家目录" 下

家目录（主目录）的符号：~
```



### 4.3 Linux的文件结构



```
Linux 的文件结构是一棵从根目录开始的多叉树

a. 叶子结点：一定是 “普通文件” 或 "空目录"

b. 非叶子结点： 一定是“非空目录”
```



Linux文件图示

![Linux文件图示](C:\Users\30398\AppData\Roaming\Typora\typora-user-images\image-20260513100351976.png)



### 4.4 Linux命令的本质



```bash
A. 可执行文件（外部命令）

简介：

例如：tree、ls、cat、mkdir、python 等，它们的本质与 Windows 上双击运行的 .exe 游戏或软件，或者手机上的 App 没有任何区别。

1.本质：它们是实实在在存储在硬盘上的文件（通常是编译好的二进制程序，或者是带执行权限的脚本）。

2.存储位置：它们通常集中存放在系统的特定目录中，比如 /bin、/usr/bin、/sbin 等。

3.安装：实际上就是从网盘（软件源）下载这个文件并放进系统的可执行目录。
```



```bash
B. Shell 的内置功能（内置命令）

简介：

它们没有对应的实体文件存放在硬盘上，它们是 Shell 或 bash 自身随身携带的技能。

1.代表命令：cd（切换目录）、pwd（显示当前路径）、echo（打印输出）。

2.内置原因：以 cd 为例，如果它是一个外部程序，当它运行完毕后，改变的只是它那个子程序的运行目录，而你当前的 Shell 终端目录并不会变。所以，像改变终端自身状态的操作，必须由终端程序自己（内置）来完成。

3.生活类比：

外部命令就像是“锤子”、“剪刀”（你需要去工具箱里拿出来用）；

内置命令就像是你自己的“手”、“脚”（长在你身上，大脑直接调用）。
```



```bash
C. 别名（Alias） 

简介：

这纯粹是你或者系统设置的“快捷方式”或“小名”。

1.代表命令：在很多系统中，你敲入 ll，会显示详细的文件列表，但系统里其实并没有一个叫 ll 的程序。
	
2.本质： 它只是 ls -alF（或类似参数）的别名，在敲 ll 时，Shell 在执行前会自动把它替换成那一长串真实的命令。
```





2.5 Linux 下的打包和压缩



```
打包：把多个文件/目录合并成一个文件，方便管理，但大小不变

作用：防止文件丢失


压缩：对文件进行算法压缩

作用：减小文件体积，节省存储空间，在网络传送中，可以有效的减少网络传输时间
```





### 4.5 Linux重定向操作 



一、 基本概念： Linux 中默认有三个标准文件描述符



| 文件描述符 | 名称   | 含义   | 默认去哪 | 干什么         |
| ---------- | ------ | ------ | -------- | -------------- |
| 0          | stdin  | 输入流 | 键盘     | 程序读数据     |
| 1          | stdout | 输出流 | 屏幕     | 程序写正常结果 |
| 2          | stderr | 错误流 | 屏幕     | 程序写错误信息 |



把一个 Linux 命令想象成一个工厂车间：

![image-20260602231100390](C:\Users\30398\AppData\Roaming\Typora\typora-user-images\image-20260602231100390.png)



- stdin (0) — 工厂的原材料入口，程序从这里读取数据

- stdout (1) — 工厂的合格品出口，程序的正常输出从这里出来
- stderr (2) — 工厂的废品出口，错误信息和警告从这里出来





二、输入重定向



```bash
stdin（文件描述符 0）— 标准输入（程序读取数据的来源，默认是键盘。）

#从文件读入数据，
command（相应指令） < file


示例A：stdin 默认从键盘读取

#read 命令从 stdin 读取你敲的键盘输入
[hamber@VM-0-14-centos ~]$ read name
zhangsan
[hamber@VM-0-14-centos ~]$ echo $name
zhangsan

示例B：从文件读取

stdin 可以被替换，比如从文件读取：

# 不再从键盘读，而是从文件读
read name < user.txt
```





三、输出重定向



```bash
stdout（文件描述符 1）— 标准输出 （程序正常输出的地方，默认是屏幕（终端））

# 覆盖写入文件
command > file

# 追加写入文件
command >> file

# 将标准错误重定向到文件
command 2> error.log



示例：

ls /home > list.txt          # 输出写入文件，而非屏幕

echo "hello" >> log.txt      # 追加，不覆盖

gcc main.c 2> errors.txt     # 编译错误写入文件
```

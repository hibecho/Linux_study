# 1.进程





## 1.1 进程的概念





基本定义： 进程是程序的一次执行过程



> 1.程序是静态的代码文件（放在磁盘上）
>
> 2.进程是动态的执行实体（跑在内存中）

  

```bash
场景描述：

假设你使用 C++ 编写了 HelloWorld.cpp，编译成可执行文件保存在硬盘上时，它只是一个静态的可执行文件——这就是程序（Program）。

当你双击运行它，或者在命令行输入命令执行它时：

1. 操作系统会把这个程序从硬盘加载到内存中。

2. 操作系统会为它分配独立的内存空间

3. CPU处理器开始逐条执行这个程序里的指令

这个拥有独立内存空间、正在被 CPU 执行的“动态实体”，就变成了一个进程（Process）。
```



如下所示：进程实例图

<img src="C:\Users\30398\AppData\Roaming\Typora\typora-user-images\image-20260612195608515.png" alt="image-20260612195608515" style="zoom:50%;" />





简单理解进程：**进程** **=** **" 内核数据结构对象（即进程控制块 PCB） +  程序自身的代码与数据 "** 



```bash
理解进程：厨房做菜场景


程序 ：抽屉里的菜谱

数据 ：调料与食材

PCB ：厨师的操作笔记/点菜单

CPU ：负责干活的厨师

内存 ：厨师的工作台



进程 = 做菜的整个过程

1. 开始做菜之前，抽屉里（磁盘）：红烧肉菜谱（代码） --- 这时候没有进程，菜谱只是菜谱，没人动它。

2. 客人点了红烧肉 → 创建进程

3. 厨师（CPU）从抽屉里拿出菜谱（加载到内存）

4. 在工作台（内存）上摆好食材和调料（数据）

5. 拿出一张点菜单（PCB），写上：
    - 做什么菜：红烧肉
    - 当前进度：第 0 步
    - 用了哪些食材：五花肉、酱油、冰糖...

从这一刻起，"做菜的过程"开始了 —— 这就是进程
```





Linux 真实的进程结构：



```bash
  进程（Linux task_struct 描述）
  |
  ├── 元信息（task_struct / PCB）
  │   ├── 进程状态、优先级、pid
  │   ├── 内存描述符 mm_struct（指向页表等）
  │   ├── 文件描述符表
  │   ├── 信号处理表
  │   ├── 凭证（uid/gid）
  │   ├── 命名空间（namespace）
  │   └── ……（共约 700+ 字段）
  │
  ├── 虚拟地址空间（用户态）
  │   ├── 代码段 .text
  │   ├── 数据段 .data / .bss
  │   ├── 堆 heap（malloc 区域）
  │   ├── mmap 区（共享库、文件映射）
  │   └──  用户栈 user stack ← 用户态函数调用用
  │
  └── 内核态资源
      ├── 页表（CR3 寄存器指向）
      └──  内核栈 kernel stack ← 内核态函数调用用
```







```
用户栈（User Stack）


进程虚拟地址空间（从低地址到高地址）：

  0x00000000 ─────────────────────
              代码段 .text
              数据段 .data
              堆 heap    ← malloc 在这
              mmap 区    ← 共享库
              ...
              用户栈    ← RSP_user 指向这里
  0x7fffffff ─────────────────────  ← 通常顶端约 0x7fff_ffff_ffff


大小

  - 默认 8MB
  - 可通过 ulimit -s 调整
  - 可被换出到磁盘（swap）
```





## 1.2 管理进程





### 1.2.1 描述进程 - PCB



 在 Linux 中，PCB 的实现是 `task_struct` 结构体（定义在 `<linux/sched.h>`），它是内核中描述一个进程所有信息的结构体：



```c
struct task_struct 
{
    volatile long state;        // 进程状态
    pid_t pid;                  // 进程 ID
    pid_t tgid;                 // 线程组 ID
    struct task_struct *parent; // 父进程
    struct fs_struct *fs;       // 文件系统相关信息
    struct mm_struct *mm;       // 内存管理
    struct files_struct *files; // 打开的文件
    // ... 几百个字段
};
```



### 1.2.2 组织进程 



**Linux 链表结点的设计：**结点不包含数据，统一将数据定义到**task_struct**中。



```C
struct list_head 
{
    struct list_head *next, *prev;
};
```



**教科书链表结点的设计：节点包含数据**



```C
struct Node 
{
    int data;           // 数据
    struct Node *next;  // 指针
};
```



**Linux中的task_struct ：嵌入结构体list_head**



```C
// 调度器中的实际用法 (简化版 task_struct)
struct task_struct 
{
    pid_t pid;                  // 进程ID
    int prio;                   // 优先级
    // ... 几百个其他字段 ...
    
    // 【关键】把 list_head 作为结构体的一个成员嵌入进去
    struct list_head run_list;  
};
```





**Linux设计该结构体的优势：**



①如果内核用教科书的方式，就必须为每种数据定义一种链表（运行队列链表，阻塞队列链表...），这会导致代码极度冗余，而是统一的将描述进程的数据放入task_struct中，在task_struct内部定义结构体成员。



② 内核把 `list_head` 当作一个“通用连接件”，直接嵌入到需要被链接的结构体内部，实现了链接与数据访问的分离。

`run_list` 就是车厢上的“连接钩”，而`task_struct` 才是真正的“车厢”，车厢里装有真正的数据。





**在Linux中 如何访问链接的结构体数据段呢 ？--以run_list为例说明**



**a. 核心逻辑：**我们通过`struct list_head *` 类型的指针访问到的是 `run_list` 的地址，但是我们需要访问的数据段在task_struct中，需要拿到task_struct（进程控制块）的地址。



**b. Linux内核实现：**利用 C 语言的指针偏移计算，内核定义了`offsetof 和 container_of `。



```C
// 获取结构体中某个成员的偏移量
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)


// 核心魔法：container_of
#define container_of(ptr, type, member) ({                      \
    const typeof(((type *)0)->member) *__mptr = (ptr);          \
    (type *)((char *)__mptr - offsetof(type, member));          \
})
```



代码解释1：` offsetof`



```C
/**
 * offsetof - 获取结构体成员在结构体内的字节偏移量
 * @TYPE:  目标结构体的类型名称（例如 struct task_struct）
 * @MEMBER: 结构体中的成员名称（例如 pid）
 *
 * 该宏在编译期计算 @MEMBER 成员在 @TYPE 结构体中的偏移量（以字节为单位）。
 * 它利用空指针（地址 0）进行地址运算，但不会引发运行时访问错误，
 * 因为整个表达式在编译阶段被编译器解析为常量。
 *
 * 此宏是内核中实现指针逆向转换（container_of）的基础工具。
 *
 * Return: 返回 @MEMBER 在 @TYPE 中的字节偏移量，类型为 size_t。
 */

#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)
```



代码解释2：`container_of`



```C
/**
 * container_of - 通过结构体成员指针反推整个结构体的起始指针
 * @ptr:   指向结构体成员 @member 的指针
 * @type:  目标结构体的类型名称（例如 struct task_struct）
 * @member: 结构体中与 @ptr 对应的成员名称（例如 run_list）
 *
 * 该宏利用 @ptr 指向的成员地址，减去该成员在结构体中的偏移量，
 * 从而获得包含该成员的结构体变量的首地址。
 *
 * 内部实现包含类型安全检查：如果 @ptr 的类型与 @member 的类型不匹配，
 * 编译器会给出警告或错误，提高了代码的安全性。
 *
 * 该宏是内核中链表操作（list_entry）、设备驱动私有数据获取等场景的核心工具。
 *
 * Return: 返回 @type 类型的结构体指针，即包含 @member 的完整结构体地址。
 */

#define container_of(ptr, type, member) ({                      \
    const typeof(((type *)0)->member) *__mptr = (ptr);          \
    (type *)((char *)__mptr - offsetof(type, member));          \
})
```



**c.通过内存分布解释**



```C
container_of(node,struct task_struct,run_list); //node为指向run_list的指针


内存地址:   0x1000          0x1008          0x1050 (假设 run_list 偏移量为 0x50)
           ┌───────────────┬───────────────┬───────────────┐
           │   pid (4B)    │   prio (4B)   │   run_list    │ ...
           └───────────────┴───────────────┴───────────────┘
           ▲                               ▲
           │                               │
      task_struct 起始地址            	 	ptr
```





### 1.2.3 进程用户栈和内核栈



#### A. 用户栈



```bash
1.进程用户栈存放位置：

进程虚拟地址空间（从低地址到高地址）：

  0x00000000 ─────────────────────
              代码段 .text
              数据段 .data
              堆 heap    ← malloc 在这
              mmap 区    ← 共享库
              ...
              用户栈    ← RSP_user 指向这里
  0x7fffffff ─────────────────────  ← 通常顶端约 0x7fff_ffff_ffff


2.进程用户栈大小

  - 默认 8MB
  - 可通过 ulimit -s 调整
  - 可被换出到磁盘（swap）


3.进程用户栈增长方向

  -向下增长（从高地址往低地址压栈）


4.进程用户栈存什么

  ┌─ 用户栈顶 ────────────────────┐
  │  main() 的栈帧                │
  │  ├─ 返回地址                  │
  │  ├─ 局部变量                  │
  │  └─ callee-saved 寄存器       │
  │  func_a() 的栈帧              │
  │  ├─ 返回地址                  │
  │  ├─ 局部变量                  │
  │  └─ callee-saved 寄存器       │
  │  func_b() 的栈帧              │
  │  ├─ 返回地址                  │
  │  ├─ 局部变量                  │
  │  └─ callee-saved 寄存器       │
  └───────────────────────────────┘


5.进程用户栈被谁使用

  - 用户态 C / C++ / Go / Rust 等函数调用
  - printf("hello")、int x = func(1, 2) 这些用户代码
```



#### B. 内核栈



```
1.进程内核栈存放位置：

不在进程的虚拟地址空间里，而是在内核虚拟地址空间中，每个进程独立一块：

  内核虚拟地址空间：
  0xffff_8000_0000_0000 ─────────────────
                                       ↑
                                       每个进程 8KB（thread_info + 栈）
                                       进程 P1 内核栈
                                       进程 P2 内核栈
                                       进程 P3 内核栈
                                       ...
  0xffff_8000_0000_0000 + n×8KB ──────────


2.进程内核栈的大小

  - 固定 8KB（x86_64 默认，可配置）
  - 不可换出（永远物理内存驻留）


3.进程内核栈的增长方向

  -向下增长（从高地址往低地址压栈）


4.进程内核栈存什么

  ┌─ 内核栈顶 ─────────────────────┐
  │  pt_regs 结构（进入内核时的快照） │  
  │  ├─ RIP_user                  │
  │  ├─ CS_user                   │
  │  ├─ RFLAGS_user               │
  │  ├─ RSP_user                  │
  │  ├─ SS_user                   │
  │  ├─ R15, R14, ..., R8         │
  │  ├─ RDI, RSI, RBP, RBX        │
  │  ├─ R11, R10, R9, R8          │
  │  └─ RAX, RCX, RDX             │
  │                               │
  │  内核函数栈帧                   │
  │  ├─ sys_read() 的栈帧          │
  │  │   ├─ 返回地址               |             
  │  │   ├─ 局部变量               │
  │  │   └─ callee-saved 寄存器    │
  │  ├─ vfs_read() 的栈帧          │
  │  ├─ ext4_file_read_iter() 栈帧 │
  │  └─ ……                        │
  └───────────────────────────────┘
         ↑
         RSP_kernel


5. 进程内核栈谁在用

  - 系统调用处理
  - 中断处理
  - 异常处理
  - 内核线程（无用户态对应物的内核工作线程）
```





## 1.3 Linux中查看进程



### 1.3.1 ps — 进程快照



 `ps`：查看当前终端的进程



```bash
ps ：查看当前终端的进程

#ps 输出示例

PID 	 TTY          TIME       CMD
7572 	pts/1    	 00:00:00    bash
24906 	pts/1   	 00:00:00    ps
```



 `ps aux`：查看所有进程（BSD 风格）



```bash
ps aux：查看所有进程（BSD 风格）


#ps aux 输出各列含义：

USER    PID  %CPU %MEM  VSZ   RSS  TTY  STAT  START  TIME  COMMAND
root     1   0.0  0.1  169k  52k    ?    Ss   Jun01  0:03 /sbin/init


#补充说明：

USER：进程运行用户

PID：进程唯一 ID

%CPU:CPU

%MEM:内存占用率

VSZ / RSS：虚拟内存、实际物理内存（单位 KB）

TTY：关联终端，`?`代表无终端

STAT：进程运行状态

START：进程启动时间

TIME：进程累计占用 CPU 时长

COMMAND：启动该进程的命令
```



> Linux 进程 STAT 状态码速查表
>
> 
>
> 主状态（首位）
>
> 
>
> **S**：可中断睡眠，等待事件唤醒
>
> **R**：运行 / 就绪，正在执行或等待 CPU 调度
>
> **D**：不可中断睡眠，多因 IO 阻塞，无法被信号终止
>
> **Z**：僵尸进程，已退出但父进程未回收
>
> **T**：进程被暂停
>
> ​	
>
> 附加标识（后续位，可组合）
>
> 
>
> **s**：会话领导者
>
> **+**：前台进程组
>
> **l**：多线程进程
>
> **<**：高优先级
>
> **N**：低优先级





`ps aux --sort=-%mem`：   按内存排序



```bash
ps aux --sort=-%mem：按照内存占用率排序所有进程


输出结果如下：

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root     19239  0.6  1.5 1022832 59480 ?       Ssl  Jun22  12:02 /usr/local/qcloud/YunJing/YDEyes/YDService
root     10078  1.4  0.6 960228 24212 ?        Sl   May26 578:53 barad_agent
hamber    1901  0.0  0.5 760976 22316 ?        Sl   Jun11   0:00 /home/hamber/.VimForCpp/cquery/bin/cquery
hamber   11162  0.0  0.5 760972 22148 ?        Sl   Jun01   0:00 /home/hamber/.VimForCpp/cquery/bin/cquery
```





`ps aux --sort=-%cpu` ：  按 CPU 排序



```bash
ps aux --sort=-%cpu：按照CPU占用率排序所有进程


输出结果如下：

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root     10078  1.4  0.6 968424 24236 ?        Sl   May26 578:54 barad_agent
root     19239  0.6  1.5 1022832 59504 ?       Ssl  Jun22  12:02 /usr/local/qcloud/YunJing/YDEyes/YDService
root         1  0.1  0.1 191152  4148 ?        Ss   May12  99:49 /usr/lib/systemd/systemd --switched-root 
root         2  0.0  0.0      0     0 ?        S    May12   0:01 [kthreadd]
```



`ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%cpu | head`  ：以 自定义列 的形式进行输出



```bash
ps -eo pid,ppid,cmd,%cpu,%mem --sort=-%cpu | head


输出结果如下所示：

  PID  PPID CMD                         %CPU %MEM
10078 10076 barad_agent                  1.4  0.6
19239     1 /usr/local/qcloud/YunJing/Y  0.6  1.5
    1     0 /usr/lib/systemd/systemd --  0.1  0.1
    2     0 [kthreadd]                   0.0  0.0
    4     2 [kworker/0:0H]               0.0  0.0
    6     2 [ksoftirqd/0]                0.0  0.0
    7     2 [migration/0]                0.0  0.0
    8     2 [rcu_bh]                     0.0  0.0
    9     2 [rcu_sched]                  0.0  0.0
```





`ps -ef`：查看所有进程 (标准风格)



```bash
ps -ef 

-e：显示所有进程（等价 -A）

-f：完整格式输出（展示 UID、PPID、CMD 等详细字段）


#执行后每行代表一个进程，列依次为：
UID   PID   PPID   C   STIME   TTY   TIME   CMD


#解释进程信息：

UID：进程所属用户 ID / 用户名

PID：进程 ID（唯一标识进程，杀进程用 kill PID）

PPID：父进程 ID（创建当前进程的父进程）

C：CPU 占用百分比（优先级相关）

STIME：进程启动时间

TTY：终端设备，? 表示后台守护进程

TIME：进程占用 CPU 的总时长

CMD：启动进程的完整命令 / 程序
```





`ps -ef | grep [进程名/关键词]`  ： 过滤特定进程



```bash
ps -ef | grep myprocess :输出指定进程信息，不包含标题

输出结果如下：

hamber    9493 14683  0 20:05 pts/1    00:00:00 ./myprocess
hamber   15151 22216  0 20:26 pts/3    00:00:00 grep --color=auto myprocess


[补充如下命令]

ps -ef | head -1 && ps -ef | grep myprocess :输出指定进程信息，包含标题，但会输出grep信息

输出结果如下：

UID        PID  PPID  C STIME TTY          TIME CMD
hamber    9493 14683  0 20:05 pts/1    00:00:00 ./myprocess
hamber   15937 22216  0 20:29 pts/3    00:00:00 grep --color=auto myprocess



ps -ef | head -1 && ps -ef | grep myprocess | grep -v grep :输出指定进程信息，包含标题，不会输出grep信息

输出结果如下：

UID        PID  PPID  C STIME TTY          TIME CMD
hamber    9493 14683  0 20:05 pts/1    00:00:00 ./myprocess
```





 `ps -p <PID> -f` : 由 PID 查看某个进程



```bash
#查看某个Pid为1234 的进程
ps -p 1234 -f 

-p PID：只显示指定 PID 的进程

-f：完整格式（UID/PID/PPID/C/STIME/TTY/TIME/CMD）


#输出示例：

UID       PID   PPID  C STIME TTY        TIME      CMD
hamber   22636  5233  0 19:06 pts/0    00:00:00 ./myprocess
```





### 1.3.2 top/htop — 实时监控



**A. top/htop：实时监控进程**



```bash
top       # 系统自带，实时刷新

htop      # 更好的交互界面（需安装：apt install htop）
```



**B. top：常用快捷键**



```bash
top 常用快捷键：

P：按 CPU 使用率排序

M：按内存使用率排序

k：输入 PID 终止进程

r：修改进程优先级

1：查看各 CPU 核心状态

f：自定义展示字段列

H：显示线程信息

q：退出 top 界面
```





### 1.3.3  proc — 查看进程详细信息



`/proc/[PID]` ：以 [PID] 为目标进程 查看进程详细信息



```bash
相关指令操作：

ls /proc/                      # 每个数字目录对应一个进程


cat /proc/<pid>/status         # 进程状态详情


cat /proc/<pid>/cmdline        # 启动命令


cat /proc/<pid>/environ        # 环境变量


ls -l /proc/<pid>/fd           # 打开的文件描述符


cat /proc/<pid>/maps           # 内存映射


cat /proc/1234/limits		   # 资源限制
```





```bash
示例：以 1234 为目标进程 PID：


#每个数字目录对应一个进程
ls /proc/

# 进程状态详情
cat /proc/1234/status

# 环境变量
cat /proc/1234/environ

# 打开的文件描述符
ls -l /proc/1234/fd/

# 内存映射
cat /proc/1234/maps

# 资源限制
cat /proc/1234/limits
```





### 1.3.4  pstree -进程树



`pstree` ：以树状显示进程关系



```bash
pstree


输出：

systemd─┬─YDLive───9*[{YDLive}]
        ├─YDService───22*[{YDService}]
        ├─acpid
        ├─2*[agetty]
        ├─atd
        ├─auditd───{auditd}
        ├─barad_agent─┬─barad_agent
        │             └─barad_agent───3*[{barad_agent}]
        ├─crond
        ├─dbus-daemon
        ├─dhclient
        ├─iscsid
        ├─2*[languageclient─┬─querydb───7*[{querydb}]]
        │                   └─3*[{languageclient}]]
        ├─3*[languageclient───2*[{languageclient}]]
        ├─languageclient─┬─querydb
        │                └─2*[{languageclient}]
        ├─lsmd
        ├─lvmetad
        ├─master─┬─pickup
        │        └─qmgr
        ├─ntpd
        ├─6*[nvim───{nvim}]
        ├─polkitd───6*[{polkitd}]
        ├─rshim───{rshim}
        ├─rsyslogd───2*[{rsyslogd}]
        ├─sgagent───{sgagent}
        ├─sh───10*[{sh}]
        ├─sshd─┬─sshd───sshd───bash
        │      └─sshd───sshd───bash───pstree
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-udevd
        ├─tat_agent───5*[{tat_agent}]
        └─tuned───4*[{tuned}]
```





`pstree -p`：以树状显示进程关系，并显示处进程的`pid`信息



```bash
pstree -p


输出：

systemd(1)─┬─YDLive(19323)─┬─{YDLive}(19325)
           │               ├─{YDLive}(19326)
           │               ├─{YDLive}(19327)
           │               ├─{YDLive}(19328)
           │               ├─{YDLive}(19329)
           │               ├─{YDLive}(19330)
           │               ├─{YDLive}(19332)
           │               ├─{YDLive}(22095)
           │               └─{YDLive}(27471)
           ├─lsmd(627)
           ├─lvmetad(444)
           ├─master(1344)─┬─pickup(15012)
           │              └─qmgr(1348)
           ├─ntpd(3866)
           ├─nvim(1877)───{nvim}(1878)
           ├─nvim(7536)───{nvim}(7538)
           ├─nvim(11136)───{nvim}(11138)
           ├─nvim(14421)───{nvim}(14423)
           ├─nvim(21711)───{nvim}(21713)
           ├─nvim(23571)───{nvim}(23573)
           │              └─{polkitd}(647)
           ├─rshim(1113)───{rshim}(1124)
           ├─rsyslogd(1360)─┬─{rsyslogd}(1383)
           │                └─{rsyslogd}(1385)
           ├─sgagent(2911)───{sgagent}(2915)
           ├─systemd-journal(406)
           ├─systemd-logind(625)
           ├─systemd-udevd(440)
           ├─tat_agent(4987)─┬─{tat_agent}(4988)
           │                 ├─{tat_agent}(4989)
           │                 ├─{tat_agent}(4990)
           │                 ├─{tat_agent}(4991)
           │                 └─{tat_agent}(4994)
           └─tuned(1110)─┬─{tuned}(1345)
                         ├─{tuned}(1346)
                         ├─{tuned}(1350)
                         └─{tuned}(1351)

```



`pstree -p <pid>`：以树状形式展示特定 `pid` 的进程关系 



```bash
pstree -p 1110


输出：

tuned(1110)─┬─{tuned}(1345)
            ├─{tuned}(1346)
            ├─{tuned}(1350)
            └─{tuned}(1351)
```





### 1.3.5 kill -信号说明



`kill` : 用于向进程发出信号，使进程做出相应操作



```bash
kill 信号说明

信号	      编号	         	说明

SIGHUP     (1)     	 	挂起信号，常用于重新加载配置

SIGINT     (2)    	 	中断（Ctrl+C）

SIGTERM    (15)   		优雅终止（默认信号）

SIGKILL    (9)    	 	强制杀死（不可被捕获）

SIGSTOP    (19)   	 	暂停进程

SIGCONT    (18)   	 	恢复暂停的进程
```





```bash
kill 常见操作


kill -1 <pid>    	# 发送 SIGHUP
  
kill -9 <pid>    	# 强制杀死指定pid进程
 
kill -STOP <pid> 	# 暂停指定pid进程

kill -CONT <pid> 	# 恢复指定pid进程
```



### 1.3.6 常见实用命令



```bash
查看占用某端口的进程   lsof -i :8080 或 netstat -tlnp | grep 8080

查看最耗 CPU 的进程   ps aux --sort=-%cpu | head

查看最耗内存的进程     ps aux --sort=-%mem | head

杀死进程             → kill <pid>（SIGTERM）/ kill -9 <pid>（SIGKILL）

按名称杀进程         → pkill nginx 或 killall nginx

查看进程运行了多久    → ps -eo pid,etime,cmd

查看进程的线程       → ps -eLf | grep <pid> 或 ls /proc/<pid>/task/
```





## 1.4 Linux中父子进程



### 1.4.1 基本概念

  

核心概念：在Linux 中所有进程（除了 PID 1）都是由另一个进程创建的。创建者是父进程（Parent），被创建的是子进程（Child）。



进程id（PID）：通过库函数`getpid()`获取，其中库函数的头文件包含在 `#include <sys/types.h>`



父进程id（PPID）：通过库函数`getppid()`获取，其中库函数的头文件包含在`#include <sys/types.h>`



**进程图树：**



```bash
 										 进程树图
 
                                +-------------------------+
                                |        systemd          |
                                |        (PID 1)          |
                                |       all processes     |
                                +------------+------------+
                                             |
                            +----------------+----------------+
                            |                |                |
                    +-------+--------+ +-----+------+ +-------+--------+
                    |      sshd      | |    cron    | |     nginx      |
                    |    (PID 567)   | |  (PID 234) | |   (PID 1234)   |
                    |  SSH daemon    | |   cronjob  | |   web server   |
                    +-------+--------+ +------------+ +--+--------+----+
                            |                           |        |
                    +-------+--------+          +-------+--+ +---+--------+ +--------+-------+
                    |      bash      |          | worker-1 | |  worker-2  | |    worker-3    |
                    |    (PID 900)   |          | (PID 35) | |  (PID 36)  | |    (PID 37)    |
                    |     shell      |          +----------+ +------------+ +----------------+
                    +-------+--------+
                            |
                    +-------+--------+
                    |       vim      |
                    |    (PID 888)   |
                    |     editor     |
                    +----------------+
```





### 1.4.2 创建子进程



`fork函数`



```c
1. 函数体： pid_t fork(void); 


2. 包含在头文件：#include <unistd.h>

    
3. 函数功能：fork() 是 Linux 中创建子进程的唯一系统调用，调用一次，返回两次。
      
    
4. 返回值：对于父进程返回子进程的pid，对于子进程返回pid=0

    
5. 执行逻辑：

时刻1：父进程调用 fork()
         │
         ↓
时刻2：内核复制进程（此时有两个进程了）
         │
         ├── 父进程（原来的）
         └── 子进程（新复制的）
         │
         ↓
时刻3：两个进程各自返回
         ├── 父进程返回子进程PID
         └── 子进程返回0

6.内核复制进程阶段做了什么？
    
复制的内容：                       效果：

代码段（执行到哪一行）          两个进程都从 fork() 返回处继续执行
数据段（变量的值）             两个进程有各自的变量副本
堆栈（函数调用链）             两个进程有各自的栈
寄存器状态                   两个进程有各自的 CPU 状态
程序计数器（PC）              两个进程都指向 fork() 的下一条指令
返回地址                     两个进程都知道返回到哪里
```





完整示例：创建子进程



```C
#include <stdio.h>
#include <unistd.h>

int main() 
{
    int x = 10;
    pid_t id = fork();
	if(id < 0)
    {
        perror("fork");
        return 1;
    }
    if (id == 0) 
    {
        // 子进程
        printf("子进程: pid=%d\n", getpid()); 
    } 
    else if (id > 0) 
    {
       // 父进程
        printf("父进程: pid=%d\n", getpid()); 
    }
    return 0;
}
```







问题1：为什么fork函数给父进程和子进程的pid值不相同？





```bash
根本原因：区分身份

1. 原因一：从父进程的角度

因为父进程的数量与子进程的数量为1:n，也就是说父进程可以有多个子进程。

父进程需要管理子进程，父进程需要知道我创建了哪个子进程，所以返回值为子进程的pid

2.原因二：从子进程的角度

子进程不需要知道自己的 pid 是多少（它可以用 getpid() 获取），但需要一种简单的方式确认"我是子进程"，所以返回值为0.
```

​	



问题2：为什么fork函数要返回两次？

> 答：
>
> fork() 的本质是把父进程完整复制一份变成子进程。复制之后，父子进程各自独立运行，都要从 fork() 调用处继续往下执行，所以两个进程都需要一个返回值。



```bash
1.根本原因：fork() 复制了整个进程

fork() 的本质是把父进程完整复制一份变成子进程。复制之后，父子进程各自独立运行，都要从 fork() 调用处继续往下执行，所以两个进程都需要一个返回值。

示例说明：

fork() 之前：只有一个进程（父进程）

           父进程
             │
         printf("A")
             │
          fork()  ← 在这里分裂
             │
         printf("B")

fork() 之后：变成两个进程，都要从 fork() 返回处继续执行

           父进程              子进程
             │                   │
         printf("A")         printf("A")  ← 已经执行过了
             │                   │
          fork() 返回          fork() 返回
          pid=1235             pid=0
             │                   │
         printf("B")         printf("B")  ← 两个进程都要执行
         

2.如果只返回一次会怎样

假设 fork() 只给父进程返回，子进程不返回：

父进程拿到pid，子进程也被创建了，也从fork()后代码语句执行。

若没有返回值，它不知道自己是子进程，无法区分身份，无法执行不同的代码分支。
```





问题3：为什么fork函数能够返回两次？

> 答：
>
> 普通函数只能返回一次，因为只有一个执行流，但 fork() 会复制整个进程，在return之前子进程就已经创建，产生两个独立的执行流，每个执行流都要返回一次，所以总共返回两次。



```bash
图示示例：

1.普通函数：

调用 → 执行 → 返回一次
──────────────────────→
    只有一条执行流


2.fork函数：

调用 → 内核复制进程 → 两条执行流各自返回
───────────────────┐
                   ├── 父进程 ──→ 返回 pid
                   └── 子进程 ──→ 返回 0
```





问题4：为什么fork函数返回的id能让 if 和 else if 同时成立？



> 答：
>
> 不是一个变量同时有两个值，而是 fork() 创建了两个进程，每个进程通过写实拷贝有各自独立的变量副本，各自的值不同，各自走不同的分支。





代码演示：



```C
#include <stdio.h>
#include <unistd.h>

int main() 
{
    pid_t id = fork();
    if (id == 0) 
    {
        printf("A\n");
    } 
    else if (id > 0) 
    {
        printf("B\n");
    }
}
```



详情解释：



```
因为 fork() 之后变量 id 在两个进程中是两个不同的副本：

fork() 之后：

    父进程                    子进程
    ┌──────────┐             ┌──────────┐
    │  id=1001 │             │   id=0   │
    └──────────┘             └──────────┘
  父进程的 id 是 1001         子进程的 id 是 0


同一个变量 id，在不同进程中有不同的值：

执行过程：

                  fork()
                    │
        ┌───────────┴───────────┐
        ↓                       ↓
      父进程                  子进程
       id = 1001             id = 0
        │                       │
        ↓                       ↓
      if (1001 == 0)         if (0 == 0)
        → false                → true
        ↓                       ↓
      else if (1001 > 0)     printf("A")  ← 执行这里
        → true                   ↓
        ↓                      return
      printf("B")  ← 执行这里
        ↓
      return
```







## 1.5 进程的状态



### 1.5.1 进程状态的定义



**Linux中进程的PCB**：



```c
struct task_struct 
{
    volatile long state;        // <-进程状态
    // ... 几百个字段
};
```



**进程状态**：在Linux核心源代码中，进程状态在代码层面上就是通过一个整数进行描述。





### 1.5.2 进程状态的分类



Linux内核源代码：通过宏定义进行定义各自进程的状态，利用字符串数组进行维护。



```C
#define R (running)      0
#define S (sleeping)     1
#define D (disk sleep)   2
#define T (stopped)      4
#define t (tracing stop) 8
#define X (dead) 		 16
#define Z (zombie)		 32

//Linux中 6 种进程状态，通过字符数组进行维护宏
static const char *const task_state_array[] = {
 "R (running)",  		//0
 "S (sleeping)", 		//1 
 "D (disk sleep)", 		//2
 "T (stopped)", 		//4 
 "t (tracing stop)", 	//8 
 "X (dead)", 			//16
 "Z (zombie)", 			//32
};
```





进程的状态解释：



```C
R       Running         正在运行 或 在运行队列中（就绪）    	-运行/就绪状态
    
S       Sleeping        可中断睡眠（等待事件，可被信号唤醒）	  -阻塞状态
    
D       Disk Sleep      不可中断睡眠（通常等待磁盘 I/O）   	 -阻塞状态
    
T       Stopped         被信号停止（如 kill -19 SIGSTOP）    -暂停状态
    
t       Tracing Stop    被调试器停止（如 ptrace）         	-暂停状态
    
Z       Zombie          僵尸进程（已死，等待父进程回收）	   	

X       Dead			这个状态只是⼀个返回状态，你不会在任务列表⾥看到这个状态。
```





#### A.  运行状态



**R 状态**： `Running / Runnable`（运行/可运行），是 Linux 进程最常见的活跃状态。



**R 状态的两种子情况**：

进程 A: 正在 CPU 上执行代码     → 真正的 Running
进程 B: 在运行队列中等调度       → Runnable（可运行，等待 CPU）



**综上所述**：R 状态 =  "进程正在 CPU 上运行"  或  "正在运行队列中等待 CPU 时间片"



**运行态的调度**：



```
R (运行中)
  |
  | 条件1：CPU 时间片用完
  |
  ├──→ R (被重新调度，可能继续执行)
  |
  | 条件2：等待 I/O（磁盘/网络）
  |
  └──→ S (可中断睡眠) 或 D (不可中断睡眠)
           |
           |   条件3：完成 I/O 后
           |
           └──→ R (回到运行队列)
```





#### B. 阻塞状态 （待完成）





#### C. 暂停状态 （待完成）



### 1.5.3 僵尸进程 和 孤儿进程



#### A. 僵尸进程（待完成）



僵尸状态的概念：子进程已终止，但父进程未调用 wait() 回收其退出状态



查看僵尸进程：`ps aux | grep 'Z'`





#### B.  孤儿进程



孤儿进程的概念：是指父进程已经终止或退出，而子进程仍在运行的情况，此时这些"无主"的子进程就被称为孤儿进程。





**问题一：为什么子进程要被1号进程领养？**





## 1.6 进程优先级



### 1.6.1 优先级的概念



####  A.优先级是什么？





#### B.为什么要优先级？





#### C.如何实现优先级？





## 1.7 进程性质



### 1.7.1 竞争性



### 1.7.2 独立性



### 1.7.3 并行性



### 1.7.4 并发性





## 1.8 进程切换



### 1.8.1 CPU 和 寄存器的初识



### 1.8.2 进程切换过程







## 1.9 进程调度算法



### 1.9.1 基于位图索引的双数组轮转算法



#### A. 数据结构定义



`struct prio_array`**：优先级索引容器**



```c
struct prio_array 
{
    unsigned int nr_active;         //记录当前数组中的进程总数
    unsigned int bitmap[5]; 		//140位的位图，用于O(1)查找
    struct list_head queue[140];	//140个优先级链表头 
};
```



`struct runqueue` ：运行队列结构体，维护进程的调度



```C
struct runqueue 
{
    // ... 其他字段 ...
    struct prio_array *active;   // 指向当前活跃数组的指针
    struct prio_array *expired;  // 指向过期数组的指针
    struct prio_array arrays[2]; // 实际存储的两个数组实体
};


struct runqueue (每个CPU一个)
├── ... 其他字段 ...
├── struct prio_array *active;   ← 指针A
├── struct prio_array *expired;  ← 指针B
└── struct prio_array arrays[2]; ← 实际内存实体
      │
      ├── arrays[0]             ← 第1块内存 (140个链表头 + 位图 + 计数器)
      │     ├── bitmap[140 bits]
      │     ├── nr_active
      │     └── queue[0..139] (链表头数组)
      │
      └── arrays[1]             ← 第2块内存 (140个链表头 + 位图 + 计数器)
            ├── bitmap[140 bits]
            ├── nr_active
            └── queue[0..139] (链表头数组)
```





`queue 数组` ：用来存储队列的表头，每个元素均为`struct list_head` 



![image-20260627171319232](../../../../AppData/Roaming/Typora/typora-user-images/image-20260627171319232.png)





#### B. 算法核心思想



##### 步骤一：通过位图决定挑选哪个队列



**核心思想：**位图不记录队列里有“几个”进程，只记录这个队列“是否为空”，其中：1 表示非空（有进程），0 表示空。



**位图使用的优势：**



a. 传统调度器寻找队列时，需要写一个 `for` 循环从 0 遍历到 139，在工程上被认为是伪 O(N)。



b. 内核直接调用 CPU 的硬件指令**BSF**，给 CPU 一个 32 位的整数，CPU 在1个时钟周期内，直接通过内部硬件电路返回从右往左第一个 `1` 所在的位索引，从而确定队列，只需要O(1)的时间复杂度。



**实现位图映射优先级：**



在 32 位 Linux 系统中，本质上是一个包含 5 个 32 位无符号整数的数组（5 × 32 = 160 位，足以覆盖 0~139 共 140 个优先级）。



```C
unsigned long bitmap[5]; 


//bitmap[0] 的 bit 0 ~ 31  对应优先级 0 ~ 31 的队列
//bitmap[1] 的 bit 0 ~ 31  对应优先级 32 ~ 63 的队列
//bitmap[2] 的 bit 0 ~ 31  对应优先级 64 ~ 95 的队列
//bitmap[3] 的 bit 0 ~ 31  对应优先级 96 ~ 127 的队列
//bitmap[4] 的 bit 0 ~ 11  对应优先级 128 ~ 139 的队列
```



**位图寻找队列算法实现：**



```C
// 简化版内核源码还原 (32位系统)
static inline int sched_find_first_bit(const unsigned long *b)
{
    // 1. 检查实时进程的高优先级区 (word 0)
    if (unlikely(b[0])) 
    {
        // 如果有 1，直接调用硬件指令 ffs (find first set) 返回索引
        //__ffs 底层就是封装了 x86 的 BSF 汇编指令
        return __ffs(b[0]);  
    }
    
    // 2. 检查实时进程的中优先级区 (word 1)
    if (unlikely(b[1])) 
    {
        return __ffs(b[1]) + 32; // 加上偏移量 32
    }
    
    // 3. 检查实时进程的低优先级区 (word 2)
    if (unlikely(b[2])) 
    {
        return __ffs(b[2]) + 64;
    }
    
    // 4. 检查普通进程的高优先级区 (word 3)
    if (unlikely(b[3])) 
    {
        return __ffs(b[3]) + 96;
    }
    
    // 5. 检查普通进程的低优先级区 (word 4)
    // 如果前4个都没有，那一定在最后一个 word 里
    return __ffs(b[4]) + 128; 
}
```





##### 步骤二：在队列中挑选合适的进程



**从队列寻找待运行进程的算法实现：**



```C
// 假设通过位图 BSF 指令，已经找到了最高优先级的索引 i
int i = sched_find_first_bit(rq->active->bitmap);

// 1. 拿到队首的 list_head 节点 (O(1) 读指针)
struct list_head *first_node = rq->active->queue[i].next;

// 2. 灵魂操作：反推出完整的 task_struct (O(1) 减法运算)
struct task_struct *next_task = list_entry(first_node, struct task_struct, run_list);

// 3. 将该进程从链表中摘除 (O(1) 修改前后指针)
list_del_init(&next_task->run_list);

// 4. 检查队列是否空了，维护位图 (O(1) 判空与位操作)
if (list_empty(&rq->active->queue[i])) 
{
    __clear_bit(i, rq->active->bitmap);
    rq->active->nr_active--;
}
```





##### 步骤三：交换




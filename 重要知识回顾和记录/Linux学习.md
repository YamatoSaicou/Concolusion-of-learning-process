# Linux的构成： 
内核和外壳shell
# Linux的设计哲学
1. 一切皆文件
2. 功能单一性
3. 连接程序
4. 避免交互性（没有gui）
5. 配置管理文本化
# Linux的操作功能模块
1. 进程调度
2. 内存管理
3. 虚拟文件系统，隐藏物理细节
4. 网络接口
5. 进程间通讯
# Linux系统需要记住的文件目录
1. /etc 存放系统管理和配置文件
2. /proc 虚拟文件系统目录，是系统内存的映射，可直接访问这个目录来获取系统信息
3. /boot 存放系统引导时使用的文件
4. /tmp 用于存放各种临时文件，是公用的临时文件存储点
5. /lost+found 系统非正常关系导致文件系统异常丢失的文件
6. /var 用于存放运行时需要改变数据的文件，也是某些大文件的溢出区，比方说各种服务的日志文件
7. /home 存放所有用户文件的根目录，是用户主目录的基点
## 账号系统文件
1. 口令文件：/etc/passwd
2. 口令影子文件(密文): /etc/shadow
3. 用户组文件: /etc/group
### 账户管理命令
1. 用户 useradd userdel
2. 用户组 groupadd groupdel
3. 修改密码 passwd
### 目录文件管理
使用命令ls -la ，可以看到目录文件属性，比如drwxr-xr-x，d代表文件是个目录。
1. 代表了所属用户，所属用户组，其他用户
2. 可读(r=4)，可写(w=2)，可执行(x=1)
### 权限管理
1. 用户属组 chown root:root -R data
2. 权限位 chmod 755 filenameXXX 
3. 账户提权 
   * 特权用户 root
   * 普通用户 su/sudo
## 进程管理
1. Cpu硬件查询
  * processor: 当前cpu号
  * model name: 型号
  * cpu MHz： 主频
  * mpstat -P ALl 1 查看每个核的利用率
2. 进程cpu利用率查询：
  * top 查看cpu使用率高的排行
  * ps aux 查看所有进程
3. 杀死进程
  * kill -9 [pid]
  * killall -9 [name]
4. 进程
  * 每个进程都有一个非负整数的进程id
  * getpid()
5. 进程调度方法
  * SCHED_NORMAL 普通分时调度策略，公平占用时间片
  * SCHED_FIFO 实时调度策略，先到先服务
  * SCHED_RR 实时调度策略，时间片轮转
6. 实时进程优先级高于普通进程
  * 静态优先级：调用nice去修改
  * 动态优先级：运行工程中动态改变程序优先级，保证调度公平性
7. 守护进程
  * 在终端中运行的程序都依赖于终端，如果终端关闭，程序结束
  * 用daemon()函数写守护进程
## 线程
1. 线程有线程ID：pthread_self()
2. 线程库：LinuxThreads与NPTL
3. -lpthread 编译时用来连接库，要手动添加
4. 注意线程栈大小，4KB
## 内存
1. 内存硬件信息查询: cat /proc/meminfo | more
2. 内存使用率: free -m  看avialiable
3. 32位系统 用户3G 系统1G  64位系统，全部是128T

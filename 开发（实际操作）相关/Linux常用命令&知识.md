## 部署时会用到的一些最最常用的命令
sudo su  切换root用户 <br>
apt-get install package 安装包 <br>
sudo apt-get upgrade 更新已安装的包 <br>
ps aux 获取进程信息，配合kill来关闭卡死的进程 <br>
lsof -i:端口号 查看端口占用 <br>
cat 显示文件内容
sh或者./ 执行sh脚本
## 搜索命令grep

1、在当前目录中，查找后缀有 file 字样的文件中包含 test 字符串的文件，并打印出该字符串的行。此时，可以使用如下命令：
```
grep test *file 
```
结果如下所示：
```
$ grep test test* #查找前缀有“test”的文件包含“test”字符串的文件  
testfile1:This a Linux testfile! #列出testfile1 文件中包含test字符的行  
testfile_2:This is a linux testfile! #列出testfile_2 文件中包含test字符的行  
testfile_2:Linux test #列出testfile_2 文件中包含test字符的行 
```
2、以递归的方式查找符合条件的文件。例如，查找指定目录/etc/acpi 及其子目录（如果存在子目录的话）下所有文件中包含字符串"update"的文件，并打印出该字符串所在行的内容，使用的命令为：
```
grep -r update /etc/acpi 
```
输出结果如下：
```
$ grep -r update /etc/acpi #以递归的方式查找“etc/acpi”  
#下包含“update”的文件  
/etc/acpi/ac.d/85-anacron.sh:# (Things like the slocate updatedb cause a lot of IO.)  
Rather than  
/etc/acpi/resume.d/85-anacron.sh:# (Things like the slocate updatedb cause a lot of  
IO.) Rather than  
/etc/acpi/events/thinkpad-cmos:action=/usr/sbin/thinkpad-keys--update 
```


## 输出命令echo

Shell的echo 指令用于字符串的输出。命令格式：
```
echo string
```
可以使用echo实现更复杂的输出格式控制。
1. 显示普通字符串:
```
echo "It is a test"
```
这里的双引号可以省略。

2. 显示转义字符
```
echo "\"It is a test\""
```
结果将是:
```
"It is a test"
```
同样，双引号也可以省略

3. 显示变量

read 命令从标准输入中读取一行,并把输入行的每个字段的值指定给 shell 变量

```
#!/bin/sh
read name 
echo "$name It is a test"
```
以上代码保存为 test.sh，name 接收标准输入的变量，结果将是:
```
[root@www ~]# sh test.sh
OK                     #标准输入
OK It is a test        #输出
```
4. 显示换行
```
echo -e "OK! \n" # -e 开启转义
echo "It is a test"
输出结果：
```
```
OK!

It is a test
```
 5. 显示不换行
 ```
#!/bin/sh
echo -e "OK! \c" # -e 开启转义 \c 不换行
echo "It is a test"
```
输出结果：
```
OK! It is a test
```
6.显示结果定向至文件
```
echo "It is a test" > myfile
```
7.原样输出字符串，不进行转义或取变量(用单引号)
```
echo '$name\"'
```
输出结果：
```
$name\"
```
8.显示命令执行结果
```
echo `date`
```

## 0 1 2
![image](https://github.com/YamatoSaicou/Kancolle-wallpaer/blob/master/gif/012.png)
在Linux系统中0 1 2是一个文件描述符,我们平时使用的
```
echo "hello" > t.log 
```
其实也可以写成
```
echo "hello" 1> t.log
```
**关于2>&1的含义**

含义：将标准错误输出重定向到标准输出
符号>&是一个整体，不可分开，分开后就不是上述含义了。
比如有些人可能会这么想：2是标准错误输入，1是标准输出，>是重定向符号，那么"将标准错误输出重定向到标准输出"是不是就应该写成"2>1"就行了？是这样吗？
如果是尝试过，你就知道2>1的写法其实是将标准错误输出重定向到名为"1"的文件里去了

**为什么2>&1要放在后面**
考虑如下一条shell命令
```
nohup java -jar app.jar >log 2>&1 &
```
(最后一个&表示把条命令放到后台执行，不是本文重点，不懂的可以自行Google)
为什么2>&1一定要写到>log后面，才表示标准错误输出和标准输出都定向到log中？
我们不妨把1和2都理解是一个指针,然后来看上面的语句就是这样的：

本来1----->屏幕 （1指向屏幕）
执行>log后， 1----->log (1指向log)
执行2>&1后， 2----->1 (2指向1，而1指向log,因此2也指向了log)
再来分析下
```
nohup java -jar app.jar 2>&1 >log &
```
本来1----->屏幕 （1指向屏幕）
执行2>&1后， 2----->1 (2指向1，而1指向屏幕,因此2也指向了屏幕)
执行>log后， 1----->log (1指向log，2还是指向屏幕)
所以这就不是我们想要的结果。

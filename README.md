# computer_base

·##linux系统编程基础

###常用核心命令
```
ls 列出目录下的文件  ls -lh 显示详情 + 人性化大小
cd   切换工作目录     cd .. 返回上级；cd ~ 回家目录
pwd  打印当前目录绝对路径
mkdir  创建目录   mkdir -p a/b/c 递归创建多级目录
rm   删除         rm-rf dir 强制递归删除目录
cp   复制       cp-r dir1 dir2复制目录
mv   移动/重命名       mv old.txt new.txt （重命名）
find  按条件搜索文件   find . -name "*.log" 按名称查找
grep 文本内容搜索   grep -rn "error" ./src 递归搜索
chmod 修改文件权限
chown 修改文件所属用户
tar 打包压缩    tar -zcvf a.tar.gz dir 打包；tar -zxvf a.tar.gz 解压


ps 查看进程快照    ps aux 查看所有进程详情
top 实时监控进程资源占用
htop  增强版top
kill 向进程发信号  终止进程
pkill  按进程名批量杀进程
nice  启动时指定进程优先级
renice 修改运行中进程的优先级


ip  查看网卡  ip   路由  ip addr 看 IP；ip route 看路由
ss  查看网络连接状态    ss -tlnp 查看所有 TCP 监听端口
ping 测试网络连通性与延迟  ping -c 4 baidu.com
telnet 测试tcp端口是否开放  telnet 192.168.1.1 8080
nc    多功能网络工具    nc -zv 192.168.1.1 8080 测端口
tcpdump 命令行抓包工具   tcpdump -i eth0 port 80

df  查看磁盘分区使用情况   df -h 人性化显示容量
du 查看目录/文件占用空间
free  查看内存使用情况
uptime  查看系统运行时长与负载
dmesg  查看内核日志

cat 查看小文件
less	分页查看大文件	less log.txt（/ 搜索，q 退出）
head	查看文件开头	head -20 log.txt 看前 20 行
tail	查看文件末尾 / 实时追踪	tail -f app.log 实时看日志
sed	流编辑器，批量替换文本	sed -i 's/old/new/g' file.txt 全局替换
awk	按列处理结构化文本	awk '{print $1}' log.txt 打印第 1 列
wc	统计行数 / 字符数 / 单词数	wc -l log.txt 统计行数
  ```

###Vim 与 GDB 核心操作

```
Vim 三大模式
普通模式：移动光标、删除、复制粘贴；按 i/a/o 进入插入模式，按 : 进入命令模式
插入模式：编辑文本；按 Esc 退回普通模式
命令模式：保存、退出、查找替换；:wq 保存退出，:q! 强制不保存退出

GDB 核心调试命令
gdb ./a.out          # 启动程序调试
b main               # 在main函数打断点（break）
b file.c:10          # 在指定文件行号打断点
r                    # 运行程序（run）
n                    # 单步执行，不进入函数（next）
s                    # 单步执行，进入函数（step）
p var                # 打印变量值（print）
bt                   # 查看函数调用栈（backtrace）
c                    # 继续运行到下一个断点（continue）
q                    # 退出gdb
```
###文件系统核心概念
```
   inode （索引节点） inode是linux文件的唯一标识，存储文件元数据: 大小 ，权限，时间戳 ，数据块指针。
   硬链接 vs 软链接
     inode    和原文件共用一个inode     独立inode
     跨文件系统    不支持               支持
     指向对象      只指向文件，不能指向目录     可指向目录
     原文件删除     链接依然有效，文件实体仍然存在    链接失效
     本质          文件名到inode映射，相当于文件别名    保存原文件路径 相当于快捷方式

    
```
### 进程管理基础
   ```

```



# computer_base

##linux系统编程基础

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
  进程五大状态 ：
        就绪态 运行态  阻塞态  停止态 僵尸态
  进程vs线程
      进程是资源分配的最小单位   线程是cpu调度的最小单位
      进程有独立地址空间，线程共享进程的地址空间
      进程切换开销大  线程切换开销小
      进程间通信复杂   线程间可直接共享全局变量
```

### make与cmake
   ```
  makefile描述编译依赖规则  make命令自动执行增量编译：
     app： main.c func.c
          gcc main.c func.c -o app

clean:
   rm-f app
执行make编译  make clean清理编译产物

cmakelist.txt:
    cmake是跨平台构建工具   先生成makefile再执行编译：
      cmake_minium_requied(VERSION 3.10)
      project(Myapp)
      #指定c++标准
      set(CMAKE_CXX_STANDARD 11)
      #生成可执行文件：目标名+原文件
      add_executable(app main.cpp func.cpp)
```


##多线程编程
   
1  线程创建： std::thread   线程创建后立即执行 主线程用join（）等待子线程结束 或detach（）分离后由系统回收资源
```
#include<iostream>
#include<thread>

   void print_hello(int id){
      std::cout<<"线程"<<id<<"运行中"<<std::endl;
}

   int main(){
     std::thread t1(print_hello,1);
     std:thread t2(print_hello,2);

   t1.join();
   t2.join();
   return 0;
}
```
2 互斥锁 std：：mutex   解决多线程竞争共享资源的问题   加锁后同一时间只有一个线程能进入临界区
用std：：lock_guard RALL自动加解锁 避免忘记解锁
```
#include<mutex>
#include<thread>

int cnt = 0;
std::mutex mtx;// 全局互斥锁

void add(){
    for(int  i=0;i<1000;++i){
         //进入作用域直接加锁 离开作用域自动解锁
     std::lock_guard<mutex> lock(mtx);
     cnt++;
}
}

int  main(){
     std::thread t1(add),t2(add);
     t1.join();
     t2.join();
// 最终cnt一定等于2000；不加锁会出现数据竞争，结果不确定 （因为有可能两个线程交替打断这三步，会出现丢失自增）
     return 0;
}
```


3 读写锁： std：：shared_mutex
     适合读多写少的场景：
         读操作： 加共享锁，多个线程可同时读
         写操作： 加独占锁，写的时候所有读/写都阻塞
```
#include <shared_mutex>
    std::shared_mutex rw_mtx;
    int data = 0;

   void read_func(){
     //共享锁：多个读线程可同时持有
    std::shared_lock<std::shared_mutex> lock(rw_mtx);
//读取data，不会和其他读操作互斥
}
  void write_func(){
    //独占锁：同一时间只有一个写线程
       std::unique_lock<std::shared_mutex> lock(rw_mtx);
     data++;//修改data
```

4 条件变量：std；；condition_variable
   用于线程间同步，典型场景：生产者-消费者模型
    等待线程： 先加锁 ，调用wait（）原子地释放锁并阻塞，被唤醒后重新加锁
    唤醒线程： 加锁修改条件后，调用notify_one()/notify_all()唤醒

消费者启动：lock(mtx) 加锁，队列为空，进入 wait
wait 原子释放锁 + 阻塞休眠
CPU 切到生产者：lock(mtx) 上锁，push 数据（条件变了）
生产者调用 notify_one() 唤醒消费者
生产者循环结束，释放锁
消费者被唤醒，自动重新抢锁，拿到锁后判断队列非空，退出 wait 继续取数据
 ```
    #include <queue>
#include <mutex>
#include <condition_variable>

std::queue<int> q;
std::mutex mtx;
std::condition_variable cv;

// 生产者
void producer() {
    for (int i = 0; i < 10; ++i) {
        std::lock_guard<std::mutex> lock(mtx);
        q.push(i);
        cv.notify_one(); // 唤醒一个等待的消费者
    }
}

// 消费者
void consumer() {
    while (true) {
        std::unique_lock<std::mutex> lock(mtx);
        // 第二个参数是谓词，条件不满足则继续等待（防止虚假唤醒）
        cv.wait(lock, [](){ return !q.empty(); });
        
        int val = q.front(); q.pop();
        lock.unlock(); // 处理数据前提前解锁，提升并发度
        // 处理数据 val
    }
}
 ```
 5  线程池实现原理与极简demo
  核心思想：预先创建一批常驻线程，从任务队列取任务执行，避免频繁创建销毁线程的开销
  核心组成：线程集合，任务队列，同步机制（互斥锁+条件变量）
  整体概述：
这是一个基础固定大小线程池，核心逻辑：
构造时创建 N 个工作线程，全部阻塞等待任务；
外部调用 submit() 投递任务到任务队列；
空闲线程自动取出任务执行；
析构函数安全关闭线程池，等待所有线程退出。
用到的核心组件：
std::vector<std::thread>：存放所有工作线程
std::queue<std::function<void()>> tasks_：任务缓冲队列，存储待执行函数
std::mutex mtx_：保护任务队列、stop 标志的互斥锁
std::condition_variable cv_：线程等待 / 唤醒
bool stop_：线程池停止标记
```
#include <vector>
#include <queue>
#include <functional>
#include <thread>
#include <mutex>
#include <condition_variable>

class ThreadPool {
public:
    ThreadPool(size_t thread_num) : stop_(false) {
        for (size_t i = 0; i < thread_num; ++i) {
            threads_.emplace_back([this]() {
                while (true) {
                    std::function<void()> task;
                    {
                        std::unique_lock<std::mutex> lock(mtx_);
                        cv_.wait(lock, [this](){ return stop_ || !tasks_.empty(); });
                        if (stop_ && tasks_.empty()) return;
                        task = std::move(tasks_.front());
                        tasks_.pop();
                    }
                    task(); // 执行任务
                }
            });
        }
    }

    // 提交任务
    template<class F>
    void submit(F&& func) {
        std::lock_guard<std::mutex> lock(mtx_);
        tasks_.emplace(std::forward<F>(func));
        cv_.notify_one();
    }

    ~ThreadPool() {
        {
            std::lock_guard<std::mutex> lock(mtx_);
            stop_ = true;
        }
        cv_.notify_all();
        for (auto& t : threads_) t.join();
    }

private:
    std::vector<std::thread> threads_;
    std::queue<std::function<void()>> tasks_;
    std::mutex mtx_;
    std::condition_variable cv_;
    bool stop_;
};

#######
threads_：保存所有工作线程对象，析构时统一 join
tasks_：任务队列，std::function<void()> 可以包装任意无返回无参可调用对象（lambda、普通函数、仿函数）
mtx_：互斥锁，所有读写 tasks_ / stop_ 的地方必须加锁
cv_：条件变量，工作线程无任务时阻塞，有任务 / 关闭时唤醒
stop_：布尔标记，true 

构造创建 4 个线程，全部进入 wait 阻塞；
外部调用 submit，任务入队，notify_one 唤醒 1 个线程；
线程拿到锁，取出任务，出作用域释放锁，执行任务；
多次 submit 会依次唤醒空闲线程，4 个线程并行处理任务；
线程无事可做时回到 wait 继续休眠；
线程池对象生命周期结束，触发析构：
stop_=true，唤醒全部线程；
线程处理完剩余任务后全部退出；
析构等待所有线程 join，资源释放完毕。
```

##进程间通信
  1  fork与exec
     fork（）
          创建子进程，调用一次返回两次
            父进程返回子进程pid   子进程返回0   失败返回-1
            子进程采用 ** 写时复制（COW）** 共享父进程地址空间，父子进程共享文件描述符。
```
 #include <unistd.h>
 #include <sys/wait.h>
 #include <iostream>

 int main() {
    pid_t pid = fork();
    if (pid == 0) {
        std::cout << "我是子进程，PID=" << getpid() << std::endl;
    } else if (pid > 0) {
        std::cout << "我是父进程，子进程PID=" << pid << std::endl;
        wait(NULL); // 等待子进程结束，回收资源，避免僵尸进程
    }
    return 0;
}
 ``` 
  2 管道
      匿名管道pipe  
           只能用于有亲缘关系的进程（父子  兄弟进程）
           半双工 ，数据只能单向流动
           本质是内核中的一块环形缓冲区
   ```
#include <unistd.h>

int main() {
    int fd[2];
    pipe(fd); // 创建管道：fd[0]是读端，fd[1]是写端

    pid_t pid = fork();
    if (pid == 0) { // 子进程：读数据
        close(fd[1]); // 关闭不用的写端
        char buf[1024];
        read(fd[0], buf, sizeof(buf));
    } else { // 父进程：写数据
        close(fd[0]); // 关闭不用的读端
        write(fd[1], "hello", 5);
        wait(NULL);
    }
    return 0;
}
 ```

  命名管道fifo  以文件形式存在文件系统中，无亲缘关系的进程也可以使用
  3  消息队列
      内核维护的消息链表 ，进程通过标识符读写
      支持按消息类型读取，不严格遵循先进先出
      单条消息有大小限制  不适合大数据传输
  4 共享内存
      速度最快的进程间通信方式  ：  将同一块物理内存映射到多个进程的虚拟地址空间，数据直接读写，无需内核拷贝
   5 信号量
       本质是内核中的计数器，专门用于进程 / 线程间的同步与互斥
    P 操作（减 1）：资源不足则阻塞；V 操作（加 1）：释放资源，唤醒等待的进程
     二元信号量（值为 0/1）功能等价于互斥锁
  6  mmap内存映射
       将文件/设备直接映射到进程的虚拟地址空间，操作内存就等于操作文件，减少一次内核态到与用户态的拷贝

  进程间通信方式对比：
        管道：实现简单  只能单向  仅亲缘进程可用  速度慢
        消息队列：  可按类型读取 有内核缓冲  适合小消息通信
        共享内存：速度最快 无内核拷贝 适合大数据 需额外同步
        信号量：  仅作同步互斥 不传输数据
        mmap：既可优化文件io 也可做进程间共享内存


   ##网络编程基础
      tcp socket通信流程 
      TCP 是面向连接、可靠、全双工协议：通信前必须建立连接，通信结束必须正常断开连接。
      TCP 全双工：读写通道独立关闭，一方发完数据可以先关闭发送通道，另一方仍能继续发剩余数据，因此需要四次报文。
   ```
        服务端 ： socket bind listen accept recv/send close 
        客户端：  socket connect send/recv close 
      核心api
        socket（） ： 创建套接字，返回文件描述符
        bind（）： 绑定ip地址和端口号
        listen（）： 开启监听，设置半连接队列大小
        accept（）： 取出一个已完成三次握手的连接 返回新的连接fd
        connect（）：客户端向服务端发起连接请求

TCP 是面向连接、可靠、全双工协议：通信前必须建立连接，通信结束必须正常断开连接。
两端：客户端（主动发起）、服务端（监听等待）
标志位重点记：
SYN：同步，用来建立连接
ACK：确认，回复收到报文
FIN：结束，用来断开连接
    
TCP 全双工：读写通道独立关闭，一方发完数据可以先关闭发送通道，另一方仍能继续发剩余数据，因此需要四次报文。

第一次挥手：客户端发 FIN
客户端数据全部发送完毕，主动关闭自己的发送通道
报文：FIN=1，seq=u
客户端状态：FIN_WAIT_1
含义：“我不再发数据给你了”
第二次挥手：服务端回复 ACK
服务端收到 FIN，先回复 ACK 确认收到关闭请求
ack = u+1
服务端状态：CLOSE_WAIT
此时半关闭状态：
客户端不能发数据，但服务端还能继续向客户端发送剩余未发完的数据
第三次挥手：服务端数据发完，发 FIN
服务端所有数据发送完成，没有数据要传了，发送 FIN
seq = v
服务端状态：LAST_ACK
含义：“我数据也发完了，我也要关闭发送通道”
第四次挥手：客户端回复 ACK
客户端收到服务端 FIN，回复 ACK 确认
ack = v+1
客户端进入 TIME_WAIT 状态（等待 2MSL 时长）
服务端收到 ACK，直接进入 CLOSED 彻底断开
客户端等待 2MSL 后，确认对方收到 ACK，才关闭连接


客户端 →[SYN x]→ 服务端
客户端 ←[SYN y, ACK x+1]← 服务端
客户端 →[ACK y+1]→ 服务端
双方：ESTABLISHED，开始传输数据

客户端 →[FIN u]→ 服务端
客户端 ←[ACK u+1]← 服务端 （服务端继续发剩余数据）
客户端 ←[FIN v]← 服务端
客户端 →[ACK v+1]→ 服务端
服务端直接关闭；客户端等待 2MSL 再关闭

拟人化表达：
客户端说 “我想和你建立连接，我的发送序号从 x 开始”
服务端说：“我收到你的请求，我也同意建立连接，我的序号从 y 开始”
客户端说“我收到你的同步信号，连接正式就绪”

客户端 “我不再发数据给你了”
服务端收到 FIN，先回复 ACK 确认收到关闭请求   此时半关闭状态：客户端不能发数据，但服务端还能继续向客户端发送剩余未发完的数据
服务端：“我数据也发完了，我也要关闭发送通道”
客户端收到服务端 FIN，回复 ACK 确认
```

udp通信
 ```
   无连接协议，不需要建立连接 直接用sendto/recvfrom收发数据
特点：  不可靠  传输快 适合视频  直播等对实时性要求高允许少量丢包的场景
 ```

 阻塞 IO vs 非阻塞 IO
 ```
   阻塞io ： 调用read/accept时，如果没有数据，线程一直挂起等待，直到数据到来
   非阻塞io：调用后立即返回 ，没有数据返回-1
 ```
select与poll
```
io多路复用：  一个线程监控多个文件描述符，事件就绪后再处理，大幅提升并发能力
select：  原理:  用位图fd_set存储监控的fd，调用后内核遍历所有fd，返回就绪集合
          缺点： fd上限是1024 每次调用都要重新拷贝fd——set,内核全量遍历，连接数越多效率越低

poll ：  原理：  用链表存储fd  解决了select的1024数量上限问题
         缺点： 依然需要在用户态 和内核态之间拷贝数据，依然需要遍历所有fd判断就绪状态
```
epoll核心原理：
   ```
   epoll 是 Linux 内核提供的IO 多路复用 API，用来替代低效的 select/poll。
核心能力：单个线程监控成千上万 socket/fd，只返回就绪事件，无无效遍历。

与poll区别：
select 有 1024 fd 硬上限；poll 无上限但性能随连接数线性下降；
每次调用 select/poll 需要把全部 fd 从用户态拷贝到内核；
内核每次线性遍历所有 fd 判断是否就绪；
返回后用户程序还要遍历全部 fd 查找就绪事件。
epoll 全部优化了以上痛点：
内核用红黑树保存监听 fd，无硬性上限；
只拷贝就绪事件，不用全量拷贝；
内核维护就绪链表，epoll_wait 直接拿就绪 fd，不用全量扫描；
```
水平触发lt 和边沿触发et：
```
   水平触发 ：  只要缓冲区有数据，就持续触发事件
   边沿触发：  只有数据到来的边沿时刻触发一次
   ```

reactor模式：
  ```
高性能网络服务器的经典架构  核心思想  io多路复用 + 事件驱动 +  非阻塞io
核心组件：
  事件分发器： 用epoll监听事件，分发给对应的事件处理器
  事件处理器： 处理具体的连接 读  写 事件
```

零拷贝技术
```
目的：减少数据在内核态和用户态之间的拷贝次数，大幅提升 IO 传输性能。
```

### shell基础命令
- rm 文件名删除
- rmdir 删除文件夹
- mkdir 创建文件夹
- >test.c直接创建文件
- ctrl+\`可以调出命令行
- sudo su进入root
- :q退出vim，！强制退出
### 文件单词读取
#### 文件单词读取的主要逻辑
##### 基础单词计数
- 设置一个状态机，每当读取到字符的时候，将状态从in转化为out
- elseif 状态为out，自增一个计数器，并将状态返回in
##### 进阶单词查找
- 当读到字符的时候，做一个判断，是否为设定的单词，如果是则计数。并对存单词的数组进行清零
添加字符结束符：
```C
current_word[word_index] = '\0';
```
清零方式为：
```C
memset(current_word, 0, sizeof(current_word));
```
- 当状态为out的时候，存下第一个字符并自增
- 加入else，即当既不是字符也不是out状态的时候，默默存数，进行自增

### 通讯录设计
#### 通讯录自测学习
- next和prev存的本质上是地址，每次也是赋值给地址，而不是改变指向的内容
- ps之所以前面有指针是因为在使用ps->next的时候，是(\*ps)->next，实际是一个一级指针。
- \*\*ppeople的原因是，他的本质是一个指向链表头的指针，而不是一个实际存入的链表内容，所以需要一个二级指针来使用，其余的情况也多为加入->后的一级指针。
#### 通讯录主要逻辑
在除文件处理外，将内容分化为了四个层次，数据结构，底层核心层，中间接口层，上层应用层
##### 数据结构
使用链表来存储相关内容，有两个结构体，一个是person的基础人员内容，另一个是存储表头指针和人员数量的通讯录结构层
##### 底层核心层
宏定义两个函数，主要是对链表进行增加人员和减少人员
##### 中间接口层
通过逻辑构建四个内容，对人员的增加，减少，搜索，遍历
##### 上层应用层
实现要求的输入，打印，删除，搜索功能
### 线程相关内容
pthread_t threadid;
#### 互锁，自旋，原子操作
##### 互锁
本质是锁住了内容，其他线程来的时候会进入阻塞状态。放弃cpu执行权限，进入等待队列，由系统唤醒线程
- cpu占用率低，适合临界区耗时较长的情况
```C
pthread_mutex_t mutex;

pthread_mutex_lock( & mutex);
( * pcount) ++; 
pthread_mutex_unlock( & mutex);

pthread_mutex_init(&mutex, NULL);

pthread_create(&threadid[i], NULL, thread_callback, &count)
```
##### 自旋
不放弃执行权限，进入循环，直到解锁
- 适合多cpu的情况，无上下文切换的开销
```C
pthread_spinlock_t spinlock;

pthread_spin_lock( & spinlock);
( * pcount) ++; 
pthread_spin_unlock( & spinlock);

pthread_spin_init(&spinlock, PTHREAD_PROCESS_SHARED);
pthread_create(&threadid[i], NULL, thread_callback, &count)
```
##### 原子操作
接收「内存地址、预期旧值、目标新值」三个参数，原子性完成「比较 - 交换」两步操作：内存实际值等于预期旧值则更新为新值（返回成功），否则不做修改（返回失败），全程不可中断。
```C
int inc(int * value, int add) {
    int old;
    __asm__ volatile(
        "lock; xaddl %2, %1;"
        : "=a"(old)
        : "m"( * value), "a"(add)
        : "cc", "memory"
    );
    return old;
}
```
#### 线程池
分为任务队列，执行队列，锁
##### 知识点解析
- task->task_func = task_entry; // 简写（推荐，标准写法） 
- task->task_func = &task_entry; // 显式取地址（合法，但多余
- pthread_cond_t cond;用来通知的工具
```C
pthread_cond_signal(&cond);//叫醒一个
pthread_cond_broadcast(&cond);//叫醒所有，谁抢到算谁的
pthread_cond_wait(&worker->manager->cond, &worker->manager->mutex);
//进入状态以后会阻塞掉，自动释放锁，如果接收到上面的信号，抢然后上锁，进入循环
```
- void \*arg是一个万能指针，即在不知道输入类型的时候可以进行通用
- 注意，传入时传入地址，使用时类型转换
```C
struct Worker *worker = (struct Worker *)arg;
struct Worker *worker = (struct Worker *)malloc(sizeof(struct Worker));
pthread_create(&tid, NULL, worker_func, worker);//这个worker本身就是地址所以不用取地址符
```
#### 线程池主要逻辑
- 主要分为了执行队列，任务队列，管理模块
- 管理模块只有一个，内容是执行任务的指针、锁和cond
- 执行队列和任务队列都是用链表来进行整体的规划
- 首先是初始化管理模块，并在其中初始化好所有的执行队列，里面用condwait全部等待加入任务后的condsignal，每当出现signal，便继续向下执行
- 后续进行任务的使能，加入不同的任务，callback会在抢占到锁以后自动向后执行。
### mysql数据库相关内容学习
gcc -o mysql mysql.c -I /usr/include/mysql/ -lmysqlclient
#### 知识点补充
- 数据库（Database）是按照数据结构来组织、存储和管理数据的仓库。每个数据库都有一个或多个不同的 API 用于创建，访问，管理，搜索和复制所保存的数据
- use mysql 实际含义是使用本机自带的database
- 客户端发起请求，nodeserver实现业务逻辑，向数据库发送请求
#### 软件初始化
##### 创建新用户
- sudo vim debian.cnf查看原始文件，可以得到基础的用户名和密码
- mysql -u 名字 -p密码（u后有空格，p后无空格）
- 创建新用户：CREATE USER 'new_user'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_user_password';
- 赋予权限：GRANT ALL PRIVILEGES ON *.* TO 'new_user'@'localhost';
- 刷新：FLUSH PRIVILEGES;
##### 赋予远程能力
- sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf 实现远程配置的修改
- 把bind修改为0.0.0.0
- 127.0.0.1 回环地址是机器内部使用的地址，对外部不用的
- sudo systemctl start mysql 开启服务器
- sudo systemctl restart mysql 重启
- stop 停止 status 查看状态 enable 开机自启
- 使用root进入：mysql -u root -p
- 修改「现有本地用户」的登录地址：
- 连接数据库：USE mysql;
- 修改远程权限：UPDATE user SET host = '%' WHERE user = 'username' AND host = 'localhost';
- 更新：FLUSH PRIVILEGES;
- 验证修改结果：SELECT user, host FROM user WHERE user = 'username';
#### mysql初始化
```mysql
drop database name_DB;#删除数据库
create database name_DB;#创建数据库
use name_DB;#使用数据库
create table TB_name(
	U_ID INT PRIMARY KEY AUTO_INCREMENT,
	U_NAME VARCHAR(32),
	U_GENDER VARCHAR(8)
);
INSERT TB_XHS(U_NAME,U_GENDER) VALUES("XHS","MAN");#插入
SELECT * FROM TB_XHS;#展示table
ALTER TABLE TB_XHS ADD U_IMG LONGBLOB;#加入一个longblob的img列

#删除内容,即为创建一个过程
DELIMITER &&
CREATE PROCEDURE PROC_DELETE_USER(IN UNAME VARCHAR(32))
BEGIN
SET SQL_SAFE_UPDATES=0;
DELETE FROM TB_XHS WHERE U_NAME =UNAME;
SET SQL_SAFE_UPDATES =1;
END&&

CALL PROC_DELETE_USER('XHS');#使用该函数
```
#### C语言中的 C API
##### 初始化
- `MYSQL mysql;`创建需要的句柄
- `MYSQL *mysql_init(MYSQL *mysql)`对句柄进行初始化 -- 失败返回NULL
- `MYSQL *mysql_real_connect(MYSQL *mysql, const char *host, const char *user, const char *passwd, const char *db, unsigned int port, const char *unix_socket, unsigned long client_flag)`建立与 MySQL 服务器的连接，参数依次为：句柄、服务器 IP、用户名、密码、数据库名、端口、unix 套接字（NULL）、客户端标志（0）--失败返回NULL
##### 普通sql执行
- `int mysql_real_query(MYSQL *mysql, const char *stmt_str, unsigned long length)`执行该语句 --成功返回0
- `MYSQL_RES *mysql_store_result(MYSQL *mysql)`将selete的结果存到本地内存 --失败返回NULL
- `my_ulonglong mysql_num_rows(MYSQL_RES *result)`返回结果的总行数
- `unsigned int mysql_num_fields(MYSQL_RES *result)`返回结果的总列数
- `MYSQL_ROW mysql_fetch_row(MYSQL_RES *result)`逐行读取结果，返回字符串数组 --失败返回NULL
- `void mysql_free_result(MYSQL_RES *result)`释放结果集的内存
##### 文件处理
- 文件大小获取：
```C
fseek(fp, 0, SEEK_END);
int length = ftell(fp); // file size
fseek(fp, 0, SEEK_SET);
```
- `fread(buffer, 1, length, fp);`从fp拿一字节，拿length次到buffer，返回文件的大小
##### 预处理sql执行
- `MYSQL_STMT *mysql_stmt_init(MYSQL *mysql)`初始化预处理语句句柄，用于执行带占位符的 SQL
- `int mysql_stmt_prepare(MYSQL_STMT *stmt, const char *stmt_str, unsigned long length)`编译预处理 SQL 语句（含 ? 占位符） -- 成功0
- `MYSQL_BIND`结构体是预处理的核心步骤，代表？的位置
	- buffer_type数据类型 如`MYSQL_TYPE_LONG`,`MYSQL_TYPE_LONG_BLOB`传入的数据类型
	- buffer内存地址，指向程序中存储数据实际地址，一般NULL，是为了后面send使用
	- length实际长度指针，存放到真实字节数
	- buffer_length buffer装多少字节
-  `int mysql_stmt_bind_param(MYSQL_STMT *stmt, MYSQL_BIND *bind)` 用于绑定结构体，如果是小数据就可以直接execute了。但是因为大数据，所以只占位，不传输，告诉传输类型 --成功0
- `int mysql_stmt_send_long_data(MYSQL_STMT *stmt, unsigned int param_number, const char *data, unsigned long length)` 向指定占位符发送长数据，param是占位符索引，从0开始。 -- 成功返回0
- `int mysql_stmt_execute(MYSQL_STMT *stmt)` 执行编译好的预处理语句 --成功0
- `int mysql_stmt_bind_result(MYSQL_STMT *stmt, MYSQL_BIND *bind)` 绑定预处理语句的输出结果到结构体
- `mysql_stmt_store_result(MYSQL_STMT *stmt)` 将结果存到本地内存
- `int mysql_stmt_fetch(MYSQL_STMT *stmt)`读取预处理的下一行结果，成功返回0，可能因为数据太大装不下，就会出现MYSQL_DATA_TRUNCATED的截断，是合理正常的
- `int mysql_stmt_fetch_column(MYSQL_STMT *stmt, MYSQL_BIND *bind, unsigned int column, unsigned long offset)`读取预处理语句结果集中指定列、指定偏移量的数据 
- result.buffer = buffer + start;就保证有 `result.buffer_length` 长度的数据填入了 `buffer+start` 的对应位置
- `int mysql_stmt_close(MYSQL_STMT *stmt)`关闭预处理语句句柄成功0

### DNS
dns的本质是计算机发出包，header和question，通过拥有的域名去问这个IP是什么
#### 字符串操作函数
- `size_t strlen(const char *str);`计算字符串的有效长度
- `char *strcpy(char *dest, const char *src);`后面的字符串拷贝到目标字符串
- `char *strncpy(char *dest, const char *src, size_t n);`拷贝n个，更安全
- `char *strcat(char *dest, const char *src);`后面的拼接并存到前面的后面，注意要符合dest的大小
- `char *strncat(char *dest, const char *src, size_t n);`拼接多少个
- `int strcmp(const char *str1, const char *str2);`比较两个字符串的全部，=0是全相等，ASCII值的大小，大于小于
- `int strncmp(const char *str1, const char *str2, size_t n);`比较前面的n个
- `char *strchr(const char *str, int c);`查找第一个指定字符，并存储该地址
- `strrchr`是最后一次出现的位置
- `char *strstr(const char *haystack, const char *needle);`后面字符串的位置，返回首地址
- `void *memset(void *ptr, int value, size_t n);`设定指定值
- `void *memcpy(void *dest, const void *src, size_t n);`拷贝任何数据
- `char *strdup(const char *s);`malloc+strcpy的作用
- `char *strtok(char *str, const char *delim);`按照后面分割，没有了返回NULL，后面是NULL开始，不是线程安全 
#### 小知识
- header->flags = htons(0x0100);计算机小端，服务器大端
#### C语言下的DNS
##### 整体流程
1. 确认希望收到IP的网址和基础的数据类型，例如IPv4/IPv6
2. 创建header和question结构体，来存储必要的内容
3. 填入header的信息
4. 使用名字填好question的内容
5. 将header和question组合成一个char \*request 顺序是header name type class 返回request的长度
6. 使用socket来进行数据的传送
	1. 设置一个函数，接受一个域名
	2. 创建UDP套接字`int sockfd = socket(AF_INET, SOCK_DGRAM, 0);` AF_INET是代表v4，SOCK_DGRAM是DNS常用的内容 --失败返回-1 <mark style="background:#ff4d4f">开一个通信通道</mark>
	3. `Struct sockaddr_in servaddr = {0}`<mark style="background:#ff4d4f">写好收件人地址</mark>
		1. `servaddr.sin_family = AF_INET;` 设置地址族IPv4
		2. `servaddr.sin_port = htons(53);` 设置端口号
		3. `servaddr.sin_addr.s_addr = inet_addr(1...4);` 配置服务器地址
	4. 调用两个创建一个合成 回程一个长度
	5. `int slen = sendto(sockfd, request, length, 0, (struct sockaddr*)&servaddr, sizeof(struct sockaddr));` <mark style="background:#ff4d4f">包裹寄给DNS服务器</mark>
		1. sendto ()：拿着你的包裹（request）、包裹大小（length），通过刚才的专属窗口（sockfd），寄到填好的 DNS 服务器地址（servaddr）；
		2. slen是实际寄出去的字节数
	6. 再创建一个收件的数组，一个确定收件的结构体，来存服务器地址，一个来告诉他们地址最大多大。`int n = recvfrom(sockfd, response, sizeof(response), 0, (struct sockaddr*)&addr, (socklen_t*)&addr_len);`n是收到的字节大小 response存报文
##### 必要的预备
- dns_header主要的组成成员：
	- id 存储一个随机数
	- flags 存一堆信息，一般htons(0x0100);就行
	- questions_count 一般就是1，有一个问题，查一个ip
	- answer_count 由服务器回答填数量
	- auth 填零
	- additional 填零
- dnsquestion 的主要组成成员
	- QNAME 例如3www5baidu3com0的形式
	- QTYPE 类型 0x001代表A
	- QCLASS 0x0001代表IN
- DNS_SERVER_PORT 是53
- DNS_SERVER_IP 可以是114.114.114.114
### HTTP
#### 基础概念明晰
首先，我们先明确几个概念
- DNS 域名服务器（也可以是沟通规则），可以通过一个www来获得这个网站的ip；
- UDP 一种传输方式，他以快速传输见长，不要求回执
- HTTP 是一套客户端和服务器沟通规则，可以接收所有信息，常用TCP来进行传输
- TCP 另一种传输方式，要有三次握手，每发一个包都要求对方回一个ACK，三次握手即为
	1. 客户端发送一个SYN包，生成一个随机序列号x，进入SYN_SENT
	2. 服务器收到后回一个ACK（x+1），同时发一个自己的SYN（随机序列y）
	3. 客户端收到后回一个ACK（y+1）
	4. 双方同时进入ESRABLISHED状态
	5. 为什么不是两次，服务器发回确认后就认为连接好了。万一由于网络拥堵，客户端发出的第一个请求包在路上转了一圈才到服务器（此时连接本该结束了），服务器会以为这是一个新请求，从而白白开启一个连接等待数据
#### HTTP的代码实现
1. 通过域名查询ip
	1. 上段代码使用了DNS来进行ip查询，我们这次使用一个更简单的办法
	2. `struct hostent *host_entry = gethostbyname(hostname);`使用系统带的结构体接受一个使用自带函数查询的ip，返回是<mark style="background:#ff4d4f">二进制</mark>结构化ip  --失败NULL
	3. `inet_ntoa(*(struct in_addr*)*host_entry->h_addr_list);`将结构化的ip转化为点分十进制方便后续建立连接
2. 创建TCP连接设置
	1. `sockfd = socket(AF_INET, SOCK_STREAM, 0)`创建TCP接套字，并写好地址结构`struct sockaddr_in sin`，和DNS相似，但是这里用了80的端口
	2. `connect(sockfd, (struct sockaddr*)&sin, sizeof(struct sockaddr_in))`建立三次握手，把sockfd和目标ip绑定  --成功返回0
	3. `fcntl(sockfd, F_SETFL, O_NONBLOCK)`设置为非阻塞，这样不影响继续运行，后面会用select监听
3. 构造一个请求报文
```C
	char buffer[BUFFER_SIZE] = {0};
	sprintf(buffer, 
	"GET %s %s\r\n\
	Host: %s\r\n\
	%s\r\n\
	\r\n",
	resource,//资源类型，写进去的
	"HTTP/1.1",//通信版本
	hostname,//域名
	"Connection: close\r\n"//连接方式，响应及关闭
);
```
4. HTTP的发送请求`send(sockfd, buffer, strlen(buffer), 0);`
5. 初始化监听
```C
fd_set fdread;//可以使用这个内容进行监听
FD_ZERO(&fdread);//清空脏数据
FD_SET(sockfd, &fdread);// 把sockfd加入“读监听集合”
// 设置select超时时间：5秒
struct timeval tv;
tv.tv_sec = 5;  // 秒
tv.tv_usec = 0; // 微秒
```
6. 创立一个动态内存储存结果
7. 创立一个循环持续进行select，数据会分多次来到，直到出现三种可能
	1. 首先进行监听`int selection = select(sockfd+1,&fdread,NULL,NULL, &tv);`之所以由fdread是因为有可能有很多需要监听的内容
	2. 检查监听状态`if (!selection || !FD_ISSET(sockfd, & fdread)) break; ` 如果超时或者sockfd不在fdread中没就绪，即退出循环
	3. 置零原本的buffer，用来接收新的数据`len = recv(sockfd, buffer, BUFFER_SIZE, 0);`返回一个结果的长度。 --失败0
	4. 每次收到数据，都扩容result`result = realloc(result, (strlen(result) + len + 1) * sizeof(char));`，存一个新的buffer`strncat(result, buffer, len);`，直到数据接受完全。
	5. 最后退出循环
```C
while (1) {
    int selection = select(sockfd + 1, & fdread, NULL, NULL, & tv);
    if (!selection || !FD_ISSET(sockfd, & fdread)) {
        break;
    } else {
        memset(buffer, 0, BUFFER_SIZE);
        int len = recv(sockfd, buffer, BUFFER_SIZE, 0);
        if (len == 0) { // disconnect
            break;
        }
        result = realloc(result, (strlen(result) + len + 1) * sizeof(char));
        strncat(result, buffer, len);
    }
}
```
8. 最后还要注意的是，请求资源的时候，加入/就代表首页，加入其他的也可以求证不同的位置地方
### TCP服务器建立
首先梳理整个流程，一个服务器会有很多个接口来接收不同客户端的信息，同时需要一个接口来接收等待的消息，有客户端在等就生成出这个客户端的socket值，然后让epoll去管理。
#### 关键代码实现
1. 首先，建立一个socket来监听一切信息
2. 设置`struct sockaddr_in addr;`地址卡，装入协议，端口，ip地址`INADDR_ANY`是所有ip
3. 绑定sockfd和地址卡，就可以建立连接
4. `listen(sockfd, 5)`建立监听5个信息
5. 使用`epoll_create(1)`创立两个结构,一个ev是注册修改监控，一个events数组是用来使用 `epoll_wait(epfd, events, EPOLL_SIZE, 5);`来接收返回的实际触发类型
6. ev.events是可以EPOLLIN可读 EPOLLOUT可写 EPOLLET边沿触发 .data.fd 是sockfd类型
7. 使用`epoll_ctl(epfd,EPOLL_CTL_ADD,sockfd,&ev)`来进行一个整体的监控，第二个变量是用来对这个fd做什么操作的
8. 创立一个循环，在里面开始使用wait来监听信息`nready = epoll_wait(epfd,events,1024,5)`直到有信息了以后
9. 如果是sockfd，接收一个clientfd`accept(sockfd,(struct sockaddr*)&client_add,&client_len)` 然后把他ctl进去
10. 

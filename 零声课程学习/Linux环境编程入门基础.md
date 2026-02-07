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
- `int mysql_stmt_fetch_column(MYSQL_STMT *stmt, MYSQL_BIND *bind, unsigned int column, unsigned long offset)` 
- `mysql_stmt_fetch_column`，就保证有 `result.buffer_length` 长度的数据填入了 `buffer` 的对应位置

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
```
- void \*arg是一个万能指针，即在不知道输入类型的时候可以进行通用
- 注意，传入时传入地址，使用时类型转换
```C
struct Worker *worker = (struct Worker *)arg;
struct Worker *worker = (struct Worker *)malloc(sizeof(struct Worker));
pthread_create(&tid, NULL, worker_func, worker);//这个worker本身就是地址所以不用取地址符
```

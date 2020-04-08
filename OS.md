# OS

### Lecture 1

Bus: data, control, address bus 总线

wifi and bluetooth are hardwares

DMA: device and memory like between  ram and keyboard

1~st~ bonus: tb shell

kernel is root of tree hierachy of linux 

system call is also called monitor call

### Lecture 2

functions provided by C are not syscalls

.so and .dll: machine code

arrangement of memory is called segmentation

in path, in bin: execute directly(no need to specify the location of file)

dir is a file

four fundamental concepts

stack & heap can switch position?



### Lab 2

tail -f: display the content of file dynamicly

programming language: c, c++, java(half programming half scripting)

scripting language: python, shell, javascript

" vs '(强引用)

### Lecture 3

#instruction decoded more than #instruction executed: 1. pre-fetching, eg. if, else. 2. branch prediction.

function of heap in address space

not swap pos of stack and heap: 

stack: no large memory, no dynamic array, 需要零散的小块地址

heap: dynamic, may need large space, need continuous memory, more intuitive for human to manage from 0 ascending

addr space + thread = process

the return value of fork(): parent: pid of child. child: 0.

执行完fork需要赋值，fork之后的都在子进程中运行。

program exit after running ls (exit 0)

init is the first process.

### lab 3

interactive login shell: sudo -i

interactive non-login shell: open terminal

non-interactive non-login shell: run a script

non-interactive login shell: rare

pty: pseudo teletyper

call sesid(void) to establish a new session without a terminal. 

When a shell is created, a terminal must be setup.

A session can have zero or a single controlling terminal.

The session leader that establishes the connection to the control terminal is called the controlling process.

Only one foreground process group in a session.

Terminals’ interrupt signals are only sent to the processes in the foreground group.

程序如何接收信号？通过terminal？

### Lecture 4

who creates init: kernel, during booting process

ls feeding data into pipe too fast: exceed buffer and write block. size of pipe 

pcb: in kernel space(in memory), stores pid, pc, running time, opened files...

real maybe != user + sys: 

real < user + sys (more frequent nowadays): multi-core

real > user + sys: IO takes time. user and sys are both CPU time. 

指向动态空间的指针不更新，那拷贝的动态空间会不会用不了 --不会，因为指针指向的是虚拟内存的地址

why not recommend exit without fclose? exit will flush buffer and close resources.

all process created by fork? no, init

interruptible process change to terminated status directly? yes

### Lab 4

connection: fifo is connection. Have to build a channel for connection. message queue is not connection. 

message queue readers read the messages in turns. not all messages available to one reader.

parent is suspended: receive the sigttou. 父进程还是background 运行tcsetpgrp会触发SIGTTOU. only team leader of foreground process can give foreground to others. 

后台进程使用tcsetpgfp后挂起，后台进程读stdin会被挂起，写的话不会，挂起后不能写

a process can set group id for itself or its children.

父进程没了子进程会收到信号？为啥子进程运行的vim会停止

### Lecture 5

difference between hardware software interrupt: key board, function calls (divided by 0)

 what cause context switch: scheduling, interrupts, user and kernel switching (only in some os)

interruptible vs un-interruptible:  can or cannot be terminated. 

context switch steps: save registers and pc into pcb. scheduler decides which to run. restore data from the decided process's pcb.

cache vs buffer cache? 

上次讲IO不算sys time，为啥IO bound说因为IO导致sys>user ? 可能是因为频繁io导致系统时间变长？

round-robin: 进程到了可以立即执行； 只有一个进程时，时间用完了再给时间也算context switch

nice value: used in user processes, lower number, higher priority.

### Lab 5

the whole process terminates if the main thread exit or a thread calls exit(). 

the return value of pthread_create on success is 0, on error is an error value.

pthread_join() has to specify which thread to wait for.

a thread is not joinable if:

- It was default-constructed(not initialized)
- If either of its member join or detach has been called
- It has been moved elsewhere

### Lecture 6

Things in a TCB:

- Thread Identifier: Unique id (tid) is assigned to every new thread
- [Stack pointer](https://en.wikipedia.org/wiki/Stack_pointer): Points to thread's stack in the process
- [Program counter](https://en.wikipedia.org/wiki/Program_counter): Points to the current program instruction of the thread
- State of the thread (running, ready, waiting, start, done)
- Thread's [register](https://en.wikipedia.org/wiki/Processor_register) values
- Pointer to the [Process control block](https://en.wikipedia.org/wiki/Process_control_block) (PCB) of the process that the thread lives on



There is no block state in thread lifecycle compared with process lifecycle



- process/thread level parallelism
- data level parallelism
- instruction level parallelism: simd

hyperthreading 可以算是multi programming

multiprocessing == multiple CPU

multiprogramming == multiple jobs or processes

multithreading == multiple threads per process

pipe is in kernel space

what is synchronization? deal with two or more process and work normally

shared objects can only be in kernel space

a race condition means the outcome of an execution depends on a particular
order in which the shared resource is accessed.

what is bounded waiting? Bounded waiting says that a bound must exist on the number of times that other processes are allowed to enter their critical sections after a process has made a request to enter its critical section and before that request is granted.

### lab 6

two process try to acquire mutex simultaneously: in hardware there is a bus arbitrator that permits only one core to control the bus that links those two cores. 

Mutex是sleep，然后OS kernel唤醒 (unblock) 了再check，若mutex lock则再block

若是多核cpu，父母切换很频繁，self spin更好，不需要context switch

sem_unlink 和close的区别：

```
sem_close: close's a semaphore, this also done when a process exits. the semaphore still remains in the system.

sem_unlink: will be removed from the system only when the reference count reaches 0 (that is after all processes that have it open, call sem_close or are exited).
```

mutex是信号量1的信号量锁. Binary semaphore is also known as mutex lock. (but lab slides 5 says not exactly the same)

condition wait是挂起（sleep）。

多牛奶问题：

```
为什么是while不是if：

过程：哥哥爸爸有牛奶睡觉（condwait），妈妈我看到没牛奶发信号然后释放mutex，哥哥爸爸收到信号后醒来，发现无mutex再挂起等mutex，获得mutex的lock然后买牛奶，回来再unlock。有while会再去确认牛奶数量，防止迟获得mutex的也去买牛奶。(cond signal有的是广播，有的是发给一个人，持保留意见)

（lab课上意见：可能是因为invalid唤醒（功用的信号量）？也可能是多个生产者？有可能指令不是立刻执行，需要过一段时间执行，需要重新check。文档说wait不成功就跳过，类似于spin？）

先unlock还是先发信号？先发signal唤醒之后更稳，防止想喝牛奶的抢到mutex
```

### Lecture 7

three principles? mutual exclusion, bounded waiting, progress

bounded waiting: 等待时间有上界 progress：等着的人在空的时候最终能用到 。

Peterson's solution support more than 2 processes??? might in the exam. 

Peterson's solution suffers from Priority Inversion

meaning of turn in Peterson's solution? Peterson中的turn像是在标记谁是最后产生兴趣的，就等待。	

how many methods to achieve mutual exclusion? what are their drawbacks? maybe in exam

what is disable_interrupt()? to ensure atomic principle. disable context switch (只考虑单核cpu).

can use semaphore to solve IPC problems, such as ls | less.

there will be a problem in these 3 syn problems in exam. producer-consumer, dining philosopher, reader-writer

### Lecture 8



bonus：三个task：哪里交通更繁忙，哪里是市中心，标注两个地区之间的路径。

starvation == deadlock? no, starvation may be caused by other factors. starvation means to wait indefinitely.

how to handle deadlock: 1. detect algorithm and force terminate. 2. monitor  lock acquisition and deny. 3. ignore the problem (in most OS, including UNIX).

how to detect deadlocks? 

 ### Lab 8

P2 (a) 可用不等式给出数学证明。最少有5次增加。

![Screenshot from 2020-04-08 16-49-07](/home/young/Pictures/Screenshot from 2020-04-08 16-49-07.png)

P2 (b) 10，12，14，16，18，20, threadA写入前可能被调度到B B更新x后又被A用旧的值更新，造成x实际上虽然应该+4但是实际只+2，可能出现0~5次此情况 

ctrl+C给进程组发信号，当前进程和fork的子进程都结束。或者kill -9 -pid

user login by remote ssh will create a new process

execution和pc不在线程间共享

fork, exec* and wait需要syscall来实现

P5 6. A 7. BC 8. A 9. C (选出不会用到user space的操作) 10. B 11. A 12. B 14. C 15. A 16. B 17. D 18. B 19. C 20. D

 


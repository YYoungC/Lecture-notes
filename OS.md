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
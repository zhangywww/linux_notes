# core dump

linux中一些特定信号的默认动作是把一个程序杀掉，然后产生一个core dump文件。core_dump文件中包含了程序被杀掉的那个时刻的内存镜像(例如可以通过这个内存镜像查看程序被杀掉时的函数调用堆栈)。使用一些调试器如gdb可以加载core dump文件来查看程序被杀掉时的一些状态，从而查找程序出错的原因。

## core dump 信号

## 不能产生core dump文件的情况

1. 目录不可写(权限不够)

> 通常core dump文件会生成在程序运行的工作目录，通常时当前目录。如果生成core dump文件的目录对于程序的 ??user来说没有可写入的权限，则无法生成corefile。或者生成的core dump文件已经存在同名的文件，且该文件是不可写入的，如权限不够、是目录或符号链接等，此时也无法生成core dump文件。core_dump文件中包含了程序被杀掉的那个时刻的内存镜像

2. 存在同名文件，但硬链接数大于1

> 虽然core dump同名文件具有可写入的权限，但是当该文件的硬链接数大于1时，也无法生成core dump文件。

3. 文件系统原因(容量不足等)

> 当将要产生core dump文件的文件系统没有更多的空间，或者没有更多的inode可用，或者文件系统以只读的方式挂载，或者用户已经达到了quota的磁盘容量配置上限。

4. 目录不存在

> 当将要产生core dump文件的目录不存在时，core dump文件无法生成。

5. 程序的RLIMIT_CORE或RLIMIT_FSIZE被设置成0

> 当程序的RLIMIT_CORE(对于core dump文件大小) 或 RLIMIT_FSIZE(对于文件大小)资源限制被设置成0时，无法生成core dump文件。 查看**getrlimit**和**ulimit**文件，获取RLIMIT_CORE和RLIMIT_SIZE的更多信息。

6. 程序的内存没有可读权限。

7. 程序执行了一个set-user-ID(不同的与real user)程序

8. 从linux3.7开始，如果内核没有配置**CONFIG_COREDUMP**选项，则不会生成core dump文件



## core文件命名 /proc/sys/kernel/core_pattern

core dump文件的默认命名为core。自从linux2.6开始，可以通过/proc/sys/kernel/core_pattern文件来设置生成core dump文件的命名规则。

```
%%  %字符
%c	core文件 软资源限制 (对于down掉的程序来说)
%d	dump的模式，和prctl PR_GET_DUMPABLE 返回值相同
%e  可执行文件的名字(不带路径前缀) (自从Linux3.7)
%E	可执行文件的pathname，并用!替代/，(自从Linux3.0)
%g	down掉进程的real GID
%h	hostname，(和uname返回的nodename相同)
%i	触发core dump的线程的TID，(正如线程所在的PID namespace中的一样)，(自从Linux3.18)
%I	触发core dump的进程的TID，(正如初始的PID namespace中的一样)
%p	触发core dump的进程的PID，(正如进程所在的PID namespace中的一样)
%P	触发core dump的进程的PID，(正如初始的PID namespace中的一样)
%s	导致core dump的信号的数值
%t	dump的时间，表示为自从UTC1970-01-01 00:00::00到现在的秒数
%u	down掉进程的real UID
```
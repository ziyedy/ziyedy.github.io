---
title: Python的线程与进程
date: 2020-05-18 09:59:00
tags:
	- Python
categories:
	- Python
	- Python基础
fileName: python7
---









内存池机制：当创建大量消耗小内存的对象时，频繁调用new/malloc会导致大量的内存碎片，使效率降低。

内存池就是预先在内存中申请一定数量的、大小相等的内存块留作备用，当有新的内存需求时，就先从内存池中分配内存给这个需求，不够了再申请新的内存，这样做最显著的优势就是能够减小内存碎片，提升效率



pymalloc

针对小对象，pymalloc会在内存池中申请内存空间

大对象，申请新的内存空间





### Python多线程

进程：一个执行中的程序，每个进程都拥有自己的地址空间、内存、数据栈以及其他用于跟踪执行的辅助数据。操作系统管理其上所有进程，并为这些进程合理分配时间。



threading模块

线程：在同一个进程下执行，并共享相同的上下文。一个进程中的各个线程与主线程共享同一片数据空间。

线程包括开始、执行顺序、结束三部分

可以被抢占（中断）和临时挂起（睡眠）

一般以并发方式执行（是一种属性，并行只是并发问题的可能方法之一，若两个事件互不影响，则两个事件是并发的）



对多核CPU的应用

GIL——全局解释器锁（强制在任何时候只有一个线程可以执行python代码）

I/O密集型应用（如网页之类），CPU密集型应用（数据分析，可借助C或CPP的拓展）



GIL执行顺序



实现一个线程

```
import threading
import time

class LoopThread(threading.Thread):
    n = 0
    def run(self) -> None:
        while self.n < 5:
            now_thread = threading.current_thread()
            print('loop now: {0}'.format(now_thread.name))
            print(self.n)
            time.sleep(1)
            self.n += 1

if __name__ == '__main__':
    now_thread = threading.current_thread()
    print('now: {0}'.format(now_thread.name))

    t = LoopThread(name='loop_thread')
    t.start()
    t.join()
    
############################################
now: MainThread
loop now: loop_thread
0
loop now: loop_thread
1
loop now: loop_thread
2
loop now: loop_thread
3
loop now: loop_thread
4
```



多线程并发

```
import threading
import time

num = 0

def test(n):
    global num
    num += n
    time.sleep(2)
    num -= n
    time.sleep(1)
    print('----------------{0}'.format(num))

class testThread(threading.Thread):
    def __init__(self, n, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.n = n

    def run(self) -> None:
        for i in range(1000):
            test(self.n)

if __name__ == '__main__':
    t1 = testThread(1)
    t2 = testThread(2)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    
############################################
----------------0
----------------2
----------------0
----------------2
----------------0
----------------1
----------------0
----------------1
```



多线程中的锁

`RLock`在一个线程中锁住了还能够再锁

不能重复锁，否则资源被死锁

```
import threading
import time

num = 0
lock = threading.Lock()

def test(n):
    global num
    num += n
    time.sleep(2)
    num -= n
    time.sleep(1)
    print('----------------{0}'.format(num))

class testThread(threading.Thread):
    def __init__(self, n, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.n = n

    def run(self) -> None:
        for i in range(1000):
            lock.acquire()
            try:
                test(self.n)
            finally:
                lock.release()

if __name__ == '__main__':
    t1 = testThread(1)
    t2 = testThread(2)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    
###########################################
----------------0
----------------0
----------------0
----------------0
----------------0
----------------0
```



产生死锁

```
def run(self) -> None:
    for i in range(1000):
        print('start lock')
        lock.acquire()
        print('lock again')
        lock.acquire()
        print('lock end')
        try:
            test(self.n)
        finally:
            lock.release()
            
###########################################
start lock
lock again
start lock
```





线程的调度与优化

```
import threading, time
from multiprocessing.dummy import Pool as ThreadPool

def run(n):
    time.sleep(1)
    print(threading.current_thread().name, n)

def test():
    t1 = time.time()
    for n in range(10):
        run(n)
    print(time.time() - t1)


if __name__ == '__main__':
    test()
    
########################################
MainThread 0
MainThread 1
MainThread 2
MainThread 3
MainThread 4
MainThread 5
MainThread 6
MainThread 7
MainThread 8
MainThread 9
10.0053870677948
```



快了很多

```
import threading, time
from multiprocessing.dummy import Pool as ThreadPool

def run(n):
    time.sleep(1)
    print(threading.current_thread().name, n)

def test_use_thread():
    t1 = time.time()
    ls = []
    for i in range(10):
        t = threading.Thread(target=run, args=(i,))
        ls.append(t)
        t.start()

    for l in ls:
        l.join()
    print(time.time() - t1)

def test():
    t1 = time.time()
    for n in range(10):
        run(n)
    print(time.time() - t1)


if __name__ == '__main__':
    test_use_thread()
    
###############################################
Thread-2 1
Thread-3 2
Thread-1 0
Thread-5 4
Thread-4 3
Thread-6 5
Thread-10 9
Thread-8 7
Thread-9 8
Thread-7 6
1.0038871765136719
```



```
def test_use_pool():
    t1 = time.time()
    n_list = range(100)
    pool = Pool(10)
    pool.map(run, n_list)
    pool.close()
    pool.join()
    print(time.time() - t1)
```



达到了线程的复用





### Python进程

进程的实现

```
import time
from multiprocessing import Process

def run(name):
    """
    name: 进程名称
    """
    print("进程名称：{}".format(name))
    time.sleep(5)
    print("进程完成")

if __name__ == '__main__':
    p = Process(target=run, args=('my process',))
    p.start()
    p.join()
    
##############################################
进程名称：my process
进程完成
```

使用过os模块下的`os.getpid()`可以得到当前进程的ID



进程间的通信



多进程中的锁：
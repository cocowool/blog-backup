---
title: Python3.6学习笔记（四）
date: 2017-03-30 14:25
tag: 
keywords: python, python 3.6, python学习笔记, python基础
description: Python3.6学习笔记，Python错误处理，调试和测试的一般方法。
---


## 错误、调试和测试
程序运行中，可能会遇到BUG、用户输入异常数据以及其它环境的异常，这些都需要程序猿进行处理。Python提供了一套内置的异常处理机制，供程序猿使用，同时PDB提供了调试代码的功能，除此之外，程序猿还应该掌握测试的编写，确保程序的运行符合预期。

### 错误处理
在一般程序处理中，可以对函数的返回值进行检查，是否返回了约定的错误码。例如系统程序调用的错误码一般都是```-1```，成功返回```0```。但是这种方式必须用大量的代码来判断是否出错，所以高级语言内置了```try...except...finally```的错误机制。
```python
try:
    print('try...')
    r = 10 / int('2')
    print('result:', r)
except ValueError as e:
    print('ValueError:', e)
except ZeroDivisionError as e:
    print('ZeroDivisionError:', e)
else:
    print('no error!')
finally:
    print('finally...')
print(‘END’）
```
当我们认为某些代码可能会出错时，就可以用```try```来运行这段代码，如果执行出错，则后续代码不会继续执行，而是直接跳转至错误处理代码，即```except```语句块，执行完```except```后，如果有```finally```语句块，则执行```finally```语句块，至此，执行完毕。如果发生了不同的错误类型，可以由不同的except语句块处理，可以没有```finally```语句块。
Python的错误也是类，所有的错误类型都继承自```BaseException```，常见的错误类型和继承关系参考[官方文档](https://docs.python.org/3/library/exceptions.html#exception-hierarchy)
> 使用```try...except```捕获错误还有一个巨大的好处，就是可以跨越多层调用，比如函数main()调用foo()，foo()调用bar()，结果bar()出错了，这时，只要main()捕获到了，就可以处理。


### 调用堆栈
如果错误没有被捕获，它就会一直往上抛，最后被Python解释器捕获，打印一个错误信息，然后程序退出。出错并不可怕，可怕的是不知道哪里出错了。解读错误信息是定位错误的关键。

### 记录错误
在C语言中，如果发生错误想要记录，必须自己编写错误记录的程序。Python内置的```logging```模块可以非常容易地记录错误信息。通过配置，logging还可以把错误记录到日志文件里，方便事后排查。
```python
# err_logging.py

import logging

def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2

def main():
    try:
        bar('0')
    except Exception as e:
        logging.exception(e)

main()
print('END')
```

### 抛出错误
抛出错误，首先需要定义一个错误 Class，选择好继承关系，然后用```raise```语句抛出一个错误实例。如果可以尽量使用Python内置的错误类型，仅在非常必要的时候自己定义错误类。
```python
# err_raise.py
class FooError(ValueError):
    pass

def foo(s):
    n = int(s)
    if n==0:
        raise FooError('invalid value: %s' % s)
    return 10 / n

foo('0')
```

### 调试 Debug
调试最简单的办法就是```print()```，这个方法最简单，但是在发布的时候需要把所有的调试信息注释掉。

#### 断言 assert
凡是用```print()```来辅助查看的地方，都可以用断言（```assert```）来替代。
```python
def foo(s):
    n = int(s)
    assert n != 0, 'n is zero!'
    return 10 / n

def main():
    foo('0')
```
assert的意思是，表达式n != 0应该是True，否则，根据程序运行的逻辑，后面的代码肯定会出错。如果断言失败，```assert```语句本身就会抛出```AssertionError```。
启动Python解释器时可以用-O参数来关闭assert。

#### logging
使用 logging 不仅可以抛出错误，还可以输出到文件。
```python
import logging
logging.basicConfig(level=logging.INFO)

s = '0'
n = int(s)
logging.info('n = %d' % n)
print(10 / n)
```
这就是logging的好处，它允许你指定记录信息的级别，有```debug，info，warning，error```等几个级别，当我们指定```level=INFO```时，```logging.debug```就不起作用了。同理，指定level=WARNING后，debug和info就不起作用了。这样一来，你可以放心地输出不同级别的信息，也不用删除，最后统一控制输出哪个级别的信息。
logging的另一个好处是通过简单的配置，一条语句可以同时输出到不同的地方，比如console和文件。

#### pdb
可以在命令行下使用pdb,启动Python的调试器pdb，让程序以单步方式运行，可以随时查看运行状态。
```python
# err.py
s = '0'
n = int(s)
print(10 / n)

$ python3 -m pdb err.py
> /Users/michael/Github/learn-python3/samples/debug/err.py(2)<module>()
-> s = ‘0'
```
输入```1```可以查看代码，输入```n```可以单步执行代码。使用```p```来查看变量，使用```q```退出调试。

#### pdb.set_trace()
这个方法也是用pdb，但是不需要单步执行，我们只需要import pdb，然后，在可能出错的地方放一个pdb.set_trace()，就可以设置一个断点。运行代码，程序会自动在pdb.set_trace()暂停并进入pdb调试环境，可以用命令p查看变量，或者用命令```c```继续运行。

### 单元测试
单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作。

### 文档测试
doctest非常有用，不但可以用来测试，还可以直接作为示例代码。通过某些文档生成工具，就可以自动把包含doctest的注释提取出来。用户看文档的时候，同时也看到了doctest。

## IO编程
IO就是Input / Output ，也就是输入和输出。IO编程中，Stream（流）是一个很重要的概念，可以把流想象成一个水管，数据就是水管里的水，但是只能单向流动。
由于计算机各个部件之间的速度不一致，所以处理IO问题时有两种办法：同步IO、异步IO。同步和异步的区别就在于是否等待IO执行的结果。

### 文件读写
读写文件是最常见的IO操作。Python内置了读写文件的函数，用法和C是兼容的。在磁盘上读写文件的功能都是由操作系统提供的，现代操作系统不允许普通的程序直接操作磁盘，所以，读写文件就是请求操作系统打开一个文件对象（通常称为文件描述符），然后，通过操作系统提供的接口从这个文件对象中读取数据（读文件），或者把数据写入这个文件对象（写文件）。

#### 读文件
```python
try:
    f = open('/path/to/file', 'r')
    print(f.read())
finally:
    if f:
        f.close()

with open('/path/to/file', 'r') as f:
    print(f.read())
```
类似于c语言，open函数默认接收一个文件名、一个打开模式参数（```r```、```w```默认对应文本文件，```rb```对应二进制文件）。默认打开的是UTF-8编码的文件，如果需要打开其它编码的，需要传入```encoding```参数，如果文本的编码不一致可能导致读取出错，可以传入错误处理参数```errors```。```read```方法一次将文件的所有内容读入内存，可以通过参数指定读入的长度```read(size)```，也可以使用```readline```方法每次读入一行，使用```readlines```一次读入所有的行。文件使用后注意要进行关闭。

#### 写文件
```python
>>> f = open('/Users/michael/test.txt', 'w')
>>> f.write('Hello, world!')
>>> f.close()

with open('/Users/michael/test.txt', 'w') as f:
    f.write('Hello, world!’)
```
写文件和读文件是一样的，唯一区别是调用open()函数时，传入标识符```’w’```或者```’wb’```表示写文本文件或写二进制文件。当我们写文件时，操作系统往往不会立刻把数据写入磁盘，而是放到内存缓存起来，空闲的时候再慢慢写入。只有调用close()方法时，操作系统才保证把没有写入的数据全部写入磁盘。

### StringIO 和 BytesIO
很多时候，数据读写不一定是文件，也可以在内存中读写。StringIO顾名思义就是在内存中读写str。
```python
>>> from io import StringIO
>>> f = StringIO()
>>> f.write('hello')
5
>>> f.write(' ')
1
>>> f.write('world!')
6
>>> print(f.getvalue())
hello world!

>>> from io import StringIO
>>> f = StringIO('Hello!\nHi!\nGoodbye!')
>>> while True:
...     s = f.readline()
...     if s == '':
...         break
...     print(s.strip())
...
Hello!
Hi!
Goodbye!
```
StringIO操作的只能是str，如果要操作二进制数据，就需要使用BytesIO。BytesIO实现了在内存中读写bytes。

### 操作文件和目录
Python内置的```os```模块也可以直接调用操作系统提供的接口函数。import os模块后，就可以调用一些系统命令。
```python
>>> import os
>>> os.name # 操作系统类型
'posix'
>>> os.uname()
posix.uname_result(sysname='Darwin', nodename='RousseaudeMacBook-Pro.local', release='15.6.0', version='Darwin Kernel Version 15.6.0: Mon Jan  9 23:07:29 PST 2017; root:xnu-3248.60.11.2.1~1/RELEASE_X86_64', machine='x86_64')
>>> os.environ
environ({'TERM_PROGRAM': 'Apple_Terminal', 'SHELL': '/bin/bash', 'TERM': 'xterm-256color', 'TMPDIR': '/var/folders/95/zrdts1md6j942mpyd7kd875h0000gn/T/', 'Apple_PubSub_Socket_Render': '/private/tmp/com.apple.launchd.fhDfjTsyk6/Render', 'TERM_PROGRAM_VERSION': '361.1', 'OLDPWD': '/Users/rousseau/Projects/python.my', 'TERM_SESSION_ID': '5A1B275C-3BE5-4673-B163-29DFF5C19C77', 'USER': 'rousseau', 'SSH_AUTH_SOCK': '/private/tmp/com.apple.launchd.mLtAPJeOFm/Listeners', '__CF_USER_TEXT_ENCODING': '0x1F5:0x0:0x0', 'PATH': '/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin', 'PWD': '/Users/rousseau/Projects/python.my/mypython', 'XPC_FLAGS': '0x0', 'XPC_SERVICE_NAME': '0', 'SHLVL': '1', 'HOME': '/Users/rousseau', 'LOGNAME': 'rousseau', 'LC_CTYPE': 'UTF-8', '_': '/usr/local/bin/python3', '__PYVENV_LAUNCHER__': '/usr/local/bin/python3’})
>>> os.path.abspath('.')    # 查看当前目录的绝对路径:
'/Users/rousseau/Projects/python.my/mypython’
# 在某个目录下创建一个新目录，首先把新目录的完整路径表示出来:
>>> os.path.join('/Users/michael', 'testdir')
'/Users/michael/testdir'
# 然后创建一个目录:
>>> os.mkdir('/Users/michael/testdir')
# 删掉一个目录:
>>> os.rmdir('/Users/michael/testdir’)
```
因为Windows和Unix的路径表达方式不一样，所以在处理路径时，尽量使用Python提供的```os.path.join()```和```os.path.split()```避免处理发生问题。其它的文件处理函数```os.rename```、```os.remove```。

### 序列化
序列号我理解的就是将内存中变量的状态和值转换为文本，以方便进行持久化的存储，也可能不进行存储，但是序列话之后方便进行传输。我们把变量从内存中变成可存储或传输的过程称之为序列化，在Python中叫pickling，在其他语言中也被称之为serialization，marshalling，flattening等等，都是一个意思。反过来，把变量内容从序列化的对象重新读到内存里称之为反序列化，即unpickling。
Python提供了```pickle```模块来实现序列化。
```python
>>> import pickle
>>> d = dict(name='Bob', age=20, score=88)
>>> pickle.dumps(d)
b'\x80\x03}q\x00(X\x03\x00\x00\x00ageq\x01K\x14X\x05\x00\x00\x00scoreq\x02KXX\x04\x00\x00\x00nameq\x03X\x03\x00\x00\x00Bobq\x04u.’

#也可以将序列化的内容写入文本
>>> f = open('dump.txt', 'wb')
>>> pickle.dump(d, f)
>>> f.close()

#读取的过程
>>> f = open('dump.txt', 'rb')
>>> d = pickle.load(f)
>>> f.close()
>>> d
{'age': 20, 'score': 88, 'name': ‘Bob’}
```

### JSON
我们要在不同的编程语言之间传递对象，就必须把对象序列化为标准格式，比如XML，但更好的方法是序列化为JSON，因为JSON表示出来就是一个字符串，可以被所有语言读取，也可以方便地存储到磁盘或者通过网络传输。JSON不仅是标准格式，并且比XML更快，而且可以直接在Web页面中读取，非常方便。
JSON表示的对象就是标准的JavaScript语言的对象，JSON和Python内置的数据类型对应如下:

Python内置的json模块提供了非常完善的Python对象到JSON格式的转换。
```python
>>> json_str = '{"age": 20, "score": 88, "name": "Bob"}'
>>> json.loads(json_str)
{'age': 20, 'score': 88, 'name': ‘Bob’}
```

### 进程和线程
进程是程序运行的最小单位，线程是进程内部的子任务。多任务的实现模式：多进程、多线程、多进程＋多线程。

#### 多进程
Unix/Linux操作系统提供了一个```fork()```系统调用，它非常特殊。普通的函数调用，调用一次，返回一次，但是fork()调用一次，返回两次，因为操作系统自动把当前进程（称为父进程）复制了一份（称为子进程），然后，分别在父进程和子进程内返回。子进程永远返回0，而父进程返回子进程的ID。这样做的理由是，一个父进程可以fork出很多子进程，所以，父进程要记下每个子进程的ID，而子进程只需要调用getppid()就可以拿到父进程的ID。
```python
import os

print('Process (%s) start...' % os.getpid())
# Only works on Unix/Linux/Mac:
pid = os.fork()
if pid == 0:
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid))
```
由于Windows环境没有```fork```调用，为了编写具备跨平台能力的代码，建议使用Python提供的multiprocessing模块。

#### multiprocessing
multiprocessing模块提供了一个Process类来代表一个进程对象。创建子进程时，只需要传入一个执行函数和函数的参数，创建一个Process实例，用```start()```方法启动，这样创建进程比fork()还要简单。```join()```方法可以等待子进程结束后再继续往下运行，通常用于进程间的同步。
```python
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))
    print('Child process will start.')
    p.start()
    p.join()
    print('Child process end.’)
```

#### Pool
可以使用进程池的方式，创建大量的子进程。对Pool对象调用join()方法会等待所有子进程执行完毕，调用join()之前必须先调用```close()```，调用close()之后就不能继续添加新的Process了。
```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(4)
    for i in range(5):
        p.apply_async(long_time_task, args=(i,))
    print('Waiting for all subprocesses done...')
    p.close()
    p.join()
    print('All subprocesses done.')
```

#### 进程间通信
Python的multiprocessing模块包装了底层的机制，提供了Queue、Pipes等多种方式来交换数据。
```python
from multiprocessing import Process, Queue
import os, time, random

# 写数据进程执行的代码:
def write(q):
    print('Process to write: %s' % os.getpid())
    for value in ['A', 'B', 'C']:
        print('Put %s to queue...' % value)
        q.put(value)
        time.sleep(random.random())

# 读数据进程执行的代码:
def read(q):
    print('Process to read: %s' % os.getpid())
    while True:
        value = q.get(True)
        print('Get %s from queue.' % value)

if __name__=='__main__':
    # 父进程创建Queue，并传给各个子进程：
    q = Queue()
    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))
    # 启动子进程pw，写入:
    pw.start()
    # 启动子进程pr，读取:
    pr.start()
    # 等待pw结束:
    pw.join()
    # pr进程里是死循环，无法等待其结束，只能强行终止:
    pr.terminate()
```

### 多线程
多任务可以由多进程完成，也可以由一个进程内的多线程完成。一个进程至少有一个线程。由于线程是操作系统直接支持的执行单元，因此，高级语言通常都内置多线程的支持，Python也不例外，并且，Python的线程是真正的Posix Thread，而不是模拟出来的线程。
Python的标准库提供了两个模块：```_thread```和```threading```，_thread是低级模块，threading是高级模块，对_thread进行了封装。绝大多数情况下，我们只需要使用```threading```这个高级模块。
由于任何进程默认就会启动一个线程，我们把该线程称为主线程，主线程又可以启动新的线程，Python的threading模块有个```current_thread()```函数，它永远返回当前线程的实例。主线程实例的名字叫```MainThread```，子线程的名字在创建时指定，如果不起名字Python就自动给线程命名为Thread-1，Thread-2……

#### Lock
多线程和多进程最大的不同在于，多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响，而多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了。
要解决上述问题，需要通过加锁来解决。创建一个锁就是通过｀``threading.Lock()```来实现，当多个线程同时执行lock.acquire()时，只有一个线程能成功地获取锁，然后继续执行代码，其他线程就继续等待直到获得锁为止。
锁的好处就是确保了某段关键代码只能由一个线程从头到尾完整地执行，坏处当然也很多，首先是阻止了多线程并发执行，包含锁的某段代码实际上只能以单线程模式执行，效率就大大地下降了。其次，由于可以存在多个锁，不同的线程持有不同的锁，并试图获取对方持有的锁时，可能会造成死锁，导致多个线程全部挂起，既不能执行，也无法结束，只能靠操作系统强制终止。
```python
import time, threading

# 假定这是你的银行存款:
balance = 0
lock = threading.Lock()

def run_thread(n):
    for i in range(100000):
        # 先要获取锁:
        lock.acquire()
        try:
            # 放心地改吧:
            change_it(n)
        finally:
            # 改完了一定要释放锁:
            lock.release()

def run_thread(n):
    for i in range(100000):
        change_it(n)

t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))
t1.start()
t2.start()
t1.join()
t2.join()
print(balance)
```

#### 多核CPU
Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。
GIL是Python解释器设计的历史遗留问题，通常我们用的解释器是官方实现的CPython，要真正利用多核，除非重写一个不带GIL的解释器。

### ThreadLocal
在多线程环境中，每个线程处理数据最好使用局部变量，但是需要在不同线程间传递参数的时候，会变的很麻烦。```ThreadLocal```提供了创建与线程名称关联的局部变量功能能。```ThreadLocal```最常用的地方就是为每个线程绑定一个数据库连接，HTTP请求，用户身份信息等，这样一个线程的所有调用到的处理函数都可以非常方便地访问这些资源。
```python
import threading

# 创建全局ThreadLocal对象:
local_school = threading.local()

def process_student():
    # 获取当前线程关联的student:
    std = local_school.student
    print('Hello, %s (in %s)' % (std, threading.current_thread().name))

def process_thread(name):
    # 绑定ThreadLocal的student:
    local_school.student = name
    process_student()

t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
t1.start()
t2.start()
t1.join()
t2.join()
```

### 进程 VS 线程
要实现多任务，通常我们会设计Master-Worker模式，Master负责分配任务，Worker负责执行任务，因此，多任务环境下，通常是一个Master，多个Worker。
如果用多进程实现Master-Worker，主进程就是Master，其他进程就是Worker。
如果用多线程实现Master-Worker，主线程就是Master，其他线程就是Worker。
多进程模式最大的优点就是稳定性高，因为一个子进程崩溃了，不会影响主进程和其他子进程。（当然主进程挂了所有进程就全挂了，但是Master进程只负责分配任务，挂掉的概率低）著名的Apache最早就是采用多进程模式。
多进程模式的缺点是创建进程的代价大，在Unix/Linux系统下，用fork调用还行，在Windows下创建进程开销巨大。另外，操作系统能同时运行的进程数也是有限的，在内存和CPU的限制下，如果有几千个进程同时运行，操作系统连调度都会成问题。
多线程模式通常比多进程快一点，但是也快不到哪去，而且，多线程模式致命的缺点就是任何一个线程挂掉都可能直接造成整个进程崩溃，因为所有线程共享进程的内存。在Windows上，如果一个线程执行的代码出了问题，你经常可以看到这样的提示：“该程序执行了非法操作，即将关闭”，其实往往是某个线程出了问题，但是操作系统会强制结束整个进程。

#### 计算密集型 vs. IO密集型
是否采用多任务的第二个考虑是任务的类型。我们可以把任务分为计算密集型和IO密集型。
计算密集型任务的特点是要进行大量的计算，消耗CPU资源，比如计算圆周率、对视频进行高清解码等等，全靠CPU的运算能力。这种计算密集型任务虽然也可以用多任务完成，但是任务越多，花在任务切换的时间就越多，CPU执行任务的效率就越低，所以，要最高效地利用CPU，计算密集型任务同时进行的数量应当等于CPU的核心数。
计算密集型任务由于主要消耗CPU资源，因此，代码运行效率至关重要。Python这样的脚本语言运行效率很低，完全不适合计算密集型任务。对于计算密集型任务，最好用C语言编写。
第二种任务的类型是IO密集型，涉及到网络、磁盘IO的任务都是IO密集型任务，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待IO操作完成（因为IO的速度远远低于CPU和内存的速度）。对于IO密集型任务，任务越多，CPU效率越高，但也有一个限度。常见的大部分任务都是IO密集型任务，比如Web应用。
IO密集型任务执行期间，99%的时间都花在IO上，花在CPU上的时间很少，因此，用运行速度极快的C语言替换用Python这样运行速度极低的脚本语言，完全无法提升运行效率。对于IO密集型任务，最合适的语言就是开发效率最高（代码量最少）的语言，脚本语言是首选，C语言最差。

#### 异步IO
考虑到CPU和IO之间巨大的速度差异，一个任务在执行的过程中大部分时间都在等待IO操作，单进程单线程模型会导致别的任务无法并行执行，因此，我们才需要多进程模型或者多线程模型来支持多任务并发执行。
现代操作系统对IO操作已经做了巨大的改进，最大的特点就是支持异步IO。如果充分利用操作系统提供的异步IO支持，就可以用单进程单线程模型来执行多任务，这种全新的模型称为事件驱动模型，Nginx就是支持异步IO的Web服务器，它在单核CPU上采用单进程模型就可以高效地支持多任务。在多核CPU上，可以运行多个进程（数量与CPU核心数相同），充分利用多核CPU。由于系统总的进程数量十分有限，因此操作系统调度非常高效。用异步IO编程模型来实现多任务是一个主要的趋势。
对应到Python语言，单进程的异步编程模型称为协程。

### 分布式进程
Python的multiprocessing模块不但支持多进程，其中managers子模块还支持把多进程分布到多台机器上。一个服务进程可以作为调度者，将任务分布到其他多个进程中，依靠网络通信。由于managers模块封装很好，不必了解网络通信的细节，就可以很容易地编写分布式多进程程序。
```python
# task_master.py

import random, time, queue
from multiprocessing.managers import BaseManager

# 发送任务的队列:
task_queue = queue.Queue()
# 接收结果的队列:
result_queue = queue.Queue()

# 从BaseManager继承的QueueManager:
class QueueManager(BaseManager):
    pass

# 把两个Queue都注册到网络上, callable参数关联了Queue对象:
QueueManager.register('get_task_queue', callable=lambda: task_queue)
QueueManager.register('get_result_queue', callable=lambda: result_queue)
# 绑定端口5000, 设置验证码'abc':
manager = QueueManager(address=('', 5000), authkey=b'abc')
# 启动Queue:
manager.start()
# 获得通过网络访问的Queue对象:
task = manager.get_task_queue()
result = manager.get_result_queue()
# 放几个任务进去:
for i in range(10):
    n = random.randint(0, 10000)
    print('Put task %d...' % n)
    task.put(n)
# 从result队列读取结果:
print('Try get results...')
for i in range(10):
    r = result.get(timeout=10)
    print('Result: %s' % r)
# 关闭:
manager.shutdown()
print('master exit.’)
```
```python
# task_worker.py

import time, sys, queue
from multiprocessing.managers import BaseManager

# 创建类似的QueueManager:
class QueueManager(BaseManager):
    pass

# 由于这个QueueManager只从网络上获取Queue，所以注册时只提供名字:
QueueManager.register('get_task_queue')
QueueManager.register('get_result_queue')

# 连接到服务器，也就是运行task_master.py的机器:
server_addr = '127.0.0.1'
print('Connect to server %s...' % server_addr)
# 端口和验证码注意保持与task_master.py设置的完全一致:
m = QueueManager(address=(server_addr, 5000), authkey=b'abc')
# 从网络连接:
m.connect()
# 获取Queue的对象:
task = m.get_task_queue()
result = m.get_result_queue()
# 从task队列取任务,并把结果写入result队列:
for i in range(10):
    try:
        n = task.get(timeout=1)
        print('run task %d * %d...' % (n, n))
        r = '%d * %d = %d' % (n, n, n*n)
        time.sleep(1)
        result.put(r)
    except Queue.Empty:
        print('task queue is empty.')
# 处理结束:
print('worker exit.’)
```













---
layout:     post                    # 使用的布局（不需要改）
title:      Java多线程（二）           # 标题 
subtitle:   Thread类      #副标题
date:       2019-06-24             # 时间
author:     Rest探路者                      # 作者
header-img: img/post-bg-re-vs-ng2.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Java多线程
---
## Thread类的实例方法
### start()
start方法内部会调用方法start方法启动一个线程，该线程返回start方法，同时Java虚拟机调用native start0启动另一个线程调用run方法，此时有两个线程并行执行；
我们来分析下start0方法，start0到底是如何调用run方法的
![](https://raw.githubusercontent.com/cjy513203427/cjy513203427.github.io/master/img/2019-06-24-Java多线程（二）：Thread类/img1.png)
Thread类里有一个本地方法叫registerNatives，此方法注册一些本地方法给Thread类使用
在[OpenJDK官网](http://hg.openjdk.java.net)找到Thread.c
```C++
#include "jni.h"
#include "jvm.h"

#include "java_lang_Thread.h"

#define THD "Ljava/lang/Thread;"
#define OBJ "Ljava/lang/Object;"
#define STE "Ljava/lang/StackTraceElement;"

#define ARRAY_LENGTH(a) (sizeof(a)/sizeof(a[0]))

static JNINativeMethod methods[] = {
    {"start0",           "()V",        (void *)&JVM_StartThread}, //Java中Thread类的start方法所调用的start0方法
    {"stop0",            "(" OBJ ")V", (void *)&JVM_StopThread},
    {"isAlive",          "()Z",        (void *)&JVM_IsThreadAlive},
    {"suspend0",         "()V",        (void *)&JVM_SuspendThread},
    {"resume0",          "()V",        (void *)&JVM_ResumeThread},
    {"setPriority0",     "(I)V",       (void *)&JVM_SetThreadPriority},
    {"yield",            "()V",        (void *)&JVM_Yield},
    {"sleep",            "(J)V",       (void *)&JVM_Sleep},
    {"currentThread",    "()" THD,     (void *)&JVM_CurrentThread},
    {"countStackFrames", "()I",        (void *)&JVM_CountStackFrames},
    {"interrupt0",       "()V",        (void *)&JVM_Interrupt},
    {"isInterrupted",    "(Z)Z",       (void *)&JVM_IsInterrupted},
    {"holdsLock",        "(" OBJ ")Z", (void *)&JVM_HoldsLock},
    {"getThreads",        "()[" THD,   (void *)&JVM_GetAllThreads},
    {"dumpThreads",      "([" THD ")[[" STE, (void *)&JVM_DumpThreads},
};

......
```
根据关键字"JVM_StartThread"再找到jvm.cpp
```C++
JVM_ENTRY(void, JVM_StartThread(JNIEnv* env, jobject jthread))
  JVMWrapper("JVM_StartThread");
  JavaThread *native_thread = NULL;
  bool throw_illegal_thread_state = false;

  {
    MutexLocker mu(Threads_lock);
    if (java_lang_Thread::thread(JNIHandles::resolve_non_null(jthread)) != NULL) {
      throw_illegal_thread_state = true;
    } else {

      jlong size =
             java_lang_Thread::stackSize(JNIHandles::resolve_non_null(jthread));
      size_t sz = size > 0 ? (size_t) size : 0;
      native_thread = new JavaThread(&thread_entry, sz); //请看这里，实例化了一个线程native_thread

      if (native_thread->osthread() != NULL) {
        // Note: the current thread is not being used within "prepare".
        native_thread->prepare(jthread);
      }
    }
  }
```
sz是大小参数，忽略之，我们看thread_entry是什么
```C++
static void thread_entry(JavaThread* thread, TRAPS) {
  HandleMark hm(THREAD);
  Handle obj(THREAD, thread->threadObj());
  JavaValue result(T_VOID);
  JavaCalls::call_virtual(&result,
                          obj,
                          KlassHandle(THREAD, SystemDictionary::Thread_klass()),
                          vmSymbols::run_method_name(),  //请看这里，jvm调用run_method_name方法
                          vmSymbols::void_method_signature(),
                          THREAD);
}
```
run_method_name在vmSymbols.hpp被定义
```C++
  /* common method and field names */                                                             
  template(run_method_name,                           "run")      //run_method_name的名称是"run"
```
简言之：当前线程调用start方法通知`ThreadGroup`当前线程可以运行了，可以被加入了，当前线程启动后，当前线程状态为"Runnable"。另一个线程等待CPU时间片，调用run方法（线程真正执行）。产生一个异步执行的效果；
用start方法来启动线程，真正实现了多线程运行，这时无需等待run方法体代码执行完毕而直接继续执行下面的代码。
代码如下
```java
public class MyThread03 extends Thread{
    public void run()
    {
        try
        {
            for (int i = 0; i < 3; i++)
            {
                Thread.sleep((int)(Math.random() * 1000));
                System.out.println("run = " + Thread.currentThread().getName());
            }
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }

    public static void main(String[] args)
    {
        MyThread03 mt = new MyThread03();
        mt.start();

        try
        {
            for (int i = 0; i < 3; i++)
            {
                Thread.sleep((int)(Math.random() * 1000));
                System.out.println("run = " + Thread.currentThread().getName());
            }
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```
执行结果如下,可以看到，Thead-0和main线程交叉执行，是无序的。很好理解，因为main和Thread-0在争抢CPU资源，这个过程是无序的。
```graph
run = main
run = Thread-0
run = main
run = main
run = Thread-0
run = Thread-0
```
再看一个例子，代码如下
```java
public class MyThread04 extends Thread{
    public void run()
    {
        System.out.println(Thread.currentThread().getName());
    }

    public static void main(String[] args)
    {
        MyThread04 mt0 = new MyThread04();
        MyThread04 mt1 = new MyThread04();
        MyThread04 mt2 = new MyThread04();

        mt0.start();
        mt1.start();
        mt2.start();
    }
}
```
执行结果如下
```graph
Thread-0
Thread-2
Thread-1
```
我们依次启动mt0,mt1,mt2，这说明线程启动顺序也是无序的。因为start方法仅仅返回调用，线程想要执行必须得到CPU时间片再执行run方法，CPU时间片的获得是无序的。

### run()
run方法是Thread类的一个普通方法，执行run方法其实是单线程执行
```java
public class MyThread05 extends Thread{

    public void run()
    {
        System.out.println("run = " + Thread.currentThread().getName());
    }

    public static void main(String[] args)
    {
        MyThread05 mt = new MyThread05();
        mt.run();

        try
        {
            for (int i = 0; i < 3; i++)
            {
                Thread.sleep((int)(Math.random() * 1000));
                System.out.println("run = " + Thread.currentThread().getName());
            }
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```
输出结果如下
```graph
run = main
run = main
run = main
run = main
```
main线程循环了3次，run方法1次，结果是main线程执行了四次，我们写在run方法体内的被main线程执行，这说明调用run方法执行多线程是不可行的。

### isAlive()
判断线程是否存活
```java
public class MyThread06 extends Thread{
    public void run()
    {
        System.out.println("run = " + this.isAlive());
    }


    public static void main(String[] args) throws Exception
    {
        MyThread06 mt = new MyThread06();
        System.out.println("begin == " + mt.isAlive());
        mt.start();
        Thread.sleep(100);
        System.out.println("end == " + mt.isAlive());
    }
}
```
输出结果如下,增加0.1秒延迟，让线程执行完
```graph
begin == false
run = true
end == false
```
可以看到，执行前false，执行中true，执行后false
### getId()
返回线程的标识符，线程ID是正值，线程ID在生命周期内不会变化，当线程终止了，线程ID可能会被重用
### getName()
返回线程名称
### getPriority()和setPriority(int)
返回优先级和设置优先级
优先级越高的线程获取CPU时间片的概率越高
请看如下的例子
```java
public class MyThread07_0 extends Thread{
    public void run()
    {
        System.out.println("MyThread07_0 run priority = " +
                this.getPriority());
    }

    public static void main(String[] args)
    {
        System.out.println("main thread begin, priority = " +
                Thread.currentThread().getPriority());
        System.out.println("main thread end, priority = " +
                Thread.currentThread().getPriority());
        MyThread07_0 thread = new MyThread07_0();
        thread.start();
    }
}
```
运行结果如下
```graph
main thread begin, priority = 5
main thread end, priority = 5
MyThread07_0 run priority = 5
```
![](https://raw.githubusercontent.com/cjy513203427/cjy513203427.github.io/master/img/2019-06-24-Java多线程（二）：Thread类/img2.png)
线程的默认优先级是5
再看如下的例子
```java
public class MyThread07_1 extends Thread {

    public void run()
    {
        System.out.println("MyThread07_1 run priority = " +
                this.getPriority());
        MyThread07_0 thread = new MyThread07_0();
        thread.start();
    }

    public static void main(String[] args)
    {
        System.out.println("main thread begin, priority = " +
                Thread.currentThread().getPriority());
        System.out.println("main thread end, priority = " +
                Thread.currentThread().getPriority());
        MyThread07_1 thread = new MyThread07_1();
        thread.start();
    }
}
```
我们在MyThread07_1线程内部启动MyThread07_0线程，我们观察MyThread07_1和MyThread07_0的优先级有什么关系。
运行结果如下
```graph
main thread begin, priority = 5
main thread end, priority = 5
MyThread07_1 run priority = 5
MyThread07_0 run priority = 5
```
MyThread07_0和MyThread07_1线程的优先级一致，说明线程具有继承性。
现在我们来设置优先级
```java
public class MyThread08 {

    static class MyThread08_0 extends Thread {
        public void run() {
            long beginTime = System.currentTimeMillis();
            for (int j = 0; j < 1000000; j++) {}
            long endTime = System.currentTimeMillis();
            System.out.println("★★★★ MyThread08_0 use time = " +
                    (endTime - beginTime));
        }
    }

    static class MyThread08_1 extends Thread {
        public void run()
        {
            long beginTime = System.currentTimeMillis();
            for (int j = 0; j < 1000000; j++){}
            long endTime = System.currentTimeMillis();
            System.out.println("☆☆☆☆ MyThread08_1 use time = " +
                    (endTime - beginTime));
        }
    }

    public static void main(String[] args)
    {
        for (int i = 0; i < 5; i++)
        {
            MyThread08_0 mt0 = new MyThread08_0();
            mt0.setPriority(5);
            mt0.start();
            MyThread08_1 mt1 = new MyThread08_1();
            mt1.setPriority(4);
            mt1.start();
        }
    }

}
```
我们给MyThread08_0线程设置更高的优先级5
运行结果如下
```graph
★★★★ MyThread08_0 use time = 7
☆☆☆☆ MyThread08_1 use time = 4
★★★★ MyThread08_0 use time = 18
★★★★ MyThread08_0 use time = 16
★★★★ MyThread08_0 use time = 20
★★★★ MyThread08_0 use time = 17
☆☆☆☆ MyThread08_1 use time = 0
☆☆☆☆ MyThread08_1 use time = 10
☆☆☆☆ MyThread08_1 use time = 9
☆☆☆☆ MyThread08_1 use time = 8
```
可以看到MyThread08_0先执行的次数更多，输出结果为实心五角星的这个。
多运行几次，都会是MyThread08_0先打印完，每次结果都不尽相同，CPU会尽量先让MyThread08_0执行完。
### isDaemon()和setDaemon(boolean)
isDaemon方法判断是否是守护线程；
setDaemon设置守护线程
在Java中有两类线程：User Thread(用户线程)、Daemon Thread(守护线程) 
我们自定义的线程和main线程都是用户线程，我们熟知的GC(垃圾回收器)就是守护线程。守护线程是用户线程的“奴仆”，当用户线程执行完毕，守护线程就会终止，因为它没有存在的必要了。
如用户线程执行结束，GC无垃圾可回收，它只能死亡
看如下代码
```java
public class MyThread09 extends Thread{
    private int i = 0;

    public void run()
    {
        try
        {
            while (true)
            {
                i++;
                System.out.println(Thread.currentThread().getName()+" i = " + i);
                Thread.sleep(1000);
            }
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }

    public static void main(String[] args)
    {
        try
        {
            MyThread09 mt = new MyThread09();
            mt.setDaemon(true);
            mt.start();
            Thread.sleep(5000);
            System.out.println("现在是"+Thread.currentThread().getName()+"线程");
            Thread.sleep(1);

        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```
我们自定义MyThread09线程的run方法里是死循环，如果是用户线程，它应该永远地执行下去，现在把它设置成守护线程。
注意：mt.setDaemon(true);要在mt.start();之前，见
![](https://raw.githubusercontent.com/cjy513203427/cjy513203427.github.io/master/img/2019-06-24-Java多线程（二）：Thread类/img3.png)
否则会抛出IllegalThreadStateException异常
运行结果如下
```graph
Thread-0 i = 1
Thread-0 i = 2
Thread-0 i = 3
Thread-0 i = 4
Thread-0 i = 5
现在是main线程
Thread-0 i = 6
MyThread09变成了守护线程，它的使命已经完成。现在是main线程
```
Thread.sleep(5000)的目的是使main线程沉睡5s，即用户线程（main线程）仍在执行，此时main线程输出，再沉睡1ms，当main线程执行完毕，守护线程就没有存在的意义了，即死亡；
main线程总共执行了大约5001ms（略大于这个数值），Thread-0打印到i=6，说明守护线程在main线程之后死亡，这个时间差极小
### interrupt()
设置中断标志位，无法中断线程
```java
public class MyThread10 extends Thread{
    public void run()
    {
        for (int i = 0; i < 500000; i++)
        {
            System.out.println("i = " + (i + 1));
        }
    }

    public static void main(String[] args)
    {
        try
        {
            MyThread10 mt = new MyThread10();
            mt.start();
            Thread.sleep(2000);
            mt.interrupt();
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }
}
```
输出结果如下
```graph
......
i = 499993
i = 499994
i = 499995
i = 499996
i = 499997
i = 499998
i = 499999
i = 500000
```
可以看到，interrupt()没有中断线程，interrupt()后续将会详细讲解
### isInterrupted()
判断线程是否被中断
### join()
等待这个线程死亡，举例说明：
线程A执行join方法，会阻塞线程B，线程A join方法执行完毕，才能执行线程B
代码如下
```java
public class MyThread11 extends Thread{
    public void run()
    {
        try
        {
            int secondValue = (int)(Math.random() * 1000);
            System.out.println(secondValue);
            Thread.sleep(secondValue);
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws Exception
    {
        MyThread11 mt = new MyThread11();
        mt.start();
        mt.join();
        System.out.println("MyThread11执行完毕之后我再执行");
    }
}
```
输出结果如下
```graph
75
MyThread11执行完毕之后我再执行
```
可以看到，main线程在mt线程之后执行。mt调用join方法，使main线程阻塞，待mt线程执行完毕，方可执行main线程。

## Thread类的静态方法
### currentThread()
返回当前正在执行线程的引用
```java
public class MyThread12 extends Thread{

    static
    {
        System.out.println("静态块的打印：" +
                Thread.currentThread().getName());
    }

    public MyThread12()
    {
        System.out.println("构造方法的打印：" +
                Thread.currentThread().getName());
    }

    public void run()
    {
        System.out.println("run()方法的打印：" +
                Thread.currentThread().getName());
    }



    public static void main(String[] args)
    {
        MyThread12 mt = new MyThread12();
        mt.start();
    }


}
```
输出结果
```graph
静态块的打印：main
构造方法的打印：main
run()方法的打印：Thread-0
```
可以看到，构造方法和静态块是main线程在调用，重写的run方法是线程自己在调用。
再看个例子
```java
public class MyThread13 extends Thread{
    public MyThread13()
    {
        System.out.println("MyThread13----->Begin");
        System.out.println("Thread.currentThread().getName()----->" +
                Thread.currentThread().getName());
        System.out.println("this.getName()----->" + this.getName());
        System.out.println("MyThread13----->end");
    }

    public void run()
    {
        System.out.println("run----->Begin");
        System.out.println("Thread.currentThread().getName()----->" +
                Thread.currentThread().getName());
        System.out.println("this.getName()----->" + this.getName());
        System.out.println("run----->end");
    }



    public static void main(String[] args)
    {
        MyThread13 mt = new MyThread13();
        mt.start();
    }


}
```
输出结果
```graph
MyThread13----->Begin
Thread.currentThread().getName()----->main
this.getName()----->Thread-0
MyThread13----->end
run----->Begin
Thread.currentThread().getName()----->Thread-0
this.getName()----->Thread-0
run----->end
```
可以看到，执行MyThread13构造方法的线程是main，执行MyThread13的线程是Thread-0（当前线程）,run方法就是被线程实例所执行。
### sleep(long)
让当前线程沉睡若干毫秒
```java
public class MyThread14 extends Thread{
    public void run()
    {
        try
        {
            System.out.println("run threadName = " +
                    this.getName() + " begin");
            Thread.sleep(2000);
            System.out.println("run threadName = " +
                    this.getName() + " end");
        }
        catch (InterruptedException e)
        {
            e.printStackTrace();
        }
    }

    public static void main(String[] args)
    {
        MyThread14 mt = new MyThread14();
        mt.start();
    }
}
```
输出结果如下
```graph
run threadName = Thread-0 begin
run threadName = Thread-0 end
```
打印完第一句两秒后打印第二句。
### yield()
当前线程放弃CPU的使用权，这里的放弃是指当前线程少用CPU资源，最后线程还是会执行完成
```java
public class MyThread15 extends Thread {
    public void run()
    {
        long beginTime = System.currentTimeMillis();
        int count = 0;
        for (int i = 0; i < 5000000; i++)
        {
            Thread.yield();
            count = count + i + 1;
        }
        long endTime = System.currentTimeMillis();
        System.out.println("用时：" + (endTime - beginTime) + "毫秒！");
    }



    public static void main(String[] args)
    {
        MyThread15 mt = new MyThread15();
        mt.start();
    }


}
```
输出结果如下
```graph
用时：4210毫秒！
```
可以看到，任务执行完毕，当我们把Thread.yield();注释掉，执行时间只需要7ms。说明当前线程放弃了一些CPU资源。
### interrupted()
![](https://raw.githubusercontent.com/cjy513203427/cjy513203427.github.io/master/img/2019-06-24-Java多线程（二）：Thread类/img4.png)
判断当前线程是否中断，静态版的isInterrupted方法。多线程中断机制，后续会详细解析。
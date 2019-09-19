

## 第六章：多线程

### 1.程序，进程，线程

- 程序（program）是为完成特定任务、用某种语言编写的一组指令的集合。即指**一段静态的代码**，静态对象。

- 进程(process)是程序的一次执行过程，或是**正在运行的一个程序**。是一个动态的过程：有它自身的产生、存在和消亡的过程。——生命周期

  - 如：运行中的QQ，运行中的MP3播放器
  - 程序是静态的，进程是动态的
  - **进程作为资源分配的单位**，系统在运行时会为每个进程分配不同的内存区域

- 线程（thread)，进程可进一步细化为线程，是一个程序内部的一条执行路径。

  - 若一个进程同一时间并行执行多个线程，就是支持多线程的
  - **线程作为调度和执行的单位，每个线程拥有独立的运行栈和程序计数器（pc)**，线程切换的开销小
  - 一个进程中的多个线程共享相同的内存单元/内存地址空间->它们从同一堆中分配对象，可以访问相同的变量和对象。这就使得线程间通信更简便、高效。但多个线程操作共享的系统资源可能就会带来**安全的隐患**。

- #####  单核CPU和多核CPU的理解

  - 单核CPU，其实是一种假的多线程，因为在一个时间单元内，也只能执行一个线程的任务。
  - 如果是多核的话，才能更好的发挥多线程的效率。（现在的服务器都是多核的）
  - 一个Java应用程序java.exe，其实至少有三个线程：main（）主线程，gc（)垃圾回收线程，异常处理线程。当然如果发生异常，会影响主线程。

- ##### 并行与并发

  - 并行：多个CPU同时执行多个任务。比如：多个人同时做不同的事。
  - 并发：一个CPU（采用时间片）同时执行多个任务。比如：秒杀、多个人做同一件事。

- #####  多线程程序的优点：

  - 1.提高应用程序的响应。对图形化界面更有意义，可增强用户体验。
  - 2.提高计算机系统CPU的**利用率**
  - 3.改善程序结构。将既长又复杂的进程分为多个线程，独立运行，利于理解和修改

- ##### 何时需要多线程

  -  程序需要同时执行两个或多个任务。
  - 程序需要实现一些需要等待的任务时，如用户输入、文件读写操作、网络操作、搜索等。
  - 需要一些后台运行的程序时。

### 2.线程的创建和使用

-  Java语言的JVM允许程序运行多个线程，它通过java.lang.Thread类来体现。

- Thread类的特性

  - 每个线程都是通过某个特定Thread对象的run（）方法来完成操作的，经常把run（)方法的主体称为线程体
  - 通过该Thread对象的start（）方法来启动这个线程，而非直接调用run(）

- ##### Thread类

  - 构造器 
    - Thread()：创建新的Thread对象 
    - Thread(String threadname):创建线程并指定线程实例名 
    - Thread(Runnable target):指定创建线程的目标对象，它实现了Runnable接 口中的run方法
    - Thread(Runnable target,String name):创建新的Thread对象

- ##### API中创建线程的两种方式

  - JDK1.5之前创建新执行线程有两种方法：

    - 继承Thread类的方式
    - 实现Runnable接口的方式

  - ##### 方式一：继承Thread类

    - 1）定义子类继承Thread类。
    - 2）子类中重写Thread类中的run方法。
    - 3）创建Thread子类对象，即创建了线程对象。
    - 4）调用线程对象start方法：启动线程，调用run方法。

  - ##### 注意点：

    - 1.如果自己手动调用run（）方法，那么就只是普通方法，没有启动多线程模式。
    - 2.run（)方法由JVM调用，什么时候调用，执行的过程控制都有操作系统的CPU调度决定。
    -  3.想要启动多线程，必须调用start方法。
    - 4.一个线程对象只能调用一次start（）方法启动，如果**重复调用**了，则将抛出以上的异常“**llegalThreadStateException**”。

  - ##### 方式二：实现Runnable接口

    - 1）定义子类，实现Runnable接口。
    - **2）子类中重写Runnable接口中的run方法。**
    - 3）通过Thread类含参构造器创建线程对象。
    - **4）将Runnable接口的子类对象作为实际参数传递给Thread类的构造器中。**
    - 5）调用Thread类的start方法：开启线程，调用Runnable子类接口的run方法。

  - ##### 继承方式和实现方式的联系与区别

    - public class Thread extends Object implements Runnable
    - 区别
      - 继承Thread：线程代码存放Thread子类run方法中。
      - 实现Runnable：线程代码存在接口的子类的run方法。
    - 实现方式的好处
      - 避免了单继承的局限性
      - 多个线程可以共享同一个接口实现类的对象，非常适合多个相同线程来处理同一份资源。

  - ##### Thread类有关的方法

    - void start():启动线程，并执行对象的run（）方法
    - run():线程在被调度时执行的操作
    - String getName():返回线程的名称
    - void setName(String name):设置该线程名称
    - static Thread currentThread():返回当前线程。在Thread子类中就是this，通常用于主线程和Runnable实现类
    -  static void yield():线程让步
      - 暂停当前正在执行的线程，把执行机会让给优先级相同或更高的线程
      - 若队列中没有同优先级的线程，忽略此方法
    - join():当某个程序执行流中调用其他线程的join（）方法时，调用线程将被阻塞，直到join（）方法加入的join线程执行完为止
      - 低优先级的线程也可以获得执行
    - static void sleep(long millis):(指定时间：毫秒）
      - 令当前活动线程在指定时间段内放弃对CPU控制，使其他线程有机会被执行，时间到后重排队。
      - 抛出InterruptedException异常
    - stop():强制线程生命期结束，不推荐使用
    - boolean isAlive():返回boolean,判断线程是否还活着

  - ##### 线程调度

    - 调度策略
      - 时间片
      - 抢占式： 高优先级的线程抢占CPU
    - Java的调度方法
      - 同优先级线程组成先进先出队列（先到先服务），使用时间片策略
      - 对高优先级，使用优先调度的抢占式策略

  - ##### 线程的优先级

    - 线程的优先级等级
      - MAX_PRIORITY:10
      - MIN _PRIORITY:1
      - NORM PRIORITY:5
    - 涉及的方法
      - getPriority()：返回线程优先值
      - setPriority(int newPriority):改变线程的优先级
    - 说明
      - 线程创建时**继承父线程的优先级**
      - **低优先级只是获得调度的概率低，并非一定是在高优先级线程之后才被调用**

  - ##### 线程的分类

    - Java中的线程分为两类：一种是守护线程，一种是用户线程。
      - 它们在几乎每个方面都是相同的，唯一的区别是**判断JVM何时离开**。
      - 守护线程是用来服务用户线程的，通过在start（）方法前调用thread.setDaemon(true)可以把一个用户线程变成一个守护线程。
      - Java垃圾回收就是一个典型的守护线程。
      - 若JVM中都是守护线程，当前JVM将退出。
      - 形象理解：**兔死狗烹，鸟尽弓藏**

### 3.线程的生命周期

- ##### JDK中用Thread.State类定义了线程的几种状态

  - 要想实现多线程，必须在主线程中创建新的线程对象。Java语言使用Thread类及其子类的对象来表示线程，在它的一个完整的生命周期中通常要经历如下的五种状态：
    - 新建：当一个Thread类或其子类的对象被声明并创建时，新生的线程对象处于新建状态
    - 就绪：处于新建状态的线程被start（)后，将进入线程队列等待CPU时间片，此时它已具备了运行的条件，只是没分配到CPU资源
    - 运行：当就绪的线程被调度并获得CPU资源时，便进入运行状态，run()方法定义了线程的操作和功能
    - 阻塞：在某种特殊情况下，被人为挂起或执行输入输出操作时，让出CPU并临时中止自己的执行，进入阻塞状态
    - 死亡：线程完成了它的全部工作或线程被提前强制性地中止或出现异常导致结束

- ##### 线程的生命周期

  ![](C:\Users\YS\Desktop\笔记\线程的生命周期.JPG)

  ![](C:\Users\YS\Desktop\笔记\线程状态转换图.JPG)

### 4.线程的同步

- ##### 问题的提出

  - 多个线程执行的不确定性引起执行结果的不稳定
  - 多个线程对账本的共享，会造成操作的不完整性，会破坏数据。

- ##### 问题的原因：

  - 当多条语句在操作同一个线程**共享数据**时，一个线程对多条语句只执行了一部分，还没有执行完，另一个线程参与进来执行。导致共享数据的错误。

- ##### 解决办法：

  - 对多条操作共享数据的语句，**只能让一个线程都执行完**，在执行过程中，其他线程不可以参与执行

- ##### Synchronize的使用方法

  - Java对于多线程的安全问题提供了专业的解决方式：同步机制
  - 1.**同步代码块**：
    - synchronized(对象）{
      - //需要被同步的代码；
      - }
  - 2.synchronized还可以放在方法声明中，表示整个方法为**同步方法**。
    - 例如：public synchronized void show(String name){
      - ……
    - }

- ##### 同步机制中的锁

  - 同步锁机制：
    - 在《Thinking in Java》中，是这么说的：对于并发工作，你需要某种方式来防止两个任务访问相同的资源（其实就是共享资源竞争）。防止这种冲突的方法就是当资源被一个任务使用时，在其上加锁。第一个访问某项资源的任务必须锁定这项资源，使其他任务在其被解锁之前，就无法访问它了，而在其被解锁之时，另一个任务就可以锁定并使用它了。
  - synchronized的锁是什么？
    - 任意对象都可以作为同步锁。所有对象都自动含有单一的锁（监视器）。
    - 同步方法的锁：静态方法（类名.class)、非静态方法（this)
    - 同步代码块：自己指定，很多时候也是指定为this或类名.class
  - 注意：
    - 必须确保使用同一个资源的**多个线程共用一把锁**，这个非常重要，否则就无法保证共享资源的安全
    - 一个线程类中的**所有静态方法共用同一把锁（类名.class)，所有非静态方法共用同一把锁（this)，同步代码块（指定需谨慎）**

- ##### 同步的范围

  -  1、如何找问题，即代码是否存在线程安全?（非常重要）
    - (1)明确哪些代码是多线程运行的代码
    - (2)明确多个线程是否有共享数据
    - (3)明确多线程运行代码中是否有多条语句操作共享数据
  - 2、如何解决呢？（非常重要）
    - 对多条操作共享数据的语句，只能让一个线程都执行完，在执行过程中，其他线程不可以参与执行。
    - 即所有操作共享数据的这些语句都要放在同步范围中
  - 3、切记：
    - 范围太小：没锁住所有有安全问题的代码
    - 范围太大：没发挥多线程的功能。

- ##### 释放锁的操作

  - 当前线程的同步方法、同步代码块执行结束。
  - 当前线程在同步代码块、同步方法中遇到break、return终止了该代码块、该方法的继续执行。
  - 当前线程在同步代码块、同步方法中出现了未处理的Error或Exception，导致异常结束。
  - 当前线程在同步代码块、同步方法中执行了线程对象的**wait()**方法，当前线程暂停，并释放锁。

- ##### 不会释放锁的操作

  - 线程执行同步代码块或同步方法时，程序调用**Thread.sleep(）、Thread.yield()**方法暂停当前线程的执行
  - 线程执行同步代码块时，其他线程调用了该线程的suspend（）方法将该线程挂起，该线程不会释放锁（同步监视器）。
    - 应尽量避免使用suspend(）和resume（）来控制线程

- ##### 单例设计模式之懒汉式（线程安全）

  ```java
  class Singleton{
      private static Singleton instance = null;
      private Singleton(){}
      public static Singleton getInstance(){
          if(instance == null){
              synchronized(Singleton.class){
                  if(instance == null){
                      instance = new Singleton();
                  }
              }
          }
          return instance;
      }
  }
  ```

- ##### 线程死锁问题

  - 死锁
    - 不同的线程分别占用对方需要的同步资源不放弃，都在等待对方放弃自己需要的同步资源，就形成了线程的死锁
    - 出现死锁后，不会出现异常，不会出现提示，只是所有的线程都处于阻塞状态，无法继续
  - 解决方法
    - 专门的算法、原则
    - 尽量减少同步资源的定义
    - 尽量避免嵌套同步

- ##### Lock（锁）

  - 从JDK5.0开始，Java提供了更强大的线程同步机制——通过显式定义同步锁对象来实现同步。同步锁使用Lock对象充当。
  - **java.util.concurrent.locks.Lock接口是控制多个线程对共享资源进行访问的工具。**锁提供了对共享资源的独占访问，每次只能有一个线程对Lock对象加锁，线程开始访问共享资源之前应先获得Lock对象。
  - ReentrantLock 类实现了Lock，它拥有与synchronized相同的并发性和内存语义，在实现线程安全的控制中，比较常用的是ReentrantLock，可以显式加锁、释放锁。
  - class A{ 
    - private final ReentrantLock lock=new Reen TrantLock()； 
    - public void m(){
      - lock.lock()； 
      - try{ 
        - //保证线程安全的代码；
        - }
        -  finally{
        -  lock.unlock()；
        - }
      - }
    - } 
    - **注意：如果同步代码有异常，要将unlock（）写入finally语句块**

- ##### synchronized 与 Lock的对比

  - 1.Lock是显式锁（手动开启和关闭锁，别忘记关闭锁），synchronized是隐式锁，出了作用域自动释放

  - **2.Lock只有代码块锁，synchronized有代码块锁和方法锁**

  - 3.使用Lock锁，JVM将花费较少的时间来调度线程，性能更好。并且具有更好的扩展性（提供更多的子类）

  - ##### 优先使用顺序：

    - Lock>同步代码块（已经进入了方法体，分配了相应资源）>同步方法（在方法体之外）

### 5.线程的通信

- #####  wait(）与notify（）和notifyAll（）

  - wait（)：令当前线程挂起并放弃CPU、同步资源并等待，使别的线程可访问并修改共享资源，而当前线程排队等候其他线程调用notify（）或notifyAll（）方法唤醒，唤醒后等待重新获得对监视器的所有权后才能继续执行。
  - notify()：唤醒正在排队等待同步资源的线程中**优先级最高者结束等待**
  - notifyAll（)：唤醒正在排队等待资源的**所有线程结束等待**.
  - 这三个方法只有**在synchronized方法或synchronized代码块中才能使用**，否则会报**java.lang.llegalMonitorStateException异常**。
  - 因为这三个方法必须有锁对象调用，而任意对象都可以作为synchronized的同步锁，因此这三个方法只能在**Object类中声明**。

- ##### wait()方法

  -  在当前线程中调用方法：对象名.wait(）
  - 使当前线程进入等待（某对象）状态，直到另一线程对该对象发出notify（或notifyAll)为止。
  - 调用方法的必要条件：当前线程必须具有对该对象的监控权（加锁）
  - **调用此方法后，当前线程将释放对象监控权，然后进入等待**
  - 在当前线程被notify后，要重新获得监控权，然后从断点处继续代码的执行。

- ##### notify()/notifyAll()

  -  在当前线程中调用方法：对象名.notify(）
  - 功能：唤醒等待该对象监控权的一个/所有线程。
  - 调用方法的必要条件：当前线程必须具有对该对象的监控权（加锁）

- #####  经典例题：生产者/消费者问题

  - 生产者（Productor）将产品交给店员（Clerk），而消费者（Customer)从店员处取走产品，店员一次只能持有固定数量的产品（比如：20），如果生产者试图生产更多的产品，店员会叫生产者停一下，如果店中有空位放产品了再通知生产者继续生产；如果店中没有产品了，店员会告诉消费者等一下，如果店中有产品了再通知消费者来取走产品。

  - 这里可能出现两个问题：

    - 生产者比消费者快时，消费者会漏掉一些数据没有取到。

    - 消费者比生产者快时，消费者会取相同的数据。

      ```java
      class Clerk{//售货员 
          private int product=0; 
          public synchronized void addproduct(){
              if(product>=20){
                  try{
                      wait();
                  }catch(InterruptedException e){
                      e.printstackTrace();
                  }
              }else{
                  product++;
                  System.out.println("生产者生产了 第"+product+"个产品");
                  notifyAl1();
              }
          }
          public synchronized void getProduct(){ 
              if(this.product<=e){
                  try{
                      wait();
                  }catch(InterruptedException e){
                      e.printStackTrace();
                  }
              }else{
                  system.out.println("消费者取走了第"+ product+"个产品");
                  product--; 
                  notifyAl1();
                  }
          }
      }
      class Productor implements Runnable{//生产者 
          Clerk clerk; 
          public Productor(Clerk clerk){ 
              this.clerk=clerk;
          } 
          public void run(){ 
              System.out.print1n("生产者开始生产产品"); 
              while(true){ 
                  try{ 
                      Thread.sleep((int)Math.random()*1000);
                  }catch(InterruptedException e){ 
                      e.printstackTrace();
                  }
                      clerk.addProduct();
              }
          }
      }
      class Consumer implements Runnable{//消费者 
          Clerk clerk;
          public Consumer(Clerk clerk){ 
              this.clerk=clerk; 
          }
          public void run(){ 
              System.out.println("消费者开始取走产品"); 
              while(true){ 
                  try{ 
                      Thread.sleep((int)Math.random()*1000);
                  }catch(InterruptedException e){ 
                      e.printStackTrace();
                  } 
                  clerk.getProduct();
              }
          }
      }
      public class ProductTest{ 
          public static void main(String[] args){ 
              Clerk clerk=new Clerk(); 
              Thread productorThread=new Thread(new Productor(clerk)); 
              Thread consumerThread =new Thread(new Consumer(clerk)); 
              productorThread.start(); 
              consumerThread.start();
          }
      }
      ```

### 6.JDK5.0新增线程创建方式

- ##### 新增方式一：实现Callable接口

  - 与使用Runnable相比，Callable功能更强大些
    - 相比run（）方法，可以有返回值
    - 方法可以抛出异常
    - 支持泛型的返回值
    - 需要借助FutureTask类，比如获取返回结果
  - Future接口
    - 可以对具体Runnable、Callable任务的执行结果进行取消、查询是否完成、获取结果等。
    - **Futrue Task是Futrue接口的唯一的实现类**
    - Future Task 同时实现了Runnable,Future接口。它既可以作为Runnable被线程执行，又可以作为Future得到Callable的返回值

- ##### 新增方式二：使用线程池

  - 背景：经常创建和销毁、使用量特别大的资源，比如并发情况下的线程，对性能影响很大。
  - 思路：提前创建好多个线程，放入线程池中，使用时直接获取，使用完放回池中。可以避免频繁创建销毁、实现重复利用。类似生活中的公共交通工具。
  - 好处：
    - 提高响应速度（减少了创建新线程的时间）
    - 降低资源消耗（重复利用线程池中线程，不需要每次都创建）
    - 便于线程管理
      - corePoolSize:核心池的大小
      - maximumPoolSize:最大线程数
      - keepAliveTime:线程没有任务时最多保持多长时间后会终止
      - ...
  - 线程池相关API
    - JDK5.0起提供了线程池相关API：ExecutorService和Executors
    - ExecutorService:真正的线程池接口。常见子类ThreadPoolExecutor
      - void execute（Runnable command):执行任务/命令，没有返回值，一般用来执行Runnable
      - <T>Future<T>submit(Callable<T>task):执行任务，有返回值，一般又来执行Callable
      - void shutdown（)：关闭连接池
    - Executors:工具类、线程池的工厂类，用于创建并返回不同类型的线程池
      - Executors.newCachedThreadPool():创建一个可根据需要创建新线程的线程池
      - Executors.newFixedThreadPool(n)；创建一个可重用固定线程数的线程池
      - Executors.newSingle ThreadExecutor():创建一个只有一个线程的线程池
      - Executors.newScheduledThreadPool(n):创建一个线程池，它可安排在给定延迟后运行命令或者定期地执行。

## 第七章：Java常用的类

### 1.字符串相关的类

- ##### String的特性

  - String类：**代表字符串**。Java程序中的所有字符串字面值（如“abc"）都作为此类的实例实现。
  - String是一个final类，代表**不可变的字符序列**。
  - 字符串是常量，用双引号引起来表示。它们的值在创建之后不能更改。
  - String对象的字符内容是存储在一个字符数组value]中的。(JDK8以后是byte数组)

- ##### String对象的创建

  ```java
  String str = "hello";
  //本质上this.value = new char[0]
  String s1 = new String();
  //this.value = original.value
  String s2 = new String(String original);
  //this.value = Arrays.copyOf(value, value.length);
  String s3 = new String(char[] a);
  String s4 = new String(char[] a, int startIndex, int count);
  ```

- 注意：

  ![](C:\Users\YS\Desktop\笔记\字符串的特性.png)

  - **字符串常量存储在字符串常量池，目的是共享**
  - **字符串非常量对象存储在堆中。**
  - **常量与常量的拼接结果在常量池。且常量池中不会存在相同内容的常量。**
  - **只要其中有一个是变量，结果就在堆中**
  - **如果拼接的结果调用intern（）方法，返回值就在常量池中**

- ##### String使用陷阱

  - **String s1="a"；**
  - 说明：在字符串常量池中创建了一个字面量为“a"的字符串。
  - **s1=s1+"b"；**
  - 说明：实际上原来的“a”字符串对象已经丢弃了，现在在堆空间中产生了一个字符串s1+"b"(也就是“ab"）。如果多次执行这些改变串内容的操作，会导致大量副本字符串对象存留在内存中，降低效率。如果这样的操作放到循环中，会极大影响程序的性能。
  - **String s2="ab"；**
  - 说明：直接在字符串常量池中创建一个字面量为“ab”的字符串。
  - **String s3="a"+"b"；**
  - 说明：s3指向字符串常量池中已经创建的“ab”的字符串。
  - **String s4=s1.intern()**；
  - 说明：堆空间的s1对象在调用intern（）之后，会将常量池中已经存在的“ab"字符串赋值给s4。

- ##### String常用的方法

  -  int length():返回字符串的长度：return value.length
  - char charAt(int index):**返回某索引处的字符** return value[index]
  - boolean isEmpty():判断是否是空字符串：return value.length == 0
  - String toLowerCase():使用默认语言环境，将String中的所有字符**转换为小写**
  - String toUpperCase():使用默认语言环境，将String中的所有字符**转换为大写**
  - String trim():返回字符串的**副本**，忽略前导空白和尾部空白
  - boolean equals(Object obj):比较字符串的内容是否相同
  - boolean equalslgnoreCase(String anotherString):与equals方法类似，**忽略大小写**
  - String concat(String str):将指定字符串连接到此字符串的**结尾**。**等价于用“+”**
  - int compare To(String anotherString):**比较两个字符串的大小**
  - String substring(int beginlndex):返回一个新的字符串，它是此字符串的**从beginlndex开始截取到最后的一个子字符串。**
  - String substring(int beginlndex,int endlndex):返回一个新字符串，它是此字符串从**beginlndex开始截取到endlndex(不包含）的一个子字符串。**(包头不包尾)
  - boolean endsWith(String suffix):测试此字符串**是否以指定的后缀结束**
  - boolean startsWith(String prefix):测试此字符串**是否以指定的前缀开始**
  - boolean startsWith(String prefix,int toffset):测试此字符串**从指定索引开始的子字符串是否以指定前缀开始**
  - boolean contains(CharSequence s):当且仅当此字符串**包含指定的char值序列时**，返回true 
  - int indexOf(String str):返回指定子字符串在此字符串中**第一次出现处的索引**
  - int indexOf(String str,int fromlndex):返回指定子字符串在此字符串中第一次出现处的索引，**从指定的索引开始**
  - int lastlndexof(String str):返回指定子字符串在此字符串中**最右边出现处的索引**
  - int lastlndexOf(String str,int fromlndex):返回指定子字符串在此字符串中最后一次出现处的索引，**从指定的索引开始反向搜索**
  - **注：indexOf和lastlndexOf方法如果未找到都是返回-1**
  -  String replace(char oldChar,char newChar):返回一个**新的字符串**，它是通过用newChar替换此字符串中出现的**所有oldChar**得到的。
  - String replace(CharSequence target,CharSequence replacement):使用指定的字面值替换序列替换此字符串所有**匹配字面值目标序列的子字符串**。
  - String replaceAll(String regex,String replacement):使用给定的replacement 替换此字符串所有匹配**给定的正则表达式的子字符串。**
  - String replaceFirst(String regex,String replacement):使用给定的replacement替换此字符串匹配给定的正则表达式的**第一个子字符串**。
  -  boolean matches(String regex):告知**此字符串是否匹配给定的正则表达式。**
  - String[] split(String regex):根据给定正则表达式的**匹配拆分此字符串**。
  - String] split(String regex,int limit):根据匹配给定的正则表达式来拆分此字符串，**最多不超过limit个**，如果**超过了，剩下的全部都放到最后一个元素中。**

- ##### String与基本数据类型转换

  - 字符串 ->基本数据类型、包装类

    - Integer包装类的public static int parselnt(String s):可以将由**“数字”字符组成的字符串转换为整型**。
    - 类似地，使用java.lang包中的Byte、Short、Long、Float、Double类调相应的类方法可以将由**“数字”字符**组成的字符串，转化为相应的基本数据类型。

  - 基本数据类型、包装类->字符串

    - 调用String类的public String valueOf(int n)可将**int型转换为字符串**
    - 相应的valueOf(byte b)、valueOf(long l)、valueOf(float f)、valueOf(doubled）、valueOf(boolean b)**可由参数的相应类型到字符串的转换**

  -  字符数组 ->字符串

    - String 类的构造器：String(char[])和 String(char[],int offset,int length)分别**用字符数组中的全部字符和部分字符创建字符串对象。**

  - 字符串->字符数组

    - public char[] toCharArray():将字符串中的**全部字符存放在一个字符数组中的方法。**
    - public void getChars(int srcBegin,int srcEnd,char[] dst,int dstBegin):提供了将**指定索引范围内的字符串存放到数组中的方法。**

  - 字节数组->字符串

    - String(byte[]):通过使用平台的默认字符集解码指定的**byte数组，构造一个新的String**。
    - String(byte[],int offset,int length):用指定的字节数组的一部分，即**从数组起始位置offset开始取length个字节构造一个字符串对象。**

  - ##### 字符串->字节数组

    - public byte[] getBytes():使用平台的默认字符集将此String编码为byte序列，**并将结果存储到一个新的byte数组中。**
    - public byte[] getBytes(String charsetName):使用**指定的字符集**将此String编码到byte序列，并将结果存储到新的byte数组。

  - ##### StringBuffer类

    - java.lang.StringBuffer代表**可变的字符序列**，JDK1.0中声明，可以对字符串内容进行增删，此时不会产生新的对象。

    - 很多方法与String相同。

    - 作为参数传递时，方法内部可以改变值。

    - StringBuffer类不同于String，其对象必须使用构造器生成。有三个构造器：

      - StringBuffer():初始容量为**16**的字符串缓冲区
      - StringBufer(int size):构造**指定容量的字符串缓冲区**
      - StringBuffer(String str):将内容初始化为指定字符串内容

    - ##### StringBuffer类的常用方法

      - StringBuffer append(xxx):提供了很多的append()方法，用于进行字符串拼接 
      - StringBuffer delete(int start,int end):删除指定位置的内容 
      - StringBuffer replace(int start,int end,String str):**把[start,end)位置替换为str** 
      - StringBuffer insert(int offset,xxx):在**指定位置插入xxx** 
      - StringBuffer reverse():把当前字符序列**逆转**
      - public int indexOf(String str) 
      - public String substring(int start,int end)
      -  public int length() 
      - public char charAt(int n) 
      - public void setCharAt(int n,char ch)

- ##### StringBuilder类

  - StringBuilder 和StringBuffer非常类似，均代表可变的字符序列，而且提供相关功能的方法也一样

  - ##### 面试题：对比String、StringBuffer、StringBuilder

    - String(JDK1.0)：不可变字符序列
    - StringBuffer(JDK1.0):可变字符序列、效率低、线程安全
    - StringBuilder(JDK5.0):可变字符序列、效率高、线程不安全
    - 注意：作为参数传递的话，方法内部String不会改变其值，StringBuffer和StringBuilder会改变其值。

### 2.JDK8之前日期时间API

- 1.java.lang.System类 System类提供的public static long currentTimeMillis()用来返回当前时 间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差。

  - **此方法适于计算时间差。**

- 计算世界时间的主要标准有： 

  - UTC(Coordinated Universal Time) 
  - GMT(Greenwich Mean Time) 
  - CST(Central Standard Time)

-   2.java.util.Date类表示特定的瞬间，精确到毫秒·

  - 构造器：

    - Date（）：使用无参构造器创建的对象可以获取本地当前时间。
    - Date(long date)

  - 常用方法

    - getTime（)：返回自1970年1月1日00:00:00GMT以来此Date对象表示的毫秒数。
    - toString()：把此Date对象转换为以下形式的String:dow mon dd hh:mm:ss zzzyyyy 其中：dow是一周中的某一天（Sun，Mon，Tue，Wed,Thu,Fri,Sat),zzz是时间标准。
    - 其它很多方法都过时了。

  - 代码

    ```java
    import java.util.Date; 
    
    Date date=new Date(); 
    System.out.println(date);
    
    System.out.println(System.currentTimeMillis()); 
    System.out.printin(date.getTime()); 
    
    Date datel=new Date(date.getTime()); 
    
    System.out.println(datel.getTime());
    System.out.println(datel.toString());
    ```

- 3.java.text.SimpleDateFormat类

  - Date类的API不易于国际化，大部分被废弃了，java.text.SimpleDateFormat类是一个不与语言环境有关的方式来格式化和解析日期的具体类。

  - 它允许进行格式化：日期->文本、解析：文本->日期

  - 格式化：

    - SimpleDateFormat():默认的模式和语言环境创建对象
    - public SimpleDateFormat(String pattern):该构造方法可以**用参数pattern指定的格式创建一个对象**，该对象调用;
    - public String format(Date date):方法格式化时间对象date

  - 解析：

    - public Date parse(String source):从给定字符串的开始解析文本，以生成一个日期。

  - 代码

    ```java
    Date date=new Date();//产生一个Date实例 
    //产生一个formater格式化的实例 
    SimpleDateFormat formater=new SimpleDateFormat(); 
    System.out.println(formater.format(date));//打印输出默认的格式 
    SimpleDateFormat formater2=new SimpleDateFormat("yyyy年MM月dd日EEE HH:mm:ss"); System.out.println(formater2.format(date));
    try{ 
        //实例化一个指定的格式对象 
        Date date2=formater2.parse("2008年08月08日星期一08:08:08"); 
        //将指定的日期解析后格式化按指定的格式输出 
        System.out.println(date2.tostring());
    }catch(ParseException e){ 
        e.printstackTrace();
    }
    ```

    | 字母 |     日期或时间元素      |       表示        |                 示例                  |
    | :--: | :---------------------: | :---------------: | :-----------------------------------: |
    |  G   |        Era标志符        |       Text        |                  AD                   |
    |  y   |           年            |       Year        |                1996;96                |
    |  M   |       年中的月份        |       Month       |              July;Jul;07              |
    |  w   |       年中的周数        |      Number       |                  27                   |
    |  W   |      月份中的周数       |      Number       |                   2                   |
    |  D   |       年中的天数        |      Number       |                  189                  |
    |  d   |      月份中的天数       |      Number       |                  10                   |
    |  F   |      月份中的星期       |      Number       |                   2                   |
    |  E   |      星期中的天数       |       Text        |              Tuesday;Tue              |
    |  a   |        Am/pm标记        |       Text        |                  PM                   |
    |  H   | 一天中的小时数（0-23）  |      Number       |                   0                   |
    |  k   | 一天中的小时数（1-24）  |      Number       |                  24                   |
    |  K   |  am/pm中的小时数(0-11)  |      Number       |                   0                   |
    |  h   | am/pm中的小时数（1-12） |      Number       |                  12                   |
    |  m   |     小时中的分钟数      |      Number       |                  30                   |
    |  s   |      分钟中的秒数       |      Number       |                  55                   |
    |  S   |         毫秒数          |      Number       |                  978                  |
    |  z   |          时区           | General time zone | Pacific Standard Time; PST; GMT-08:00 |
    |  Z   |          时区           | RFC 822 time zone |                 -0800                 |

  - 4.java.util.Calendar(日历)类 

    - Calendar是一个抽象基类，主用用于完成**日期字段之间相互操作的功能**。

    - 获取Calendar实例的方法

      - 使用Calendar.getlnstance（）方法 
      - 调用它的子类GregorianCalendar的构造器。

    - 一个Calendar的实例是系统时间的抽象表示，通过get（int field)方法来取得想 要的时间信息。比如YEAR、MONTH、DAY_OF_WEEK、HOUR_OF_DAY、 MINUTE、SECOND 

      - public void set(int field,int value) 
      - public void add(int field,int amount) 
      - public final Date getTime() 
      - public final void setTime(Date date) 

    - 注意： 

    - 获取月份时：一月是0，二月是1，以此类推，12月是11 

    - 获取星期时：周日是1，周二是2，。。。。周六是7

    - 代码

      ```java
      Calendar calendar=Calendar.getInstance(); 
      //从一个Calendar对象中获取Date对象 
      Date date=calendar.getTime(); 
      //使用给定的Date 设置此Calendar的时间 
      date=new Date(234234235235L); 
      calendar.setTime(date); 
      calendar.set(Calendar.DAY_ OF_MONTH,8); 
      System.out.println("当前时间日设置为8后，时间是："+calendar.getTime()); calendar.add(Calendar.HOUR,2); 
      System.out.println（"当前时间加2小时后，时间是："+calendar.getTime()); calendar.add(Calendar.MONTH,-2); 
      System.out.println("当前日期减2个月后，时间是："+calendar.getTime()）；
      ```

### 3.JDK8中的新日期时间API

- 出现的背景：

  - 如果我们可以跟别人说：“我们在1502643933071见面，别晚了！”那么就再简单不过了。但是我们希望时间与昼夜和四季有关，于是事情就变复杂了。JDK1.0中包含了一个java.util.Date类，但是它的大多数方法已经在JDK 1.1引入Calendar类之后被弃用了。而Calendar并不比Date好多少。它们面临的问题是：
  - **可变性：像日期和时间这样的类应该是不可变的。**
  - **偏移性：Date中的年份是从1900开始的，而月份都从0开始**。
  - **格式化：格式化只对Date有用，Calendar则不行。**
  - **此外，它们也不是线程安全的；不能处理闰秒等。**
  - 总结：对日期和时间的操作一直是Java程序员最痛苦的地方之一。

- ##### 新时间日期API

  - 第三次引入的APl是成功的，并且Java8中引入的java.timeAPI已经纠正了过去的缺陷，将来很长一段时间内它都会为我们服务。

  - Java 8吸收了Joda-Time的精华，以一个新的开始为Java创建优秀的APl。新的java.time中包含了所有关于**本地日期（LocalDate）、本地时间（LocalTime)、本地日期时间（LocalDateTime)、时区（ZonedDateTime)和持续时间（Duration)的类**。历史悠久的Date类新增了tolnstant（)方法，用于把Date转换成新的表示形式。这些新增的本地化时间日期API大大简化了日期时间和本地化的管理。

  - **java.time-包含值对象的基础包**

  - **java.time.chrono-提供对不同的日历系统的访问**

  - **java.time.format-格式化和解析时间和日期**

  - **java.time.temporal-包括底层框架和扩展特性**

  - **java.time.zone-包含时区支持的类**

  - LocalDate、LocalTime、LocalDateTime类是其中较重要的几个类，它们的实例是**不可变的对象**，分别表示使用ISO-8601日历系统的日期、时间、日期和时间。它们提供了简单的本地日期或时间，并不包含当前的时间信息，也不包含与时区相关的信息。

    - LocalDate代表IOS格式（yyyy-MM-dd)的日期，可以存储生日、纪念日等日期。
    - LocalTime表示一个时间，而不是日期。
    - LocalDateTime是用来表示日期和时间的，这是一个最常用的类之一。

  - **注：ISO-8601日历系统是国际标准化组织制定的现代公民的日期和时间的表示法，也就是公历。**

    |                             方法                             |                             描述                             |
    | :----------------------------------------------------------: | :----------------------------------------------------------: |
    |                  now() /* now(Zoneld zone)                   |        静态方法，根据当前时间创建对象/指定时区的对象         |
    |                             of()                             |             静态方法，根据指定日期/时间创建对象              |
    |               getDayOfMonth() / getDayOfYear()               |            获取月份天数(1-31)/获取年份天数(1-366)            |
    |                        getDayOfWeek()                        |           获取星期几(返回一个DayOfWeek**枚举值**)            |
    |                          getMonth()                          |              获取月份，返回一个**Month枚举值**               |
    |                 getMonthValue() / getYear()                  |                 获取**月份(1-12)**/获取年份                  |
    |            getHour() / getMinute() / getSecond()             |               获取当前对象对应的小时，分钟，秒               |
    | withDayOfMonth() / withDayOfYear()/<br /> withMonth/ withYear() | 将月份天数，年份天数，月份，年份修改为指定的值并返回<br />新的对象 |
    | plusDays(),plusWeeks(),<br />plusMonths(),plusYears(),plusHours() |        向当前对象添加几天，几周，几个月，几年，几小时        |
    | minusMonths() / minusWeeks()<br />minusDays()/minusYears()/minusHours() |         从当前对象减去几月，几周，几天，几年，几小时         |

  - ##### 瞬时：Instant

    -  Instant：时间线上的一个瞬时点。这可能被用来记录应用程序中的**事件时间戳**。
    - 在处理时间和日期的时候，我们通常会想到年，月，日，时，分，秒。然而，这只是时间的一个模型，是**面向人类的**。第二种通用模型是**面向机器的**，或者说是连续的。在此模型中，时间线中的一个点表示为一个很大的数，这有利于计算机处理。在UNIX中，这个数从1970年开始，以秒为的单位；同样的，在Java中，也是从1970年开始，但以毫秒为单位。
    - **java.time包通过值类型Instant提供机器视图，不提供处理人类意义上的时间单位**。Instant表示时间线上的一点，而不需要任何上下文信息，例如，时区。概念上讲，它只是简单的表示自1970年1月1日0时0分0秒（UTC）开始的秒数。因为java.time包是基于纳秒计算的，所以Instant的精度可以达到纳秒级。
    - （1ns=10-9s)1秒=1000毫秒=106微秒=109纳秒

    |             方法              |                             描述                             |
    | :---------------------------: | :----------------------------------------------------------: |
    |             now()             |            静态方法，返回默认UTC时区的Instant对象            |
    | ofEpochMilli(long epochMilli) | 静态方法，返回在1970-01-01 00:00:00基础上加上指定毫秒<br />数之后的Instant类的对象 |
    |  atOffset(ZoneOffset offset)  |            结合即时的偏移来创建一个OffsetDateTime            |
    |        toEpochMilli()         |    返回1970-01-01 00:00:00到当前时间的毫秒数，即为时间戳     |

  - ##### 格式化与解析日期或时间

    - java.time.format.Date TimeFormatter类：该类提供了三种格式化方法： 

      - 预定义的标准格式。如： 

      - ISO_LOCAL DATE_TIME;ISO_ LOCAL DATE;ISO_LOCAL TIME 

      - 本地化相关的格式。如：ofLocalizedDate Time（FormatStyle.LONG) 

      - 自定义的格式。如：ofPattern("yyyy-MM-dd hh:mm:ss”)

        |            方法            |                           描述                            |
        | :------------------------: | :-------------------------------------------------------: |
        | ofPattern(String pattern)  | 静态方法，返回一个指定字符串格式的<br />DateTimeFormatter |
        | format(TemporalAccessor t) |             格式化一个日期，时间，返回字符串              |
        |  parse(CharSequence text)  |         将指定格式的字符序列解析为一个日期，时间          |

  - ##### 其他API

    - Zoneld：该类中包含了所有的时区信息，一个时区的ID，如Europe/Paris
    - ZonedDateTime:一个在ISO-8601日历系统时区的日期时间，如2007-12-03T10:15:30+01:00 Europe/Paris。
      - 其中每个时区都对应着ID，地区ID都为“{区域{城市}”的格式，例如：Asia/Shanghai等
    - Clock:使用时区提供对当前即时、日期和时间的访问的时钟。
    - 持续时间：Duration，用于计算两个“时间”间隔
    - 日期间隔：Period，用于计算两个“日期”间隔
    - TemporalAdjuster:时间校正器。有时我们可能需要获取例如：将日期调整到“下一个工作日”等操作。
    - TemporalAdjusters:该类通过静态方法（firstDayOfXxx()/lastDayOfXxx()/nextXxx())提供了大量的常用TemporalAdjuster的实现。

  - ##### 代码

    ```java
    //ZoneId:类中包含了所有的时区信息 
    //ZoneId的getAvailableZoneIds():获取所有的ZoneId 
    Set<String>zoneIds=ZoneId.getAvailableZoneIds(); 
    for(Strings:zoneIds){ 
        System.out.print1n(s);
    } 
    //ZoneId的of(）:获取指定时区的时间 
    LocalDateTime localDateTime = LocalDateTime.now(ZoneId.of("Asia/Tokyo")); System.out.println(localDateTime); 
    
    //ZonedDateTime:带时区的日期时间 
    //ZonedDateTime的now():获取本时区的ZonedDateTime对象 
    ZonedDateTime zonedDateTime = ZonedDateTime.now(); 
    System.out.println(zonedDateTime); 
    //ZonedDateTime的now(ZoneId id):获取指定时区的ZonedDateTime对象 
    ZonedDateTime zonedDateTime1 = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"))； System.out.println(zonedDateTime1);
    
    //Duration:用于计算两个“时间”间隔，以秒和纳秒为基准 
    LocalTime localTime = LocalTime.now(); 
    LocalTime localTimel = LocalTime.of(15,23,32); 
    //between():静态方法，返回Duration对象，表示两个时间的间隔 
    Duration duration = Duration.between(localTimel,localTime); 
    System.out.printin(duration); 
    
    System.out.println(duration.getseconds());
    System.out.println(duration.getNano()); 
    
    LocalDateTime localDateTime = LocalDateTime.of(2016,6,12,15,23,32);
    LocalDateTime localDateTime1 = LocalDateTime.of(2017,6,12,15,23,32);
    
    Duration durationl = Duration.between(localDateTimel,localDateTime); System.out.println(durationl.toDays());
    
    //Period:用于计算两个“日期”间隔，以年、月、日衡量 
    LocalDate localDate = LocalDate.now(); 
    LocalDate localDate1 = LocalDate.of(2028,3,18); 
    
    Period period = Period.between(localDate,localDatel); 
    System.out.println(period); 
    
    System.out.println(period.getYears());
    System.out.println(period.getMonths());
    System.out.println(period.getDays());
    
    Period periodl = period.withYears(2); 
    System.out.println(period1);
    
    //TemporalAdjuster:时间校正器 
    //获取当前日期的下一个周日是哪天？ 
    TemporalAdjuster temporalAdjuster = TemporalAdjusters.next(DayOfweek.SUNDAY); 
    LocalDateTime localDateTime = LocalDateTime.now().with(temporalAdjuster); System.out.printin(localDateTime); 
    
    //获取下一个工作日是哪天？ 
    LocalDate localDate = LocalDate.now().with(new TemporalAdjuster(){ 
        @Override 
        public Temporal adjustInto(Temporal temporal){ 
            LocalDate date=(LocalDate)temporal; 
            if(date.getDayofWeek().equals(Dayofweek.FRIDAY)){ 
                return date.plusDays(3); 
            }else if(date.getDayofweek().equals(DayOfweek.SATURDAY)){ 
                return date.plusDays(2); 
            }else{ 
                return date.plusDays(1);
            } 
        }); 
        System.out.printin("下一个工作日是："+localDate）;
    ```

  - ##### 与传统日期处理的转换

    |                              类                              |               To遗留类                |         From遗留类          |
    | :----------------------------------------------------------: | :-----------------------------------: | :-------------------------: |
    |              java.time.Instant与java.util.Date               |          Date.from(instant)           |      date.toInstant()       |
    |            java.time.Instant与java.sql.TimeStamp             |        Timestamp.from(instant)        |    timestamp.toInstant()    |
    |  java.time.ZonedDateTime与<br />java.util.GregorianCalendar  | GregorianCalendar.from(zonedDateTime) |    cal.toZonedDateTime()    |
    |              java.time.LocalDate与java.sql.Time              |        Date.valueOf(localDate)        |     date.toLocalDate()      |
    |              java.time.LocalTime与java.sql.Time              |        Date.valueOf(localDate)        |     date.toLocalTime()      |
    |      java.time.LocalDateTime与<br />java.sql.Timestamp       |   Timestamp.valueOf(localDateTime)    | timestamp.toLocalDateTime() |
    |             java.time.Zoneld与java.util.TimeZone             |       Timezone.getTimeZone(id)        |     timeZone.toZoneld()     |
    | java.time.format.DateTimeFormatter与<br />java.text.DateFormat |         formatter.toFormat()          |             无              |

### 4.Java比较器

-   在Java中经常会涉及到对象数组的排序问题，那么就涉及到对象之间的比较问题。

- Java实现对象排序的方式有两种：

  - 自然排序：java.lang.Comparable
  - 定制排序：java.util.Comparator

- #####  方式一：自然排序：java.lang.Comparable

  - Comparable接口**强行对实现它的每个类的对象进行整体排序**。这种排序被称为类的自然排序。

  -  实现Comparable的类必须实现compareTo（Object obj)方法，两个对象即通过compare To（Object obj）方法的返回值来比较大小。**如果当前对象this大于形参对象obj，则返回正整数，如果当前对象this小于形参对象obj，则返回负整数，如果当前对象this等于形参对象obj，则返回零**。

  -  实现Comparable接口的对象列表（和数组）可以通过Collections.sort或Arrays.sort进行自动排序。实现此接口的对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器。

  - 对于类C的每一个e1和e2来说，当且仅当e1.compareTo（e2)==0与e1.equals(e2)具有相同的boolean值时，类C的自然排序才叫做与equals一致。建议（虽然不是必需的）**最好使自然排序与equals一致。**

  - Comparable的典型实现：（**默认都是从小到大排列的**）

    - String:按照字符串中字符的Unicode值进行比较
    - Character:按照字符的Unicode值来进行比较
    - 数值类型对应的包装类以及Biglnteger、BigDecimal：按照它们对应的数值大小进行比较
    - Boolean:true对应的包装类实例大于false对应的包装类实例
    - Date、Time等：后面的日期时间比前面的日期时间大

    ```java
     class Goods implements Comparable { 
         private String name; 
         private double price; 
         //按照价格，比较商品的大小
         @Override 
         public int compareTo(Object o){ 
             if(o instanceof Goods){ 
                 Goods other=(Goods)o; 
                 if(this.price>other.price){ 
                     return 1; 
                 }else if(this.price< other.price){
                     return-1; 
                 }
                 return 0; 
             } 
             throw new RuntimeException("输入的数据类型不一致"); 
         }
         //构造器、getter、setter、tostring（）方法略
    ```

    ```java
    public class ComparableTest{ 
        public static void main(String[] args){ 
            Goods[] all = new Goods[4]; 
            all[0] = new Goods("《红楼梦》",180); 
            all[1] = new Goods("《西游记》",80);
            all[2] = new Goods("《三国演义》",140); 
            all[3] = new Goods("《水浒传》",120); 
            Arrays.sort(all); 
            System.out.printin(Arrays.tostring(al1));
        }
    }
    ```

- #####  方式二：定制排序：java.util.Comparator

  - 当元素的类型没有实现java.lang.Comparable接口而又不方便修改代码，或者实现了java.lang.Comparable接口的排序规则不适合当前的操作，那么可以考虑使用Comparator的对象来排序，**强行对多个对象进行整体排序的比较。**

  - 重写compare（Object o1,Object o2)方法，比较01和o2的大小：**如果方法返回正整数，则表示o1大于o2；如果返回0，表示相等；返回负整数，表示01小于o2。**

  - 可以将Comparator传递给 sort方法（如Collections.sort 或Arrays.sort)，从而允许在排序顺序上实现精确控制。

  - 还可以使用Comparator 来控制某些数据结构（如有序set或有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序。

    ```java
    Goods[] all = new Goods[4]; 
    all[0] = new Goods("War and Peace",100); 
    all[1] = new Goods("Childhood",80); 
    all[2] = new Goods("Scarlet and Black",140); 
    all[3] = new Goods("Notre Dame de Paris",120); 
    Arrays.sort(all,new Comparator(){ 
        @Override 
        public int compare(Object ol,Object o2){ 
            Goods g1 = (Goods)ol; 
            Goods g2 = (Goods)o2; 
            return g1.getName().compareTo(g2.getName());
        } 
    });
    System.out.println(Arrays.toString(all))；
    ```

### 5.System类

- System类代表系统，系统级的很多属性和控制方法都放置在该类的内部。该类位于java.lang包。

- 由于该类的构造器是private的，所以无法创建该类的对象，也就是无法实例化该类。其内部的成员变量和成员方法都是static的，所以也可以很方便的进行调用。

- 成员变量

  - System类内部包含in、out和err三个成员变量，分别代表标准输入流（键盘输入），标准输出流（显示器）和标准错误输出流（显示器）。

- 成员方法

  - native long currentTimeMillis():

  - 该方法的作用是返回当前的计算机时间，时间的表达格式为当前计算机时间和GMT时间（格林威治时间）1970年1月1号0时0分0秒所差的毫秒数。

  - void exit(int status):

  - 该方法的作用是退出程序。其中status的值为0代表正常退出，非零代表异常退出。**使用该方法可以在图形界面编程中实现程序的退出功能**等。

  - void gc()：

  - 该方法的作用是请求系统进行垃圾回收。至于系统是否立刻回收，则取决于系统中垃圾回收算法的实现以及系统执行时的情况。

  - String getProperty(String key):

  - 该方法的作用是获得系统中属性名为key的属性对应的值。系统中常见的属性名以及属性的作用如下表所示：

    |    属性名    |      属性说明      |
    | :----------: | :----------------: |
    | java.version | java运行时环境版本 |
    |  java.home   |    java安装目录    |
    |   os.name    |   操作系统的名称   |
    |  os.version  |   操作系统的版本   |
    |  user.name   |   用户的账户名称   |
    |  user.home   |    用户的主目录    |
    |   user.dir   | 用户的当前工作目录 |

### 6.Math类

-  java.lang.Math提供了一系列静态方法用于科学计算。其方法的参数和返回 值类型一般为double型。
  - abs绝对值 
  - acos,asin,atan,cos,sin,tan 三角函数 
  - sqrt 平方根 
  - pow(double a,doble b)a的b次幂 
  - log自然对数 
  - exp e为底指数 
  - max(double a,double b) 
  - min(double a,double b) 
  - random()返回0.0到1.0的随机数 
  - long round(double a)double型数据a转换为long型（四舍五入） 
  - toDegrees(double angrad)弧度—>角度 
  - toRadians(double angdeg)角度—>弧度

### 7.BigInteger与BigDecimal

-  Integer类作为int的包装类，能存储的最大整型值为2^31-1，Long类也是有限的，最大为2^63-1。如果要表示再大的整数，不管是基本数据类型还是他们的包装类都无能为力，更不用说进行运算了。
- java.math包的Biglnteger可以表示不可变的任意精度的整数。Biglnteger提供所有Java的基本整数操作符的对应物，并提供java.lang.Math的所有相关方法。另外，Biglnteger还提供以下运算：模算术、GCD计算、质数测试、素数生成、位操作以及一些其他操作。
- 构造器
  - Biglnteger(String val):根据字符串构建Biglnteger对象
- 常用方法 
  - public Biglnteger abs():返回此Biglnteger的绝对值的Biglnteger。
  - Biglnteger add(Biglnteger val):返回其值为（this+val)的Biglnteger 
  - Biglnteger subtract(Biglnteger val):返回其值为（this-val)的Biglnteger 
  - Biglnteger multiply(Biglnteger val):返回其值为（this *val)的Biglnteger 
  - Biglnteger divide(Biglnteger val):返回其值为（this/val)的Biglnteger。**整数 相除只保留整数部分**。
  - Biglnteger remainder(Biglnteger val):返回其值为（this % val)的Biglnteger。
  - Biglnteger[] divideAndRemainder(Biglnteger val):返回包含（this/val)后跟 （this % val)的两个Biglnteger的数组。
  - Biglnteger pow(int exponent):返回其值为（this^exponent)的Biglnteger。
-  一般的Float类和Double类可以用来做科学计算或工程计算，但在商业计算中， 要求数字精度比较高，故用到java.math.BigDecimal类。
- BigDecimal类支持不可变的、任意精度的有符号十进制定点数。
- 构造器 
  - public BigDecimal(double val) 
  - public BigDecimal(String val) 
- 常用方法 
  - public BigDecimal add(BigDecimal augend) 
  - public BigDecimal subtract(BigDecimal subtrahend) 
  - public BigDecimal multiply(BigDecimal multiplicand) 
  - public BigDecimal divide(BigDecimal divisor,int scale,int roundingMode)

### 8.面试题

- ##### 下列程序运行的结果

  ```java
  public class StringTest{
      String str = new String("good");
      char[] ch = {'t', 'e', 's', 't'};
      public void change(String str, char ch[]){
          str = "test ok";
          ch[0] = 'b';
      }
      public static void main(String[] args){
          StringTest ex = new StringTest();
          ex.change(ex.str, ex.ch);
          System.out.print(ex.str + " and ");
          System.out.println(ex.ch);
          //good and best
      }
  }
  ```

- ##### 程序输出

  ```java
  String str = null;
  StringBuffer sb = new StringBuffer();
  sb.append(str);
  
  System.out.println(sb.length());//4
  
  System.out.println(sb);//null
  
  StringBuffer sb1 = new StringBuffer(str);
  System.out.println(sb1);//空指针异常
  ```

## 第八章：枚举类与注解

### 1.枚举类的使用

- ##### 类的对象只有有限个，确定的。举例如下：

  - 星期：Monday(星期一）、……Sunday（星期天）
  - 性别：Man(男）、Woman(女）
  - 季节：Spring（春节）.……Winter(冬天）
  - 支付方式：Cash（现金）、WeChatPay（微信）、Alipay（支付宝）、BankCard(银行卡）、CreditCard(信用卡）
  - 就职状态：Busy、Free、Vocation、Dimission
  - 订单状态：Nonpayment（未付款）、Paid（已付款）、Delivered(已发货）、Return(退货）、Checked(已确认）Fulfilled(已配货）、
  - 线程状态：创建、就绪、运行、阻塞、死亡

- **当需要定义一组常量时，强烈建议使用枚举类**

-  枚举类的实现

  - JDK1.5之前需要自定义枚举类
  - JDK1.5新增的**enum关键字**用于定义枚举类
  - 若枚举只有一个对象，则可以作为**一种单例模式的实现方式**。

- 枚举类的属性

  - **枚举类对象的属性不应允许被改动，所以应该使用private final 修饰**
  - 枚举类的使用private final修饰的属性应该在构造器中为其赋值
  - 若枚举类显式的定义了带参数的构造器，则在列出枚举值时也必须对应的传入参数

- 自定义枚举类

  - **私有化**类的构造器，保证不能在类的外部创建其对象
  - 在类的内部创建枚举类的实例。声明为：public static final
  - 对象如果有实例变量，应该声明为private final，并在构造器中初始化

- 使用enum定义枚举类

  - 使用说明
    - 使用enum定义的枚举类默认继承了java.lang.Enum类，因此不能再继承其他类
    - 枚举类的构造器只能使用private权限修饰符
    - 枚举类的所有实例必须在枚举类中显式列出（，分隔；结尾）。列出的实例系统会**自动添加public static final**修饰
    - **必须在枚举类的第一行声明枚举类对象**
  - J**DK1.5中可以在switch表达式中使用Enum定义的枚举类的对象作为表达式**，case子句可以直接使用枚举值的名字，无需添加枚举类作为限定。

- 主要的方法

  - values()方法：返回枚举类型的对象数组。该方法可以很方便地遍历所有的枚举值。
  - valueOf(String str):可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象的“名字”。如不是，会有运行时异常：lllegalArgumentException。
  - toString():返回当前枚举类对象常量的名称

- 实现接口的枚举类

  - 和普通Java类一样，枚举类可以实现一个或多个接口
  - 若每个枚举值在调用实现的接口方法呈现相同的行为方式，则只要统一实现该方法即可。
  - 若需要每个枚举值在调用实现的接口方法呈现出不同的行为方式，则可以让每个枚举值分别来实现该方法

### 2.注解

-  从JDK5.0开始，Java增加了对元数据（MetaData)的支持，也就是Annotation(注解）

- Annotation其实就是代码里的特殊标记，这些标记可以在编译，类加载，运行时被读取，并执行相应的处理。通过使用Annotation，程序员可以在不改变原有逻辑的情况下，在源文件中嵌入一些补充信息。代码分析工具、开发工具和部署工具可以通过这些补充信息进行验证或者进行部署。

- Annotation可以像修饰符一样被使用，可用于**修饰包，类，构造器，方法，成员变量，参数，局部变量的声明**，这些信息被保存在Annotation的“name=value”对中。

-   在JavaSE中，注解的使用目的比较简单，例如标记过时的功能，忽略警告等。在JavaEE/Android中注解占据了更重要的角色，例如用来配置应用程序的任何切面，代替JavaEE旧版中所遗留的繁冗代码和XML配置等。

- 未来的开发模式都是基于注解的，JPA是基于注解的，Spring2.5以上都是基于注解的，Hibernate3.x以后也是基于注解的，现在的Struts2有一部分也是基于注解的了，注解是一种趋势，一定程度上可以说：**框架=注解+反射+设计模式。**

- ##### 常见的注解

  - 使用Annotation时要在其前面增加@符号，并把该Annotation当成一个修饰符使用。用于修饰它支持的程序元素

  - ##### 示例一：生成文档相关的注解

    - @author标明开发该类模块的作者，多个作者之间使用，分割
    - @version 标明该类模块的版本
    - @see参考转向，也就是相关主题
    - @since从哪个版本开始增加的
    - @param对方法中某参数的说明，如果没有参数就不能写
    - @return 对方法返回值的说明，如果方法的返回值类型是void就不能写
    - @exception对方法可能抛出的异常进行说明，如果方法没有用throws显式抛出的异常就不能写
    - 其中
      - @param @return和@exception 这三个标记都是只用于方法的。
      - @param的格式要求：@param 形参名 形参类型 形参说明
      - @return的格式要求：@return返回值类型 返回值说明
      - @exception的格式要求：@exception 异常类型 异常说明
      - @param和@exception可以并列多个

  - ##### 示例二：在编译时进行格式检查（JDK内置的三个基本注解）

    - @Override:限定重写父类方法，该注解只能用于方法
    - @Deprecated:用于表示所修饰的元素（类，方法等）已过时。通常是因为所修饰的结构危险或存在更好的选择
    - @SuppressWarnings:抑制编译器警告

- ##### 自定义Annotation

  -  定义新的Annotation 类型使用@interface关键字
  - 自定义注解自动继承了**java.lang.annotation.Annotation接口**
  - Annotation的成员变量在Annotation定义中以无参数方法的形式来声明。其方法名和返回值定义了该成员的名字和类型。我们称为配置参数。类型只能是八种基本数据类型、**string类型、class类型、enum类型、Annotation类型、以上所有类型的数组**。
  - 可以在定义Annotation的成员变量时为其指定初始值，指定成员变量的初始值可使用**default关键字**
  - 如果只有一个参数成员，建议使用参数名为value
  - 如果定义的注解含有配置参数，那么使用时必须指定参数值，除非它有默认值。格式是“参数名=参数值”，如果只有一个参数成员，且名称为value，可以省略“value=”
  - 没有成员定义的Annotation称为标记；包含成员变量的Annotation称为元数据Annotation

- **注意：自定义注解必须配上注解的信息处理流程才有意义。**

  ```java
  @MyAnnotation(value="尚硅谷") 
  public class MyAnnotationTest { 
      public static void main(String[] args){ 
          Class clazz = MyAnnotationTest.class; 
          Annotation a = clazz.getAnnotation(MyAnnotation.class); 
          MyAnnotation m = (MyAnnotation)a; 
          String info = m.value(); 
          System.out.println(info); 
      } 
  }
  @Retention(RetentionPolicy.RUNTIME) 
  @Target(ElementType.TYPE) 
  @interface MyAnnotation{ 
      String value()default "auguigu";
  }
  ```

- JDK中的元注解

  - JDK的元Annotation 用于修饰其他Annotation定义 

  - JDK5.0提供了4个标准的meta-annotation类型，分别是： 

    - Retention 
    - Target 
    - Documented 
    - Inherited 
    - 元数据的理解： String name="atguigu"；

  - @Retention:只能用于修饰一个Annotation定义，用于指定该Annotation的生命周期，@Rentention包含一个RetentionPolicy类型的成员变量，使用@Rentention 时必须为该value成员变量指定值：

    - **RetentionPolicy.SOURCE:在源文件中有效（即源文件保留）**，编译器直接丢弃这种策略的注释

    - **RetentionPolicy.CLASS:在class文件中有效（即class保留）**，当运行Java程序时，JVM不会保留注解。这是默认值

    - **RetentionPolicy.RUNTIME:在运行时有效（即运行时保留），当运行Java程序时，JVM会保留注释。程序可以通过反射获取该注释。**

      ![](C:\Users\YS\Desktop\笔记\注解.JPG)

  - @Target:用于修饰Annotation定义，用于指定被修饰的Annotation能用于修饰哪些程序元素。@Target也包含一个名为value的成员变量。

    |      取值      |                  |   取值    |                                                |
    | :------------: | :--------------: | :-------: | :--------------------------------------------: |
    |  CONSTRUCTOR   |  用于描述构造器  |  PACKAGE  |                   用于描述包                   |
    |     FIELD      |    用于描述域    | PARAMETER |                  用于描述参数                  |
    | LOCAL_VARIABLE | 用于描述局部变量 |   TYPE    | 用于描述类，接口<br />(包括注解类型)或enum声明 |
    |     METHOD     |   用于描述方法   |           |                                                |

  - @Documented:用于指定被该元Annotation修饰的Annotation类将被javadoc 工具提取成文档。默认情况下，javadoc是不包括注解的。

    - 定义为Documented的注解必须设置Retention值为RUNTIME。

  - @lnherited:被它修饰的Annotation将具有**继承性**。如果某个类使用了被@Inherited 修饰的Annotation，则其子类将自动具有该注解。

    - 比如：如果把标有@lnherited注解的自定义的注解标注在类级别上，子类则可以继承父类类级别的注解
    - 实际应用中，使用较少

  - ##### 利用反射获取注解信息

    - JDK 5.0在java.lang.reflect 包下新增了AnnotatedElement接口，该接口代表程序中可以接受注解的程序元素
    - 当一个Annotation 类型被定义为运行时Annotation后，该注解才是运行时可见，当class文件被载入时保存在class文件中的Annotation 才会被虚拟机读取
    - 程序可以调用AnnotatedElement对象的如下方法来访问Annotation信息

  - ##### JDK8中的注解

    - Java 8对注解处理提供了两点改进：可重复的注解及可用于类型的注解。此外，反射也得到了加强，在Java8中能够得到方法参数的名称。这会简化标注在方法参数上的注解。
    - 类型注解：
      - JDK1.8之后，关于元注解@Target的参数类型ElementType枚举值多了两个：**TYPE PARAMETER,TYPE USE。**
      - 在Java 8之前，注解只能是在声明的地方所使用，Java8开始，注解可以应用在**任何地方。**
        - ElementType.TYPE PARAMETER 表示该注解能写在**类型变量的声明语句中**（如：泛型声明）。
        - ElementType.TYPE USE 表示该注解能**写在使用类型的任何语句中**。

## 第九章：Java的集合

### 1.Java集合框架概述

- 一方面，面向对象语言对事物的体现都是以对象的形式，为了方便对多个对象的操作，就要对对象进行存储。

- 另一方面，使用Array存储对象方面具有一些弊端，而Java集合就像一种容器，可以**动态地**把多个对象的引用放入容器中。

- ##### 数组在内存存储方面的特点：

  - 数组初始化以后，长度就确定了。
  - 数组声明的类型，就决定了进行元素初始化时的类型

- ##### 数组在存储数据方面的弊端：

  - 数组初始化以后，长度就不可变了，不便于扩展
  - 数组中提供的属性和方法少，不便于进行添加、删除、插入等操作，且效率不高。同时无法直接获取存储元素的个数
  - 数组存储的数据是有序的、可以重复的。---->存储数据的特点单一

- Java集合类可以用于存储数量不等的多个**对象**，还可用于保存具有映射关系的关联数组。

- ##### 集合的使用场景

  - Android客户端：
    - 将JSON对象或JSON数组转换为Java对象或Java对象构成的List
  - 服务器端：
    - 将Java对象或Java对象构成的List转换为JSON对象或JSON数组

- ##### Java集合的分类

  - Java 集合可分为Collection和Map两种体系
    - Collection接口：单列数据，定义了存取一组对象的方法的集合
      - List：元素有序、可重复的集合
      - Set:元素无序、不可重复的集合
    - Map接口：双列数据，保存具有映射关系“**key-value对**”的集合

- ##### Collection接口继承树

  ![](.\Java集合框架.JPG)

- ##### Map接口继承树

  ![](.\Java集合框架2.JPG)

### 2.Collection接口方法

-  Collection 接口是List、Set和Queue接口的父接口，该接口里定义的方法既可用于操作Set集合，也可用于操作List和Queue集合。

- JDK不提供此接口的任何直接实现，而是提供更具体的子接口（如：Set和List）实现。

- 在Java5之前，Java集合会丢失容器中所有对象的数据类型，把所有对象都当成Object类型处理；从JDK5.0增加了**泛型**以后，Java集合可以记住容器中对象的数据类型。

- ##### 接口中的方法

  - 1、添加
    - add(Object obj)
    - addAll(Collection coll)
  - 2、获取有效元素的个数
    - int size()
  - 3、清空集合
    - void clear()
  - 4、是否是空集合
    - boolean isEmpty()
  - 5、是否包含某个元素
    - boolean contains(Object obj):是通过元素的**equals方法**来判断是否是同一个对象
    - boolean containsAll(Collection c):也是调用元素的equals方法来比较的。拿**两个集合**的元素**挨个比较。**
  - 6、删除
    - boolean remove(Object obj):通过元素的equals方法判断是否是要删除的那个元素。只会**删除找到的第一个元素**
    - boolean removeAll(Collection coll):取当前集合的**差集**
  - 7、取两个集合的交集
    - boolean retainAl(Collection c):把交集的结果存在当前集合中，**不影响c**
  - 8、集合是否相等
    - boolean equals(Object obj)
  - 9、转成对象数组
    - Object[] toArray()
  - 10、获取集合对象的哈希值
    - hashCode()
  - 11、遍历
    - iterator():返回迭代器对象，用于集合遍历

### 3.Iterator迭代器接口

- ##### 使用Iterator接口遍历集合元素

  - Iterator对象称为迭代器（设计模式的一种），主要用于遍历Collection集合中的元素。
  - GOF给迭代器模式的定义为：提供一种方法访问一个容器（container)对象中各个元素，而又不需暴露该对象的内部细节。迭代器模式，就是为容器而生。类似于“公交车上的售票员”、“火车上的乘务员”、“空姐”。
  - Collection接口继承了java.lang.lterable接口，该接口有一个iterator（）方法，那么所有实现了Collection接口的集合类都有一个iterator（）方法，用以返回一个实现了Iterator接口的对象。
  - Iterator仅用于遍历集合，lterator本身并不提供承装对象的能力。如果需要创建Iterator对象，则必须有一个被迭代的集合。
  - 集合对象每次调用iterator（）方法都得到一个全新的迭代器对象，**默认游标都在集合的第一个元素之前**。

- 在调用it.next（）方法之前必须要调用it.hasNext（）进行检测。若不调用，且下一条记录无效，直接调用it.next（）会抛出NoSuchElementException异常。

  ```java
  Iterator iter = coll.iterator();//回到起点 
  while(iter.hasNext()){ 
      Object obj=iter.next();
      if(obj.equals("Tom")){ 
          iter.remove()
      }
  }
  ```

-  注意：

  - Iterator可以删除集合的元素，但是是遍历过程中通过迭代器对象的remove方法，不是集合对象的remove方法。
  - **如果还未调用next（）或在上一次调用next 方法之后已经调用了remove方法，再调用remove都会报lllegalStateException。**

- ##### 使用foreach循环遍历集合元素

  - Java 5.0提供了foreach 循环迭代访问 Collection和数组。

  - 遍历操作不需获取Collection或数组的长度，无需使用索引访问元素。

  -  **遍历集合的底层调用lterator完成操作。**

  -  foreach还可以用来遍历数组。

    ```java
    public class ForTest{
        public static void main(String[] args){
            String[] str = new String[5];
            for(String myStr : str){
                myStr = "atguigu";
                System.out.println(myStr);
            }
            for(int i = 0; i < str.length; i++){
                System.out.println(str[i]);
            }
        }
    }
    // atguigu
    // atguigu
    // atguigu
    // atguigu
    // atguigu
    // null
    // null
    // null
    // null
    // null
    ```

### 4.List接口

- ##### List接口的概述

  - 鉴于Java中数组用来存储数据的局限性，我们通常使用List替代数组
  - List集合类中**元素有序、且可重复**，集合中的每个元素都有其对应的顺序索引。
  - List容器中的元素都对应一个整数型的序号记载其在容器中的位置，可以根据序号存取容器中的元素。
  - JDK API 中List接口的实现类常用的有：ArrayList、LinkedList和Vector。

- ##### List接口的方法

  - List除了从Collection集合继承的方法外，List集合里添加了一些**根据索引来 操作集合元素**的方法。
    - void add(int index,Object ele):在index位置插入ele元素 
    - boolean addAll(int index,Collection eles):从index位置开始将eles中 的所有元素添加进来 
    - Object get(int index):**获取指定index位置的元素** 
    - int indexOf(Object obj):返回obj在集合中**首次**出现的位置 
    - int lastlndexOf(Object obj):返回obj在当前集合中**末次**出现的位置 
    - Object remove(int index):**移除指定index位置的元素，并返回此元素** 
    - Object set(int index,Object ele):**设置指定index位置的元素为ele** 
    - List subList(int fromlndex,int tolndex):返回从fromlndex到tolndex 位置的子集合

- ##### List实现类之一：ArrayList

  - ArrayList是List接口的典型实现类、主要实现类

  - 本质上，ArrayList是对象引用的一个”变长”数组

  - **数组ArrayList的JDK1.8之前与之后的实现区别？**

    - JDK1.7：ArrayList像饿汉式，直接创建一个初始容量为10的数组
    - JDK1.8：ArrayList像懒汉式，一开始创建一个长度为0的数组，当添加第一个元素时再创建一个始容量为10的数组

  - Arrays.asList(..…)方法返回的List集合，既不是ArrayList实例，也不是Vector 实例。Arrays.asList(..…)**返回值是一个固定长度的List集合**

    ```java
    @Test
    public void testListRemove(){
        List list = new ArrayList();
        list.add(1);
        list.add(2);
        list.add(3);
        updateList(list);
        System.out.println(list);//[1,2]
    }
    private static void updateList(List list){
        list.remove(2);
    }
    ```

- ##### List实现类之二：LinkedList

  - 对于**频繁的插入或删除元素**的操作，建议使用LinkedList类，效率较高 
  - 新增方法： 
    - void addFirst(Object obj) 
    - void addLast(Object obj) 
    - Object getFirst()
    - Object getLast() 
    - Object removeFirst() 
    - Object removeLast()
  - LinkedList:**双向链表**，内部没有声明数组，而是定义了Node类型的first和last，用于记录首末元素。同时，定义内部类Node，作为LinkedList中保存数据的基本结构。Node除了保存数据，还定义了两个变量：
    - prev变量记录前一个元素的位置
    - next变量记录下一个元素的位置

- ##### List 实现类之三：Vector

  - Vector是一个古老的集合，JDK1.0就有了。大多数操作与ArrayList 相同，区别之处在于**Vector是线程安全的**。
  - 在各种list中，最好把ArrayList作为缺省选择。当插入、删除频繁时， 使用LinkedList；Vector总是比ArrayList慢，所以尽量避免使用。
  - 新增方法： 
    - void addElement(Object obj) 
    - void insertElementAt(Object obj,int index) 
    - void setElementAt(Object obj,int index) 
    - void removeElement(Object obj) 
    - void removeAllElements()

- ##### 面试题

  -  请问ArrayList/LinkedList/Vector的异同？谈谈你的理解？ArrayList底层是什么？扩容机制？Vector和ArrayList的最大区别？
  - ArrayList和LinkedList的异同
  - 二者都线程不安全，相对线程安全的Vector，执行效率高。
  - 此外，ArrayList是实现了基于动态数组的数据结构，LinkedList基于链表的数据结构。对于随机访问get和set，ArrayList觉得优于LinkedList,因为LinkedList要移动指针。对于新增和删除操作add（特指插入）和remove，LinkedList比较占优势，因为ArrayList要移动数据。
  - ArrayList和Vector的区别
  - Vector和ArrayList几乎是完全相同的，唯一的区别在于Vector是同步类（synchronized)，属于强同步类。因此开销就比ArrayList要大，访问要慢。正常情况下，大多数的Java程序员使用ArrayList而不是Vector，因为同步完全可以由程序员自己来控制。**Vector每次扩容请求其大小的2倍空间，而ArrayList是1.5倍。**Vector还有一个子类Stack。

### 5.Set接口

- ##### Set接口概述

  - Set接口是Collection的子接口，set接口没有提供额外的方法
  - Set集合不允许包含相同的元素，如果试把两个相同的元素加入同一个Set集合中，则添加操作失败。
  - **Set判断两个对象是否相同不是使用==运算符，而是根据equals（）方法**

- ##### Set实现类之一：HashSet

  - HashSet是Set接口的典型实现，大多数时候使用Set集合时都使用这个实现类。

  - HashSet 按Hash算法来存储集合中的元素，因此具有很好的存取、查找、删除性能。

  - HashSet具有以下特点：

    - 不能保证元素的排列顺序
    - HashSet不是线程安全的
    - 集合元素可以是null

  - HashSet 集合判断两个元素相等的标准：

    - 两个对象通过hashCode（）方法比较相等，并且两个对象的equals（）方法返回值也相等。

  - 对于存放在Set容器中的对象，对应的类一定要重写equals(）和hashCode（Objectobi）方法，以实现对象相等规则。即：“**相等的对象必须具有相等的散列码**”。

  - 向HashSet中添加元素的过程：

    - 当向HashSet集合中存入一个元素时，HashSet会调用该对象的hashCode（）方法来得到该对象的hashCode值，然后根据hashCode值，通过某种散列函数决定该对象在HashSet底层数组中的存储位置。（<u>这个散列函数会与底层数组的长度相计算得到在数组中的下标，并且这种散列函数计算还尽可能保证能均匀存储元素，越是散列分布，该散列函数设计的越好</u>）
    - 如果两个元素的hashCode（）值相等，会再继续调用equals方法，如果equals方法结果为true，添加失败；如果为false，那么会保存该元素，**但是该数组的位置已经有元素了，那么会通过链表的方式继续链接**。
      如果两个元素的equals（）方法返回true，但它们的**hashCode（）返回值不相等**，hashSet将会把它们存储在**不同的位置**，但**依然可以添加成功**。

    ![](.\HashSet.JPG)

  - 底层也是数组，初始容量为16，**当如果使用率超过0.75，(16*0.75=12）就会扩大容量为原来的2倍。**（16扩容为32，依次为64,128...等）

  - ##### 重写hashCode()方法的基本原则

    - 在程序运行时，同一个对象多次调用hashCode（）方法应该返回相同的值。
    - 当两个对象的equals（）方法比较返回true时，这两个对象的hashCode（）方法的返回值也应相等。
    - 对象中用作equals（）方法比较的Field，都应该用来计算hashCode值。
    -  以自定义的Customer类为例，何时需要重写equals(）?
      - 当一个类有自己特有的“**逻辑相等**”概念，当改写equals（）的时候，总是要改写hashCode（），根据一个类的equals方法（改写后），两个截然不同的实例有可能在逻辑上是相等的，但是，根据Object.hashCode（）方法，它们仅仅是两个对象。
      - **因此，违反了“相等的对象必须具有相等的散列码”。**
    -  结论：复写equals方法的时候一般都需要同时复写hashCode方法。**通常参与计算hashCode的对象的属性也应该参与到equals（）中进行计算。**

  - 以Eclipse/IDEA为例，在自定义类中可以调用工具自动重写equals和hashCode。

  - 问题：为什么用Eclipse/IDEA复写hashCode方法，有**31这个数字**？

    - **选择系数的时候要选择尽量大的系数**。因为如果计算出来的hash地址越大，所谓的“冲突”就越少，查找起来效率也会提高。（减少冲突）
    - 并且31只占用5bits，相乘造成数据溢出的概率较小。
    - 31可以由i*31==（i<<5)-1来表示，现在很多虚拟机里面都有做相关优化。（提高算法效率）
    - 31是一个素数，**素数作用就是如果我用一个数字来乘以这个素数，那么最终出来的结果只能被素数本身和被乘数还有1来整除！（减少冲突）**

- ##### Set实现类之二：LinkedHashSet

  - LinkedHashSet是HashSet的子类
  - LinkedHashSet根据元素的 hashCode值来决定元素的存储位置，但它同时使用双向链表维护元素的次序，这使得元素看起来是以**插入顺序保存**的。
  - **LinkedHashSet插入性能略低于HashSet**，但在迭代访问Set里的全部元素时有很好的性能。
  - LinkedHashSet不允许集合元素重复。

- ##### Set实现类之三：TreeSet

  -  TreeSet是SortedSet接口的实现类，TreeSet可以确保集合元素处于**排序状态**。

  - TreeSet底层使用**红黑树结构存储数据** 

  - 新增的方法如下：（了解） 

    - Comparator comparator() 
    - Object first() 
    - Object last() 
    - Object lower(Objecte) 
    - Object higher(Objecte) 
    - SortedSet subSet(fromElement,toElement) 
    - SortedSet headSet(toElement) 
    - SortedSet tailSet(fromElement) 

  - TreeSet两种排序方法：**自然排序和定制排序。**默认情况下，TreeSet采用自然排序。

  - TreeSet和后面要讲的TreeMap采用红黑树的存储结构

  - 特点：有序，查询速度比List快

    ![](.\TreeSet.JPG)

  - 链接：http://www.cnblogs.com/yangecnu/p/Introduce-Red-Black-Tree.html

  - ##### 排序-自然排序

    -  自然排序：TreeSet会调用集合元素的compareTo（Object obj)方法来比较元素之间的大小关系，然后将集合元素按升序（默认情况）排列
    - **如果试图把一个对象添加到TreeSet时，则该对象的类必须实现Comparable接口。**
      - 实现Comparable的类必须实现compare To（Object obj)方法，两个对象即通过compareTo（Object obj)方法的返回值来比较大小。
    - Comparable的典型实现：
      - BigDecimal、Biglnteger以及所有的数值型对应的包装类：按它们对应的数值大小进行比较
      - Character:按字符的unicode值来进行比较
      - Boolean:true 对应的包装类实例大于false对应的包装类实例
      - String:按字符串中字符的unicode值进行比较
      - Date、Time:后边的时间、日期比前面的时间、日期大
    -  向TreeSet中添加元素时，只有第一个元素无须比较compareTo（）方法，后面添加的所有元素都会调用compareTo（）方法进行比较。
    - **因为只有相同类的两个实例才会比较大小，所以向TreeSet中添加的应该是同一个类的对象。**
    - 对于TreeSet集合而言，它判断**两个对象是否相等的唯一标准**是：两个对象通过compare To（Object obj)方法比较返回值。
    - 当需要把一个对象放入TreeSet中，重写该对象对应的equals（）方法时，应保证该方法与compareTo（Object obj)方法有一致的结果：如果两个对象通过equals()方法比较返回true，则通过compareTo（Object obj)方法比较应返回0。否则，让人难以理解。

  - ##### 排序-定制排序

    - TreeSet的自然排序要求元素所属的类实现Comparable接口，如果元素所属的类没有实现Comparable接口，或不希望按照升序（默认情况）的方式排列元素或希望按照其它属性大小进行排序，则考虑使用定制排序。定制排序，通过Comparator接口来实现。需要**重写compare（To1，To2）方法**。
    - 利用int compare（To1，To2)方法，比较o1和o2的大小：如果方法返回正整数，则表示o1大于o2；如果返回0，表示相等；返回负整数，表示01小于o2。
    - 要实现定制排序，需要将实现Comparator接口的实例作为形参传递给TreeSet的构造器。
    - 此时，**仍然只能向TreeSet中添加类型相同的对象**。否则发生ClassCastException异常。
    -  使用定制排序判断两个元素相等的标准是：**通过Comparator比较两个元素返回了0。**

### 6.Map接口

- ##### Map接口概述

  - Map与Collection并列存在。用于保存具有**映射关系**的数据：key-value
  - Map中的key和value都可以是任何引用类型的数据
  - Map中的**key用Set来存放，不允许重复**，即同一个Map对象所对应的类，须重写hashCode（）和equals（）方法
  - 常用String类作为Map的“键”key和value之间存在单向一对一关系，即通过指定的key总能找到唯一的、确定的value
  - Map接口的常用实现类：HashMap、TreeMap、LinkedHashMap和Properties。其中，**HashMap是Map接口使用频率最高的实现类**

- ##### Map接口常用的方法

  - 添加、删除、修改操作： 
    - Object put(Object key,Object value):将指定key-value添加到（或修改）当前map对象中 
    - void putAll(Map m):将m中的所有key-value对存放到当前map中 
    - Object remove(Object key):移除指定key的key-value对，并返回value 
    - void clear():清空当前map中的所有数据 
  - 元素查询的操作： 
    - Object get(Object key):获取指定key对应的value 
    - boolean containsKey(Object key):是否包含指定的key 
    - boolean containsValue(Object value):是否包含指定的value 
    - int size():返回map中key-value对的个数 
    - boolean isEmpty():判断当前map是否为空 
    - boolean equals(Object obj):判断当前map和参数对象obj是否相等 
  - 元视图操作的方法： 
    - Set keySet():返回所有key构成的Set集合 
    - Collection values():返回所有value构成的Collection集合 
    - Set entrySet():返回所有key-value对构成的Set集合

- ##### Map实现类之一：HashMap

  - HashMap是Map接口使用频率**最高**的实现类。
  - 允许使用nul;键和null值，与HashSet一样，不保证映射的顺序。
  - 所有的key构成的集合是Set：无序的、不可重复的。所以，key所在的类要重写：equals()和hashCode()
  - 所有的value构成的集合是Collection：无序的、可以重复的。所以，value所在的类要重写：equals（）
  - 一个key-value构成一个entry
  - 所有的entry构成的集合是Set:无序的、不可重复的
  - HashMap判断两个key相等的标准是：**两个key 通过equals（）方法返回true，hashCode值也相等。**
  - HashMap 判断两个value相等的标准是：**两个value通过equals（）方法返回true。**

- ##### HashMap源码中的重要常量

  -  DEFAULT INITIAL CAPACITY:HashMap的默认容量，16
  - MAXIMUM CAPACITY:HashMap的最大支持容量，2^30
  - DEFAULT LOAD FACTOR:HashMap的默认加载因子
  - TREEIFY THRESHOLD:Bucket中链表长度大于该默认值，转化为红黑树
  - UNTREEIFY THRESHOLD:Bucket中红黑树存储的Node小于该默认值，转化为链表
  - MIN_TREEIFY CAPACITY:桶中的Node被树化时最小的hash表容量。（当桶中Node的数量大到需要变红黑树时，若hash表容量小于MIN TREEIFY CAPACITY时，此时应执行resize扩容操作这个MIN TREEIFY CAPACITY的值至少是TREEIFY THRESHOLD的4倍。)
  - table:存储元素的数组，总是2的n次幂
  - entrySet:存储具体元素的集
  - size:HashMap中存储的键值对的数量
  - modCount:HashMap扩容和结构改变的次数。
  - threshold:扩容的临界值，=容量*填充因子
  - loadFactor:填充因子

- ##### HashMap的存储结构

  - JDK7及以前版本：HashMap是数组+链表结构（即为链地址法)

  - JDK 8版本发布以后：HashMap是数组+链表+红黑树实现。

  - ##### JDK1.8之前

    - HashMap的内部存储结构其实是**数组和链表**的结合。当实例化一个HashMap时，系统会创建一个长度为Capacity的Entry数组，这个长度在哈希表中被称为容量(Capacity)，在这个数组中可以存放元素的位置我们称之为“**桶**”（bucket)，每个bucket都有自己的索引，系统可以根据索引快速的查找bucket中的元素。

    -  每个bucket中存储一个元素，即一个Entry对象，但每一个Entry对象可以带一个引用变量，用于指向下一个元素，因此，在一个桶中，就有可能生成一个Entry链。**而且新添加的元素作为链表的head**。

    - ##### 添加元素的过程：

      - 向HashMap中添加entry1(key，value），需要首先计算entry1中key的哈希值（根据key所在类的hashCode（）计算得到），此哈希值经过处理以后，得到在底层Entry]数组中要存储的位置i。
      - 如果位置i让没有元素，则entry1直接添加成功。
      - 如果位置i上已经存在entry2(或还有链表存在的entry3，entry4)，则需要通过循环的方法，依次比较entry1中key和其他的entry。如果彼此hash值不同，则直接添加成功。如果hash值不同，继续比较二者是否equals。**如果返回值为true，则使用entry1的value去替换equals为true的entry的value。**
      - 如果遍历一遍以后，发现所有的equals返回都为false,则entry1仍可添加成功。entry1指向原有的entry元素。

    - ##### HashMap的扩容

      - 当HashMap中的元素越来越多的时候，hash冲突的几率也就越来越高，因为数组的长度是固定的。所以为了提高查询的效率，就要对HashMap的数组进行扩容，而在HashMap数组扩容之后，最消耗性能的点就出现了：原数组中的数据必须重新计算其在新数组中的位置，并放进去，这就是resize。

    - ##### 那么HashMap什么时候进行扩容呢？

      - 当HashMap中的元素个数超过数组大小（数组总大小length，不是数组中个数size)*loadFactor时，就会进行数组扩容，loadFactor的默认值（DEFAULT LOAD FACTOR)为0.75，这是一个折中的取值。也就是说，默认情况下，数组大小（DEFAULT INITIAL CAPACITY)为16，那么当HashMap中元素个数超过16*0.75=12（这个值就是代码中的threshold值，也叫做临界值）的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，**所以如果我们已经预知HashMap中元素的个数，那么预设元素的个数能够有效的提高HashMap的性能**。

  - ##### JDK 1.8

    - HashMap的内部存储结构其实是**数组+链表+树**的结合。当实例化一个HashMap时，会初始化initialCapacity和loadFactor，在put第一对映射关系时，系统会创建一个长度为initialCapacity的Node数组，这个长度在哈希表中被称为容量（Capacity)，在这个数组中可以存放元素的位置我们称之为“桶”（bucket)，每个bucket都有自己的索引，系统可以根据索引快速的查找bucket中的元素。

    - **每个bucket中存储一个元素，即一个Node对象**，但每一个Node对象可以带一个引用变量next，用于指向下一个元素，因此，**在一个桶中，就有可能生成一个Node链。也可能是一个一个TreeNode对象**，每一个TreeNode对象可以有两个叶子结点left和right，因此，在一个桶中，就有可能生成一个TreeNode树。而新添加的元素作为链表的last，或树的叶子结点。

    - **那么HashMap什么时候进行扩容和树形化呢？**

      - 当HashMap中的元素个数超过数组大小（数组总大小length，不是数组中个数size)\*loadFactor时，就会进行数组扩容，loadFactor的默认值（DEFAULT LOAD FACTOR)为0.75,这是一个折中的取值。也就是说，默认情况下，数组大小（DEFAULT INITIAL CAPACITY)为16，那么当HashMap中元素个数超过16*0.75=12（这个值就是代码中的threshold值，也叫做临界值）的时候，就把数组的大小扩展为2*16=32，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，**所以如果我们已经预知HashMap中元素的个数，那么预设元素的个数能够有效的提高HashMap的性能。**
      - **当HashMap中的其中一个链的对象个数如果达到了8个，此时如果capacity没有达到64，那么HashMap会先扩容解决，如果已经达到了64，那么这个链会变成树，结点类型由Node变成TreeNode类型。当然，如果当映射关系被移除后，下次resize方法时判断树的结点个数低于6个，也会把树再转为链表。**

    - **关于映射关系的key是否可以修改？answer：不要修改**

      - 映射关系存储到HashMap中会存储key的hash值，这样就不用在每次查找时重新计算每一个Entry或Node（TreeNode)的hash值了，因此如果已经put到Map中的映射关系，再修改key的属性，而这个属性又参与hashcode值的计算，那么会导致匹配不上。

    - ##### 总结：JDK1.8相较于之前的变化：

      - 1.HashMap map=new HashMap()；//默认情况下，先不创建长度为16的数组
      - 2.当首次调用map.put（)时，再创建长度为16的数组
      - 3.数组为Node类型，在jdk7中称为Entry类型
      - 4.形成链表结构时，新添加的key-value对在链表的尾部（七上八下）
      - 5.当数组指定索引位置的链表长度>8时，且map中的数组的长度>64时，此索引位置上的所有key-value对使用红黑树进行存储。

    - #####  面试题：负载因子值的大小，对HashMap有什么影响

      - 负载因子的大小决定了HashMap的数据密度。
      - 负载因子越大密度越大，发生碰撞的几率越高，数组中的链表越容易长，造成查询或插入时的比较次数增多，性能会下降。
      - 负载因子越小，就越容易触发扩容，数据密度也越小，意味着发生碰撞的几率越小，数组中的链表也就越短，查询和插入时比较的次数也越小，性能会更高。但是会浪费一定的内容空间。而且经常扩容也会影响性能，建议初始化预设大一点的空间。
      - 按照其他语言的参考及研究经验，会考虑将负载因子设置为0.7~0.75，此时平均检索长度接近于常数。

- ##### Map实现类之二：LinkedHashMap

  - LinkedHashMap是HashMap的子类
  - 在HashMap存储结构的基础上，使用了一对**双向链表**来记录添加元素的顺序
  - 与LinkedHashSet类似，LinkedHashMap可以维护Map的迭代
    - 顺序：迭代顺序与Key-Value对的插入顺序一致

- ##### Map实现类之三：TreeMap

  - TreeMap存储Key-Value 对时，需要根据key-value对进行排序。TreeMap 可以保证所有的Key-Value对处于**有序**状态。
  - TreeSet底层使用**红黑树**结构存储数据
  - TreeMap的Key的排序：
    - 自然排序：TreeMap的所有的Key 必须实现Comparable接口，而且所有的Key 应该是同一个类的对象，否则将会抛出ClasssCastException
    - 定制排序：创建 TreeMap时，传入一个Comparator对象，该对象负责对TreeMap中的所有key 进行排序。此时不需要Map的Key实现Comparable 接口
  - TreeMap判断两个key相等的标准：**两个key通过compareTo（）方法或者compare（）方法返回0。**

- ##### Map实现类之四：Hashtable

  - Hashtable是个古老的Map 实现类，JDK1.0就提供了。不同于HashMap，Hashtable是线程安全的。
  - Hashtable实现原理和HashMap相同，功能相同。底层都使用哈希表结构，查询速度快，很多情况下可以互用。
  - **与HashMap不同，Hashtable 不允许使用null作为key和value**
  - 与HashMap一样，Hashtable也不能保证其中Key-Value对的顺序
  - Hashtable判断两个key相等、两个value相等的标准，与HashMap一致。

- ##### Map实现类之五：Properties

  - Properties类是Hashtable的子类，该对象用于处理属性文件
  - 由于属性文件里的key、value都是字符串类型，所以**Properties里的key和value都是字符串类型**
  - 存取数据时，建议使用setProperty（String key,String value)方法和getProperty(String key)方法

### 7.Collections工具类

- Collections是一个操作Set、List和Map等集合的工具类

- Collections中提供了一系列静态的方法对集合元素进行排序、查询和修改等操作，还提供了对集合对象设置不可变、对集合对象实现同步控制等方法

- 排序操作：（均为static方法）

  - reverse(List)：反转List中元素的顺序
  - shuffle(List)：对List集合元素进行随机排序
  - sort(List)：根据元素的自然顺序对指定List集合元素按升序排序
  - sort(List, Comparator):根据指定的Comparator产生的顺序对List集合元素进行排序
  - swap(List, int, int):将指定list集合中的i处元素和j处元素进行交换

- ##### Collection常用方法

  - 查找、替换 
    - Object max(Collection):根据元素的自然顺序，返回给定集合中的最大元素 
    - Object max(Collection,Comparator):根据Comparator 指定的顺序，返回 给定集合中的最大元素 
    - Object min(Collection) 
    - Object min(Collection,Comparator) 
    - int frequency(Collection,Object):返回指定集合中指定元素的出现次数 
    - void copy(List dest,List src):将src中的内容复制到dest中 
    - boolean replaceAll(List list,Object oldVal,Object newVal):使用新值替换 List对象的所有旧值
  - 同步控制：
    - Collections 类中提供了多个synchronizedXxx（)方法，该方法可使将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题

### 8.源码解析

#### JDK7

##### HashMap的实现原理

1.  HashMap 概述

   - HashMap 是基于哈希表的 Map 接口的**非同步实现**。此实现提供所有可选的映射操作，
   **并允许使用 null 值和 null 键**。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。 

2. HashMap的数据结构

   - 在 java 编程语言中，最基本的结构就是两种，一个是数组，另外一个是模拟指针（引
   用），所有的数据结构都可以用这两个基本结构来构造的， HashMap 也不例外。 HashMap
     实际上是一个“链表散列”的数据结构，即数组和链表的结合体。 

     ![](.\HashMap.JPG)

     从上图中可以看出， HashMap 底层就是一个数组结构，数组中的每一项又是一个链表。当新建一个 HashMap 的时候，就会初始化一个数组。 

     ```java
   /**
      * The table, resized as necessary. Length MUST Always be a power of two.
      */
     //transient修饰的变量不会被序列化
     transient Entry[] table;
     
     static class Entry<K,V> implements Map.Entry<K,V> {
         final K key;
         V value;
         Entry<K,V> next;
         final int hash; 
         //……
     }
     ```

     可以看出， Entry 就是数组中的元素，每个 Map.Entry 其实就是一个 key-value 对，它
   持有一个指向下一个元素的引用，这就构成了链表。 

3. HashMap的存取实现

   - 存储

     ```java
   public V put(K key, V value) {
         // HashMap 允许存放 null 键和 null 值。
         // 当 key 为 null 时，调用 putForNullKey 方法，将 value 放置在数组第一个位置。
         if (key == null)
             return putForNullKey(value);
         // 根据 key 的 keyCode 重新计算 hash 值。
         int hash = hash(key.hashCode());
         // 搜索指定 hash 值在对应 table 中的索引。
         int i = indexFor(hash, table.length);
         // 如果 i 索引处的 Entry 不为 null，通过循环不断遍历 e 元素的下一个元素。
         for (Entry<K,V> e = table[i]; e != null; e = e.next) {
             Object k;
             if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
                 V oldValue = e.value;
                 e.value = value;
                 e.recordAccess(this);
                 return oldValue;
             }
         }
         // 如果 i 索引处的 Entry 为 null，表明此处还没有 Entry。
         modCount++;
         // 将 key、 value 添加到 i 索引处。
         addEntry(hash, key, value, i);
         return null;
     }
     ```

     从上面的源代码中可以看出：当我们往 HashMap 中 put 元素的时候，先根据 key 的hashCode 重新计算 hash 值，根据 hash 值得到这个元素在数组中的位置（即下标），如果数组该位置上已经存放有其他元素了，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放在链尾。如果数组该位置上没有元素，就直接将该元素放到此数组中的该位置上。 

     addEntry(hash, key, value, i)方法根据计算出的 hash 值，将 key-value 对放在数组 table的 i 索引处。 addEntry 是 HashMap 提供的一个**包访问权限**的方法 

     ```java
   void addEntry(int hash, K key, V value, int bucketIndex) {
         // 获取指定 bucketIndex 索引处的 Entry
         Entry<K,V> e = table[bucketIndex];
         // 将新创建的 Entry 放入 bucketIndex 索引处，并让新的 Entry 指向原来的 Entry
         table[bucketIndex] = new Entry<K,V>(hash, key, value, e);
         // 如果 Map 中的 key-value 对的数量超过了极限
         if (size++ >= threshold)
             // 把 table 对象的长度扩充到原来的 2 倍。
             resize(2 * table.length);
     }
     ```

     当系统决定存储 HashMap 中的 key-value 对时，完全没有考虑 Entry 中的 value，仅仅只是根据 key 来计算并决定每个 Entry 的存储位置。我们完全可以把 Map 集合中的 value 当成 key 的附属，当系统决定了 key 的存储位置之后， value 随之保存在那里即可。 

     hash(int h)方法根据 key 的 hashCode 重新计算一次散列。此算法加入了高位计算，防止低位不变，高位变化时，造成的 hash 冲突。

     ```java
   static int hash(int h) {
     	h ^= (h >>> 20) ^ (h >>> 12);
     	return h ^ (h >>> 7) ^ (h >>> 4);
     }
     ```

     我们可以看到在 HashMap 中要找到某个元素，需要根据 key 的 hash 值来求得对应数组中的位置。如何计算这个位置就是 hash 算法。前面说过 HashMap 的数据结构是数组和链表的结合，所以我们当然希望这个 HashMap 里面的 元素位置尽量的分布均匀些，尽量使得每个位置上的元素数量只有一个，那么当我们用 hash 算法求得这个位置的时候，马上就可以知道对应位置的元素就是我们要的，而不用再去遍历链表，这样就大大优化了查询的效率。

     **对于任意给定的对象，只要它的 hashCode() 返回值相同，那么程序调用 hash(int h) 方法所计算得到的 hash 码值总是相同的。**我们首先想到的就是把 hash 值对数组长度取模运算，这样一来， **元素的分布相对来说是比较均匀的**。但是， “模”运算的消耗还是比较大的，在 HashMap 中是这样做的：调用indexFor(int h, int length) 方法来计算该对象应该保存在 table 数组的哪个索引处。

     ```java
   static int indexFor(int h, int length) {
         return h & (length-1);
     }
     ```

      这个方法非常巧妙，它通过 h & (table.length -1) 来得到该对象的保存位，而 HashMap底层数组的长度总是 2 的 n 次方，这是 HashMap 在速度上的优化。

     ```java
   int capacity = 1;
     	while (capacity < initialCapacity)
     		capacity <<= 1;
     ```

     这段代码保证初始化时 HashMap 的容量总是 2 的 n 次方，即底层数组的长度总是为 2的 n 次方。当 length 总是 2 的 n 次方时， h& (length-1)运算等价于对 length 取模，也就是h%length，但是&比%具有更高的效率。

     **当数组长度为 2 的 n 次幂的时候，不同的 key 算得得 index相同的几率较小，那么数据在数组上分布就比较均匀，也就是说碰撞的几率小，相对的，查询的时候就不用遍历某个位置上的链表，这样查询效率也就较高了。** 

     根据上面 put 方法的源代码可以看出，当程序试图将一个 key-value 对放入 HashMap中时，程序首先根据该 key 的 hashCode() 返回值决定该 Entry 的存储位置：

     如果两个 Entry 的 key 的 hashCode() 返回值相同，那它们的存储位置相同。

     如果这两个 Entry 的 key 通过 equals 比较返回 true，新添加 Entry 的 value 将覆盖集合中原有 Entry 的 value，但 key 不会覆盖。

     如果这两个 Entry 的 key 通过 equals 比较返回 false，新添加的 Entry 将与集合中原有 Entry 形成 Entry 链，而且新添加的 Entry 位于 Entry 链的头部——具体说明继续看 addEntry() 方法的说明。 

   - 读取

     ```java
   public V get(Object key) {
         if (key == null)
             return getForNullKey();
         int hash = hash(key.hashCode());
         for (Entry<K,V> e = table[indexFor(hash, table.length)];
              e != null;
              e = e.next) {
             Object k;
             if (e.hash == hash && ((k = e.key) == key || key.equals(k)))
                 return e.value;
         }
         return null;
     }
     ```

     **从 HashMap 中 get 元素时，首先计算 key 的 hashCode，找到数组中对应位置的某一元素，然后通过 key 的 equals 方法在对应位置的链表中找到需要的元素。** 

     归纳起来简单地说， HashMap 在底层将 key-value 当成一个整体进行处理，这个整体就是一个 Entry 对象。 

     HashMap 底层采用一个 Entry[] 数组来保存所有的 key-value 对，当需要存储一个 Entry 对象时，会根据 hash 算法来决定其在数组中的存储位置，在根据equals 方法决定其在该数组位置上的链表中的存储位置；

     当需要取出一个 Entry 时，也会根据 hash 算法找到其在数组中的存储位置，再根据 equals 方法从该位置上的链表中取出该 Entry。 

4. HashMap 的 resize（ rehash）

   - 当 HashMap 中的元素越来越多的时候， hash 冲突的几率也就越来越高，因为数组的长度是固定的。所以为了提高查询的效率，就要对 HashMap 的数组进行扩容，数组扩容这个操作也会出现在 ArrayList 中，这是一个常用的操作，而在 HashMap 数组扩容之后，**最消耗性能的点就出现了：原数组中的数据必须重新计算其在新数组中的位置，并放进去，这就是 resize。**
 - **那么 HashMap 什么时候进行扩容呢？**
     - 当 HashMap 中的元素个数**超过**数组大小\*loadFactor 时，就会进行数组扩容， loadFactor 的默认值为 0.75，这是一个折中的取值。也就是说，默认情况下，数组大小为 16，那么当 HashMap 中元素个数超过 16\*0.75=12的时候，就把数组的大小扩展为 2*16=32，即扩大一倍，然后重新计算每个元素在数组中
       的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知 HashMap 中元素的个
       数，那么预设元素的个数能够有效的提高 HashMap 的性能。

5. HashMap 的性能参数 

  - HashMap 包含如下几个构造器：

    - HashMap()：构建一个初始容量为 16，负载因子为 0.75 的 HashMap。
  - HashMap(int initialCapacity)：构建一个初始容量为 initialCapacity，负载因子为 0.75 的 HashMap。
    - HashMap(int initialCapacity, float loadFactor)：以指定初始容量、指定的负载因子创建一个 HashMap。
    - HashMap 的基础构造器 HashMap(int initialCapacity, float loadFactor)带有两个参数，它们是初始容量 initialCapacity 和加载因子 loadFactor。
    - initialCapacity： HashMap 的最大容量，即为底层数组的长度。
    - loadFactor：负载因子 loadFactor 定义为：散列表的实际元素数目(n)/ 散列表的容量(m)。
  - 负载因子衡量的是一个散列表的空间的使用程度，负载因子越大表示散列表的装填程度越高，反之愈小。对于使用链表法的散列表来说，查找一个元素的平均时间是 O(1+a)，因此 如果负载因子越大，对空间的利用更充分，然而后果是查找效率的降低；如果负载因子太小，那么散列表的数据将过于稀疏，对空间造成严重浪费。

  ```java
    threshold = (int)(capacity * loadFactor);
  ```

    结合负载因子的定义公式可知， threshold 就是在此 loadFactor 和 capacity 对应下允许的最大元素数目，超过这个数目就重新 resize，以降低实际的负载因子。默认的的负载因子0.75是对空间和时间效率的一个平衡选择。当容量超出此最大容量时， resize后的HashMap容量是容量的两倍： 

  ```java
    if (size++ >= threshold)
  	resize(2 * table.length);
  ```

6.  Fail-Fast 机制 

   - 我们知道 java.util.HashMap 不是线程安全的，因此如果在使用迭代器的过程中有其他线程修改了 map，那么将抛出 ConcurrentModificationException，这就是所谓 fail-fast策略。 

   - 这一策略在源码中的实现是通过 **modCount 域**， modCount 顾名思义就是**修改次数**，对HashMap 内容的修改都将增加这个值，那么在**迭代器初始化过程中会将这个值赋给迭代器的 expectedModCount**。 

      ```java
      HashIterator() {
          expectedModCount = modCount;
          if (size > 0) { // advance to first entry
              Entry[] t = table;
              while (index < t.length && (next = t[index++]) == null);
          }
      }
      ```

      在迭代过程中，判断 modCount 跟 expectedModCount 是否相等，如果不相等就表示已经有其他线程修改了 Map :注意到 modCount 声明为 volatile，保证线程之间修改的可见性 。

      ```java
      final Entry<K,V> nextEntry() {
          if (modCount != expectedModCount)
              throw new ConcurrentModificationException();
      ```

      在 HashMap 的 API 中指出：

    ​		由所有 HashMap 类的“collection 视图方法”所返回的迭代器都是快速失败的：在迭代器创建之后，如果从结构上对映射进行修改，除非通过迭代器本身的 remove 方法，其他任何时间任何方式的修改，迭代器都将抛出 ConcurrentModificationException。因此，面对并发的修改，迭代器很快就会完全失败，而不冒在将来不确定的时间发生任意不确定行为的风险。
      ​		注意，迭代器的快速失败行为不能得到保证，一般来说，存在非同步的并发修改时，不可能作出任何坚决的保证。快速失败迭代器尽最大努力抛出 ConcurrentModificationException。因此，编写依赖于此异常的程序的做法是错误的，正确做法是：**迭代器的快速失败行为应该仅用于检测程序错误。** 

##### HashSet的实现原理

1. HashSet 概述 

   - HashSet 实现 Set 接口，由哈希表（实际上是一个 HashMap 实例）支持。它不保证 set 的迭代顺序；特别是它不保证该顺序恒久不变。此类允许使用 null 元素。

2. HashSet的实现

   -  对于 HashSet 而言，它是基于 HashMap 实现的， HashSet 底层使用 HashMap 来保存所有元素，因此 HashSet 的实现比较简单，相关 HashSet 的操作，基本上都是直接**调用底层 HashMap** 的相关方法来完成。

   ```java
   public class HashSet<E> extends AbstractSet<E>implements Set<E>,Cloneable,java.io.Serializable{
         static final long serialVersionUID = -5024744406713321676L;
         
         // 底层使用 HashMap 来保存 HashSet 中所有元素。
         private transient HashMap<E,Object> map;
     
         // 定义一个虚拟的 Object 对象作为 HashMap 的 value，将此对象定义为 static final。
         private static final Object PRESENT = new Object();
         /**
         * 默认的无参构造器，构造一个空的 HashSet。
         *
         * 实际底层会初始化一个空的 HashMap，并使用默认初始容量为 16 和加载因子 0.75。
         */
         public HashSet() {
             map = new HashMap<E,Object>();
         }
       
       /**
       * 构造一个包含指定 collection 中的元素的新 set。
       *
       * 实际底层使用默认的加载因子 0.75 和足以包含指定
       * collection 中所有元素的初始容量来创建一个 HashMap。
       * @param c 其中的元素将存放在此 set 中的 collection。
       */
       public HashSet(Collection<? extends E> c) {
           map = new HashMap<E,Object>(Math.max((int) (c.size()/.75f) + 1, 16));
           addAll(c);
       }
       
       /**
       * 以指定的 initialCapacity 和 loadFactor 构造一个空的 HashSet。
       *
       * 实际底层以相应的参数构造一个空的 HashMap。
       * @param initialCapacity 初始容量。
       * @param loadFactor 加载因子。
       */
       public HashSet(int initialCapacity, float loadFactor) {
           map = new HashMap<E,Object>(initialCapacity, loadFactor);
       }
       
       /**
       * 以指定的 initialCapacity 构造一个空的 HashSet。
       *
       * 实际底层以相应的参数及加载因子 loadFactor 为 0.75 构造一个空的 HashMap。
       * @param initialCapacity 初始容量。
       */
       public HashSet(int initialCapacity) {
           map = new HashMap<E,Object>(initialCapacity);
       }
       
       /**
       * 以指定的 initialCapacity 和 loadFactor 构造一个新的空链接哈希集合。
       * 此构造函数为包访问权限，不对外公开，实际只是是对 LinkedHashSet 的支持。
       *
       * 实际底层会以指定的参数构造一个空 LinkedHashMap 实例来实现。
       * @param initialCapacity 初始容量。
       * @param loadFactor 加载因子。
       * @param dummy 标记。
       */
       HashSet(int initialCapacity, float loadFactor, boolean dummy) {
           map = new LinkedHashMap<E,Object>(initialCapacity, loadFactor);
       }
         
       /**
       * 返回对此 set 中元素进行迭代的迭代器。返回元素的顺序并不是特定的。
       *
       * 底层实际调用底层 HashMap 的 keySet 来返回所有的 key。
       * 可见 HashSet 中的元素，只是存放在了底层 HashMap 的 key 上，
       * value 使用一个 static final 的 Object 对象标识。
       * @return 对此 set 中元素进行迭代的 Iterator。
       */
       public Iterator<E> iterator() {
           return map.keySet().iterator();
       }
         
       /**
       * 返回此 set 中的元素的数量（ set 的容量）。
       *
       * 底层实际调用 HashMap 的 size()方法返回 Entry 的数量，就得到该 Set 中元素的个数。
       * @return 此 set 中的元素的数量（ set 的容量）。
       */
       public int size() {
           return map.size();
       }
         
       /**
       * 如果此 set 不包含任何元素，则返回 true。
       *
       * 底层实际调用 HashMap 的 isEmpty()判断该 HashSet 是否为空。
       * @return 如果此 set 不包含任何元素，则返回 true。
       */
       public boolean isEmpty() {
           return map.isEmpty();
       }
        
       /**
       * 如果此 set 包含指定元素，则返回 true。
       * 更确切地讲，当且仅当此 set 包含一个满足(o==null ? e==null : o.equals(e))
       * 的 e 元素时，返回 true。
       *
       * 底层实际调用 HashMap 的 containsKey 判断是否包含指定 key。
       * @param o 在此 set 中的存在已得到测试的元素。
       * @return 如果此 set 包含指定元素，则返回 true。
       */
       public boolean contains(Object o) {
           return map.containsKey(o);
       }
        
       /**
       * 如果此 set 中尚未包含指定元素，则添加指定元素。
       * 更确切地讲，如果此 set 没有包含满足(e==null ? e2==null : e.equals(e2))
       * 的元素 e2，则向此 set 添加指定的元素 e。
       * 如果此 set 已包含该元素，则该调用不更改 set 并返回 false。
       *
       * 底层实际将将该元素作为 key 放入 HashMap。
       * 由于 HashMap 的 put()方法添加 key-value 对时，当新放入 HashMap 的 Entry 中 key
       * 与集合中原有 Entry 的 key 相同（ hashCode()返回值相等，通过 equals 比较也返回 true），
       * 新添加的 Entry 的 value 会将覆盖原来 Entry 的 value，但 key 不会有任何改变，
       * 因此如果向 HashSet 中添加一个已经存在的元素时，新添加的集合元素将不会被放入 HashMap 中，
       * 原来的元素也不会有任何改变，这也就满足了 Set 中元素不重复的特性。
       * @param e 将添加到此 set 中的元素。
       * @return 如果此 set 尚未包含指定元素，则返回 true。
       */
       public boolean add(E e) {
           return map.put(e, PRESENT)==null;
       }
        
       /**
       * 如果指定元素存在于此 set 中，则将其移除。
       * 更确切地讲，如果此 set 包含一个满足(o==null ? e==null : o.equals(e))的元素e，
       * 则将其移除。如果此 set 已包含该元素，则返回 true
       * （或者：如果此 set 因调用而发生更改，则返回 true）。（一旦调用返回，则此 set 不再包含该元素）。
       *
       * 底层实际调用 HashMap 的 remove 方法删除指定 Entry。
       * @param o 如果存在于此 set 中则需要将其移除的对象。
       * @return 如果 set 包含指定元素，则返回 true。
       */
       public boolean remove(Object o) {
           return map.remove(o)==PRESENT;
       }
       
       /**
       * 从此 set 中移除所有元素。此调用返回后，该 set 将为空。
       *
       * 底层实际调用 HashMap 的 clear 方法清空 Entry 中所有元素。
       */
       public void clear() {
           map.clear();
       }
         
       /**
       * 返回此 HashSet 实例的浅表副本：并没有复制这些元素本身。
       *
       * 底层实际调用 HashMap 的 clone()方法，获取 HashMap 的浅表副本，并设置到 HashSet 中。
       */
       public Object clone() {
           try {
               HashSet<E> newSet = (HashSet<E>) super.clone();
               newSet.map = (HashMap<E, Object>) map.clone();
               return newSet;
             } catch (CloneNotSupportedException e) {
               throw new InternalError();
             }
         }
     }
   ```

3. 相关说明 

   - 对于 HashSet 中保存的对象，请注意正确重写其 equals 和 hashCode 方法，以保证放入的对象的唯一性。 

##### ArrayList的实现原理

1. ArrayList概述

   - ArrayList是List接口的可变数组的实现。实现了所有可选列表操作，并允许包括null在内的所有元素。除了实现List 接口外，此类还提供一些方法来操作内部用来存储列表的数组的大小。
   - 每个ArrayList实例都有一个容量，该容量是指用来存储列表元素的数组的大小。它总是至少等于列表的大小。随着向ArrayList中不断添加元素，其容量也自动增长。**自动增长会带来数据向新数组的重新拷贝**，因此，如果可预知数据量的多少，可在构造ArrayList时指定其容量。在添加大量元素前，应用程序也可以使用ensureCapacity操作来增加ArrayList实例的容量，这可以减少递增式再分配的数量。
   - 注意，此实现不是同步的。如果多个线程同时访问一个ArrayList实例，而其中至少一个线程从结构上修改了列表，那么它必须保持外部同步。
   - **相对比的，Vector是线程安全的，其中涉及线程安全的方法皆被同步操作了。**

2. ArrayList的实现

   - 底层使用数组实现

     ```java
     private transient Object[] elementData;
     ```

   - 构造器

     - ArrayList 提供了三种方式的构造器，

       - 可以构造一个默认初始容量为 10 的空列表、
       - 构造一个指定初始容量的空列表
       - 以及构造一个包含指定 collection 的元素的列表，这些元素按照该 collection 的迭代器返回它们的顺序排列的 

       ```java
       //构造一个默认初始容量为 10 的空列表
       public ArrayList() {
        	this(10);
       }
       
       //构造指定容量的空列表
       public ArrayList(int initialCapacity) {
           super();
           if (initialCapacity < 0)
               throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
           this.elementData = new Object[initialCapacity];
        }
       
       public ArrayList(Collection<? extends E> c) {
           elementData = c.toArray();
           size = elementData.length;
           // c.toArray might (incorrectly) not return Object[] (see 6260652)
           if (elementData.getClass() != Object[].class)
               elementData = Arrays.copyOf(elementData, size, Object[].class);
       }
       ```

   - 存储

     ```java
     // 用指定的元素替代此列表中指定位置上的元素，并返回以前位于该位置上的元素。
     public E set(int index, E element) {
         RangeCheck(index);
         
         E oldValue = (E) elementData[index];
         elementData[index] = element;
         return oldValue;
     }
     ```

     ```java
     // 将指定的元素添加到此列表的尾部。
     public boolean add(E e) {
         ensureCapacity(size + 1);
         elementData[size++] = e;
         return true;
     }
     ```

     ```java
     // 将指定的元素插入此列表中的指定位置。
     // 如果当前位置有元素，则向右移动当前位于该位置的元素以及所有后续元素（将其索引加 1）。
     public void add(int index, E element) {
         if (index > size || index < 0)
             throw new IndexOutOfBoundsException("Index: "+index+", Size: "+size);
         // 如果数组长度不足，将进行扩容。
         ensureCapacity(size+1); // Increments modCount!!
         // 将 elementData 中从 Index 位置开始、长度为 size-index 的元素，
         // 拷贝到从下标为 index+1 位置开始的新的 elementData 数组中。
         // 即将当前位于该位置的元素以及所有后续元素右移一个位置。
         System.arraycopy(elementData, index, elementData, index + 1, size - index);
         elementData[index] = element;
         size++;
     }
     ```

     ```java
     // 按照指定 collection 的迭代器所返回的元素顺序，将该 collection 中的所有元素添加到此列表的尾部。
     public boolean addAll(Collection<? extends E> c) {
         Object[] a = c.toArray();
         int numNew = a.length;
         ensureCapacity(size + numNew); // Increments modCount
         System.arraycopy(a, 0, elementData, size, numNew);
         size += numNew;
         return numNew != 0;
     }
     ```

     ```java
     // 从指定的位置开始，将指定 collection 中的所有元素插入到此列表中。
     public boolean addAll(int index, Collection<? extends E> c) {
         if (index > size || index < 0)
             throw new IndexOutOfBoundsException("Index: " + index + ", Size: " + size);
     
         Object[] a = c.toArray();
         int numNew = a.length;
         ensureCapacity(size + numNew); // Increments modCount
         int numMoved = size - index;
         if (numMoved > 0)
             System.arraycopy(elementData, index, elementData, index + numNew, numMoved);
         
         System.arraycopy(a, 0, elementData, index, numNew);
         size += numNew;
         return numNew != 0;
     }
     ```

   - 读取

     ```java
     // 返回此列表中指定位置上的元素。
     public E get(int index) {
     	RangeCheck(index);
     
     	return (E) elementData[index];
     }
     ```

   - 删除

     ```java
     // 移除此列表中指定位置上的元素。
     public E remove(int index) {
     	RangeCheck(index);
     
         modCount++;
     	E oldValue = (E) elementData[index];
         
         int numMoved = size - index - 1;
         if (numMoved > 0)
             System.arraycopy(elementData, index+1, elementData, index, numMoved);
     
         elementData[--size] = null; // Let gc do its work
     
         return oldValue;
     }
     ```

     ```java
     // 移除此列表中首次出现的指定元素（如果存在）。这是应为ArrayList中允许存放重复的元素。
     public boolean remove(Object o) {
         // 由于 ArrayList 中允许存放 null，因此下面通过两种情况来分别处理。
         if (o == null) {
             for (int index = 0; index < size; index++)
                 if (elementData[index] == null) {
                     // 类似 remove(int index)，移除列表中指定位置上的元素。
                     fastRemove(index);
                     return true;
                 }
     } else {
             for (int index = 0; index < size; index++)
                 if (o.equals(elementData[index])) {
                     fastRemove(index);
                     return true;
                 }
         }
         return false;
     }
     ```

   - 注意：从数组中移除元素的操作，也会导致被移除的元素以后的所有元素的向左移动一个位置。 

   - 调整数组容量

     - 从上面介绍的向 ArrayList 中存储元素的代码中，我们看到，每当向数组中添加元素时，
       都要去检查添加后元素的个数是否会超出当前数组的长度，如果超出，数组将会进行扩容，
       以满足添加数据的需求。**数组扩容通过一个公开的方法 ensureCapacity(int minCapacity)来**
       **实现**。在实际添加大量元素前，我也可以**使用 ensureCapacity 来手动增加 ArrayList 实例的*
       容量，以减少递增式再分配的数量。**

       ```java
       public void ensureCapacity(int minCapacity) {
           modCount++;
           int oldCapacity = elementData.length;
           if (minCapacity > oldCapacity) {
               Object oldData[] = elementData;
               int newCapacity = (oldCapacity * 3)/2 + 1;
               //计算出来的长度，小于要扩容的长度
               if (newCapacity < minCapacity)
                   newCapacity = minCapacity;
               // minCapacity is usually close to size, so this is a win:
               elementData = Arrays.copyOf(elementData, newCapacity);
           }
       }
       ```

       - 从上述代码中可以看出，数组进行扩容时，会将老数组中的元素重新拷贝一份到新的
         数组中，每次数组容量的增长大约是其原容量的 **1.5 倍**。这种操作的代价是很高的，因此在
         实际使用时，我们应该尽量避免数组容量的扩张。当我们可预知要保存的元素的多少时，
         要在构造 ArrayList 实例时，就指定其容量，以避免数组扩容的发生。或者**根据实际需求，**
         **通过调用 ensureCapacity 方法来手动增加 ArrayList 实例的容量**。 

     - ArrayList 还给我们提供了**将底层数组的容量调整为当前列表保存的实际元素的大小的功能**。它可以通过 trimToSize 方法来实现。 

       ```java
       public void trimToSize() {
           modCount++;
           int oldCapacity = elementData.length;
           if (size < oldCapacity) {
               elementData = Arrays.copyOf(elementData, size);
           }
       }
       ```

   - Fail-Fast 机制 

     - ArrayList 也采用了快速失败的机制，通过记录 modCount 参数来实现。在面对并发的
       修改时，迭代器很快就会完全失败，而不是冒着在将来某个不确定时间发生任意不确定行为
       的风险。

##### LinkedHashMap的实现原理

1. LinkedHashMap 概述

   - LinkedHashMap 是 Map 接口的哈希表和链接列表实现，具有可预知的迭代顺序。此实现提供所有可选的映射操作，**并允许使用 null 值和 null 键**。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。
   - LinkedHashMap 实现与 HashMap 的不同之处在于，后者维护着一个运行于所有条目的**双重链接列表**。此链接列表定义了迭代顺序，该迭代顺序**可以是插入顺序或者是访问顺序**。
   - **注意，此实现不是同步的。如果多个线程同时访问链接的哈希映射，而其中至少一个线程从结构上修改了该映射，则它必须保持外部同步。** 

2. LinkedHashMap 的实现 

   - 对于 LinkedHashMap 而言，它继承于 HashMap、底层使用**哈希表与双向链表**来保存所有元素。 其基本操作与父类 HashMap 相似，它通过**重写父类相关的方法**，来实现自己的链接列表特性。 

   - Entry元素

     - LinkedHashMap 采用的 **hash 算法和 HashMap 相同**，但是它重新定义了数组中保存的元素 Entry，该 Entry 除了保存**当前对象的引用外**，还保存了**其上一个元素 before 和下一个元素 after 的引用**，从而在哈希表的基础上又构成了**双向链接列表**。 

       ```java
       /**
       * 双向链表的表头元素。
       */
       private transient Entry<K,V> header;
       
       /**
       * LinkedHashMap 的 Entry 元素。
       * 继承 HashMap 的 Entry 元素，又保存了其上一个元素 before 和下一个元素 after 的引用。
       */
       private static class Entry<K,V> extends HashMap.Entry<K,V> {
           Entry<K,V> before, after;
           //……
       }
       ```

   - 初始化

     - 通过源代码可以看出，在 LinkedHashMap 的构造器中，实际调用了父类 HashMap 的相关构造器来构造一个底层存放的 table 数组。

       ```java
       public LinkedHashMap(int initialCapacity, float loadFactor) {
           super(initialCapacity, loadFactor);
           accessOrder = false;
       }
       ```

     - 构造方法

       ```java
       public HashMap(int initialCapacity, float loadFactor) {
           if (initialCapacity < 0)
               throw new IllegalArgumentException("Illegal initial capacity: " + initialCapacity);
           if (initialCapacity > MAXIMUM_CAPACITY)
               initialCapacity = MAXIMUM_CAPACITY;
           if (loadFactor <= 0 || Float.isNaN(loadFactor))
               throw new IllegalArgumentException("Illegal load factor: " +loadFactor);
       
           // Find a power of 2 >= initialCapacity
           int capacity = 1;
           while (capacity < initialCapacity)
               //乘以2，保证是2的次方倍数
               capacity <<= 1;
           
           this.loadFactor = loadFactor;
           threshold = (int)(capacity * loadFactor);
           table = new Entry[capacity];
           init();
       }
       ```

     - 我们已经知道 LinkedHashMap 的 Entry 元素继承 HashMap 的 Entry，提供了双向链表的功能。在上述 HashMap 的构造器中，最后会调用 init()方法，进行相关的初始化，这个方法在 HashMap 的实现中并无意义，只是提供给子类实现相关的初始化调用。

     - LinkedHashMap 重写了 init()方法，在调用父类的构造方法完成构造后，进一步实现了对其元素 Entry 的初始化操作。

       ```java
       void init() {
           header = new Entry<K,V>(-1, null, null, null);
           //初始化的时候，三个节点指向同一个地方
           header.before = header.after = header;
       }
       ```

     - 存储

       - LinkedHashMap 并未重写父类 HashMap 的 put 方法，而是重写了父类 HashMap 的put 方法调用的子方法 **void addEntry(int hash, K key, V value, int bucketIndex) 和 void createEntry(int hash, K key, V value, int bucketIndex)**，提供了自己特有的双向链接列表的
         实现。 

         ```java
         void addEntry(int hash, K key, V value, int bucketIndex) {
             // 调用 create 方法，将新元素以双向链表的的形式加入到映射中。
             createEntry(hash, key, value, bucketIndex);
             
             // 删除最近最少使用元素的策略定义
             Entry<K,V> eldest = header.after;
             if (removeEldestEntry(eldest)) {
                 removeEntryForKey(eldest.key);
             } else {
                 if (size >= threshold)
                     resize(2 * table.length);
             }
         }
         ```

         ```java
         void createEntry(int hash, K key, V value, int bucketIndex) {
             HashMap.Entry<K,V> old = table[bucketIndex];
             Entry<K,V> e = new Entry<K,V>(hash, key, value, old);
             table[bucketIndex] = e;
             // 调用元素的 addBefore 方法，将元素加入到哈希、双向链接列表。
             e.addBefore(header);
             size++;
         }
         ```

         ```java
         private void addBefore(Entry<K,V> existingEntry) {
             after = existingEntry;
             before = existingEntry.before;
             before.after = this;
             after.before = this;
         }
         ```

       - 读取

         - LinkedHashMap 重写了父类 HashMap 的 **get 方法**，实际在调用父类 getEntry()方法取得查找的元素后，再判断当排序模式 **accessOrder 为 true 时**，记录访问顺序，将最新访问的元素添加到**双向链表的表头**，并从原来的位置删除。由于的链表的增加、删除操作是常量
           级的，故并不会带来性能的损失。 

           ```java
           public V get(Object key) {
               // 调用父类 HashMap 的 getEntry()方法，取得要查找的元素。
               Entry<K,V> e = (Entry<K,V>)getEntry(key);
               if (e == null)
                   return null;
               // 记录访问顺序。
               e.recordAccess(this); 
               return e.value;
           }
           ```

           ```java
           void recordAccess(HashMap<K,V> m) {
               LinkedHashMap<K,V> lm = (LinkedHashMap<K,V>)m;
               // 如果定义了 LinkedHashMap 的迭代顺序为访问顺序，
               // 则删除以前位置上的元素，并将最新访问的元素添加到链表表头。
               if (lm.accessOrder) {
                   lm.modCount++;
                   remove();
                   //加入到表头，就可以记录好位置了
                   addBefore(lm.header);
               }
           }
           ```

       - 排序模式 

         - LinkedHashMap 定义了排序模式 accessOrder，该属性为 boolean 型变量，**对于访问顺序，为 true；对于插入顺序，则为 false。** 

           ```java
           private final boolean accessOrder;
           ```

         - 一般情况下，不必指定排序模式，其迭代顺序即为默认为插入顺序。看 LinkedHashMap
           的构造方法 

           ```java
           public LinkedHashMap(int initialCapacity, float loadFactor) {
               super(initialCapacity, loadFactor);
               accessOrder = false;
           }
           ```

         - 这些构造方法都会默认指定排序模式为插入顺序。如果你想构造一个 LinkedHashMap，并打算按从近期访问最少到近期访问最多的顺序（即访问顺序）来保存元素，那么请使用下
           面的构造方法构造 LinkedHashMap 

           ```java
           public LinkedHashMap(int initialCapacity,
                                float loadFactor,
                                boolean accessOrder) {
               super(initialCapacity, loadFactor);
               this.accessOrder = accessOrder;
           }
           ```

         - 该哈希映射的迭代顺序就是最后访问其条目的顺序，这种映射很适合构建 LRU 缓存。
           LinkedHashMap 提供了 **removeEldestEntry(Map.Entry<K,V> eldest)方法**，**在将新条目插入到映射后， put 和 putAll 将调用此方法。该方法可以提供在每次添加新条目时移除最旧条目的实现程序，默认返回 false，这样，此映射的行为将类似于正常映射，即永远不能移除最旧的元素。** 

           ```java
           protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
               return false;
           }
           ```

         - 此方法通常不以任何方式修改映射，相反允许映射在其返回值的指引下进行自我修改。
           如果用此映射构建 LRU 缓存，则非常方便，它允许映射通过删除旧条目来减少内存损耗。

         - 例如：重写此方法，维持此映射只保存 100 个条目的稳定状态，在每次添加新条目时删
           除最旧的条目。  

           ```java
           private static final int MAX_ENTRIES = 100;
           protected boolean removeEldestEntry(Map.Entry eldest) {
               return size() > MAX_ENTRIES;
           }
           ```

##### LinkedHahSet的实现原理

1. LinkedHashSet 概述 

   - LinkedHashSet 是具有可预知迭代顺序的 Set 接口的哈希表和链接列表实现。此实现与HashSet 的不同之处在于，后者维护着一个运行于**所有条目的双重链接列表**。此链接列表定义了迭代顺序，该迭代顺序可为**插入顺序或是访问顺序。**
   - 注意，此实现不是同步的。如果多个线程同时访问链接的哈希 Set，而其中至少一个线程修改了该 Set，则它必须保持外部同步。 

2. LinkedHashSet 的实现

   - 对于 LinkedHashSet 而言，它继承与 HashSet、又基于 **LinkedHashMap 来实现的**。 

   - LinkedHashSet 底层使用 LinkedHashMap 来保存所有元素，它继承与 HashSet，其所有的方法操作上又与 HashSet 相同，因此 LinkedHashSet 的实现上非常简单，只提供了四个构造方法，并通过传递一个标识参数，调用父类的构造器，底层构造一个 LinkedHashMap来实现，在相关操作上与父类 HashSet 的操作相同，直接调用父类 HashSet 的方法即可。

     ```java
     public class LinkedHashSet<E> extends HashSet<E> implements Set<E>, Cloneable, java.io.Serializable {
         
         private static final long serialVersionUID = -2851667679971038690L;
         
         /**
         * 构造一个带有指定初始容量和加载因子的新空链接哈希 set。
         *
         * 底层会调用父类的构造方法，构造一个有指定初始容量和加载因子的 LinkedHashMap 实例。
         * @param initialCapacity 初始容量。
         * @param loadFactor 加载因子。
         */
         public LinkedHashSet(int initialCapacity, float loadFactor) {
             //访问的顺序
             super(initialCapacity, loadFactor, true);
         }
         
         /**
         * 构造一个带指定初始容量和默认加载因子 0.75 的新空链接哈希 set。
         *
         * 底层会调用父类的构造方法，构造一个带指定初始容量和默认加载因子 0.75 的 LinkedHashMap 实例。
         * @param initialCapacity 初始容量。
         */
         public LinkedHashSet(int initialCapacity) {
             //访问的顺序
             super(initialCapacity, .75f, true);
         }
         
         /**
         * 构造一个带默认初始容量 16 和加载因子 0.75 的新空链接哈希 set。
         *
         * 底层会调用父类的构造方法，构造一个带默认初始容量 16 和加载因子 0.75 的 LinkedHashMap 实例。
         */
         public LinkedHashSet() {
             //访问的顺序
             super(16, .75f, true);
         }
         
         /**
         * 构造一个与指定 collection 中的元素相同的新链接哈希 set。
         *
         * 底层会调用父类的构造方法，构造一个足以包含指定 collection
         * 中所有元素的初始容量和加载因子为 0.75 的 LinkedHashMap 实例。
         * @param c 其中的元素将存放在此 set 中的 collection。
         */
         public LinkedHashSet(Collection<? extends E> c) {
             super(Math.max(2*c.size(), 11), .75f, true);
             addAll(c);
         }
     }
     ```

   - 在父类 HashSet 中，专为 LinkedHashSet 提供的构造方法如下，该方法为包访问权限，并未对外公开。

     ```java
     /**
     * 以指定的 initialCapacity 和 loadFactor 构造一个新的空链接哈希集合。
     * 此构造函数为包访问权限，不对外公开，实际只是是对 LinkedHashSet 的支持。
     *
     * 实际底层会以指定的参数构造一个空 LinkedHashMap 实例来实现。
     * @param initialCapacity 初始容量。
     * @param loadFactor 加载因子。
     * @param dummy 标记。
     */
     HashSet(int initialCapacity, float loadFactor, boolean dummy) {
         map = new LinkedHashMap<E,Object>(initialCapacity, loadFactor);
     }
     ```

   - 由上述源代码可见， LinkedHashSet 通过继承 HashSet，底层使用 LinkedHashMap，以很简单明了的方式来实现了其自身的所有功能。 

#### JDK8

##### ArrayList的实现原理

1. 概述 

   - 我们可以得知 ArrayList 是一个动态数组，实现了 List 接口以及 list相关的所有方法，它允许所有元素的插入，包括 null。另外， ArrayList 和 Vector 除了线程不同步之外，大致相等。 

2. 属性

   ```java
   //默认容量的大小
   private static final int DEFAULT_CAPACITY = 10;
   
   //空数组常量
   private static final Object[] EMPTY_ELEMENTDATA = {};
   
   //默认的空数组常量
   private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
   
   //存放元素的数组，从这可以发现 ArrayList 的底层实现就是一个 Object数组
   transient Object[] elementData;
   
   //数组中包含的元素个数
   private int size;
   
   //数组的最大上限
   private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
   ```

   - ArrayList 的属性非常少，就只有这些。其中最重要的莫过于 elementData 了， ArrayList所有的方法都是建立在 elementData 之上。 

3. 方法

   1. 构造方法

     ```java
     public ArrayList(int initialCapacity) {
       if (initialCapacity > 0) {
           this.elementData = new Object[initialCapacity];
       } else if (initialCapacity == 0) {
           this.elementData = EMPTY_ELEMENTDATA;
       } else {
           throw new IllegalArgumentException("Illegal Capacity: "+initialCapacity);
       }
     }
     
     public ArrayList() {
         this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
     }
     ```

     - 从构造方法中我们可以看见，默认情况下， elementData 是一个**大小为 0 的空数组**，当我们指定了初始大小的时候， elementData 的初始大小就变成了我们所指定的初始大小了。 
   
   2. get方法
   
      ```java
      public E get(int index) {
          rangeCheck(index);
          return elementData(index);
      }
      
      private void rangeCheck(int index) {
          if (index >= size)
              throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
      }
      
      E elementData(int index) {
          return (E) elementData[index];
      }
      ```
   
      - 因为 ArrayList 是采用数组结构来存储的，所以它的 get 方法非常简单，先是判断一下有没有越界，之后就可以直接通过数组下标来获取元素了，所以 get 的时间复杂度是 O(1)。 
   
   3. add方法
   
      ```java
      public boolean add(E e) {
          ensureCapacityInternal(size + 1); // Increments modCount!!
          elementData[size++] = e;
          return true;
      }
      
      public void add(int index, E element) {
          rangeCheckForAdd(index);
          ensureCapacityInternal(size + 1); // Increments modCount!!
          
          //调用一个 native 的复制方法，把 index 位置开始的元素都往后挪一位
          System.arraycopy(elementData, index, elementData, index + 1, size - index);
          elementData[index] = element;
          size++;
      }
      
      private void ensureCapacityInternal(int minCapacity) {
          if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
              minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
          }
          ensureExplicitCapacity(minCapacity);
      }
      
      private void ensureExplicitCapacity(int minCapacity) {
          modCount++;
          if (minCapacity - elementData.length > 0)
              grow(minCapacity);
      }
      ```
   
      - ArrayList 的 add 方法也很好理解，在插入元素之前，它会先检查是否需要扩容，然后再把元素添加到数组中最后一个元素的后面。在 ensureCapacityInternal 方法中，我们可以看见，如果当 elementData 为空数组时，它会使用默认的大小去扩容。所以说，通过无参构造方法来创建 ArrayList 时，它的大小其实是为 0 的，只有在使用到的时候，才会通过 grow 方法去**创建一个大小为 10 的数组**。
      - 第一个 add 方法的复杂度为 O(1)，虽然有时候会涉及到扩容的操作，但是扩容的次数是非常少的，所以这一部分的时间可以忽略不计。如果使用的是带指定下标的 add方法，则复杂度为 O(n)，因为涉及到对数组中元素的移动，这一操作是非常耗时的。 
   
   4. set方法
   
      ```java
      public E set(int index, E element) {
          rangeCheck(index);
          E oldValue = elementData(index);
          elementData[index] = element;
          return oldValue;
      }
      ```
   
      - set 方法的作用是把下标为 index 的元素替换成 element，跟 get 非常类似，所以就不在赘述了，时间复杂度度为 O(1)。 
   
   5. remove方法
   
      ```java
      public E remove(int index) {
          rangeCheck(index);
          modCount++;
          E oldValue = elementData(index);
          int numMoved = size - index - 1;
          if (numMoved > 0)
              System.arraycopy(elementData, index+1, elementData,index, numMoved);
          elementData[--size] = null; // clear to let GC do its work
          return oldValue;
      }
      ```
   
      - remove 方法与 add 带指定下标的方法非常类似，也是调用系统的 arraycopy 方法来移动元素，时间复杂度为 O(n)。 
   
   6. grow方法
   
      ```java
      private void grow(int minCapacity) {
          // overflow-conscious code
          int oldCapacity = elementData.length;
          //1.5倍
          int newCapacity = oldCapacity + (oldCapacity >> 1);
          if (newCapacity - minCapacity < 0)
              newCapacity = minCapacity;
          if (newCapacity - MAX_ARRAY_SIZE > 0)
              newCapacity = hugeCapacity(minCapacity);
          // minCapacity is usually close to size, so this is a win:
          elementData = Arrays.copyOf(elementData, newCapacity);
      }
      ```
   
      - grow 方法是在数组进行扩容的时候用到的，从中我们可以看见， ArrayList 每次扩容都是扩 **1.5 倍**，然后调用 Arrays 类的 copyOf 方法，把元素重新拷贝到一个新的数组中去。 
   
   7. size方法
   
      ```java
      public int size() {
          return size;
      }
      ```
   
      - size 方法非常简单，它是直接返回 size 的值，也就是返回数组中元素的个数，时间复杂度为 O(1)。这里要注意一下，返回的**并不是数组的实际大小 .**
   
   8. indexOf 方法和 lastIndexOf 
   
      ```java
      public int indexOf(Object o) {
          if (o == null) {
              for (int i = 0; i < size; i++)
                  if (elementData[i]==null)
                      return i;
          } else {
              for (int i = 0; i < size; i++)
                  if (o.equals(elementData[i]))
                      return i;
          }
          return -1;
      }
      
      public int lastIndexOf(Object o) {
          if (o == null) {
              for (int i = size-1; i >= 0; i--)
                  if (elementData[i]==null)
                      return i;
          } else {
              for (int i = size-1; i >= 0; i--)
                  if (o.equals(elementData[i]))
                      return i;
          }
          return -1;
      }
      ```
   
      - indexOf 方法的作用是返回第一个等于给定元素的值的下标。它是通过遍历比较数组中每个元素的值来查找的，所以它的时间复杂度是 O(n)。
      - lastIndexOf 的原理跟 indexOf 一样，而它仅仅是从后往前找起罢了。 
   
4. Vector

   - Vector 比 ArrayList 多了一个属性 

      ```java
      protected int capacityIncrement;
      ```

   - 这个属性是在扩容的时候用到的，它表示每次扩容只扩 capacityIncrement 个空间就足够了。该属性可以通过构造方法给它赋值。先来看一下构造方法：

      ```java
      public Vector(int initialCapacity, int capacityIncrement){
          super();
          if (initialCapacity < 0)
              throw new IllegalArgumentException("Illegal Capacity: "+initialCapacity);
          this.elementData = new Object[initialCapacity];
          this.capacityIncrement = capacityIncrement;
      }
      
      public Vector(int initialCapacity) {
          this(initialCapacity, 0);
      }
      
      public Vector() {
          this(10);
      }
      ```

   - 从构造方法中，我们可以看出 Vector 的默认大小也是 10，而且它在初始化的时候就已经创建了数组了，这点跟 ArrayList 不一样。 

      ```java
      private void grow(int minCapacity) {
          // overflow-conscious code
          int oldCapacity = elementData.length;
          int newCapacity = oldCapacity + ((capacityIncrement >0) ? capacityIncrement :oldCapacity);
          if (newCapacity - minCapacity < 0)
              newCapacity = minCapacity;
          if (newCapacity - MAX_ARRAY_SIZE > 0)
              newCapacity = hugeCapacity(minCapacity);
          elementData = Arrays.copyOf(elementData, newCapacity);
      }
      ```

   - 从grow 方法中我们可以发现， newCapacity 默认情况下是两倍的 oldCapacity，而当指定了 capacityIncrement 的值之后， newCapacity 变成了oldCapacity+capacityIncrement。 

5. 总结

   1. ArrayList 创建时的大小为 0；当加入第一个元素时，进行第一次扩容时，默认容量大小为 10。(与jdk8之前不一样)
   2. ArrayList 每次扩容都以当前数组大小的 **1.5 倍**去扩容。
   3. Vector 创建时的默认**大小为 10**。 
   4. Vector 每次扩容都以当前数组大小的 2 倍去扩容。当指定了 capacityIncrement 之后，每次扩容仅在原先基础上增加 capacityIncrement 个单位空间。
   5. ArrayList 和 Vector 的 add、 get、 size 方法的复杂度都为 O(1)， remove 方法的复杂度为 O(n)。
   6. ArrayList 是非线程安全的， Vector 是线程安全的。 

##### LinkedList的实现原理

1. 概述 

   - 我们可以得知 LinkedList 是通过一个双向链表来实现的，它允许插入所有元素，包括 null，同时，它是线程不同步的。如果对双向链表这个数据结构很熟悉的话，学习 LinkedList 就没什么难度了。下面是双向链表的结构 

     ![](.\双向链表.JPG)

   - 双向链表每个结点除了数据域之外，还有一个前指针和后指针，分别指向前驱结点和后继结点（如果有前驱/后继的话）。另外，双向链表还有一个 first 指针，指向头节点，和 last 指针，指向尾节点。 

2. 属性

   ```java
   //链表的节点个数
   transient int size = 0;
   
   //指向头节点的指针
   transient Node<E> first;
   
   //指向尾节点的指针
   transient Node<E> last;
   ```

   - LinkedList 的属性非常少，就只有这些。通过这三个属性，其实我们大概也可以猜测出它是怎么实现的了。

3. 方法

   1. 结点结构

      -  Node 是在 LinkedList 里定义的一个静态内部类，它表示链表每个节点的结构，包括一个数据域 item，一个后置指针 next，一个前置指针 prev。 

        ```java
        private static class Node<E> {
            E item;
            Node<E> next;
            Node<E> prev;
            Node(Node<E> prev, E element, Node<E> next) {
                this.item = element;
                this.next = next;
                this.prev = prev;
            }
        }
        ```

   2. 添加元素

      - 对于链表这种数据结构来说，添加元素的操作无非就是在表头/表尾插入元素，又或者在指定位置插入元素。因为 LinkedList 有头指针和尾指针，所以在表头或表尾进行插入元素只需要 **O(1)** 的时间，而在指定位置插入元素则需要先遍历一下链表，所以复杂度为 **O(n)**。 

      - 在表头添加元素的过程如下 ：

        ![](.\链表添加元素.JPG)

      - 当向表头插入一个节点时，很显然当前节点的前驱一定为 null，而后继结点是 first指针指向的节点，当然还要修改 first 指针指向新的头节点。除此之外，原来的头节点变成了第二个节点，所以还要修改原来头节点的前驱指针，使它指向表头节点，源码的实现如下：

        ```java
        private void linkFirst(E e) {
            final Node<E> f = first;
            //当前节点的前驱指向 null，后继指针原来的头节点
            final Node<E> newNode = new Node<>(null, e, f);
            //头指针指向新的头节点
            first = newNode;
            //如果原来有头节点，则更新原来节点的前驱指针，否则更新尾指针
            if (f == null)
                last = newNode;
            else
                f.prev = newNode;
            size++;
            modCount++;
        }
        ```

      - 在表尾添加元素跟在表头添加元素大同小异，如图所示： 

        ![](.\链表表尾添加元素.JPG)

      - 当向表尾插入一个节点时，很显然当前节点的后继一定为 null，而前驱结点是 last指针指向的节点，然后还要修改 last 指针指向新的尾节点。此外，还要修改原来尾节点的后继指针，使它指向新的尾节点，源码的实现如下： 

        ```java
        void linkLast(E e) {
            final Node<E> l = last;
            //当前节点的前驱指向尾节点，后继指向 null
            final Node<E> newNode = new Node<>(l, e, null);
            //尾指针指向新的尾节点
            last = newNode;
            //如果原来有尾节点，则更新原来节点的后继指针，否则更新头指针
            if (l == null)
                first = newNode;
           else
               l.next = newNode;
            size++;
            modCount++;
        }
        ```

      - 最后，在指定节点之前插入，如图所示： 

        ![](.\在链表指定位置添加元素.JPG)

      - 当向指定节点之前插入一个节点时，当前节点的后继为指定节点，而前驱结点为指定节点的前驱节点。此外，还要修改前驱节点的后继为当前节点，以及后继节点的前驱为当前节点，源码的实现如下： 

        ```java
        void linkBefore(E e, Node<E> succ) {
            // assert succ != null;
            //指定节点的前驱
            final Node<E> pred = succ.prev;
            //当前节点的前驱为指点节点的前驱，后继为指定的节点
            final Node<E> newNode = new Node<>(pred, e, succ);
            //更新指定节点的前驱为当前节点
            succ.prev = newNode;
            //更新前驱节点的后继
            if (pred == null)
                first = newNode;
            else
                pred.next = newNode;
            size++;
            modCount++;
        }
        ```

   3. 删除元素

      - 删除操作与添加操作大同小异，例如删除指定节点的过程如下图所示，需要把当前节点的前驱节点的后继修改为当前节点的后继，以及当前节点的后继结点的前驱修改为当前节点的前驱（是不是很绕？）： 

        ![](.\删除链表节点.JPG)

      - 删除头节点和尾节点跟删除指定节点非常类似 

        ```java
        //删除表头节点，返回表头元素的值
        private E unlinkFirst(Node<E> f) {
            // assert f == first && f != null;
            final E element = f.item;
            final Node<E> next = f.next;
            f.item = null;
            f.next = null; // help GC
            first = next; //头指针指向后一个节点
            if (next == null)
                last = null;
            else
                next.prev = null; //新头节点的前驱为 null
            size--;
            modCount++;
            return element;
        }
        
        //删除表尾节点，返回表尾元素的值
        private E unlinkLast(Node<E> l) {
            // assert l == last && l != null;
            final E element = l.item;
            final Node<E> prev = l.prev;
            l.item = null;
            l.prev = null; // help GC
            last = prev; //尾指针指向前一个节点
            if (prev == null)
                first = null;
            else
                prev.next = null; //新尾节点的后继为 null
            size--;
            modCount++;
            return element;
        }
        
        //删除指定节点，返回指定元素的值
        E unlink(Node<E> x) {
            // assert x != null;
            final E element = x.item;
            final Node<E> next = x.next; //当前节点的后继
            final Node<E> prev = x.prev; //当前节点的前驱
            if (prev == null) {
                first = next;
            } else {
                prev.next = next; //更新前驱节点的后继为当前节点的后继
                x.prev = null;
            }
            if (next == null) {
                last = prev;
            } else {
                next.prev = prev; //更新后继节点的前驱为当前节点的前驱
                x.next = null;
            }
            x.item = null;
            size--;
            modCount++;
            return element;
        }
        ```

   4. 获取元素

      ```java
      //获取表头元素
      public E getFirst() {
          final Node<E> f = first;
          if (f == null)
              throw new NoSuchElementException();
          return f.item;
      }
      
      //获取表尾元素
      public E getLast() {
          final Node<E> l = last;
          if (l == null)
              throw new NoSuchElementException();
          return l.item;
      }
      
      //获取指定下标的元素
      Node<E> node(int index) {
          // assert isElementIndex(index);
          //根据下标是否超过链表长度的一半，来选择从头部开始遍历还是从尾部开始遍历
          if (index < (size >> 1)) {
              Node<E> x = first;
              for (int i = 0; i < index; i++)
                  x = x.next;
              return x;
          } else {
              Node<E> x = last;
              for (int i = size - 1; i > index; i--)
                  x = x.prev;
              return x;
          }
      }
      ```

   5. 常用方法 

      - 前面介绍了链表的添加和删除操作，你会发现那些方法都不是 public 的， LinkedList是在这些基础的方法进行操作的，下面就来看看我们可以调用的方法有哪些。 

        ```java
        //删除表头元素
        public E removeFirst() {
            final Node<E> f = first;
            if (f == null)
                throw new NoSuchElementException();
            return unlinkFirst(f);
        }
        
        //删除表尾元素
        public E removeLast() {
            final Node<E> l = last;
            if (l == null)
                throw new NoSuchElementException();
            return unlinkLast(l);
        }
        
        //插入新的表头节点
        public void addFirst(E e) {
            linkFirst(e);
        }
        
        //插入新的表尾节点
        public void addLast(E e) {
            linkLast(e);
        }
        
        //链表的大小
        public int size() {
            return size;
        }
        
        //添加元素到表尾
        public boolean add(E e) {
            linkLast(e);
            return true;
        }
        
        //删除指定元素
        public boolean remove(Object o) {
            if (o == null) {
                for (Node<E> x = first; x != null; x = x.next) {
                    if (x.item == null) {
                        unlink(x);
                        return true;
                    }
                }
            } else {
                for (Node<E> x = first; x != null; x = x.next) {
                    if (o.equals(x.item)) {
                        unlink(x);
                        return true;
                    }
                }
            }
            return false;
        }
        
        //获取指定下标的元素
        public E get(int index) {
            checkElementIndex(index); //先检查是否越界
            return node(index).item;
        }
        
        //替换指定下标的值
        public E set(int index, E element) {
            checkElementIndex(index);
            Node<E> x = node(index);
            E oldVal = x.item;
            x.item = element;
            return oldVal;
        }
        
        //在指定位置插入节点
        public void add(int index, E element) {
            checkPositionIndex(index);
            if (index == size)
                linkLast(element);
            else
                linkBefore(element, node(index));
        }
        
        //删除指定下标的节点
        public E remove(int index) {
            checkElementIndex(index);
            return unlink(node(index));
        }
        
        //获取表头节点的值，表头为空返回 null
        public E peek() {
            final Node<E> f = first;
            return (f == null) ? null : f.item;
        }
        
        //获取表头节点的值，表头为空抛出异常
        public E element() {
            return getFirst();
        }
        
        //获取表头节点的值，并删除表头节点，表头为空返回 null
        public E poll() {
            final Node<E> f = first;
            return (f == null) ? null : unlinkFirst(f);
        }
        
        //添加元素到表头
        public void push(E e) {
            addFirst(e);
        }
        
        //删除表头元素
        public E pop() {
            return removeFirst();
        }
        ```

4. 总结

   1. LinkedList 的底层结构是一个带头/尾指针的双向链表，可以快速的对头/尾节点进行操作。
   2. 相比数组，链表的特点就是在指定位置插入和删除元素的效率较高，但是查找的效率就不如数组那么高了。

##### HashMap 的实现原理  

1. 概述 

   - 大概就是在说，这个哈希表是基于 Map 接口的实现的，它允许 null 值和null 键，它不是线程同步的，同时也不保证有序。

   - Map 的这种实现方式为 get（取）和 put（存）带来了比较好的性能。但是如果涉及到大量的遍历操作的话，就
     尽量不要把 capacity 设置得太高（或 load factor 设置得太低），否则会严重降低遍历的效率  。

   - 影响 HashMap 性能的两个重要参数： “initial capacity”（初始化容量）和”loadfactor“（负载因子）。简单来说，容量就是哈希表桶的个数，负载因子就是键值对个数与哈希表长度的一个比值，当比值超过负载因子之后， HashMap 就会进行 rehash操作来进行扩容。

   - HashMap 的大致结构如下图所示，其中哈希表是一个数组，我们经常把数组中的每一个节点称为一个桶，哈希表中的每个节点都用来存储一个键值对。在插入元素时，如果发生冲突（即多个键值对映射到同一个桶上）的话，就会通过链表的形式来解决冲突。因为一个桶上可能存在多个键值对， 所以在查找的时候，会先通过 key 的
     哈希值先定位到桶，再遍历桶上的所有键值对，找出 key 相等的键值对，从而来获取 value。

     ![](.\HashMap底层.JPG)  

2. 属性 

   ```java
   //默认的初始容量为 16
   static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
   //最大的容量上限为 2^30
   static final int MAXIMUM_CAPACITY = 1 << 30;
   //默认的负载因子为 0.75
   static final float DEFAULT_LOAD_FACTOR = 0.75f;
   //变成树型结构的临界值为 8
   static final int TREEIFY_THRESHOLD = 8;
   //恢复链式结构的临界值为 6
   static final int UNTREEIFY_THRESHOLD = 6;
   //哈希表
   transient Node<K,V>[] table;
   //哈希表中键值对的个数
   transient int size;
   //哈希表被修改的次数
   transient int modCount;
   //它是通过 capacity*load factor 计算出来的，当 size 到达这个值时，就会进行扩容操作
   int threshold;
   //负载因子
   final float loadFactor;
   //当哈希表的大小超过这个阈值，才会把链式结构转化成树型结构，否则仅采取扩容来尝试减少冲突
   static final int MIN_TREEIFY_CAPACITY = 64;
   ```

   - 下面是 Node 类的定义，它是 HashMap 中的一个静态内部类，哈希表中的每一个节点都是 Node 类型。我们可以看到， Node 类中有 4 个属性，其中除了 key 和value 之外，还有 hash 和 next 两个属性。 hash 是用来存储 key 的哈希值的， next是在构建链表时用来指向后继节点的。 

     ```java
     static class Node<K,V> implements Map.Entry<K,V> {
         final int hash;
         final K key;
         V value;
         Node<K,V> next;
         Node(int hash, K key, V value, Node<K,V> next) {
             this.hash = hash;
             this.key = key;
             this.value = value;
             this.next = next;
         }
         
         public final K getKey() { return key; }
         
         public final V getValue() { return value; }
         
         public final String toString() { return key + "=" + value; }
         
         public final int hashCode() {
             return Objects.hashCode(key) ^ Objects.hashCode(value);
         }
         
         public final V setValue(V newValue) {
             V oldValue = value;
             value = newValue;
             return oldValue;
         }
         
         public final boolean equals(Object o) {
             if (o == this)
                 return true;
             if (o instanceof Map.Entry) {
                 Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                 if (Objects.equals(key, e.getKey()) &&Objects.equals(value, e.getValue()))
                     return true;
             }
             return false;
         }
     }
     ```

3. 方法

   1. get 方法 

      ```java
      //get 方法主要调用的是 getNode 方法，所以重点要看 getNode 方法的实现
      public V get(Object key) {
          Node<K,V> e;
          return (e = getNode(hash(key), key)) == null ? null: e.value;
      }
      
      final Node<K,V> getNode(int hash, Object key) {
          Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
          //如果哈希表不为空 && key 对应的桶上不为空
          if ((tab = table) != null && (n = tab.length) > 0 
              &&(first = tab[(n - 1) & hash]) != null){
              //是否直接命中
              if (first.hash == hash && // always check first node
                  ((k = first.key) == key || (key != null && key.equals(k))))
                  return first;
              //判断是否有后续节点
              if ((e = first.next) != null) {
                  //如果当前的桶是采用红黑树处理冲突，则调用红黑树的 get 方法去获取节点
                  if (first instanceof TreeNode)
                      return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                  //不是红黑树的话，那就是传统的链式结构了，通过循环的方法判断链中是否存在该 key
                  do {
                      if (e.hash == hash &&((k = e.key) == key || (key != null &&key.equals(k))))
                          return e;
                  } while ((e = e.next) != null);
              }
          }
          return null;
      }
      ```

      - 实现步骤大致如下：
        - 通过 hash 值获取该 key 映射到的桶。
        - 桶上的 key 就是要查找的 key，则直接命中。
        - 桶上的 key 不是要查找的 key，则查看后续节点：
          - 如果后续节点是树节点，通过调用树的方法查找该 key。
          - 如果后续节点是链式节点，则通过循环遍历链查找该 key。 

   2. put方法

      ```java
      //put 方法的具体实现也是在 putVal 方法中，所以我们重点看下面的 putVal 方法
      public V put(K key, V value) {
          return putVal(hash(key), key, value, false, true);
      }
      
      final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
          Node<K,V>[] tab; Node<K,V> p; int n, i;
          //如果哈希表为空，则先创建一个哈希表
          if ((tab = table) == null || (n = tab.length) == 0)
              n = (tab = resize()).length;
          //如果当前桶没有碰撞冲突，则直接把键值对插入，完事
          if ((p = tab[i = (n - 1) & hash]) == null)
              tab[i] = newNode(hash, key, value, null);
          else {
              Node<K,V> e; K k;
              //如果桶上节点的 key 与当前 key 重复，那你就是我要找的节点了
              if (p.hash == hash &&((k = p.key) == key || (key != null && key.equals(k))))
                  e = p;
              //如果是采用红黑树的方式处理冲突，则通过红黑树的 putTreeVal 方法去插入这个键值对
              else if (p instanceof TreeNode)
                  e = ((TreeNode<K,V>)p).putTreeVal(this, tab,hash, key, value);
              //否则就是传统的链式结构
              else {
                  //采用循环遍历的方式，判断链中是否有重复的 key
                  for (int binCount = 0; ; ++binCount) {
                      //到了链尾还没找到重复的 key，则说明 HashMap 没有包含该键
                      if ((e = p.next) == null) {
                          //创建一个新节点插入到尾部
                          p.next = newNode(hash, key, value, null);
                          //如果链的长度大于 TREEIFY_THRESHOLD 这个临界值，则把链变为红黑树
                          if (binCount >= TREEIFY_THRESHOLD - 1)// -1 for 1st
                              treeifyBin(tab, hash);
                          break;
                      }
                      //找到了重复的 key
                      if (e.hash == hash &&((k = e.key) == key || (key != null &&key.equals(k))))
                          break;
                      p = e;
                  }
              }
              //这里表示在上面的操作中找到了重复的键，所以这里把该键的值替换为新值
              if (e != null) { // existing mapping for key
                  V oldValue = e.value;
                  if (!onlyIfAbsent || oldValue == null)
                      e.value = value;
                  afterNodeAccess(e);
                  return oldValue;
              }
          }
          ++modCount;
          //判断是否需要进行扩容
          if (++size > threshold)
              resize();
          afterNodeInsertion(evict);
          return null;
      }
      ```

      - put 方法比较复杂，实现步骤大致如下：
        - 先通过 hash 值计算出 key 映射到哪个桶。
        - 如果桶上没有碰撞冲突，则直接插入。
        - 如果出现碰撞冲突了，则需要处理冲突：
          - 如果该桶使用红黑树处理冲突，则调用红黑树的方法插入。
          - 否则采用传统的链式方法插入。如果链的长度到达临界值，则把链转变为红黑树。
        - 如果桶中存在重复的键，则为该键替换新值。
        - 如果 size 大于阈值，则进行扩容。 

   3. remove方法

      ```java
      //remove 方法的具体实现在 removeNode 方法中，所以我们重点看下面的 removeNode 方法
      public V remove(Object key) {
          Node<K,V> e;
          return (e = removeNode(hash(key), key, null, false, true)) == null ?null : e.value;
      }
      
      final Node<K,V> removeNode(int hash, Object key, Object value,
                                 boolean matchValue, boolean movable) {
          Node<K,V>[] tab; Node<K,V> p; int n, index;
          //如果当前 key 映射到的桶不为空
          if ((tab = table) != null && (n = tab.length) > 0 &&
              (p = tab[index = (n - 1) & hash]) !=null) {
              Node<K,V> node = null, e; K k; V v;
              //如果桶上的节点就是要找的 key，则直接命中
              if (p.hash == hash && ((k = p.key) == key || (key !=null && key.equals(k))))
                  node = p;
              else if ((e = p.next) != null) {
                  //如果是以红黑树处理冲突，则构建一个树节点
                  if (p instanceof TreeNode)
                      node = ((TreeNode<K,V>)p).getTreeNode(hash,key);
                  //如果是以链式的方式处理冲突，则通过遍历链表来寻找节点
                  else {
                      do {
                          if (e.hash == hash && ((k = e.key) == key 
                                                 || (key != null &&key.equals(k)))) {
                              node = e;
                              break;
                          }
                          p = e;
                      } while ((e = e.next) != null);
                  }
              }
              //比对找到的 key 的 value 跟要删除的是否匹配
              if (node != null && (!matchValue || (v = node.value)== value 
                                   ||(value != null &&value.equals(v)))) {
                  //通过调用红黑树的方法来删除节点
                  if (node instanceof TreeNode)
                      ((TreeNode<K,V>)node).removeTreeNode(this, tab, movable);
                  //使用链表的操作来删除节点
                  else if (node == p)
                      tab[index] = node.next;
                  else
                      p.next = node.next;
                  ++modCount;
                  --size;
                  afterNodeRemoval(node);
                  return node;
              }
          }
          return null;
      }
      ```

   4. hash 方法 

      - 在 get 方法和 put 方法中都需要先计算 key 映射到哪个桶上，然后才进行之后的操作，计算的主要代码如下 

        ```java
        (n - 1) & hash
        ```

      - 上面代码中的 n 指的是哈希表的大小， hash 指的是 key 的哈希值， hash 是通过下面这个方法计算出来的，采用了二次哈希的方式，其中 key 的 hashCode 方法是一个native 方法：

        ```java
        static final int hash(Object key) {
            int h;
            return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>>16);
        }
        ```

      -  这个 hash 方法先通过 key 的 hashCode 方法获取一个哈希值，再拿这个哈希值与它的高 16 位的哈希值做一个**异或**操作来得到最后的哈希值，计算过程可以参考下图。为啥要这样做呢？注释中是这样解释的：

      - 如果当 n 很小，假设为 64 的话，那么 n-1即为 63（0x111111），这样的值跟 hashCode()直接做与操作，实际上只使用了**哈希值的后 6 位**。如果当哈希值的高位变化很大，低位变化很小，这样就很容易造成冲突了，所以这里把高低位都利用起来，从而解决了这个问题。 

        ![](.\计算hash值.JPG)

      - 正是因为与的这个操作，决定了 HashMap 的大小只能是 2 的幂次方，想一想，如果不是 2 的幂次方，会发生什么事情？即使你在创建 HashMap 的时候指定了初始大小，HashMap 在构建的时候也会调用下面这个方法来调整大小：

        ```java
        static final int tableSizeFor(int cap) {
            int n = cap - 1;
            n |= n >>> 1;
            n |= n >>> 2;
            n |= n >>> 4;
            n |= n >>> 8;
            n |= n >>> 16;
            return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
        }
        ```

      - 这个方法的作用看起来可能不是很直观，它的实际作用就是把 cap 变成第一个大于等于 2 的幂次方的数。例如， 16 还是 16， 13 就会调整为 16， 17 就会调整为 32。 

   5. resize 方法 

      - HashMap 在进行扩容时，使用的 rehash 方式非常巧妙，因为每次扩容都是翻倍，与原来计算（n-1） &hash 的结果相比，只是多了一个 bit 位，所以节点要么就在原来的位置，要么就被分配到“原位置+旧容量”这个位置。 

      - 例如，原来的容量为 32，那么应该拿 hash 跟 31（0x11111）做与操作；在扩容扩到了 64 的容量之后，应该拿 hash 跟 63（0x111111）做与操作。新容量跟原来相比只是多了一个 bit 位，假设原来的位置在 23，那么当新增的那个 bit 位的计算结果为 0时，那么该节点还是在 23；相反，计算结果为 1 时，则该节点会被分配到 23+31 的桶上。 

      - 正是因为这样巧妙的 rehash 方式，保证了 rehash 之后每个桶上的节点数必定小于等于原来桶上的节点数，即保证了 rehash 之后不会出现更严重的冲突。 

        ```java
        final Node<K,V>[] resize() {
            Node<K,V>[] oldTab = table;
            int oldCap = (oldTab == null) ? 0 : oldTab.length;
            int oldThr = threshold;
            int newCap, newThr = 0;
            //计算扩容后的大小
            if (oldCap > 0) {
                //如果当前容量超过最大容量，则无法进行扩容
                if (oldCap >= MAXIMUM_CAPACITY) {
                    threshold = Integer.MAX_VALUE;
                    return oldTab;
                }
                //没超过最大值则扩为原来的两倍
                else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY 
                         &&oldCap >= DEFAULT_INITIAL_CAPACITY)
                    newThr = oldThr << 1; // double threshold
            }
            else if (oldThr > 0) // initial capacity was placed in threshold
                newCap = oldThr;
            else { // zero initial threshold signifies using defaults
                newCap = DEFAULT_INITIAL_CAPACITY;
                newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
            }
            if (newThr == 0) {
                float ft = (float)newCap * loadFactor;
                newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                          (int)ft : Integer.MAX_VALUE);
            }
            //新的 resize 阈值
            threshold = newThr;
            //创建新的哈希表
            @SuppressWarnings({"rawtypes","unchecked"})
            Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
            table = newTab;
            if (oldTab != null) {
                //遍历旧哈希表的每个桶，重新计算桶里元素的新位置
                for (int j = 0; j < oldCap; ++j) {
                    Node<K,V> e;
                    if ((e = oldTab[j]) != null) {
                        oldTab[j] = null;
                        //如果桶上只有一个键值对，则直接插入
                        if (e.next == null)
                            newTab[e.hash & (newCap - 1)] = e;
                        //如果是通过红黑树来处理冲突的，则调用相关方法把树分离开
                        else if (e instanceof TreeNode)
                            ((TreeNode<K,V>)e).split(this, newTab, j,oldCap);
                        //如果采用链式处理冲突
                        else { // preserve order
                            Node<K,V> loHead = null, loTail = null;
                            Node<K,V> hiHead = null, hiTail = null;
                            Node<K,V> next;
                            //通过上面讲的方法来计算节点的新位置
                            do {
                                next = e.next;
                                if ((e.hash & oldCap) == 0) {
                                    if (loTail == null)
                                        loHead = e;
                                    else
                                        loTail.next = e;
                                    loTail = e;
                                }else {
                                    if (hiTail == null)
                                        hiHead = e;
                                    else
                                        hiTail.next = e;
                                    hiTail = e;
                                }
                            } while ((e = next) != null);
                            if (loTail != null) {
                                loTail.next = null;
                                newTab[j] = loHead;
                            }
                            if (hiTail != null) {
                                hiTail.next = null;
                                newTab[j + oldCap] = hiHead;
                            }
                        }
                    }
                }
            }
            return newTab;
        }
        ```

      - 在这里有一个需要注意的地方，有些文章指出当哈希表的**桶占用超过阈值**时就进行扩容，这是不对的；实际上是当哈希表中的**键值对个数超过阈值**时，才进行扩容的。 

4. 总结 

   - 按照原来的拉链法来解决冲突，如果一个桶上的冲突很严重的话，是会导致哈希表的效率降低至 O（n），而通过红黑树的方式，可以把效率改进至 O（logn）。相比链式结构的节点，树型结构的节点会占用比较多的空间，所以这是一种以空间换时间的改进方式。 

##### LinkedHashMap的实现原理 

1. 概述 

   - LinkedHashMap 是通过哈希表和链表实现的，它通过维护一个链表来保证对哈希表迭代时的有序性，而这个有序是指键值对插入的顺序。另外，当向哈希表中重复插入某个键的时候，不会影响到原来的有序性。也就是说，假设你插入的键的顺序为 1、 2、 3、 4，后来再次插入 2，迭代时的顺序还是 1、 2、3、 4，而不会因为后来插入的 2 变成 1、 3、 4、 2。（但其实我们可以改变它的规则，使它变成 1、 3、 4、 2） 
   - LinkedHashMap 的实现主要分两部分，一部分是哈希表，另外一部分是链表。哈希表部分继承了 HashMap，拥有了 HashMap 那一套高效的操作，所以我们要看的就是LinkedHashMap 中链表的部分，了解它是如何来维护有序性的。 
   - LinkedHashMap 的大致实现如下图所示，当然链表和哈希表中相同的键值对都是指向同一个对象，这里把它们分开来画只是为了呈现出比较清晰的结构。![](.\LinkedHashMap.JPG)

2. 属性

   - 在看属性之前，我们先来看一下 LinkedHashMap 的声明 

     ```java
     public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>
     ```

   - 从上面的声明中，我们可以看见 LinkedHashMap 是继承自 HashMap 的，所以它已经从 HashMap 那里继承了与哈希表相关的操作了，那么在 LinkedHashMap 中，它可以专注于链表实现的那部分，所以与链表实现相关的属性如下。

     ```java
     //LinkedHashMap 的链表节点继承了 HashMap 的节点，而且每个节点都包含了前指针和后指针，所以这里可以看出它是一个双向链表
     static class Entry<K,V> extends HashMap.Node<K,V> {
         Entry<K,V> before, after;
         Entry(int hash, K key, V value, Node<K,V> next) {
             super(hash, key, value, next);
         }
     }
     //头指针
     transient LinkedHashMap.Entry<K,V> head;
     
     //尾指针
     transient LinkedHashMap.Entry<K,V> tail;
     
     //默认为 false。当为 true 时，表示链表中键值对的顺序与每个键的插入顺序一致，也就是说重复插入键，也会更新顺序
     
     //简单来说，为 false 时，就是上面所指的 1、 2、 3、 4 的情况；为 true 时，就是 1、 3、 4、 2 的情况
     final boolean accessOrder;
     ```

3. 方法

   - 如果你有仔细看过 HashMap 源码的话，你会发现 HashMap 中有如下三个方法 

     ```java
     // Callbacks to allow LinkedHashMap post-actions
     void afterNodeAccess(Node<K,V> p) { }
     void afterNodeInsertion(boolean evict) { }
     void afterNodeRemoval(Node<K,V> p) { }
     ```

   - 如果你没有注意到注释的解释的话，你可能会很奇怪为什么会有三个空方法，而且有不少地方还调用过它们。其实这三个方法表示的是在访问、插入、删除某个节点之后，进行一些处理，它们在 LinkedHashMap 都有各自的实现。 LinkedHashMap **正是通过重写这三个方法来保证链表的插入、删除的有序性。** 

   1. afterNodeAccess 方法 
   
      ```java
      void afterNodeAccess(Node<K,V> e) { // move node to last
          LinkedHashMap.Entry<K,V> last;
          //当 accessOrder 的值为 true，且 e 不是尾节点
          if (accessOrder && (last = tail) != e) {
              LinkedHashMap.Entry<K,V> p =
                  (LinkedHashMap.Entry<K,V>)e, b = p.before, a =p.after;
              p.after = null;
              if (b == null)
                  head = a;
              else
                  b.after = a;
              if (a != null)
                  a.before = b;
              else
                  last = b;
              if (last == null)
                  head = p;
              else {
                  p.before = last;
                  last.after = p;
              }
              tail = p;
              ++modCount;
          }
      }
      ```
   
      - 这段代码的意思简洁明了，就是把当前节点 e 移至链表的尾部。因为使用的是双向链表，所以在尾部插入可以以 O（1）的时间复杂度来完成。并且只有当 accessOrder 设置为 true 时，才会执行这个操作。在 HashMap 的 putVal 方法中，就调用了这个方法。
   
   2.  afterNodeInsertion 方法 
   
      ```java
      void afterNodeInsertion(boolean evict) { // possibly remove eldest
          LinkedHashMap.Entry<K,V> first;
          if (evict && (first = head) != null && removeEldestEntry(first)) {
              K key = first.key;
              removeNode(hash(key), key, null, false, true);
          }
      }
      ```
   
      - afterNodeInsertion 方法是在哈希表中插入了一个新节点时调用的，它会把链表的头节点删除掉，删除的方式是通过调用 HashMap 的 removeNode 方法。想一想，通过afterNodeInsertion 方法和 afterNodeAccess 方法，是不是就可以简单的实现一个基于**最近最少使用（LRU）**的淘汰策略了？当然，我们还要重写 removeEldestEntry 方法，因为它默认返回的是 false。
   
   3.  afterNodeRemoval 方法 
   
      ```java
      void afterNodeRemoval(Node<K,V> e) { // unlink
          LinkedHashMap.Entry<K,V> p =
              (LinkedHashMap.Entry<K,V>)e, b = p.before, a = p.after;
          p.before = p.after = null;
          if (b == null)
              head = a;
          else
              b.after = a;
          if (a == null)
              tail = b;
          else
              a.before = b;
      }
      ```
   
      - 这个方法是当 HashMap 删除一个键值对时调用的，它会把在 HashMap 中删除的那个键值对一并从链表中删除，保证了哈希表和链表的一致性。 
   
   4. get 方法 
   
      ```java
      public V get(Object key) {
          Node<K,V> e;
          if ((e = getNode(hash(key), key)) == null)
              return null;
          if (accessOrder)
              afterNodeAccess(e);
          return e.value;
      }
      ```
   
      - 你没看错， LinkedHashMap 的 get 方法就是这么简单，因为它调用的是 HashMap 的getNode 方法来获取结果的。并且，如果你把 accessOrder 设置为 true，那么在获取 到值之后，还会调用 afterNodeAccess 方法。这样是不是就能保证一个 LRU 的算法了？ 
   
   5. put 方法和 remove 方法 
   
      - 我在 LinkedHashMap 的源码中没有找到 put 方法，这就说明了它并没有重写 put 方法，所以我们调用的 put 方法其实是 HashMap 的 put 方法。因为 HashMap 的 put 方法中调用了 afterNodeAccess 方法和 afterNodeInsertion 方法，已经足够保证链表的有序性了，所以它也就没有重写 put 方法了。 remove 方法也是如此。 

##### HashTable的实现原理

1. 概述 

   - Hashtable 可以说已经具有一定的历史了，现在也很少使用到 Hashtable 了，更多的是使用 HashMap 或 ConcurrentHashMap。 HashTable 是一个**线程安全**的哈希表，它通过使用synchronized 关键字来对方法进行加锁，从而保证了线程安全。但这也导致了在单线程环境中效率低下等问题。 Hashtable 与 HashMap 不同，**它不允许插入 null 值和 null 键。** 

2. 属性

   - Hashtable 并没有像 HashMap 那样定义了很多的常量，而是直接写死在了方法里（看下去就知道了），所以它的属性相比 HashMap 来说，可以获取的信息还是比较少的。

     ```java
     //哈希表
     private transient Entry<?,?>[] table;
     //记录哈希表中键值对的个数
     private transient int count;
     //扩容的阈值
     private int threshold;
     //负载因子
     private float loadFactor;
     ```

3.  方法

   1. 构造方法

      ```java
      public Hashtable(int initialCapacity, float loadFactor) {
          if (initialCapacity < 0)
              throw new IllegalArgumentException("Illegal Capacity:"+initialCapacity);
          if (loadFactor <= 0 || Float.isNaN(loadFactor))
              throw new IllegalArgumentException("Illegal Load: "+loadFactor);
          if (initialCapacity==0)
              initialCapacity = 1;
          this.loadFactor = loadFactor;
          table = new Entry<?,?>[initialCapacity];
          threshold = (int)Math.min(initialCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
      }
      
      public Hashtable(int initialCapacity) {
          this(initialCapacity, 0.75f);
      }
      
      public Hashtable() {
          this(11, 0.75f);
      }
      ```

      - 二话不说，上来先丢了三个构造函数。从构造函数中，我们可以获取到这些信息：Hashtable **默认的初始化容量为 11** （与 HashMap 不同），负载因子默认为 **0.75** （与 HashMap相同）。而正因为默认初始化容量的不同，同时也没有对容量做调整的策略，所以可以先推断出， **Hashtable 使用的哈希函数跟 HashMap 是不一样的（事实也确实如此）。** 

   2. get方法

      ```java
      public synchronized V get(Object key) {
          Entry<?,?> tab[] = table;
          int hash = key.hashCode();
          //通过哈希函数，计算出 key 对应的桶的位置
          int index = (hash & 0x7FFFFFFF) % tab.length;
          //遍历该桶的所有元素，寻找该 key
          for (Entry<?,?> e = tab[index] ; e != null ; e = e.next) {
              if ((e.hash == hash) && e.key.equals(key)) {
                  return (V)e.value;
              }
          }
          return null;
      }
      ```

      - 跟HashMap 相比， Hashtable 的 get 方法非常简单。我们首先可以看见 get 方法使用了synchronized 来修饰，所以它能保证线程安全。并且它是通过链表的方式来处理冲突的。另外，我们还可以看见 HashTable 并没有像 HashMap 那样封装一个哈希函数，而是直接把哈希函数写在了方法中。而哈希函数也是比较简单的，它**仅对哈希表的长度进行了取模**。 

   3. put方法

      ```java
      public synchronized V put(K key, V value) {
          // Make sure the value is not null
          if (value == null) {
              throw new NullPointerException();
          }
          // Makes sure the key is not already in the hashtable.
          Entry<?,?> tab[] = table;
          int hash = key.hashCode();
          //计算桶的位置
          int index = (hash & 0x7FFFFFFF) % tab.length;
          
          @SuppressWarnings("unchecked")
          Entry<K,V> entry = (Entry<K,V>)tab[index];
          //遍历桶中的元素，判断是否存在相同的 key
          for(; entry != null ; entry = entry.next) {
              if ((entry.hash == hash) && entry.key.equals(key)) {
                  V old = entry.value;
                  entry.value = value;
                  return old;
              }
          }
          //不存在相同的 key，则把该 key 插入到桶中
          addEntry(hash, key, value, index);
          return null;
      }
      
      private void addEntry(int hash, K key, V value, int index) {
          modCount++;
          Entry<?,?> tab[] = table;
          //哈希表的键值对个数达到了阈值，则进行扩容
          if (count >= threshold) {
              // Rehash the table if the threshold is exceeded
              rehash();
              tab = table;
              hash = key.hashCode();
              index = (hash & 0x7FFFFFFF) % tab.length;
          }
          
          // Creates the new entry.
          @SuppressWarnings("unchecked")
          Entry<K,V> e = (Entry<K,V>) tab[index];
          //把新节点插入桶中（头插法）
          tab[index] = new Entry<>(hash, key, value, e);
          count++;
      }
      ```

      - put 方法一开始就表明了不能有 null 值，否则就会向你抛出一个空指针异常。 Hashtable的 put 方法也是使用 synchronized 来修饰。你可以发现，在 Hashtable 中，几乎所有的方法都使用了 synchronized 来保证线程安全。 

   4. remove方法

      ```java
      public synchronized V remove(Object key) {
          Entry<?,?> tab[] = table;
          int hash = key.hashCode();
          int index = (hash & 0x7FFFFFFF) % tab.length;
          @SuppressWarnings("unchecked")
          Entry<K,V> e = (Entry<K,V>)tab[index];
          for(Entry<K,V> prev = null ; e != null ; prev = e, e = e.next) {
              if ((e.hash == hash) && e.key.equals(key)) {
                  modCount++;
                  if (prev != null) {
                      prev.next = e.next;
                  } else {
                      tab[index] = e.next;
                  }
                  count--;
                  V oldValue = e.value;
                  e.value = null;
                  return oldValue;
              }
          }
          return null;
      }
      ```

   5. rehash方法

      ```java
      protected void rehash() {
          int oldCapacity = table.length;
          Entry<?,?>[] oldMap = table;
          //扩容扩为原来的两倍+1
          int newCapacity = (oldCapacity << 1) + 1;
          //判断是否超过最大容量
          if (newCapacity - MAX_ARRAY_SIZE > 0) {
              if (oldCapacity == MAX_ARRAY_SIZE)
                  // Keep running with MAX_ARRAY_SIZE buckets
                  return;
              newCapacity = MAX_ARRAY_SIZE;
          }
          Entry<?,?>[] newMap = new Entry<?,?>[newCapacity];
          modCount++;
          //计算下一次 rehash 的阈值
          threshold = (int)Math.min(newCapacity * loadFactor, MAX_ARRAY_SIZE + 1);
          table = newMap;
          //把旧哈希表的键值对重新哈希到新哈希表中去
          for (int i = oldCapacity ; i-- > 0 ;) {
              for (Entry<K,V> old = (Entry<K,V>)oldMap[i] ; old != null; ) {
                  Entry<K,V> e = old;
                  old = old.next;
                  int index = (e.hash & 0x7FFFFFFF) % newCapacity;
                  e.next = (Entry<K,V>)newMap[index];
                  newMap[index] = e;
              }
          }
      }
      ```

      - Hashtable 的 rehash 方法相当于 HashMap 的 resize 方法。跟 HashMap 那种巧妙的 rehash方式相比， Hashtable 的 rehash 过程需要对每个键值对都重新计算哈希值，而比起异或和与操作，**取模是一个非常耗时的操作，所以这也是导致效率较低的原因之一。** 

##### 深入分析HashMap

1. 传统HashMap的缺点

   - JDK 1.8 以前 HashMap 的实现是 **数组+链表**，即使哈希函数取得再好，也很难达到元素百分百均匀分布。
   - 当 HashMap 中有大量的元素都存放到同一个桶中时，这个桶下有一条长长的链表，这个时候 HashMap 就相当于一个单链表，假如单链表有 n 个元素，遍历的时间复杂度就是 O(n)，完全失去了它的优势。
   - 针对这种情况， JDK 1.8 中引入了**红黑树**（查找时间复杂度为 O(logn)）来优化这个问题 

2. JDK1.8 中 HashMap 的数据结构 

   1. HashMap 是数组+链表+红黑树（JDK1.8 增加了红黑树部分）实现的 

      ![](.\1.8HashMap.JPG)

      - 新增红黑树 

        ```java
        static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
            TreeNode<K,V> parent; // red-black tree links
            TreeNode<K,V> left;
            TreeNode<K,V> right;
            TreeNode<K,V> prev; // needed to unlink next upon deletion
            boolean red;
        }
        ```

   2. HashMap 中关于红黑树的三个关键参数

      |           TREEIFY_THRESHOLD <br />一个桶的树化阈值           |         UNTREEIFY_THRESHOLD <br />一个树的链表还原阈         |       MIN_TREEIFY_CAPACITY<br/>哈希表的最小树形化容量        |
      | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
      |            static final int TREEIFY_THRESHOLD = 8            |         static final int<br/>UNTREEIFY_THRESHOLD = 6         |          static final int MIN_TREEIFY_CAPACITY = 64          |
      | 当桶中元素个数超过这个值时<br/>需要使用红黑树节点替换链表节点 | 当扩容时，桶中元素个数小于这个值<br/>就会把树形的桶元素 还原（切分）为链<br/>表结构 | 当哈希表中的容量大于这个值时，表中的桶才能进行树形化<br/>否则桶内元素太多时会扩容，而不是树形化<br/>为了避免进行扩容、树形化选择的冲突，这个值不能小于 4 * TREEIFY_THRESHOLD |

   3.  HashMap 在 JDK 1.8 中新增的操作：桶的树形化 treeifyBin() 

      - 在 Java 8 中，如果一个桶中的元素个数超过 TREEIFY_THRESHOLD(默认是 8 )，就使用红黑树来替换链表，从而提高速度。这个替换的方法叫 treeifyBin() 即树形化。 

        ```java
        //将桶内所有的 链表节点 替换成 红黑树节点
        final void treeifyBin(Node<K,V>[] tab, int hash) {
            int n, index; Node<K,V> e;
            //如果当前哈希表为空，或者哈希表中元素的个数小于 进行树形化的阈值(默认为 64)，就去新建/扩容
            if (tab == null || (n = tab.length) < MIN_TREEIFY_CAPACITY)
                resize();
            else if ((e = tab[index = (n - 1) & hash]) != null) {
                //如果哈希表中的元素个数超过了 树形化阈值，进行树形化
                // e 是哈希表中指定位置桶里的链表节点，从第一个开始
                TreeNode<K,V> hd = null, tl = null; //红黑树的头、尾节点
                do {
                    //新建一个树形节点，内容和当前链表节点 e 一致
                    TreeNode<K,V> p = replacementTreeNode(e, null);
                    if (tl == null) //确定树头节点
                        hd = p;
                    else {
                        p.prev = tl;
                        tl.next = p;
                    }
                    tl = p;
                } while ((e = e.next) != null);
                //让桶的第一个元素指向新建的红黑树头结点，以后这个桶里的元素就是红黑树而不是链表了
                if ((tab[index] = hd) != null)
                    hd.treeify(tab);
            }
        }
        TreeNode<K,V> replacementTreeNode(Node<K,V> p, Node<K,V> next) {
            return new TreeNode<>(p.hash, p.key, p.value, next);
        }
        ```

        - 上述操作做了这些事:
          - 根据哈希表中元素个数确定是扩容还是树形化
          - 如果是树形化遍历桶中的元素，创建相同个数的树形节点，复制内容，建立起联系
          - 然后让桶第一个元素指向新建的树头结点，替换桶的链表内容为树形内容 

3. 分析HashMap的put方法

   1. 图解

      ![](.\HashMap的put().png)

      - ①.判断键值对数组 table[i]是否为空或为 null，否则执行 resize()进行扩容；

      - ②.根据键值 key 计算 hash 值得到插入的数组索引 i，如果 table[i]==null，直接新建节点添加，
        转向⑥，如果 table[i]不为空，转向③；

      - ③.判断 table[i]的首个元素是否和 key 一样，如果相同直接覆盖 value，否则转向④，这里的相
        同指的是 hashCode 以及 equals；

      - ④.判断 table[i] 是否为 treeNode，即 table[i] 是否是红黑树，如果是红黑树，则直接在树中插
        入键值对，否则转向⑤；

      - ⑤.遍历 table[i]，判断链表长度是否大于 8，大于 8 的话把链表转换为红黑树，在红黑树中执
        行插入操作，否则进行链表的插入操作；遍历过程中若发现 key 已经存在直接覆盖 value 即可；

      - ⑥.插入成功后，判断实际存在的键值对数量 size 是否超多了最大容量 threshold，如果超过，
        进行扩容。 

        ```java
        //JDK1.8HashMap 的 put 方法源码
        public V put(K key, V value) {
            // 对 key 的 hashCode()做 hash
            return putVal(hash(key), key, value, false, true);
        }
        
        final V putVal(int hash, K key, V value, boolean onlyIfAbsent,boolean evict) {
            Node<K,V>[] tab; Node<K,V> p; int n, i;
            // 步骤①： tab 为空则创建
            if ((tab = table) == null || (n = tab.length) == 0)
                n = (tab = resize()).length;
            // 步骤②：计算 index，并对 null 做处理
            if ((p = tab[i = (n - 1) & hash]) == null)
                tab[i] = newNode(hash, key, value, null);
            else {
                Node<K,V> e; K k;
                // 步骤③：节点 key 存在，直接覆盖 value
                if (p.hash == hash &&
                    ((k = p.key) == key || (key != null && key.equals(k))))
                    e = p;
                // 步骤④：判断该链为红黑树
                else if (p instanceof TreeNode)
                    e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
                // 步骤⑤：该链为链表
                else {
                    for (int binCount = 0; ; ++binCount) {
                        if ((e = p.next) == null) {
                            p.next = newNode(hash, key,value,null);
                            //链表长度大于 8 转换为红黑树进行处理
                            if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                                treeifyBin(tab, hash);
                            break;
                        }
                        // key 已经存在直接覆盖 value
                        if (e.hash == hash &&
                            ((k = e.key) == key || (key != null && key.equals(k))))
                            break;
                        p = e;
                    }
                }
                
                if (e != null) { // existing mapping for key
                    V oldValue = e.value;
                    if (!onlyIfAbsent || oldValue == null)
                        e.value = value;
                    afterNodeAccess(e);
                    return oldValue;
                }
            }
            ++modCount;
            // 步骤⑥：超过最大容量 就扩容
            if (++size > threshold)
                resize();
            afterNodeInsertion(evict);
            return null;
        }
        
        //JDK1.7HashMap 的 put 方法源码
        public V put(K key, V value) {
            if (table == EMPTY_TABLE) { //空表 table 的话，根据 size 的阈值填充
                inflateTable(threshold);
            }
            if (key == null)
                return putForNullKey(value);
            int hash = hash(key);//成 hash，得到索引 Index 的映射
            int i = indexFor(hash, table.length);
            for (Entry<K,V> e = table[i]; e != null; e = e.next) {//遍历当前索引的冲突链，找是否存在对应的 key
                Object k;
                if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {//如果存在对应的 key， 则替换 oldValue 并返回 oldValue
                    V oldValue = e.value;
                    e.value = value;
                    e.recordAccess(this);
                    return oldValue;
                }
            }
            //冲突链中不存在新写入的 Entry 的 key
            modCount++;
            addEntry(hash, key, value, i);
            return null;
        }
        ```

   2. HashMap 在 JDK 1.8 中新增的操作： 红黑树中查找元素 getTreeNode() 

      ```java
      final Node<K,V> getNode(int hash, Object key) {
          Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
          if ((tab = table) != null && (n = tab.length) > 0 &&
              (first = tab[(n - 1) & hash]) != null) {
              if (first.hash == hash && // always check first node
                  ((k = first.key) == key || (key != null && key.equals(k))))
                  return first;
              if ((e = first.next) != null) {
                  if (first instanceof TreeNode)
                      return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                  do {
                      if (e.hash == hash &&
                          ((k = e.key) == key || (key != null && key.equals(k))))
                          return e;
                  } while ((e = e.next) != null);
              }
          }
          return null;
      }
      ```

      - (1)HashMap 的查找方法是 get(),它通过计算指定 key 的哈希值后，调用内部方法 getNode()；

      - (2)这个 getNode() 方法就是根据哈希表元素个数与哈希值求模（使用的公式是 (n - 1)&hash）得到 key 所在的桶的头结点，如果头节点恰好是红黑树节点，就调用红黑树节点的 getTreeNode() 方法，否则就遍历链表节点。

      - (3)getTreeNode 方法使通过调用树形节点的 find()方法进行查找 

        ```java
        final TreeNode<K,V> getTreeNode(int h, Object k) {
            return ((parent != null) ? root() : this).find(h, k, null);
        }
        ```

      - (4)由于之前添加时已经保证这个树是有序的，因此查找时基本就是折半查找，效率很高。

      - (5)这里和插入时一样，如果对比节点的哈希值和要查找的哈希值相等，就会判断 key 是否相等，相等就直接返回；不相等就从子树中递归查找 。

   3. JDK1.8 VS JDK1.7 扩容机制 

      - 下面举个例子说明下扩容过程。假设了我们的 hash 算法就是简单的用 key mod 一下表的大小（也就是数组的长度）。其中的哈希桶数组 table 的 size=2， 所以 key = 3、 7、 5， put 顺序依次为 5、 7、 3。在 mod 2 以后都冲突在 table[1]这里了。这里假设负载因子 loadFactor=1，即当键值对的实际大小 size 大于 table 的实际大小时进行扩容。接下来的三个步骤是哈希桶数组 resize 成 4，然后所有的 Node 重新 rehash 的过程。 

        ![](.\1.7put.JPG)

      - 下面我们讲解下 JDK1.8 做了哪些优化。经过观测可以发现，我们使用的是 2 次幂的扩展(指长度扩为原来 2 倍)，所以，元素的位置要么是在原位置，要么是在原位置再移动 2 次幂的位置。
        看下图可以明白这句话的意思， n 为 table 的长度，图（a）表示扩容前的 key1 和 key2 两种key 确定索引位置的示例，图（b）表示扩容后 key1 和 key2 两种 key 确定索引位置的示例，其中 hash1 是 key1 对应的哈希与高位运算结果。 

        ![](.\hash.JPG)

      - 元素在重新计算 hash 之后，因为 n 变为 2 倍，那么 n-1 的 mask 范围在高位多 1bit(红色)，因此新的 index 就会发生这样的变化：

        ![](.\hash2.JPG) 

      - 因此，我们在扩充 HashMap 的时候，不需要像 JDK1.7 的实现那样重新计算 hash，只需要看看原来的 hash 值新增的那个 bit 是 1 还是 0 就好了，是 0 的话索引没变，是 1 的话索引变成“原索引+oldCap”，可以看看下图为 16 扩充为 32 的 resize 示意图：![](.\hash3.JPG)
      - 这个设计确实非常的巧妙，既省去了重新计算 hash 值的时间，而且同时，由于新增的 1bit 是0还是1可以认为是随机的，因此resize的过程，均匀的把之前的冲突的节点分散到新的bucket了。这一块就是 JDK1.8 新增的优化点。

## 第十章：泛型

### 1.为什么要有泛型

- 泛型：标签
- 举例：
  - 中药店，每个抽屉外面贴着标签
  - 超市购物架上很多瓶子，每个瓶子装的是什么，有标签
- 泛型的设计背景
  - 集合容器类在设计阶段/声明阶段不能确定这个容器到底实际存的是什么类型的对象，所以在JDK1.5之前只能把元素类型设计为object，JDK1.5之后使用泛型来解决。因为这个时候除了元素的类型不确定，其他的部分是确定的，例如关于这个元素如何保存，如何管理等是确定的，因此此时把元素的类型设计成一个参数，**这个类型参数叫做泛型**。Collection<E>，List<E>，ArrayList<E>这个<E>就是类型参数，即泛型。
- 泛型的概念
  -  所谓泛型，就是允许在定义类、接口时通过一个标识表示类中某个属性的类型或者是某个方法的返回值及参数类型。这个类型参数将在使用时（例如，继承或实现这个接口，用这个类型声明变量、创建对象时）确定（即传入实际的类型参数，也称为类型实参）。
  - 从JDK1.5以后，Java引入了“参数化类型（Parameterized type)”的概念，允许我们在创建集合时再指定集合元素的类型，正如：List<String>，这表明该List只能保存字符串类型的对象。
  - JDK1.5改写了集合框架中的全部接口和类，为这些接口、类增加了泛型支持，从而可以在声明集合变量、创建集合对象时传入类型实参。
-  那么为什么要有泛型呢，直接Object不是也可以存储数据吗？
  - 1.解决元素存储的安全性问题，好比商品、药品标签，不会弄错。
  - 2.解决获取数据元素时，需要类型强制转换的问题，好比不用每回拿商品、药品都要辨别。
- **Java泛型可以保证如果程序在编译时没有发出警告，运行时就不会产生ClassCastException异常。同时，代码更加简洁、健壮。**

### 2.在集合中使用泛型

```java
ArrayList<Integer> list = new Arraylist<>();
//类型推断 
list.add(78); 
list.add(88);
list.add(77);
list.add(66); 
//遍历方式一： 
//for(Integeri:list){ 
//不需要强转 
//System.out.println(i); 
//} 
//遍历方式二： 
Iterator<Integer> iterator = list.iterator(); 
while(iterator.hasNext()){ 
    System.out.println(iterator.next());
}
```

```java
Map<String,Integer> map = new HashMap<String,Integer>(); 
map.put("Tom1",34); 
map.put("Tom2",44); 
map.put("Tom3",33); 
map.put("Tom4",32);
//添加失败 
//map.put(33,"Tom"); 
Set<Entry<String,Integer>> entrySet = map.entrySet();
Iterator<Entry<String,Integer>>iterator = entrySet.iterator(); 
while(iterator.hasNext()){ 
    Entry<String,Integer> entry = iterator.next(); 
    System.out.println(entry.getKey()+"--->"+entry.getValue());
}
```

### 3.自定义泛型结构

- ##### 泛型的声明

  - interface List<T>和class GenTest<K,V>其中，T，K，V不代表值，而是表示类型。这里使用任意字母都可以。常用T表示，是Type的缩写。

- ##### 泛型的实例化：

  - 一定要在类名后面指定类型参数的值（类型）。如：
  - List<String>strList = new ArrayList<String>();
  - Iterator<Customer>iterator = customers.iterator()；
  - T只能是类，不能用基本数据类型填充。但可以使用包装类填充
  - 把一个集合中的内容限制为一个特定的数据类型，这就是generics背后的核心思想

- 体会：使用泛型的主要优点是能够在编译时而不是在运行时检测错误。

- ##### 泛型类，泛型接口

  - 泛型类可能有多个参数，此时应将多个参数一起放在尖括号内。比如：<E1,E2,E3>

  - 泛型类的构造器如下：public GenericClass（){}。

    - 而下面是错误的：public GenericClass<E>（){}

  - 实例化后，操作原来泛型位置的结构必须与指定的泛型类型一致。

  - 泛型不同的引用不能相互赋值。

    - 尽管在编译时ArrayList<String>和ArrayList<lnteger>是两种类型，但是，在运行时只有一个ArrayList被加载到JVM中。

  - 泛型如果不指定，将被擦除，泛型对应的类型均按照Object处理，但不等价于Object。经验：泛型要使用一路都用。要不用，一路都不要用。

  - 如果泛型结构是一个**接口或抽象类**，则不可创建泛型类的对象。

  - jdk1.7,泛型的简化操作：ArrayList<Fruit>flist=new ArrayList<>()；

  - 泛型的指定中不能使用基本数据类型，可以使用包装类替换。

    ```java
    class GenericTest{ 
        public static void main(String[] args){ 
            //1、使用时：类似于0bject，不等同于Object 
            ArrayList list=new Arraylist(); 
            //list.add(new Date());//有风险 
            list.add("hel1o"); 
            test(1ist);//泛型擦除，编译不会类型检查 
            //Arraylist<object>list2=new ArrayList<object>(); 
            //test(list2)；//一旦指定object，编译会类型检查，必须按照object处理 
        } 
        
        public static void test(Arraylist<String>list){ 
            String str=""; 
            for(Strings:list){ 
                str+=s+","; 
                system.out.println("元素："+str); 
            }
        }
    ```

  -  在类/接口上声明的泛型，在本类或本接口中即代表某种类型，可以作为非静态属性的类型、非静态方法的参数类型、非静态方法的返回值类型。但在**静态方法中不能使用类的泛型**。

  - 异常类不能是泛型的

  - 不能使用newE[]。但是可以：E[] elements=（E[])new Object[capacity]；

    - 参考：ArrayList源码中声明：Object[] elementData，而非泛型参数类型数组。

  - 父类有泛型，子类可以选择保留泛型也可以选择指定泛型类型：

    - 子类不保留父类的泛型：按需实现
      - 没有类型擦除
      - 具体类型
    - 子类保留父类的泛型：泛型子类
      - 全部保留
      - 部分保留

  - **结论：子类必须是“富二代”，子类除了指定或保留父类的泛型，还可以增加自己的泛型**

  ```java
  class Father<T1,T2>{ 
  }
  
  //子类不保留父类的泛型 
  //1)没有类型擦除 
  class Son1 extends Father {//等价于class Son extends Father<object,Object>{ 
  }
  
  //2)具体类型 
  class Son2 extends Father<Integer,String>{ 
  }
  
  //子类保留父类的泛型 
  //1)全部保留 
  class Son3<T1,T2>extends Father<T1,T2>{ 
  }
  
  //2）部分保留 
  class Son4<T2>extends Father<Integer,T2>{
  }
  
  //子类不保留父类的泛型 
  //1）没有类型擦除 
  class Son5<A,B>extends Father{//等价于class son extends Father<object,object>{ 
  }
  
  //2)具体类型 
  class Son6<A,B>extends Father<Integer,String>{ 
  }
  
  //子类保留父类的泛型 
  //1）全部保留 
  class Son7<T1,T2,A,B>extends Father<T1,T2>{ 
  }
  
  //2）部分保留 
  class Son8<T2,A,B>extends Father<Integer,T2>{
  }
  ```

  ```java
  class Person〈T>{ 
      
      //使用T类型定义变量 
      private T info;
      
      //使用T类型定义一般方法
      public T getInfo(){ 
          return info;
      } 
      
      public void setInfo(T info){ 
          this.info=info; 
      }
      
      //使用T类型定义构造器 
      public Person(){
      }
      
      public Person(T info){ 
          this.info=info;
      }
      
      //static的方法中不能声明泛型 
      //public static void show(T t){
      // 
      //} 
      //不能在try-catch中使用泛型定义
      //public void test(){ 
      //try{ 
      // 
      //}catch(MyException<T>ex){ 
      // 
      //}
      //}
  }
  ```

- ##### 泛型方法

  - 方法，也可以被泛型化，不管此时定义在其中的类是不是泛型类。在泛型方法中可以定义泛型参数，此时，参数的类型就是传入数据的类型。

  - 泛型方法的格式：

    - [访问权限]<泛型>返回类型方法名（[泛型标识参数名称]）抛出的异常

  - 泛型方法声明泛型时也可以指定上限

    ```java
    public static <T>void fromArrayToCollection(T[]a,Collection<T>c){ 
        for(To:a){ 
            c.add(o));
        } 
    }
    
    public static void main(string[] args){ 
        Object[] ao = new object[10e]; 
        Collection<object> co = new Arraylist<object>(); 
        fromArrayToCollection(ao,co); 
        String[] sa=new String[20]; 
        Collection<String> cs = new Arraylist<>(); 
        fromArrayToCollection(sa,cs); 
        Collection<Double>cd=new ArrayList<>(); 
        //下面代码中T是Doub1e类，但sa是string类型，编译错误。
        //fromArrayToCollection(sa,cd); 
        //下面代码中T是object类型，sa是string类型，可以赋值成功。
        fromArrayToCollection(sa,co);
    }
    ```

### 4.泛型在继承上的体现

-  如果B是A的一个子类型（子类或者子接口），而G是具有泛型声明的类或接口，**G<B>并不是G<A>的子类型**！比如：String是Object的子类，但是List<String>并不是List<Object>的子类。

### 5.通配符的使用

- 使用类型通配符：？

  - 比如：List<?>，Map<?,？>List<?>是List<String>、List<Object>等各种泛型List的父类。

- 读取List<?>的对象list中的元素时，永远是安全的，因为不管list的真实类型是什么，它包含的**都是Object。**

- 写入list中的元素时，不行。因为我们不知道c的元素类型，我们不能向其中添加对象。

  - 唯一的例外是null，它是所有类型的成员。

- 将任意元素加入到其中不是类型安全的：

  - Collection<?> c = new ArrayList<String>()；
  - c.add(new Object（））；//编译时错误
  - 因为我们不知道c的元素类型，我们不能向其中添加对象。add方法有类型参数E作为集合的元素类型。我们传给add的任何参数都必须是一个未知类型的子类。因为我们不知道那是什么类型，所以我们无法传任何东西进去

- **唯一的例外的是null，它是所有类型的成员。**

- **另一方面，我们可以调用get(）方法并使用其返回值。返回值是一个未知的类型，但是我们知道，它总是一个Object。**

  ```java
  public static void main(String[] args){ 
      List<?> list = null; 
      list = new ArrayList<String>(); 
      list = new ArrayList<Double>(); 
      //list.add(3)；//编译不通过 
      list.add(nul1); 
      
      List<string> l1 = new Arraylist<String>(); 
      List<Integer> l2 = new Arraylist<Integer>(); 
      l1.add("尚硅谷"); 
      l2.add(15); 
      read(l1); 
      read(l2);
  } 
  
  public static void read(List<?>list){ 
      for(object o:list){ 
          System.out.print1n(o);
      }
  }
  ```

- 注意点

  ```java
  //注意点1：编译错误：不能用在泛型方法声明上，返回值类型前面<>不能使用？
  public static <?>void test(Arraylist<?>list){}
  //注意点2：编译错误：不能用在泛型类的声明上
  class GenericTypeClass<?>{}
  //注意点3：编译错误：不能用在创建对象上，右边属于创建集合对象
  Arraylist<?>list2=new ArrayList<?>();
  ```

- ##### 有限的统配符

  - <?>

    - 允许所有泛型的引用调用

  - 通配符指定上限

    - 上限extends：使用时指定的类型必须是继承某个类，或者实现某个接口，即<=

  - 通配符指定下限

    - 下限super：使用时指定的类型不能小于操作的类，即>=

  - 举例：

    - <? extends Number>(无穷小，Number]只允许泛型为Number及Number子类的引用调用
    - <? super Number>[Number,无穷大）只允许泛型为Number及Number父类的引用调用
    - <? extends Comparable>只允许泛型为实现Comparable接口的实现类的引用调用

    ```java
    public static void printCollection3(Collection<? extends Person>coll){ 
        //Iterator只能用Iterator<?>或Iterator<?extends Person>.why?
        Iterator<?>iterator = coll.iterator();
        while(iterator.hasNext()){ 
            System.out.println(iterator.next());
        } 
        public static void printCollection4(Collection<? super Person>coll){ 
            //Iterator只能用Iterator<?>或Iterator<?super Person>.why?
            Iterator<?>iterator=coll.iterator(); 
            while(iterator.hasNext()){ 
                System.out.println(iterator.next());
            }
        }
    ```

### 6.泛型应用举例

- 泛型嵌套

  ```java
  public static void main(String[]args){ 
      HashMap<String,ArrayList<Citizen>>map = new HashMap<String,ArrayList<Citizen>>();
      ArrayList<Citizen>list=new Arraylist<Citizen>(); 
      list.add(new Citizen("刘恺威"));
      list.add(new Citizen("杨幂")); 
      list.add(new Citizen("小糯米")); 
      map.put("刘恺威",list); 
      Set<Entry<String,Arraylist<Citizen>>>entryset = map.entryset();
      Iterator<Entry<String,ArrayList<Citizen>>>iterator = entryset.iterator();
      while(iterator.hasNext()){ 
          Entry<String,Arraylist<Citizen>>entry =iterator.next(); 
          String key=entry.getkey(); 
          Arraylist<Citizen> value=entry.getValue();
          System.out.println("户主："+key);
          System.out.println("家庭成员："+value);
      }
  }
  ```

## 第十一章：IO流

### 1.File类的使用

- java.io.File类：文件和文件目录路径的抽象表示形式，与平台无关

- File能新建、删除、重命名文件和目录，但File不能访问文件内容本身。如果需要访问文件内容本身，则需要使用输入/输出流。

- 想要在Java程序中表示一个真实存在的文件或目录，那么必须有一个File对象，但是Java程序中的一个File对象，可能没有一个真实存在的文件或目录。

- File对象可以作为参数传递给流的构造器

- ##### 常用构造器

  - public File(String pathname)
    - 以pathname为路径创建File对象，可以是**绝对路径或者相对路径**，如果pathname是相对路径，则默认的当前路径在系统属性**user.dir**中存储。
      - 绝对路径：是一个固定的路径，从盘符开始
      - 相对路径：是相对于某个位置开始
  - public File(String parent,String child)
    - 以parent为父路径，child为子路径创建File对象。
  - public File(File parent,String child)
    - 根据一个父File对象和子文件路径创建File对象

- ##### 路径分隔符

  - 路径中的每级目录之间用一个路径分隔符隔开。

  - 路径分隔符和系统有关：

    - windows和DOS系统默认使用“\”来表示
    - UNIX和URL使用“/”来表示

  - Java程序支持跨平台运行，因此路径分隔符要慎用。

    - 为了解决这个隐患，File类提供了一个常量：public static final String separator。根据操作系统，动态的提供分隔符。

  - 举例

    ```java
    File filel = new File("d:\\atguigu\\info.txt"); 
    File file2 = new File("d:"+File.separator+"atguigu"+File.separator+"info.txt"); 
    File file3 = new File("d:/atguigu");
    ```

- ##### 常用方法

  - File类的获取功能
    - **public String getAbsolutePath():获取绝对路径**
    - public String getPath():获取路径
    - public String getName():获取名称
    - **public String getParent():获取上层文件目录路径。若无，返回null**
    - public long length():获取文件长度（即：字节数）。不能获取目录的长度。
    - public long lastModified():获取最后一次的修改时间，毫秒值
    - public String[] list():获取指定目录下的所有文件或者文件目录的名称数组
    - public File[] listFiles():获取指定目录下的所有文件或者文件目录的File数组
  - File类的重命名功能
    - public boolean rename To(File dest):把文件重命名为指定的文件路径
  - File类的判断功能 
    - **public boolean isDirectory():判断是否是文件目录** 
    - **public boolean isFile():判断是否是文件** 
    - **public boolean exists():判断是否存在** 
    - public boolean canRead():判断是否可读 
    - public boolean canWrite():判断是否可写 
    - public boolean isHidden():判断是否隐藏
  - File类的创建功能
    - public boolean createNewFile():创建文件。若文件存在，则不创建，返回false
    - public boolean mkdir():创建文件目录。如果此文件目录存在，就不创建了。
      - 如果此文件目录的上层目录不存在，也不创建。
    - public boolean mkdirs():创建文件目录。如果上层文件目录不存在，一并创建
      - **注意事项：如果你创建文件或者文件目录没有写盘符路径，那么，默认在项目路径下。**
  - File类的删除功能
    - public boolean delete()：删除文件或者文件夹
    - 删除注意事项：
      - Java中的删除不走回收站。
      - 要删除一个文件目录，请注意该文件目录内不能包含文件或者文件目录

### 2.IO流原理及流的分类

- Java IO原理

  -  I/O是Input/Output的缩写，I/O技术是非常实用的技术，用于**处理设备之间的数据传输**。如读/写文件，网络通讯等。
  - Java程序中，对于数据的输入/输出操作以“**流(stream)**”的方式进行。
  - java.io包下提供了各种“流”类和接口，用以获取不同种类的数据，并通过**标准的方法**输入或输出数据。
  - 输入input：读取外部数据（磁盘、光盘等存储设备的数据）到程序（内存）中。
  - 输出output：将程序（内存）数据输出到磁盘、光盘等存储设备中。

- 流的分类

  - 按操作数据单位不同分为：字节流（8bit)，字符流（16bit)

  - 按数据流的流向不同分为：输入流，输出流

  - 按流的角色的不同分为：节点流，处理流

    | （抽象基类） |    字节流    | 字符流 |
    | :----------: | :----------: | :----: |
    |    输入流    | InputStream  | Reader |
    |    输出流    | OutputStream | Writer |

  - Java的IO流共涉及40多个类，实际上非常规则，都是从如上4个抽象基类派生的。

  - 由这四个类派生出来的子类名称都是以其父类名作为子类名后缀。

  - IO流体系

    | 分类       | 字节输入流           | 字节输出流            | 字符输入流        | 字符输出流         |
    | ---------- | -------------------- | --------------------- | ----------------- | ------------------ |
    | 抽象基类   | InputStream          | OutputStream          | Reader            | Writer             |
    | 访问文件   | FileInputStream      | FileOutputStream      | FileReader        | FileWriter         |
    | 访问数组   | ByteArrayInputStream | ByteArrayOutputStream | CharArrayReader   | CharArrayWriter    |
    | 访问管道   | PipedInputStream     | PipedOutputStream     | PipedReader       | PipedWriter        |
    | 访问字符串 |                      |                       | StringReader      | StringWriter       |
    | 缓冲流     | BufferedInputStream  | BufferedOutputStream  | BufferedReader    | BufferedWriter     |
    | 转换流     |                      |                       | InputStreamReader | OutputStreamWriter |
    | 对象流     | ObjectInputStream    | ObjectOutputStream    |                   |                    |
    |            | FilterInputStream    | FilterOutStream       | FilterReader      | FilterWriter       |
    | 打印流     |                      | PrintStream           |                   | PrintWriter        |
    | 推回输入流 | PushbackInputStream  |                       | PushbackReader    |                    |
    | 特殊流     | DataInputStream      | DataOutputStream      |                   |                    |

  - ##### 节点流和处理流

    - 节点流：直接从数据源或目的地读写数据
    - 处理流：不直接连接到数据源或目的地，而是“连接”在已存在的流（节点流或处理流)之上，通过对数据的处理为程序提供更为强大的读写功能。

  - ##### InputStream & Reader

    - InputStream和Reader是所有输入流的基类。
    - InputStream(典型实现：FilelnputStream)
      - int read()
      - int read(byte[] b)
      - int read(byte[] b,int off,int len)
    - Reader(典型实现：FileReader)
      - int read()
      - int read(char []c)
      - int read(char []c,int off,int len)
    - 程序中打开的文件IO资源不属于内存里的资源，垃圾回收机制无法回收该资源，所以应该**显式关闭文件IO资源。**
    - FilelnputStream 从文件系统中的某个文件中获得输入字节。FilelnputStream用于读取非文本数据之类的原始字节流。要读取字符流，需要使用FileReader

  - ##### InputStream

    - int read()
      - 从输入流中读取数据的下一个字节。返回0到255范围内的int字节值。如果因为已经到达流末尾而没有可用的字节，则返回值-1。
    - int read(byte[] b)
      - 从此输入流中将最多b.length个字节的数据读入一个byte数组中。如果因为已经到达流末尾而没有可用的字节，则返回值-1。否则**以整数形式返回实际读取的字节数**。
    - int read(byte[] b,int off,int len)
      - 将输入流中最多len个数据字节读入byte数组。尝试读取len个字节，但读取的字节也可能小于该值。以整数形式返回实际读取的字节数。如果因为流位于文件末尾而没有可用的字节，则返回值-1。
    - public void close()throws IOException
      - 关闭此输入流并释放与该流关联的所有系统咨源。

  - ##### Reader

    - int read()
      - 读取单个字符。作为整数读取的字符，范围在0到65535之间（0x00-0xfff）（2个字节的Unicode码），如果已到达流的末尾，则返回-1
    - int read(char[] cbuf)
      - 将字符读入数组。如果已到达流的末尾，则返回-1。否则返回本次读取的字符数。
    - int read(char[] cbuf,int off,int len)
      - 将字符读入数组的某一部分。存到数组cbuf中，从off处开始存储，最多读len个字符。如果已到达流的末尾，则返回-1。否则返回本次读取的字符数。
    - public void close()throws IOException
      - 关闭此输入流并释放与该流关联的所有系统资源。

  - ##### OutputStream & Writer

    - OutputStream和Writer也非常相似： 
      - void write(int b/int c); 
      - void write(byte[] b/char[] cbuf); 
      - void write(byte[] b/char] buff,int off,int len); 
      - void flush()； 
      - void close()；需要先刷新，再关闭此流 
    - 因为字符流直接以字符作为操作单位，所以Writer可以用字符串来替换字符数组， 即以String对象作为参数 
      - void write(String str)； 
      - void write(String str,int off,int len); 
    - FileOutputStream 从文件系统中的某个文件中获得输出字节。FileOutputStream 用于写出非文本数据之类的原始字节流。要写出字符流，需要使用FileWriter

- ##### OutPutStream

  - void write(int b)
    - 将指定的字节写入此输出流。write的常规协定是：向输出流写入一个字节。要写入的字节是参数b的八个低位。b的24个高位将被忽略。即写入0~255范围的。
  - void write(byte[]b)
    - 将b.length个字节从指定的byte数组写入此输出流。write(b)的常规协定是：应该与调用write(b，0,b.length）的效果完全相同。
  - void write(byte[] b,int off,int len)
    - 将指定byte数组中从偏移量off开始的len个字节写入此输出流。
  - public void flush()throws IOException
    - 刷新此输出流并强制写出所有缓冲的输出字节，调用此方法指示应将这些字节立即写入它们预期的目标
  - public void close()throws IOException
    - 关闭此输出流并释放与该流关联的所有系统资源。

- ##### Writer

  - void write(intc)
    - 写入单个字符。要写入的字符包含在给定整数值的16个低位中，16高位被忽略。即写入0到65535之间的Unicode码。
  - void write(char[] cbuf)
    - 写入字符数组。
  - void write(char[] cbuf,int off,int len)
    - 写入字符数组的某一部分。从off开始，写入len个字符void write(String str)写入字符串。
  - void write(String str,int off,int len)
    - 写入字符串的某一部分。
  - void flush()
    - 刷新该流的缓冲，则立即将它们写入预期目标。
  - public void close()throws IOException
    - 关闭此输出流并释放与该流关联的所有系统资源。

### 3.节点流（或文件流）

- #####  读取文件

  ```java
  //1.建立一个流对象，将已存在的一个文件加载进流。
  FileReader fr = new FileReader(new File("Test.txt"));
  //2.创建一个临时存放数据的数组。
  char[] ch = new char[1024];
  //3.调用流对象的读取方法将流中的数据读入到数组中。
  fr.read(ch);
  //4.关闭资源。
  fr.close();
  ```
  ```java
  FileReader fr = null; 
  try{ 
      fr = new FileReader(new File("c:\\test.txt"));
      char[] buf = new char[1024]; 
      int len;
      while((len=fr.read(buf))!=-1){ 
          System.out.print(new String(buf,e,1en));
      }catch(IOException e){ 
          System.out.println("read-Exception:"+e.getMessage());
      }finally{ 
          if(fr!=null){ 
              try{ 
                  fr.close();
              }catch(IOExceptione){ 
                  System.out.println("close-Exception:"+e.getMessage());
              }
          }
      }
  ```

- ##### 写入文件

  ```java
  //1.创建流对象，建立数据存放文件
  FileWriter fw = new FileWriter(new File("Test.txt"));
  //2.调用流对象的写入方法，将数据写入流
  fw.write("atguigu-songhongkang");
  //3.关闭流资源，并将流中的数据清空到文件中。
  fw.close();
  ```

  ```java
  FileWriter fw = null; 
  try{ 
      fw=new Filewriter(new File("Test.txt")); 
      fw.write("atguigu-songhongkang");
  }catch(IOException e){ 
      e.printStackTrace();
  }finally{ 
      if(fw!=nul1) 
          try{ 
              fw.close(); 
          }catch(IOException e){
              e.printStackTrace();
          }
  }
  ```

- ##### 注意点

  - 定义文件路径时，注意：可以用“/”或者“\\"。
  - 在写入一个文件时，如果使用构造器FileOutputStream(file)，则目录下有同名文件将被覆盖。
  - **如果使用构造器FileOutputStream(file,true),则目录下的同名文件不会被覆盖，在文件内容末尾追加内容。**
  - 在读取文件时，必须保证该文件已存在，否则报异常。
  - 字节流操作字节，比如：.mp3，.avi,.rmvb，mp4，.jpg，.doc，.ppt
  - 字符流操作字符，只能操作普通文本文件。最常见的文本文件：txt，.java，.c，.cpp等语言的源代码。尤其注意.doc，excel,ppt这些不是文本文件。

### 4.缓冲流

- 为了提高数据读写的速度，JavaAPI提供了带缓冲功能的流类，在使用这些流类时，会创建一个内部缓冲区数组，缺省使用8192个字节（8Kb)的缓冲区。

- 缓冲流要“套接”在相应的节点流之上，根据数据操作单位可以把缓冲流分为：

- BufferedlnputStream 和 BufferedOutputStream

- BufferedReader和BufferedWriter

- 当读取数据时，数据按块读入缓冲区，其后的读操作则直接访问缓冲区

- 当使用BufferedlnputStream读取字节文件时，BufferedlnputStream会一次性从文件中读取8192个（8Kb)，存在缓冲区中，直到缓冲区装满了，才重新从文件中读取下一个8192个字节数组。

- 向流中写入字节时，不会直接写到文件，先写到缓冲区中直到缓冲区写满，BufferedOutputStream才会把缓冲区中的数据一次性写到文件里。使用方法**flush()可以强制将缓冲区的内容全部写入输出流**

- 关闭流的顺序和打开流的顺序相反。只要关闭最外层流即可，关闭最外层流也会相应关闭内层节点流

- flush()方法的使用：**手动将bufer中内容写入文件**

- 如果是带缓冲区的流对象的close（）方法，不但会关闭流，还会在关闭流之前刷新缓冲区，关闭后不能再写出

  ```java
  BufferedReader br = null; 
  Bufferedwriter bw=null; 
  try{ 
      //创建缓冲流对象：它是处理流，是对节点流的包装 
      br=new BufferedReader(new FileReader("d:\\IOTest\\source.txt")); 
      bw=new Bufferedwriter(new FileWriter("d:\\IOTest\\dest.txt")); 
      String str; 
      while((str=br.readLine())!=nul1){//一次读取字符文本文件的一行字符 
          bw.write(str);//一次写入一行字符串
          bw.newLine();//写入行分隔符 
          bw.flush();//刷新缓冲区 
      }catch(IOException e){
          e.printstackTrace();
      }finally{ 
          //关闭IO流对象 
          try{ 
              if(bw!=null){
                  bw.close();//关闭过滤流时，会自动关闭它所包装的底层节点流
              } 
          }catch(IOException e){ 
              e.printStackTrace(); 
          } 
          try{ 
              if(brl=null){ 
                  br.close(); 
              } 
          }catch(IOException e){
              e.printstackTrace();
          }
      }
  }
  ```

### 5.转换流

- 转换流提供了在字节流和字符流之间的转换

- JavaAPI提供了两个转换流：

  - InputStreamReader:将InputStream转换为Reader
  - OutputStreamWriter:将Writer转换为OutputStream

- 字节流中的数据都是字符时，转成字符流操作更高效。

- 很多时候我们使用**转换流来处理文件乱码问题**。实现编码和解码的功能。

- ##### InputStreamReader 

  - **实现将字节的输入流按指定字符集转换为字符的输入流。**
  - 需要和InputStream“套接”。
  - 构造器 
    - public InputStreamReader(InputStream in) 
    - public InputSreamReader(InputStream in,String charsetName) 
    - 如：Reader isr=new InputStreamReader(System.in,'gbk");

- ##### OutputStreamWriter 

  - **实现将字符的输出流按指定字符集转换为字节的输出流**。
  - 需要和OutputStream“套接”。
  - 构造器 
    - public OutputStreamWriter(OutputStream out) 
    - public OutputSreamWriter(OutputStream out,String charsetName)

  ```java
  public void testMyInput()throws Exception{ 
      FileInputStream fis = new FileInputStream("dbcp.txt"); 
      FileoutputStream fos = new Fileoutputstream("dbcp5.txt"); 
      InputStreamReader isr = new InputStreamReader(fis,"GBK");
      OutputStreamwriter osw = new OutputStreamdriter(fos,"GBK"); 
      BufferedReader br = new BufferedReader(isr); 
      Bufferedwriter bw = new Bufferedwriter(osw); 
      String str = null; 
      while((str=br.readLine())!=null){ 
          bw.write(str);
          bw.newline();
          bw.flush();
      } 
      bw.close(); 
      br.close();
  }
  ```

- ##### 字符编码

  - 编码表的由来计算机只能识别二进制数据，早期由来是电信号。为了方便应用计算机，让它可以识别各个国家的文字。就将各个国家的文字用数字来表示，并一一对应，形成一张表。这就是编码表。
  - 常见的编码表
    - ASCIl:美国标准信息交换码。
      - 用一个字节的7位可以表示。
    - ISO8859-1:拉丁码表。欧洲码表
      - 用一个字节的8位表示。
    - GB2312：中国的中文编码表。最多两个字节编码所有字符
    - GBK：中国的中文编码表升级，融合了更多的中文文字符号。最多两个字节编码>
    - Unicode:国际标准码，融合了目前人类使用的所有字符。为每个字符分配唯一的字符码。所有的文字都用两个字节来表示。
    - UTF-8：变长的编码方式，可用1-4个字节来表示一个字符。
    - **在Unicode出现之前，所有的字符集都是和具体编码方案绑定在一起的（即字符集=编码方式）**，都是直接将字符和最终字节流绑定死了。
    - GBK等双字节编码方式，用最高位是1或0表示两个字节和一个字节。
    - Unicode不完美，这里就有三个问题，一个是，我们已经知道，英文字母只用一个字节表示就够了，第二个问题是如何才能区别Unicode和ASCll?计算机怎么知道两个字节表示一个符号，而不是分别表示两个符号呢？第三个，如果和GBK等双字节编码方式一样，用最高位是1或0表示两个字节和一个字节，就少了很多值无法用于表示字符，不够表示所有字符。Unicode在很长一段时间内无法推广，直到互联网的出现。
    - 面向传输的众多UTF（UCS Transfer Format)标准出现了，顾名思义，UTF-8就是每次8个位传输数据，而UTF-16就是每次16个位。这是为传输而设计的编码，并使编码无国界，这样就可以显示全世界上所有文化的字符了。
    - Unicode只是定义了一个庞大的、全球通用的字符集，并为每个字符规定了唯一确定的编号，具体存储成什么样的字节流，取决于字符编码方案。推荐的Unicode编码是UTF-8和UTF-16。
    - 编码：字符串->字节数组
    - 解码：字节数组->字符串
    - 转换流的编码应用
      - 可以将字符按指定编码格式存储
      - 可以对文本数据按指定编码格式来解读
      - 指定编码表的动作由构造器完成

### 6.标准输入，输出流

-  System.in和System.out分别代表了系统标准的输入和输出设备 

- 默认输入设备是：键盘，输出设备是：显示器 

- System.in的类型是InputStream 

- System.out的类型是PrintStream，其是OutputStream的子类 FilterOutputStream的子类 

- 重定向：通过System类的setln，setOut方法对默认设备进行改变。

  - public static void setin(InputStream in) 
  - public static void setOut(PrintStream out)

  ```java
  System.out.println("请输入信息（退出输入e或exit）:");
  //把“标准“输入流（键盘输入）这个字节流包装成字符流，再包装成缓冲流 
  BufferedReader br = new BufferedReader(new InputStreamReader(System.in)); 
  String s = null; 
  try{ 
      while((s=br.readLine())!=null){//读取用户输入的一行数据-->阻塞程序 
          if("e".equalsIgnoreCase(s) || "exit".equalsIgnoreCase(s)){ 
              System.out.println("安全退出！！");
              break; 
          }
          //将读取到的整行字符串转成大写输出 
          System.out.println("-->:"+s.toUpperCase());
          System.out.println("继续输入信息");
      } 
  }catch(IOException e){ 
      e.printStackTrace();
  }finally{ 
      try{ 
          if(br!=null){ 
              br.close();//关闭过滤流时，会自动关闭它包装的底层节点流 
          } 
      }catch(IOException e){ 
          e.printstackTrace();
      }
  }
  ```

### 7.打印流

- 实现将基本数据类型的数据格式转化为字符串输出

- 打印流：PrintStream和PrintWriter

  - 提供了一系列重载的print（）和println（）方法，用于多种数据类型的输出
  - PrintStream和PrintWriter的输出不会抛出IOException异常
  - PrintStream和PrintWriter有**自动flush功能**
  - PrintStream 打印的所有字符都使用平台的默认字符编码转换为字节。在需要写入字符而不是写入字节的情况下，应该使用PrintWriter类。
  - System.out返回的是PrintStream的实例

  ```java
  PrintStream ps = null;
  try{ 
      Fileoutputstream fos=new FileoutputStream(new File("D:\\IO\\text.txt"));
      //创建打印输出流，设置为自动刷新模式（写入换行符或字节‘\n’时都会刷新输出缓冲区） 
      ps = new Printstream(fos,true); 
      if（ps!=nu11){//把标准输出流（控制台输出）改成文件 
          System.setout(ps);
          for(int i = 0; i <= 255;i++){//输出ASCII字符 
              System.out.print((char)i);
              if(i%50==e){//每50个数据一行 
                  System.out.println();//换行 
              } 
          } 
      }catch(FileNotFoundException e){ 
          e.printStackTrace();
      }finally{ 
          if( ps ! = null){
              ps.close();
          }
      }
  ```

### 8.数据流

- 为了方便地操作Java语言的基本数据类型和String的数据，可以使用数据流。
- 数据流有两个类：（用于读取和写出基本数据类型、String类的数据） 
  - DatalnputStream和DataOutputStream 
  - 分别“套接”在InputStream和OutputStream子类的流上 
- DatalnputStream中的方法 
  - boolean readBoolean()
  - byte readByte() 
  - char readChar()
  - float readFloat()
  -  double readDouble()
  - short readShort() 
  - long readLong()
  - int readlnt() 
  - String readUTF()
  - void readFully(bytel] b) 
- DataOutputStream中的方法 
  - 将上述的方法的read改为相应的write即可。

```java
DataoutputStream dos = null; 
try {//创建连接到指定文件的数据输出流对象 
    dos = new DataoutputStream(new FileoutputStream("destData.dat"));
    dos.writeUTF("我爱北京天安门");//写UTF字符串 
    dos.writeBoolean(false);//写入布尔值 
    dos.writeLong(1234567890L);//写入长整数 
    System.out.println("写文件成功！");
}catch(IOException e){ 
    e.printstackTrace();
}finally{//关闭流对象 
    try{ 
        if(dos!=null){ 
            //关闭过滤流时，会自动关闭它包装的底层节点流 
            dos.close();
        }
    }catch(IOExceptione){ 
        e.printStackTrace();
    }
}

DataInputStream dis = null; 
try{ 
    dis = new DataInputStream(new FileInputStream("destData.dat"));
    String info = dis.readUTF(); 
    boolean flag = dis.readBoolean();
    long time = dis.readLong();
    System.out.println(info); 
    System.out.println(flag); 
    System.out.println(time); 
}catch(Exception e){
    e.printStackTrace();
}finally{ 
    if(dis != nul1){ 
        try{ 
            dis.close();
        }catch(IOException e){ 
            e.printStackTrace();
        }
    }
}
```

### 9.对象流

- ObjectInputStream和OjbectOutputSteam

  - 用于存储和读取**基本数据类型**数据或**对象**的处理流。它的强大之处就是可以把Java中的对象写入到数据源中，也能把对象从数据源中还原回来。

- 序列化：用ObjectOutputStream类**保存**基本类型数据或对象的机制

- 反序列化：用ObjectlnputStream类**读取**基本类型数据或对象的机制

- ObjectOutputStream和ObjectlnputStream不能序列化static和transient修饰的成员变量

- ##### 对象的序列化

  - **对象序列化机制**允许把内存中的Java对象转换成平台无关的二进制流，从而允许把这种二进制流持久地保存在磁盘上，或通过网络将这种二进制流传输到另一个网络节点。当其它程序获取了这种二进制流，就可以恢复成原来的Java对象
  - 序列化的好处在于可将任何实现了Serializable接口的对象转化为**字节数据**，使其在保存和传输时可被还原
  - 序列化是RMI（Remote Method Invoke-远程方法调用）过程的参数和返回值都必须实现的机制，而RMI是JavaEE的基础。因此序列化机制是JavaEE 平台的基础
  - 如果需要让某个对象支持序列化机制，则必须让对象所属的类及其属性是可序列化的，为了让某个类是可序列化的，该类必须实现如下两个接口之一。否则，会抛出NotSerializableException异常
    - Serializable
    - Externalizable
  - 凡是实现Serializable接口的类都有一个表示序列化版本标识符的静态变量：
  - private static final long serialVersionUID;
  - serialVersionUID用来表明类的不同版本间的兼容性。简言之，其目的是以序列化对象进行版本控制，有关各版本反序列化时是否兼容。
  - 如果类没有显示定义这个静态常量，它的值是Java运行时环境根据类的内部细节自动生成的。若类的实例变量做了修改，serialVersionUID可能发生变化。**故建议，显式声明。**
  - 简单来说，Java的序列化机制是通过在运行时判断类的serialVersionUID来验证版本一致性的。在进行反序列化时，JVM会把传来的字节流中的serialVersionUID与本地相应实体类的serialVersionUID进行比较，如果相同就认为是一致的，可以进行反序列化，否则就会出现序列化版本不一致的异常。（InvalidCastException)

- ##### 使用对象流序列化对象

  - 若某个类实现了Serializable接口，该类的对象就是可序列化的：

    - 创建一个ObjectOutputStream
    - 调用ObjectOutputStream对象的writeObject(对象）方法输出可序列化对象
    - 注意写出一次，操作flush(）一次

  - 反序列化

    - 创建一个ObjectinputStream
    - 调用readObject（)方法读取流中的对象

  - 强调：如果某个类的属性不是基本数据类型或String类型，而是另一个引用类型，**那么这个引用类型必须是可序列化的**，否则拥有该类型的Field的类也不能序列化

    ```java
    //序列化：将对象写入到磁盘或者进行网络传输。 
    //要求对象必须实现序列化
    ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("data.txt"));
    Person p = new Person("韩梅梅",18,"中华大街",new Pet());
    oos.writeObject(p);
    oos.flush();
    oos.close(); 
    
    //反序列化：将磁盘中的对象数据源读出。
    ObjectInputstream ois = new ObjectInputStream(new FileInputstream("data.txt"));
    Person p1 = (Person)ois.readObject();
    System.out.println(p1.tostring());
    ois.close();
    ```

  -  谈谈你对java.io.Serializable接口的理解，我们知道它用于序列化，是空方法接口，还有其它认识吗？

    - 实现了Serializable接口的对象，可将它们转换成一系列字节，并可在以后完全恢复回原来的样子。**这一过程亦可通过网络进行。这意味着序列化机制能自动补偿操作系统间的差异。**换句话说，可以先在Windows机器上创建一个对象，对其序列化，然后通过网络发给一台Unix机器，然后在那里准确无误地重新“装配”。不必关心数据在不同机器上如何表示，也不必关心字节的顺序或者其他任何细节。
    - 由于大部分作为参数的类如String、Integer等都实现了java.io.Serializable的接口，也可以利用多态的性质，作为参数使接口更灵活。

### 10.随机存取文件流

- ##### RandomAccessFile类

  - RandomAccessFile 声明在java.io包下，但直接继承于java.lang.Object类。并且它实现了Datalnput、DataOutput这两个接口，也就意味着这个类**既可以读也可以写**。
  - RandomAccessFile类支持“随机访问”的方式，程序可以直接跳到文件的任意地方来**读、写文件**
    - 支持只访问文件的部分内容
    - 可以向已存在的文件后追加内容
  - RandomAccessFile对象包含一个记录指针，用以标示当前读写处的位置。
  - RandomAccessFile类对象可以自由移动记录指针：
    - long getFilePointer():获取文件记录指针的当前位置
    - void seek(long pos):将文件记录指针定位到pos位置
  - 构造器
    - public RandomAccessFile(File file,String mode)
    - public RandomAccessFile(String name,String mede)
  - 创建 RandomAccessFile 类实例需要指定一个mode参数，该参数指定RandomAccessFile的访问模式：
    - r : 以只读方式打开
    - rw：打开以便读取和写入
    - rwd：打开以便读取和写入；同步文件内容的更新
    - rws：打开以便读取和写入；同步文件内容和元数据的更新
  - 如果模式为只读r。则不会创建文件，而是会去读取一个已经存在的文件，如果读取的文件不存在则会出现异常。如果模式为**rw读写。如果文件不存在则会去创建文件，如果存在则不会创建。**
  - 我们可以用RandomAccessFile这个类，来实现一个**多线程断点下载**的功能，用过下载工具的朋友们都知道，下载前都会建立**两个临时文件**，一个是与被下载文件大小相同的空文件，另一个是记录文件指针的位置文件，每次暂停的时候，都会保存上一次的指针，然后断点下载的时候，会继续从上一次的地方下载，从而实现断点下载或上传的功能，有兴趣的朋友们可以自己实现下。

- ##### 读取文件内容

  ```java
  RandomAccessFile raf = new RandomAccessFile("test.txt","rw");
  raf.seek(5);
  byte [] b = new byte[1024];
  int off = 0;
  int len = 5; 
  raf.read(b,off,len);
  String str = new String(b,0,len);
  System.out.println(str);
  raf.close();
  ```

- ##### 写入文件内容

  ```java
  RandomAccessFile raf = new RandomAccessFile("test.txt","rw");
  raf.seek(5);
  //先读出来 
  String temp = raf.readLine();
  raf.seek(5);
  raf.write("xyz".getBytes());
  raf.write(temp.getBytes());
  raf.close();
  
  RandomAccessFile rafl=new RandomAccessFile("hello.txt","rw"); 
  raf1.seek(5); 
  //方式一： 
  //StringBuilder info = new stringBuilder((int)file.length()); 
  //byte[] buffer = new byte[10]; 
  //int len; 
  //while((len=raf1.read(buffer))!=-1){ 
  ////info += new String(buffer,0,len); 
  //info.append(new String(buffer,e,len)); 
  //} //方式二：
  ByteArrayOutputStream baos = new ByteArrayOutputStream();
  byte[]buffer=new byte[10];
  int len;
  while((len=rafl.read(buffer))!=-1){ 
      baos.write(buffer,e,len);
  }
  raf1.seek(5); 
  rafl.write("xyz".getBytes());
  raf1.write(baos.tostring().getBytes());
  baos.close();
  raf1.close();
  ```

  - ##### 小结

    - 流是用来处理数据的。
    - 处理数据时，一定要先明确数据源，与数据目的地
      - 数据源可以是文件，可以是键盘。
      - 数据目的地可以是文件、显示器或者其他设备。
    - 而流只是在帮助数据进行传输，并对传输的数据进行处理，比如过滤处理、转换处理等。

### 11.NIO.2中Path，Paths,Files类的使用

- ##### Java NIO概述

  -  Java NIO(New IO,Non-Blocking IO)是从Java1.4版本开始引入的一套新的IO API，可以替代标准的Java IOAPl。NIO与原来的IO有同样的作用和目的，但是使用的方式完全不同，NIO支持面向缓冲区的（IO是面向流的）、基于通道的IO操作。**NIO将以更加高效的方式进行文件的读写操作**。
  - Java API中提供了两套NIO，**一套是针对标准输入输出NIO，另一套就是网络编程NIO**。
    - |---java.nio.channels.Channel
      - |-----FileChannel:处理本地文件
      - |----SocketChannel:TCP网络编程的客户端的Channel
      - |----ServerSocketChannel:TCP网络编程的服务器端的Channel
      - |-----DatagramChannel：UDP网络编程中发送端和接收端的Channel

- ##### NIO.2

  - 随着JDK7的发布，Java对NIO进行了极大的扩展，增强了对文件处理和文件系统特性的支持，以至于我们称他们为NIO.2。因为NIO提供的一些功能，NIO已经成为文件处理中越来越重要的部分。

- Path,Paths和Files使用

  - 早期的Java只提供了一个File类来访问文件系统，但File类的功能比较有限，所提供的方法性能也不高。而且，**大多数方法在出错时仅返回失败，并不会提供异常信息。**
  - NIO.2为了弥补这种不足，引入了Path接口，代表一个平台无关的平台路径，描述了目录结构中文件的位置。**Path可以看成是File类的升级版本，实际引用的资源也可以不存在**。
  - 在以前IO操作都是这样写的：
    - import java.io.File;
    - File file=new File("index.html");
  - 但在Java7中，我们可以这样写：
    - import java.nio.file.Path;
    - import java.nio.file.Paths;
    - Path path=Paths.get("index.html");
  - 同时，NIO.2在java.nio.file包下还提供了Files、Paths工具类，Files包含了大量静态的工具方法来操作文件；Paths则包含了两个返回Path的静态工厂方法。
  - Paths 类提供的静态get（）方法用来获取Path对象：
    - static Path get(String first,String..…more):用于将多个字符串串连成路径
    - static Path get(URI uri):返回指定uri对应的Path路径
  - Path常用方法：
    - String toString():返回调用Path对象的字符串表示形式
    - boolean startsWith(String path):判断是否以path路径开始
    - boolean endsWith(String path)：判断是否以path路径结束
    - boolean isAbsolute():判断是否是绝对路径
    - Path getParent():返回Path对象包含整个路径，不包含Path对象指定的文件路径
    - Path getRoot():返回调用Path对象的根路径
    - Path getFileName():返回与调用Path对象关联的文件名
    - int getNameCount（)：返回Path根目录后面元素的数量
    - Path getName(int idx):返回指定索引位置idx的路径名称
    - Path toAbsolutePath(）：作为绝对路径返回调用Path对象
    - Path resolve(Path p):合并两个路径，返回合并后的路径对应的Path对象
    - File toFile():将Path转化为File类的对象
  - java.nio.file.Files用于操作文件或目录的工具类。
  - Files常用方法： 
    - Path copy(Path src,Path dest,CopyOption..…how):文件的复制 
    - Path createDirectory(Path path,FileAttribute<?>...attr):创建一个目录 
    - Path createFile(Path path,FileAttribute<?>..…arr):创建一个文件 
    - void delete(Path path):删除一个文件/目录，如果不存在，执行报错
    - void deletelfExists(Path path):Path对应的文件/目录如果存在，执行删除 
    - Path move(Path src,Path dest,CopyOption...how):将src 移动到dest位置 
    - long size(Path path):返回path 指定文件的大小
  - Files常用方法：用于判断
    - boolean exists(Path path,LinkOption...opts):判断文件是否存在 
    - boolean isDirectory(Path path,LinkOption...opts):判断是否是目录 
    - boolean isRegularFile(Path path,LinkOption...opts):判断是否是文件 
    - boolean isHidden(Path path):判断是否是隐藏文件 
    - boolean isReadable(Path path):判断文件是否可读 
    - boolean isWritable(Path path):判断文件是否可写 
    - boolean notExists(Path path,LinkOption...opts):判断文件是否不存在
  - Files常用方法：用于操作内容 
    - SeekableByteChannel newByteChannel(Path path,OpenOption...how):获取与指定文件的连 接，how指定打开方式。
    - DirectoryStream<Path>newDirectoryStream(Path path):打开path指定的目录 
    - InputStream newlnputStream(Path path,OpenOption...how):获取InputStream对象 
    - OutputStream newOutputStream(Path path,OpenOption...how):获取OutputStream对象

## 第十二章：网络编程

### 1.网络编程概述

-  Java是Internet上的语言，它从语言级上提供了对网络应用程序的支持，程序员能够很容易开发常见的网络应用程序。

- Java提供的网络类库，可以实现无痛的网络连接，联网的底层细节被隐藏在Java的本机安装系统里，由JVM进行控制。并且Java实现了一个跨平台的网络库，**程序员面对的是一个统一的网络编程环境**。

- ##### 网络基础

  - ##### 计算机网络：

    - 把分布在不同地理区域的计算机与专门的外部设备用通信线路互连成一个规模大、功能强的网络系统，从而使众多的计算机可以方便地互相传递信息、共享硬件、软件、数据信息等资源。

  - ##### 网络编程的目的：

    - 直接或间接地通过网络协议与其它计算机实现数据交换，进行通讯。

  - ##### 网络编程中有两个主要的问题：

    - 如何准确地定位网络上一台或多台主机；定位主机上的特定的应用
    - 找到主机后如何可靠高效地进行数据传输

### 2.网络通信要素概述

- 如何实现网络中的主机互相通信

  - 通信双方地址
    - IP
    - 端口号
  - 一定的规则（即：网络通信协议。有两套参考模型）
    - OSI参考模型：模型过于理想化，未能在因特网上进行广泛推广
    - TCP/IP参考模型（或TCP/IP协议）：事实上的国际标准。

- 网络通信协议

  | OSI参考模型 | TCP/IP参考模型  | TCP/IP参考模型各层对应的协议 |
  | :---------- | --------------- | ---------------------------- |
  | 应用层      | 应用层          | HTTP，FTP，Telnet，DNS...    |
  | 表示层      |                 |                              |
  | 会话层      |                 |                              |
  | 传输层      | 传输层          | TCP,UDP...                   |
  | 网络层      | 网络层          | IP,ICMP,ARP...               |
  | 数据链路层  | 物理+数据链路层 | Link                         |
  | 物理层      |                 |                              |

  ![](.\数据的传输.JPG)

### 3.IP和端口号

- #####  IP地址：InetAddress

  - 唯一的标识Internet上的计算机（通信实体）
  - 本地回环地址（hostAddress)：127.0.0.1主机名（hostName)：localhost
  - IP地址分类方式1：IPV4和IPV6
    - IPV4：4个字节组成，4个0-255。大概42亿，30亿都在北美，亚洲4亿。2011年初已经用尽。以点分十进制表示，如192.168.0.1
    - IPV6：128位（16个字节），写成8个无符号整数，每个整数用四个十六进制位表示，数之间用冒号（：）分开，如：3ffe:3201：1401：1280：c8ff:fe4d:db39：1984
  - IP地址分类方式2：公网地址（万维网使用）和私有地址（局域网使用）。192.168.开头的就是私有址址，范围即为192.168.0.0--192.168.255.255，专门为组织机构内部使用
  - 特点：不易记忆

- #####  端口号标识正在计算机上运行的进程（程序）

  - 不同的进程有不同的端口号
  - 被规定为一个16位的整数0~65535。
  - 端口分类：
    - 公认端口：0~1023。被预先定义的服务通信占用（如：HTTP占用端口80，FTP占用端口21，Telnet占用端口23）
    - 注册端口：1024～49151。分配给用户进程或应用程序。（如：Tomcat占用端口8080，MySQL占用端口3306，Oracle占用端口1521等）。
    - 动态/私有端口：49152~65535。
  - 端口号与IP地址的组合得出一个网络套接字：Socket。

- ##### InetAddress类

  - Internet上的主机有两种方式表示地址：

    - 域名（hostName)：www.atguigu.com
    - IP 地址（hostAddress)：202.108.35.210

  - InetAddress类主要表示IP地址，两个子类：Inet4Address、Inet6Address。

  - InetAddress 类对象含有一个Internet主机地址的域名和IP地址：www.atguigu.com和202.108.35.210。

  - 域名容易记忆，当在连接网络时输入一个主机的域名后，域名服务器（DNS）负责将域名转化成IP地址，这样才能和主机建立连接。------域名解析

  - InetAddress类没有提供公共的构造器，而是提供了如下几个静态方法来获取InetAddress实例

    - public static InetAddress getLocalHost() 
    - public static InetAddress getByName(String host) 

  - InetAddress提供了如下几个常用的方法

    - public String getHostAddress():返回IP地址字符串（以文本表现形式）。
    - public String getHostName():获取此IP地址的主机名 
    - public boolean isReachable(int timeout):测试是否可以达到该地址

    ```java
    InetAddress address_1 = InetAddress.getByName("www.atguigu.com");
    System.out.println(address_1); 
    //获取InetAddress对象所含的域名 
    System.out.println(address_1.getHostName()); 
    //获取InetAddress对象所含的IP地址 
    System.out.println(address_1.getHostAddress()); 
    
    //获取本机的域名和IP地址。
    InetAddress address_2 = InetAddress.getLocalHost(); 
    System.out.println(address_2);
    ```

### 4.网络协议

- 网络通信协议

  - 计算机网络中实现通信必须有一些约定，即通信协议，**对速率、传输代码、代码结构、传输控制步骤、出错控制等制定标准。**

- 问题：网络协议太复杂

  - 计算机网络通信涉及内容很多，比如指定源地址和目标地址，加密解密，压缩解压缩，差错控制，流量控制，路由控制，如何实现如此复杂的网络协议呢?

- 通信协议分层的思想

  - 在制定协议时，把复杂成份分解成一些简单的成份，再将它们复合起来。最常用的复合方式是层次方式，**即同层间可以通信、上一层可以调用下一层，而与再下一层不发生关系。**各层互不影响，利于系统的开发和扩展。

- ##### TCP/IP协议簇

  - 传输层协议中有两个非常重要的协议：
    - 传输控制协议TCP（Transmission Control Protocol)
    - 用户数据报协议UDP（User Datagram Protocol)。
  - TCP/IP 以其两个主要协议：**传输控制协议（TCP）和网络互联协议（IP）**而得名，实际上是一组协议，包括多个具有不同功能且互为关联的协议。
  - IP(Internet Protocol)协议是网络层的主要协议，支持网间互连的数据通信。
  - TCP/IP协议模型从更实用的角度出发，形成了高效的四层体系结构，即**物理链路层、IP层、传输层和应用层。**

- ##### TCP和UDP

  - TCP协议：

    - 使用TCP协议前，须先建立TCP连接，形成传输数据通道
    - 传输前，采用“三次握手”方式，点对点通信，是可靠的
    - TCP协议进行通信的两个应用进程：客户端、服务端。
    - 在连接中可进行大数据量的传输
    - 传输完毕，需释放已建立的连接，效率低

  - UDP协议：

    - 将数据、源、目的封装成数据包，不需要建立连接
    - 每个数据报的大小限制在64K内
    - 发送不管对方是否准备好，接收方收到也不确认，故是不可靠的
    - 可以广播发送
    - 发送数据结束时无需释放资源，开销小，速度快

  - TCP三次握手

    ![](.\TCP三次握手.JPG)

  - TCP四次挥手

    ![](.\TCP四次挥手.JPG)

  - ##### Socket

    - 利用套接字（Socket）开发网络应用程序早已被广泛的采用，以至于成为事实上的标准。
    - 网络上具有唯一标识的IP地址和端口号组合在一起才能构成唯一能识别的标识符套接字。
    - 通信的两端都要有Socket，是两台机器间通信的端点。
    - 网络通信其实就是Socket间的通信。
    - Socket允许程序把网络连接当成一个流，数据在两个Socket间通过IO传输。
    - 一般主动发起通信的应用程序属客户端，等待通信请求的为服务端。
    - Socket分类：
      - 流套接字（stream socket)：使用TCP提供可依赖的字节流服务
      - 数据报套接字（datagram socket）：使用UDP提供“尽力而为”的数据报服务
    - Socket类的常用构造器：
      - public Socket(InetAddress address,int port)创建一个流套接字并将其连接到指定IP地址的指定端口号。
      - public Socket(String host,int port)创建一个流套接字并将其连接到指定主机上的指定端口号。
    - Socket类的常用方法：
      - public InputStream getlnputStream()返回此套接字的输入流。可以用于接收网络消息
      - public OutputStream getOutputStream()返回此套接字的输出流。可以用于发送网络消息
      - public InetAddress getlnetAddress()此套接字连接到的远程IP地址；如果套接字是未连接的，则返回null。
      - public InetAddress getlocalAddress()获取套接字绑定的本地地址。即本端的IP地址
      - public int getPort()此套接字连接到的远程端口号；如果尚未连接套接字，则返回0。
      - public int getlocalPort()返回此套接字绑定到的本地端口。如果尚未绑定套接字，则返回-1。即本端的端口号。
      - public void close()关闭此套接字。套接字被关闭后，便不可在以后的网络连接中使用（即无法重新连接或重新绑定）。需要创建新的套接字对象。关闭此套接字也将会关闭该套接字的InputStream和OutputStream。
      - public void shutdownlnput()如果在套接字上调用shutdownlnput（)后从套接字输入流读取内容，则流将返回EOF（文件结束符）。即不能在从此套接字的输入流中接收任何数据。
      - public void shutdownOutput()禁用此套接字的输出流。对于TCP套接字，任何以前写入的数据都将被发送，并且后跟TCP的正常连接终止序列。如果在套接字上调用shutdownOutput（)后写入套接字输出流，则该流将抛出IOException。即不能通过此套接字的输出流发送任何数据。

### 5.TCP网络编程

- Java语言的基于套接字编程分为服务端编程和客户端编程，其通信模型如图所示：

  ![](.\通信模型.JPG)

- ##### 基于Socket的TCP编程

  - 客户端Socket的工作过程包含以下四个基本的步骤：

    - 创建Socket：根据指定服务端的IP地址或端口号构造Socket类对象。若服务器端响应，则建立客户端到服务器的通信线路。若连接失败，会出现异常。
    - 打开连接到Socket的输入/出流：使用getlnputStream（）方法获得输入流，使用getOutputStream()方法获得输出流，进行数据传输
    - 按照一定的协议对Socket 进行读/写操作：通过输入流读取服务器放入线路的信息（但不能读取自己放入线路的信息），通过输出流将信息写入线程。
    - 关闭Socket：断开客户端到服务器的连接，释放线路

  - ##### 客户端创建Socket对象

    - 客户端程序可以使用Socket类创建对象，创建的同时会自动向服务器方发起连接。Socket的构造器是：
      - Socket（String host,int port)throws UnknownHostException,IOException:向服务器（域名是host。端口号为port）发起TCP连接，若成功，则创建Socket对象，否则抛出异常。
      - Socket(InetAddress address,int port)throws IOException:根据InetAddress对象所表示的IP地址以及端口号port发起连接。
    - 客户端建立socketAtClient对象的过程就是向服务器发出套接字连接请求

    ```java
    Socket s = new Socket("192.168.40.165", 9999);
    OutputStream out = s.getOutputStram();
    out.write("hello".getBytes());
    s.close();
    ```

  - ##### 服务器程序工作

    - 服务器程序的工作过程包含以下四个基本的步骤：
      - 调用ServerSocket(int port)：创建一个服务器端套接字，并绑定到指定端口上。用于监听客户端的请求。
      - 调用accept（)：监听连接请求，如果客户端请求连接，则接受连接，返回通信套接字对象。
      - 调用该Socket类对象的 getOutputStream()和getlnputStream（）：获取输出流和输入流，开始网络数据的发送和接收。
      - 关闭ServerSocket和Socket对象：客户端访问结束，关闭通信套接字。

  - 服务器建立ServerSocket对象

    - ServerSocket对象负责等待客户端请求建立套接字连接，类似邮局某个窗口中的业务员。也就是说，服务器必须事先建立一个等待客户请求建立套接字连接的ServerSocket对象。

    - 所谓“接收”客户的套接字请求，就是accept（）方法会返回一个Socket对象

      ```java
      ServerSocket ss = new ServerSocket(9999); 
      Sockets=ss.accept(); 
      InputStream in =s.getinputStream();
      byte[] buf=new byte[1024];
      int num=in.read(buf);
      String str=new String(buf,0,num); System.out.println(s.getlnetAddress().toString()+":"+str);
      s.close();
      ss.close();
      ```

### 6.UDP网络编程

- UDP网络通信

  - 类DatagramSocket和DatagramPacket实现了基于UDP协议网络程序。
  - UDP数据报通过数据报套接字DatagramSocket发送和接收，**系统不保证UDP数据报一定能够安全送到目的地，也不能确定什么时候可以抵达。**
  - DatagramPacket对象封装了UDP数据报，在数据报中包含了发送端的IP地址和端口号以及接收端的IP地址和端口号。
  - UDP协议中每个数据报都给出了完整的地址信息，因此无须建立发送方和接收方的连接。如同发快递包裹一样。

- ##### DatagramSocket类的常用方法

  - public DatagramSocket(int port)创建数据报套接字并将其绑定到本地主机上的指定端口。套接字将被绑定到通配符地址，IP地址由内核来选择。
  - public DatagramSocket(int port,InetAddress laddr)创建数据报套接字，将其绑定到指定的本地地址。本地端口必须在0到65535之间（包括两者）。如果IP地址为0.0.0.0，套接字将被绑定到通配符地址，IP地址由内核选择。
  - public void close()关闭此数据报套接字。
  - public void send(DatagramPacket p)从此套接字发送数据报包。DatagramPacket包含的信息指示：将要发送的数据、其长度、远程主机的IP地址和远程主机的端口号。
  - public void receive(DatagramPacket p)从此套接字接收数据报包。当此方法返回时，DatagramPacket的缓冲区填充了接收的数据。数据报包也包含发送方的IP地址和发送方机器上的端口号。此方法在接收到数据报前一直阻塞。数据报包对象的length字段包含所接收信息的长度。如果信息比包的长度长，该信息将被截短。
  - public InetAddress getlocalAddress()获取套接字绑定的本地地址。
  - public int getLocalPort()返回此套接字绑定的本地主机上的端口号。
  - public InetAddress getlnetAddress()返回此套接字连接的地址。如果套接字未连接，则返回null。
  - public int getPort()返回此套接字的端口。如果套接字未连接，则返回-1。
  - public DatagramPacket(byte[] buf,int length)构造 DatagramPacket，用来接收长度为length的数据包。length参数必须小于等于buf.length。
  - public DatagramPacket(byte[] bufint length,lnetAddress address,int port)构造数据报包，用来将长度为length的包发送到指定主机上的指定端口号。length参数必须小于等于buf.length。
  - public InetAddress getAddress()返回某台机器的IP地址，此数据报将要发往该机器或者是从该机器接收到的。
  - public int getPort()返回某台远程主机的端口号，此数据报将要发往该主机或者是从该主机接收到的。
  - public byte[]getData()返回数据缓冲区。接收到的或将要发送的数据从缓冲区中的偏移量offset处开始，持续length长度。
  - public int getLength()返回将要发送或接收到的数据的长度。

- ##### UDP网络通信

  - 流程：

    - 1.DatagramSocket与DatagramPacket
    - 2.建立发送端，接收端
    - 3.建立数据包
    - 4.调用Socket的发送、接收方法
    - 5.关闭Socket

  - 发送端与接收端是两个独立的运行程序

  - 发送端

    ```java
    DatagramSocket ds = null; 
    try { 
        ds = new DatagramSocket();
        byte[] by = "hello,atguigu.com".getBytes(); 
        DatagramPacket dp = new DatagramPacket(by,e,by.1ength, InetAddress.getByName("127.0.0.1"),10000);
        ds.send(dp);
    }catch(Exception e){ 
        e.printStackTrace();
    }finally{ 
        if(ds!=nul1) 
            ds.close();
    }
    ```

  - 接收端

    - 在接收端，要指定监听的端口

      ```java
      DatagramSocket ds = null; 
      try{ 
          ds = new DatagramSocket(10000);
          byte[] by = new byte[1024]; 
          DatagramPacket dp = new DatagramPacket(by,by.1ength); 
          ds.receive(dp);
          String str=new String(dp.getData(),e,dp.getLength());
          System.out.println(str +"--"+dp.getAddress());
      }catch(Exception e){ 
          e.printStackTrace();
      }finally{ 
          if(ds!=null) 
              ds.close();
      }
      ```

### 7.URL编程

- URL类

  - URL(Uniform Resource Locator):统一资源定位符，它表示Internet上某一资源的地址。
  - 它是一种具体的URI，即URL可以用来标识一个资源，而且还指明了如何locate这个资源。
  - 通过URL我们可以访问Internet上的各种网络资源，比如最常见的www，ftp站点。浏览器通过解析给定的URL可以在网络上查找相应的文件或其他资源。
  - URL的基本结构由5部分组成：
    - <传输协议>：<主机名>：<端口号><文件名>#片段名？参数列表
    - 例如：http://192.168.1.100:8080/helloworld/index.jsp#a?username=shkstart&password=123>
    - \#片段名：即锚点，例如看小说，直接定位到章节
    - 参数列表格式：参数名=参数值&参数名=参数值.…

- URL类构造器

  - 为了表示URL，java.net中实现了类URL。我们可以通过下面的构造器来初 始化一个URL对象： 
    - public URL(String spec):通过一个表示URL地址的字符串可以构造一个URL对象。
      - 例 如：URL url=new URL(""http://www.atguigu.com/"); 
    - public URL(URL context,String spec):通过基URL和相对URL构造一个URL对象。
      - 例如：URL downloadUrl=new URL(url,“download.html") 
    - public URL(String protocol,String host,String file);
      - 例如：new URL("http", "www.atguigu.com"，“download.html")； 
    - public URL(String protocol,String host,int port,String file);
      - 例如：URL gamelan=new URL("http","www.atguigu.com",80,“download.html");
  -  URL类的构造器都声明抛出非运行时异常，必须要对这一异常进行处理，通常是用try-catch 语句进行捕获。

- ##### URL类常用的方法

  - 一个URL对象生成后，其属性是不能被改变的，但可以通过它给定的方法来获取这些属性：
    - public String getProtocol()获取该URL的协议名
    - public String getHost()获取该URL的主机名
    - public String getPort()获取该URL的端口号
    - public String getPath()获取该URL的文件路径
    - public String getFile()获取该URL的文件名
    - public String getQuery()获取该URL的查询名

- ##### URLConnection类

  -  URL的方法openStream（）：能从网络上读取数据
  - 若希望输出数据，例如向服务器端的CGl（公共网关接口-Common GatewayInterface-的简称，是用户浏览器和服务器端的应用程序进行连接的接口）程序发送些数据，则必须先与URL建立连接，然后才能对其进行读写，此时需要使用URLConnection。
  - URLConnection:表示到URL所引用的远程对象的连接。当与一个URL建立连接时，首先要在一个URL对象上通过方法openConnection（）生成对应的URLConnection对象。如果连接过程失败，将产生IOException.
    - URL netchinaren=new URL("http://www.atguigu.com/index.shtml");
    - URLConnectonn u=netchinaren.openConnection()；
  - 通过URLConnection对象获取的输入流和输出流，即可以与现有的CGI 程序进行交互。
    - public Object getContent()throws IOException 
    - public int getContentLength() 
    - public String getContentType() 
    - public long getDate() 
    - public long getLastModified() 
    - public InputStream getlnputStream()throws IOException 
    - public OutputSteram getOutputStream()throws IOException

- ##### URI，URL和URN的区别

  - URI,是uniform resource identifier，统一资源标识符，用来唯一的标识一个资源。
  - URL是uniform resource locator，统一资源定位符，它是一种具体的URI，即URL可以用来标识一个资源，而且还指明了如何locate这个资源。
  - 而URN，uniform resource name，统一资源命名，是通过名字来标识资源，比如mailto:javanet@java.sun.com。
  - 也就是说，URI是以一种抽象的，高层次概念定义统一资源标识，而URL和URN则是具体的资源标识的方式。URL和URN都是一种URI。
  - 在Java的URl中，一个URI实例可以代表绝对的，也可以是相对的，只要它符合URI的语法规则。而URL类则URI不仅符合语义，还包含了定位该资源的信息，因此它不能是相对的。

- ##### 小结

  -  位于网络中的计算机具有唯一的IP地址，这样不同的主机可以互相区分。
  - 客户端一服务器是一种最常见的网络应用程序模型。服务器是一个为其客户端提供某种特定服务的硬件或软件。客户机是一个用户应用程序，用于访问某台服务器提供的服务。端口号是对一个服务的访问场所，它用于区分同一物理计算机上的多个服务。套接字用于连接客户端和服务器，客户端和服务器之间的每个通信会话使用一个不同的套接字。TCP协议用于实现面向连接的会话。
  - Java中有关网络方面的功能都定义在java.net程序包中。Java用InetAddress对象表示IP地址，该对象里有两个字段：主机名（String）和IP地址（int）。
  - 类Socket和ServerSocket 实现了基于TCP协议的客户端一服务器程序。Socket是客户端和服务器之间的一个连接，连接创建的细节被隐藏了。这个连接提供了一个安全的数据传输通道，这是因为TCP协议可以解决数据在传送过程中的丢失、损坏、重复、乱序以及网络拥挤等问题，它保证数据可靠的传送。
  - 类URL和URLConnection 提供了最高级网络应用。URL的网络资源的位置来同一表示Internet 上各种网络资源。通过URL对象可以创建当前应用程序和URL表示的网络资源之间的连接，这样当前程序就可以读取网络资源数据，或者把自己的数据传送到网络上去。

## 第十三章：Java反射机制

### 1.Java反射机制概述

- ##### Java Reflection

  -  Reflection(反射）是被视为**动态语言**的关键，反射机制允许程序在执行期借助于Reflection API取得任何类的内部信息，并能直接操作任意对象的内部属性及方法。
  - 加载完类之后，在堆内存的方法区中就产生了一个Class类型的对象（一个类只有一个Class对象），这个对象就包含了完整的类的结构信息。我们可以通过这个对象看到类的结构。**这个对象就像一面镜子，透过这个镜子看到类的结构，所以，我们形象的称之为：反射。**
  - 正常方式：
    - 引入需要的“包类”名称->通过new实例化->取得实例化对象
  - 反射方式：
    - 实例化对象->getClass()方法->得到完整的“包类”名称

- ##### 动态语言vs静态语言

  - 动态语言
    - 是一类在运行时可以改变其结构的语言：例如新的函数、对象、甚至代码可以被引进，已有的函数可以被删除或是其他结构上的变化。通俗点说就是**在运行时代码可以根据某些条件改变自身结构**。
      主要动态语言：Object-C、C#、JavaScript、PHP、Python、Erlang。
    - 静态语言与动态语言相对应的，运行时结构不可变的语言就是静态语言。如Java、C、C++。
    - Java不是动态语言，但Java可以称之为“准动态语言”。即Java有一定的动态性，我们可以利用反射机制、字节码操作获得类似动态语言的特性。Java的动态性让编程的时候更加灵活！

- ##### Java反射机制提供的功能

  - 在运行时判断任意一个对象所属的类
  - 在运行时构造任意一个类的对象
  - 在运行时判断任意一个类所具有的成员变量和方法
  - 在运行时获取泛型信息
  - 在运行时调用任意一个对象的成员变量和方法
  - 在运行时处理注解
  - 生成动态代理

- 反射相关的主要API

  - java.lang.Class:代表一个类 
  - java.lang.reflect.Method:代表类的方法 
  - java.lang.reflect.Field:代表类的成员变量 
  - java.lang.reflect.Constructor:代表类的构造器
  - ...

### 2.理解Class类并获取Class的实例

- ##### class类
  - 在Object类中定义了以下的方法，此方法将被所有子类继承：

  - public final Class getClass()

  - 以上的方法返回值的类型是一个Class类，此类是Java反射的源头，实际上所谓反射从程序的运行结果来看也很好理解，即：可以通过对象反射求出类的名称。

  - 对象照镜子后可以得到的信息：某个类的属性、方法和构造器、某个类到底实现了哪些接口。对于每个类而言，JRE都为其保留一个不变的Class类型的对象。一个Class对象包含了特定某个结构（class/interface/enum/annotation/primitive type/void/D)的有关信息。

    - Class本身也是一个类
    - Class对象只能由系统建立对象
    - 一个加载的类在JVM中只会有一个Class实例
    - 一个Class对象对应的是一个加载到JVM中的一个.class文件
    - 每个类的实例都会记得自己是由哪个Class实例所生成
    - 通过Class可以完整地得到一个类中的**所有被加载的结构**
    - Class类是Reflection的根源，针对任何你想动态加载、运行的类，**唯有先获得相应的Class对象**

  - Class类的常用方法

    | 方法名                                             | 功能说明                                                     |
    | -------------------------------------------------- | ------------------------------------------------------------ |
    | static Class forName(String name)                  | 返回指定类名name的Class对象                                  |
    | Object newInstance()                               | 调用缺省构造参数，返回该Class对象的一个实例                  |
    | getName()                                          | 返回此Class对象所表示的实体（类，接口，数组类，基本类型或void）名称 |
    | Class getSuperClass()                              | 返回当前Class对象的父类的Class对象                           |
    | Class [] getInterfaces()                           | 获取当前Class对象的接口                                      |
    | ClassLoader getClassLoader()                       | 返回该类的类加载器                                           |
    | Class getSuperclass()                              | 返回表示此Class锁表是的实体的超类的Class                     |
    | Constructor[] getConstructors()                    | 返回一个包含某些Constructor对象                              |
    | Field[] getDeclaredFields()                        | 返回Field对象的一个数组                                      |
    | Method getMethod(String name, Class... paramTypes) | 返回以Method对象，此对象的形参类型为paramType                |

  - ##### 获取Class类的实例(四种方法)

    - 前提：若已知具体的类，通过类的class属性获取，该方法最为安全可靠，程序性能最高
      - 实例：Class clazz=String.class；
    - 前提：已知某个类的实例，调用该实例的getClass（）方法获取Class对象
      - 实例：Class clazz=“www.atguigu.com".getClass()；
    - 前提：已知一个类的全类名，且该类在类路径下，可通过Class类的静态方法forName（）获取，可能抛出ClassNotFoundException
      - 实例：Class clazz=Class.forName(“java.lang.String")；
    - 其他方式（不做要求）
      - ClassLoader cl=this.getClass().getClassLoader()；
      - Class clazz4=cl.loadClass(“类的全类名”）；

  - ##### 那些类型可以有Class对象

    -  (1）class:

      - 外部类，成员（成员内部类，静态内部类），局部内部类，匿名内部类

    - （2）interface:接口

    - （3)[]:数组

    - （4）enum:枚举

    - （5）annotation:注解@interface

    - （6）primitive type:基本数据类型

    - （7）void

      ```java
      Class c1=Object.class;
      Class c2=Comparable.class;
      Class c3=String[].class;
      Class c4=int[][].class; 
      Class c5=ElementType.class; 
      Class c6=Override.class; 
      Class c7=int.class;
      Class c8=void.class;
      Class c9=Class.class;
      int[] a=new int[1e]; 
      int[]b=new int[100]; 
      Class c10=a.getClass();
      Class cl1=b.getClass();
      //只要元素类型与维度一样，就是同一个Class 
      System.out.println(c10==c11);
      ```

### 3.类的加载与ClassLoader的理解

- ##### 类的加载过程

  - 类的加载

    - 将类的class文件读入内存，并为之创建一个java.lang.Class对象。此过程由类加载器完成

  - 类的链接

    - 将类的二进制数据合并到JRE中

  - 类的初始化

    - JVM负责对类进行初始化

  -  加载：将class文件字节码内容加载到内存中，并将这些静态数据转换成方法区的运行时数据结构，然后生成一个代表这个类的java.lang.Class对象，作为方法区中类数据的访问入口（即引用地址）。所有需要访问和使用类数据只能通过这个Class对象。这个加载的过程**需要类加载器**参与。

  - 链接：将Java类的二进制代码合并到JVM的运行状态之中的过程。

    - 验证：确保加载的类信息符合JVM规范，例如：以cafe开头，没有安全方面的问题
    - 准备：正式为类变量（static)分配内存并**设置类变量默认初始值**的阶段，这些内存都将在方法区中进行分配。
    - 解析：虚拟机常量池内的符号引用（常量名）替换为直接引用（地址）的过程。

  - 初始化：

    - 执行类构造器<clinit>）方法的过程。类构造器<clinit>（）方法是由编译期自动收集类中所有类变量的赋值动作和静态代码块中的语句合并产生的。（类构造器是构造类信息的，不是构造该类对象的构造器）。

    - 当初始化一个类的时候，如果发现其父类还没有进行初始化，则需要先触发其父类的初始化。

    - 虚拟机会保证一个类的<clinit>（）方法在多线程环境中被正确加锁和同步。

      ```java
      public class ClassLoadingTest{
          public static void main(String[] args){
              System.out.println(A.m);
          }
      }
      class A{
          static{
              m=300;
          }
          static int m=100;
      }
      //第二步：链接结束后m=0
      //第三步：初始化后，m的值由<clinit>()方法执行决定
      //这个A的类构造器<c1init>()方法由类变量的赋值和静态代码块中的语句按照顺序合并产生，类似于
      //<clinit>(){
      //m=300;
      //m=100;
      //}
      ```

  - ##### 什么时候会发生类的初始化

    - 类的主动引用（一定会发生类的初始化）
      - 当虚拟机启动，先初始化main方法所在的类
      - new一个类的对象
      - 调用类的静态成员（除了final常量）和静态方法
      - 使用java.lang.reflect包的方法对类进行反射调用
      - 当初始化一个类，如果其父类没有被初始化，则先会初始化它的父类
    - 类的被动引用（不会发生类的初始化）
      - 当访问一个静态域时，只有真正声明这个域的类才会被初始化
        - 当通过子类引用父类的静态变量，不会导致子类初始化
      - 通过数组定义类引用，不会触发此类的初始化
      - 引用常量不会触发此类的初始化（常量在链接阶段就存入调用类的常量池中了）

  - ##### 类加载器的作用

    -  类加载的作用：将class文件字节码内容加载到内存中，并将这些静态数据**转换成方法区的运行时数据结构**，然后在堆中生成一个代表这个类的java.lang.Class对象，作为方法区中类数据的访问入口。
    - 类缓存：标准的JavaSE类加载器可以按要求查找类，但一旦某个类被加载到类加载器中，它将维持加载（缓存）一段时间。不过JVM垃圾回收机制可以回收这些Class对象。

  - ClassLoader

    ![](.\类加载器.JPG)

    ```java
    //1.获取一个系统类加载器 
    ClassLoader classloader = ClassLoader.getSystemClassLoader(); 
    System.out.printin(classloader); 
    //2.获取系统类加载器的父类加载器，即扩展类加载器 
    classloader=classloader.getParent(); 
    System.out.printin(classloader); 
    //3.获取扩展类加载器的父类加载器，即引导类加载器 
    classloader=classloader.getParent(); 
    System.out.printin(classloader); 
    //4.测试当前类由哪个类加载器进行加载 
    classloader = Class.forName("exer2.ClassloaderDemo").getClassLoader(); System.out.println(classloader);
    //5.测试JDK提供的Object类由哪个类加载器加载 
    classloader = Class.forName("java.lang.Object").getClassLoader(); System.out.println(classloader); 
    //6.关于类加载器的一一个主要方法: getResourceAsStream(String str):获取类路 径下的指定文件的输入流 
    InputStream in = null; 
    in = this.getClass().getClassLoader().getResourceAsStream("exer2\test.properties"); System.out.println(in);
    ```

### 4.创建运行时类的对象

- #####  有了 Class对象,能做什么?

  - 创建类的对象:调用Classnewlnstance对象的()方法

    - 要求:
      - 1)类必须有一个无参数的构造器。
      - 2)类的构造器的访问权限需要足够。

  - 难道没有无参的构造器就不能创建对象了吗?

  - 不是!只要在操作的时候明确的调用类中的构造器,并将参数传递进去之后,才可以实例化操作步骤如下:

    - 1)通过ClassgetDeclared类的 Constructor(Class.. parameterTypes)取得本类的指定形参类型的构造器
    - 2)向构造器的形参中传递一个对象数组进去,里面包含了构造器中所需的各个参数。
    - 3)通过Constructor实例化对象。

    ```java
    //1.根据全类名获取对应的Class对象 
    String name ="atguigu.java. Person"; 
    Class clazz = null;
    clazz = Class.forName(name); 
    //2.调用指定参数结构的构造器,生成 Constructor的实例 
    Constructor con = clazz.getConstructorString.class, Integer.class); 
    //3.通过Constructor的实例创建对应类的对象,并初始化类属性 
    Person p2 = (Person) con.newlnstance("Peter", 20); 
    System.out.printin(p2);
    ```

### 5.获取运行时类的完整结构

- ##### 通过反射获取运行时类的完整结构

  - Field、 Method、 Constructor、 Superclass、 Interface、 Annotation
  - 实现的全部接口
  - 所继承的父类
  - 全部的构造器
  - 全部的方法
  - 全部的Field

- 使用反射可以取得:

  - 实现的全部接口
    - public Class<?>[] getInterfaces()确定此对象所表示的类或接口实现的接口。
  - 所继承的父类
    - public Class<? Super> getSuperclass()返回表示此 Class所表示的实体(类、接口、基本类型)的父类的Class.
  - 全部的构造器 
    - public Constructor<t>[] getConstructors() 返回此 Class对象所表示的类的所有 **public**构造方法。
    - public Constructor<T>[] getDeclaredConstructors() 返回此 Class对象表示的类**声明**的所有构造方法。
    - Constructor类中:
      - 取得修饰符: public int getModifiers(); 
      - 取得方法名称: public String getName(); 
      - 取得参数的类型: public Class<?> getParameterTypes();
  - 全部的方法 
    - public Method[] getDeclaredMethods() 
    - 返回此 Class对象所表示的类或接口的全部方法 
    - public Method[] getMethods() 
    - 返回此 Class对象所表示的类或接口的public的方法 
    - Method类中:
      - public Class<?> getReturnType()取得全部的返回值 
      - public Class<?> getParameterTypes()取得全部的参数 
      - public int getModifiers()取得修饰符
      - public Class<?> getException Types()取得异常信息
  - 全部的Field 
    - public Field[] getFields() 
    - 返回此 ClasspublicField对象所表示的类或接口的的.
    - public Field[] getDeclared Fields() 
    - 返回此 ClassField对象所表示的类或接口的全部 
    - Field方法中:
      - public int getModifiers()以整数形式返回此Field的修饰符 
      - public Class<?> getType()得到Field的属性类型
      - public String getName()返回Field的名称
  - Annotation相关 
    - get Annotation(Class<t> annotationClass) 
    - getDeclaredAnnotations() 
  - 泛型相关 
    - 获取父类泛型类型: Type getGenericSuperclass() 
    - 泛型类型: ParameterizedType 
    - 获取实际的泛型类型参数数组: getActualTypeArguments 
  - 类所在的包 Package getPackage()

- 小结:

  - 在实际的操作中，取得类的信息的操作代码，并不会经常开发。  
  - 一 定要熟悉java.lang.reflect包的作用， 反射机制。

  - 如何取得属性、方法、构造器的名称，修饰符等。

### 6.调用运行时类的指定结构

- 调用指定方法
  - 通过反射，调用类中的方法，通过Method类完成。步骤:
    - 1.通过Class类的getMethod(String name,Class...parameterTypes)方法取得一个Method对象，并设置此方法操作时所需要的参数类型。
    - 2.之后使用Object invoke(Object obj, Object[] args)进行调用，并向方法中传递要设置的obj对象的参数信息。
  - Object invoke(Object obj, Object... args)
    - 说明:
      - Object对应原方法的返回值,若原方法无返回值,此时返回null
      - 若原方法若为静态方法,此时形参Object obj可为null
      - 若原方法形参列表为空,则 Object [] args为null
      - 若原方法声明为private,则需要在调用此invoke()方法前,显式调用方法对象的 setAccessible(true)方法,将可访问 private的方法。
- 调用指定属性
  - 在反射机制中,可以直接通过 Field类操作类中的属性,通过 Field类提供的set()和get()方法就可以完成设置和取得属性内容的操作。
  - public Field getField(String name)返回此lass对象表示的类或接口的指定的public的Field
  - public Field getDeclaredField(String name)返回此Class对象表示的类或接口的指定的 Field
  - 在 Field中:
    - public Object get(Object obj)取得指定对象obj上此 Field的属性内容
    - public void set(Object obj, Object value)设置指定对象obj上此 Field的属性内容
- 关于setAccessible方法的使用
  - MethodField和、 ConstructorsetAccessible对象都有()方法
  - setAccessible启动和禁用访问安全检查的开关
  - 参数值为true则指示反射的对象在使用时应该取消Java语言访问检查。
    - 提高反射的效率。如果代码中必须用反射,而该句代码需要频繁的被调用,那么请设置为true
    - 使得原本无法访问的私有成员也可以访问
  - 参数值为 false则指示反射的对象应该实施Java语言访问检查。

### 7.反射的应用：动态代理

- 代理设计模式的原理:

  - 使用一个代理将对象包装起来,然后用该代理对象取代原始对象。任何对原始对象的调用都要通过代理。代理对象决定是否以及何时将方法调用转到原始对象上。
  - 之前为大家讲解过代理机制的操作,属于静态代理,特征是代理类和目标对象的类都是在编译期间确定下来,不利于程序的扩展。同时,每一个代理类只能为一个接口服务,这样一来程序开发中必然产生过多的代理。最好可以通过一个代理类完成全部的代理功能。
  - 动态代理是指客户通过代理类来调用其它对象的方法,并且是在程序运行时根据需要动态创建目标类的代理对象。
  - 动态代理使用场合:    
    - 调试
    - 远程方法调用
  - 动态代理相比于静态代理的优点:
    - 抽象角色中(接口)声明的所有方法都被转移到调用处理器一个集中的方法中处理,这样,我们可以更加灵活和统一的处理众多的方法。

- Java动态代理相关API

  - Proxy:专门完成代理的操作类，是所有动态代理类的父类。通过此类为一个或多个接口动态地生成实现类。

  - 提供用于创建动态代理类和动态代理对象的静态方法

    - static Class<?>getProxyClass(ClassLoader loader,Class<?>...interfaces)创建一个动态代理类所对应的Class对象
    - static Object newProxylnstance(ClassLoader loader,Class<?>l interfaces,InvocationHandler h)直接创建一个动态代理对象个
    - loader 类加载器
    - interfaces 得到被代理类实现的全部接口
    - 得到InvocationHandler 接口的实现类实例

  - 动态代理步骤

    - 创建一个实现接口InvocationHandler的类，它必须实现invoke方法，以完成代理的具体操作。

      ```java
      // theProxy 代理类的对象
      // method 要调用的方法
      // params 方法调用时所需要的参数
      public Object invoke(Object theProxy, Method method, Object[] params) throws Throwable{
          try{
              Object retval = method.invoke(targetObj,params);
              System.out.println(retval);
              return retval;
          }catch(Exception exc){
              
          }
      }
      ```

    - 创建被代理的类以及接口

    - 通过Proxy的静态方法 

    - newProxylnstance(ClassLoader loader,Classl interfaces,InvocationHandler h)创建 一个Subject接口代理 

      - RealSubject target=new RealSubject()； 
      - // Create a proxy to wrap the original implementation 
      - DebugProxy proxy=new DebugProxy(target)； 
      - // Get a reference to the proxy through the Subject interface 
      - Subject sub=(Subject)Proxy.newProxylnstance( 
      - Subject.class.getClassLoader(),new Class]{ Subject.class },proxy);

    - 通过Subject代理调用RealSubject实现类的方法

  - 动态代理和AOP

    - 使用Proxy生成一个动态代理时，往往并不会凭空产生一个动态代理，这样没有太大的意义。通常都是为指定的目标对象生成动态代理
    - 这种动态代理在AOP中被称为AOP代理，AOP代理可代替目标对象，AOP代理包含了目标对象的全部方法。但AOP代理中的方法与目标对象的方法存在差异：AOP代理里的方法可以在执行目标方法之前、之后插入一些通用处理

## 第十四章：Java8的其他新特性

### 1.Lambda表达式

- ##### 为什么使用Lambda表达式

  - Lambda是一个匿名函数，我们可以把Lambda表达式理解为是一段可以传递的代码（将代码像数据一样进行传递）。使用它可以写出更简洁、更灵活的代码。作为一种更紧凑的代码风格，使Java的语言表达能力得到了提升。

- ##### 从匿名类到Lambda的转换举例

  ```java
  //匿名内部类
  Runnable r1 = new Runnable(){
      @Override
      public void run(){
          System.out.println("Hello World!");
      }
  }
  
  //Lambda表达式
  Runnable r1 = () -> System.out.println("Hello Lambda!");
  
  //原来使用匿名内部类作为参数传递 
  TreeSet<String> ts = new TreeSet<>(new Comparator<String>(){ 
      @Override public int compare(String ol,String o2){ 
          return Integer.compare(ol.1ength(),o2.length());
      }
  });
  
  //Lambda 表达式作为参教传递 
  TreeSet<String>ts2=new TreeSet<>( (o1,o2)->Integer.compare(o1.1ength(),o2.1ength()));
  ```

- 语法

  - Lambda表达式：在Java8语言中引入的一种新的语法元素和操作符。这个操作符为“->”，该操作符被称为Lambda操作符或箭头操作符。它将Lambda分为两个部分：
  
  - 左侧：指定了Lambda表达式需要的参数列表
  
  - 右侧：指定了Lambda体，是抽象方法的实现逻辑，也即Lambda表达式要执行的功能。
  
    ```java
    //语法格式一：无参，无返回值 
    Runnable r1 = () -> {System.out.println("Hello Lambda!");};
    
    //语法格式二：Lambda 需要一个参数，但是没有返回值。
    Consumer<String> con = (String str) -> {System.out.println(str);};
    
    //语法格式三：数据类型可以省略，因为可由编译器推断得出，称为“类型推断” 
    Consumer<String> con = (str) -> {System.out.println(str);};
    
    //语法格式四：Lambda若只需要一个参数时，参数的小括号可以省略
    Consumer<String> con = str -> {System.out.println(str);};
    
    //语法格式五：Lambda需要两个或以上的参数，多条执行语句，并且可以有返回值
    Comparator<Integer> com = (x,y) -> {
        System.out.println("实现函数式接口方法！");
        return Integer.compare(x,y);
    }
    
    //语法格式六：当Lambda体只有一条语句时，return与大括号若有，都可以省略
    Comparator<Integer> com = (x,y) -> Integer.compare(x,y);
    ```
  
  - 类型推断
  
    - 上述Lambda表达式中的参数类型都是由编译器推断得出的。Lambda表达式中无需指定类型，程序依然可以编译，这是因为javac根据程序的上下文，在后台推断出了参数的类型。Lambda表达式的类型依赖于上下文环境，是由编译器推断出来的。这就是所谓的“**类型推断**”。
  
      ![](.\类型推断.JPG)
  

### 2.函数式（Functional）接口

- ##### 概念

  - **只包含一个抽象方法的接口，称为函数式接口。**
  - 你可以通过Lambda表达式来创建该接口的对象。（若Lambda表达式抛出一个受检异常（即：非运行时异常），那么该异常需要在目标接口的抽象方法上进行声明）。
  - 我们可以在一个接口上使用@Functionallnterface注解，这样做可以检查它是否是一个函数式接口。同时javadoc也会包含一条声明，说明这个接口是一个函数式接口。
  - **在java.util.function包下定义了Java8的丰富的函数式接口**

- ##### 理解函数式接口

  - Java从诞生日起就是一直倡导“一切皆对象”，在Java里面面向对象（OOP）编程是一切。但是随着python、scala等语言的兴起和新技术的挑战，Java不得不做出调整以便支持更加广泛的技术要求，也即java不但可以支持OOP还可以支持OOF（面向函数编程）
  - 在函数式编程语言当中，函数被当做一等公民对待。在将函数作为一等公民的编程语言中，Lambda表达式的类型是函数。但是在Java8中，有所不同。在Java8中，**Lambda表达式是对象**，而不是函数，它们必须依附于一类特别的对象类型——函数式接口。
  - 简单的说，在Java8中，**Lambda表达式就是一个函数式接口的实例**。这就是Lambda表达式和函数式接口的关系。也就是说，只要一个对象是函数式接口的实例，那么该对象就可以用Lambda表达式来表示。
  - **所以以前用匿名实现类表示的现在都可以用Lambda表达式来写。**

- 作为参数传递Lambda表达式

  - 作为参数传递Lambda表达式：为了将Lambda表达式作为参数传递，接收Lambda表达式的参数类型必须是与该Lambda表达式兼容的函数式接口的类型。

- ##### Java内置四大核心函数式接口

  | 函数式接口                    | 参数类型 | 返回类型 | 用途                                                         |
  | ----------------------------- | -------- | -------- | ------------------------------------------------------------ |
  | Consumer<T><br />消费型接口   | T        | void     | 对类型为T的对象应用操作，包含的方法：<br />void accept(T t)  |
  | Supplier<T><br />供给型接口   | 无       | T        | 返回类型为T的对象，包含方法: T get()                         |
  | Function<T,R><br />函数型接口 | T        | R        | 对类型为T的对象应用操作，并返回结果。结<br />果是R类型的对象。包含方法:R apply(T t) |
  | Predicate<T><br />断定型接口  | T        | boolean  | 确定类型为T的对象是否满足约束，并返回<br />boolean值。包含方法: boolean test(T t) |

  | 函数式接口                                                   | 参数类型                  | 返回类型                  | 用途                                                         |
  | ------------------------------------------------------------ | ------------------------- | ------------------------- | ------------------------------------------------------------ |
  | BiFunction<T, U, R>                                          | T,U                       | R                         | 对类型参数T，U参数应用操作，返回R类型结<br />果。包含方法为R apply(T t, U u); |
  | UnaryOperator<T><br />（Function子接口）                     | T                         | T                         | 对类型为T的对象进行一元运算，并返回T类型的<br />结果。包含方法为：T apply(T t); |
  | BinaryOperator<T><br />(BiFunction子接口)                    | T,T                       | T                         | 对类型为T的对象进行二元运算，并返回T类型的<br />结果。包含方法为: T apply(T t1, T t2); |
  | BiConsumer<T,U>                                              | T,U                       | void                      | 对类型为T，U参数应用操作。<br />包含方法为：void accept(T t, U u) |
  | BiPredicate<T,U>                                             | T,U                       | boolean                   | 包含方法：boolean test(T t, U u)                             |
  | ToIntFunction<T><br />ToLongFunction<T><br />ToDoubleFunction<T> | T                         | int<br />long<br />double | 分别计算int,long,double值的函数                              |
  | IntFunction<R><br />LongFunction<R><br />DoubleFunction<R>   | int<br />long<br />double | R                         | 参数分别为int，long,double类型的函数                         |

### 3.方法引用与构造器引用

- ##### 方法引用

  - 当要传递给Lambda体的操作，已经有实现的方法了，可以使用方法引用！

  - 方法引用可以看做是Lambda表达式深层次的表达。换句话说，方法引用就是Lambda表达式，也就是函数式接口的一个实例，通过方法的名字来指向一个方法，可以认为是Lambda表达式的一个**语法糖**。

  - 要求：**实现接口的抽象方法的参数列表和返回值类型，必须与方法引用的方法的参数列表和返回值类型保持一致！**

  - 格式：使用操作符“::”将类（或对象）与方法名分隔开来。

  - 如下三种主要使用情况：

    - 对象：：实例方法名
    - 类：：静态方法名
    - 类：：实例方法名

  - 举例：

    ```java
    Consumer<String> con = (x) -> System.out.printin(x); 
    //等同于： 
    Consumer<String> con2 = System.out::println; 
    
    Comparator<Integer> com = (x,y)->Integer.compare(x,y); 
    //等同于： 
    Comparator<Integer>com1 = Integer::compare; 
    int value= com.compare(12,32);
    
    BiPredicate<String,String> bp = (x,y)->x.equals(y); 
    //等同于： 
    BiPredicate<String,String> bp1 = String::equals; 
    boolean flag = bp1.test("hello","hi"); 
    //注意：当函数式接口方法的第一个参数是需要引用方法的调用者，并且第二 个参数是需要引用方法的参数（或无参数）时：ClassName:methodName
    ```

- 构造器引用

  - 格式：ClassName：:new

  - 与函数式接口相结合，自动与函数式接口中方法兼容。可以把构造器引用赋值给定义的方法，要求**构造器参数列表要与接口中抽象方法的参数列表一致！且方法的返回值即为构造器对应类的对象。**

    ```java
    Function<Integer,MyClass>fun=(n)->new MyClass(n); 
    //等同于： 
    Function<Integer,MyClass>fun = Myclass::new;
    ```

- 数组引用

  - 格式： type[] :: new

    ```java
    Function<Integer,Integer[]>fun=(n)->new Integer[n]; 
    //等同于： 
    Function<Integer,Integer[]>fun =Integer[]::new;
    ```

### 4.强大的Stream API

- Stream API说明

  - Java8中有两大最为重要的改变。第一个是**Lambda表达式**；另外一个则是**Stream API**。
  - Stream API(java.util.stream)把真正的函数式编程风格引入到Java中。这是目前为止对Java类库最好的补充，因为StreamAPI可以极大提供Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。
  - Stream是Java8中处理集合的关键抽象概念，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。**使用Stream API对集合数据进行操作，就类似于使用SQL执行的数据库查询。**也可以使用StreamAPI来并行执行操作。简言之，StreamAPI提供了一种高效且易于使用的处理数据的方式。

- 为什么要使用Stream API

  - 实际开发中，项目中多数数据源都来自于Mysql，Oracle等。但现在数据源可以更多了，有MongDB，Radis等，而这些NoSQL的数据就需要Java层面去处理。
  - Stream和Collection集合的区别：**Collection是一种静态的内存数据结构，而Stream是有关计算的。**前者是主要面向内存，存储在内存中，后者主要是面向CPU，通过CPU实现计算。

- 什么是Stream

  - 是数据渠道，用于操作数据源（集合、数组等）所生成的元素序列。
  - “集合讲的是数据，Stream讲的是计算！”
  - 注意：
    - ①Stream自己不会存储元素。
    - ②Stream 不会改变源对象。相反，他们会返回一个持有结果的新Stream。
    - ③Stream操作是延迟执行的。这意味着他们会等到需要结果的时候才执行。

- Stream的操作的三个步骤

  - 1-创建 Stream
    - 一个数据源（如：集合、数组），获取一个流
  - 2-中间操作
    - 一个中间操作链，对数据源的数据进行处理
  - 3-终止操作（终端操作）
    - 一旦执行终止操作，**就执行中间操作链**，并产生结果。之后，不会再被使用

- ##### 创建Stream方式

  - ###### 方式一：通过集合

    - Java8中的Collection 接口被扩展，提供了两个获取流的方法：
      - default Stream<E>stream():返回一个顺序流
      - default Stream<E>parallelStream():返回一个并行流

  - ###### 方式二：通过数组

    - Java8中的Arrays的静态方法stream（)可以获取数组流： 
      - static<T>Stream<T>stream(T[I array):返回一个流 
    - 重载形式，能够处理对应基本类型的数组： 
      - public static IntStream stream(int[] array) 
      - public static LongStream stream(long[] array) 
      - public static DoubleStream stream(double[] array)

  - ###### 方式三：通过Stream的of()

    - 可以调用Stream类静态方法of（），通过显示值创建一个流。它可以接收任意数量的数。
      - public static<T>Stream<T>of(T...values):返回一个流

  - ###### 方式四：创建无限流

    - 可以使用静态方法Streamiterate()和Stream.generate()， 创建无限流。

      - 迭代 public static<T> Stream<T> iterate(final T seed,final UnaryOperator<T>f)
      - 生成 public static<T> Stream<T> generate(Supplier<T>s)

      ```java
      //方式四：创建无限流 
      @Test 
      public void test4(){ 
          //迭代 
          // public static<T>Stream<T>iterate(final T seed,final 
          // UnaryOperator<T>f) 
          Stream<Integer>stream=Stream.iterate(0,x->x+2); 
          stream.limit(10).forEach(System.out:println); 
          
          //生成 
          // public static<T>Stream<T> generate(Supplier<T>s) 
          Stream<Double> stream1 = Stream.generate(Math::random);
          stream1.limit(10).forEach(System.out:println);
      ```

- ##### Steam的中间操作

  - 多个**中间操作**可以连接起来形成一个**流水线**，除非流水线上触发终止操作，否则**中间操作不会执行任何的处理！而在终止操作时一次性全部处理，称为“惰性求值”。**

  - 筛选与切片

    | 方法                | 描述                                                         |
    | ------------------- | ------------------------------------------------------------ |
    | filter(Predicate p) | 接收Lambda，从流中排除某些元素                               |
    | distinct()          | 筛选，通过流所生成元素的hashCode()和equals()去除重复元素     |
    | limit(long maxSize) | 截断流，使其元素不超过给定数量                               |
    | skip(long n)        | 跳过元素，返回一个扔掉了前n个元素的流。若流中元素不足n个，则返回一<br />个空流。与limit(n)互补 |

  - 映射

    | 方法                            | 描述                                                         |
    | ------------------------------- | ------------------------------------------------------------ |
    | map(Function f)                 | 接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。 |
    | mapToDouble(ToDoubleFunction f) | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的DoubleStream。 |
    | mapToInt(ToIntFunction f)       | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的IntStream。 |
    | mapToLong(ToLongFunction f)     | 接收一个函数作为参数，该函数会被应用到每个元素上，产生一个新的LongStream。 |
    | flatMap(Function f)             | 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。 |

  - 排序

    | 方法                   | 描述                               |
    | ---------------------- | ---------------------------------- |
    | sorted()               | 产生一个新流，其中按自然顺序排序   |
    | sorted(Comparator com) | 产生一个新流，其中按比较器顺序排序 |

- ##### Stream的终止操作

  - 终端操作会从流的流水线生成结果。其结果可以是任何不是流的值，例如：List、Integer，甚至是void。

  - 流进行了终止操作后，不能再次使用。

  - 匹配与查找

    | 方法                   | 描述                                                         |
    | ---------------------- | ------------------------------------------------------------ |
    | allMatch(Predicate p)  | 检查是否匹配所有元素                                         |
    | anyMatch(Predicate p)  | 检查是否至少匹配一个元素                                     |
    | noneMatch(Predicate p) | 检查是否没有匹配所有元素                                     |
    | findFirst()            | 返回第一个元素                                               |
    | findAny()              | 返回当前流中的任意元素                                       |
    | count()                | 返回流中元素总数                                             |
    | max(Comparator c)      | 返回流中最大值                                               |
    | min(Comparator c)      | 返回流中最小值                                               |
    | forEach(Consumer c)    | 内部迭代（使用Collection接口需要用户去做迭代，<br />称为外部迭代。相反，StreamAPI使用内部迭<br />代——它帮你把迭代做了） |

  - 规约

    | 方法                             | 描述                                                    |
    | -------------------------------- | ------------------------------------------------------- |
    | reduce(T iden, BinaryOperator b) | 可以将流中元素反复结合起来，得到一个值。返回T           |
    | reduce(BinaryOperator b)         | 可以将流中元素反复结合起来，得到一个值。返回Optional<T> |

  - 收集

    | 方法                 | 描述                                                         |
    | -------------------- | ------------------------------------------------------------ |
    | collect(Collector c) | 将流转换为其他形式。接收一个Collector<br />接口的实现，用于给Stream中元素做汇总的方法 |

- ##### Collectors

  | 方法              | 返回类型             | 作用                                                         |
  | ----------------- | -------------------- | ------------------------------------------------------------ |
  | toList            | List<T>              | 把流中元素收集到List                                         |
  | toSet             | Set<T>               | 把流中元素收集到Set                                          |
  | toCollection      | Collection<T>        | 把流中元素收集到创建的集合                                   |
  | counting          | Long                 | 计算流中元素的个数                                           |
  | summingInt        | Integer              | 对流中元素的整数属性求和                                     |
  | averagingInt      | Double               | 计算流中元素Integer属性的平均值                              |
  | summarizingInt    | IntSummaryStatistics | 收集流中Integer属性的统计值。如：平均值                      |
  | joining           | String               | 连接流中每个字符串                                           |
  | maxBy             | Optional<T>          | 根据比较器选择最大值                                         |
  | minBy             | Optional<T>          | 根据比较器选择最小值                                         |
  | reducing          | 规约产生的类型       | 从一个作为累加器的初始值开始，利用BinaryOperator与流中元素逐个结合，从而归约成单个值 |
  | collectionAndThen | 转换函数返回的类型   | 包裹另一个收集器，对其结果转换函数                           |
  | groupingBy        | Map<K, List<T>>      | 根据某属性值对流分组，属性为K,结果为V                        |
  | partitioningBy    | Map<Boolean,List<T>> | 根据true或false进行分区                                      |

### 5.Optional类

- 到目前为止，臭名昭著的空指针异常是导致Java应用程序失败的最常见原因。以前，为了解决空指针异常，Google公司著名的Guava项目引入了Optional类，Guava通过使用检查空值的方式来防止代码污染，它鼓励程序员写更干净的代码。受到Google Guava的启发，Optional类已经成为Java8类库的一部分。

- Optional<T>类（java.util.Optional)是一个容器类，它可以保存类型T的值，代表这个值存在。或者仅仅保存null，表示这个值不存在。原来用null 表示一个值不存在，现在Optional可以更好的表达这个概念。并且可以避免空指针异常。

- Optional类的Javadoc描述如下：这是一个可以为null的容器对象。如果值存在则isPresent（）方法会返回true，调用get（）方法会返回该对象。

- Optional提供很多有用的方法，这样我们就不用显式进行空值检测。

- 创建Optional类对象的方法：

  - Optional.of（T t）：创建一个Optional实例，t必须非空；
  - Optional.empty():创建一个空的Optional实例
  - Optional.ofNullable（T t):t可以为null

- 判断Optional容器中是否包含对象：

  - boolean isPresent):判断是否包含对象
  - void ifPresent(Consumer<? super T> consumer):如果有值，就执行Consumer接口的实现代码，并且该值会作为参数传给它。

- 获取Optional容器的对象：

  - T get（)：如果调用对象包含值，返回该值，否则抛异常

  - T orElse（T other):如果有值则将其返回，否则返回指定的other对象。

  - T orElseGet(Supplier<?extends T>other):如果有值则将其返回，否则返回由Supplier接口实现提供的对象。

  - T orElseThrow(Supplier<?extends X>exceptionSupplier):如果有值则将其返回，否则抛出由Supplier接口实现提供的异常。

    ```java
    @Test 
    public void test1(){ 
        Boy b=new Boy("张三"); 
        Optional<Girl> opt = Optional.ofNullable(b.getGrilFriend()); 
        //如果女朋友存在就打印女朋友的信息 
        opt.ifPresent(System.out::println); 
    }
    
    @Test 
    public void test2(){ 
        Boy b=new Boy("张三");       
        optional<Girl>opt=Optional.ofNullable(b.getGrilFriend());
        //如果有女朋友就返回他的女朋友，否则只能欣赏“嫦娥”了 
    Girl girl=opt.orElse(new Girl("嫦娥")); 
        System.out.println("他的女朋友是："+girl.getName());
    }
    
    @Test 
    public void test3(){ 
        Optional<Employee> opt = Optional.of(new Employee("张三"，8888));
        //判断opt中员工对象是否满足条件，如果满足就保留，否则返回空 
        Optional<Employee>emp=opt.filter(e->e.getsalary()>10000);
        System.out.println(emp);
    } 
        
    @Test 
    public void test4(){ 
        Optional<Employee> opt =Optional.of(new Employee("张三"，8888));
        //如果opt中员工对象不为空，就涨薪10% 
        Optional<Employee>emp=opt.map(e-> {e.setSalary(e.getSalary()%1.1);return e;}); 
        System.out.println(emp);
    }
    ```

## 第十五章：java9,10,11新特性

### 1.Java9的新特性

- ##### 模块化系统

  -  谈到Java9大家往往第一个想到的就是Jigsaw项目。众所周知，Java已经发展超过20年（95年最初发布），Java和相关生态在不断丰富的同时也越来越暴露出一些问题：
    - **Java运行环境的膨胀和臃肿。**每次JVM启动的时候，至少会有30~60MB的内存加载，主要原因是JVM需要加载rt.jar，不管其中的类是否被classloader加载，第一步整个jar都会被JVM加载到内存当中去（而模块化可以根据模块的需要加载程序运行需要的class)
    - **当代码库越来越大，创建复杂，盘根错节的“意大利面条式代码”的几率呈指数级的增长。**不同版本的类库交叉依赖导致让人头疼的问题，这些都阻碍了Java开发和运行效率的提升。
    - 很难真正地对代码进行封装，而系统并没有对不同部分（也就是JAR文件）之间的依赖关系有个明确的概念。**每一个公共类都可以被类路径之下任何其它的公共类所访问到，这样就会导致无意中使用了并不想被公开访问的APl。**
  - 本质上讲也就是说，用模块来管理各个package，通过声明某个package暴露，模块（module)的概念，其实就是package外再裹一层，不声明默认就是隐藏。因此，模块化使得代码组织上更安全，因为它可以指定哪些部分可以暴露，哪些部分隐藏。
  - 实现目标
    - 模块化的主要目的在于减少内存的开销
    - 只须必要模块，而非全部jdk模块，可简化各种类库和大型应用的开发和维护
    - 改进Java SE平台，使其可以适应不同大小的计算设备
    - 改进其安全性，可维护性，提高性能
  - 模块将由通常的类和新的模块声明文件（module-info.java)组成。该文件是位于java代码结构的顶层，该模块描述符明确地定义了我们的**模块需要什么依赖关系，以及哪些模块被外部使用。**在exports子句中未提及的所有包默认情况下将封装在模块中，不能在外部使用。
  - **exports:控制着哪些包可以被其它模块访问到。所有不被导出的包默认都被封装在模块里面。**
  - **requires:指明对其它模块的依赖。**

- ##### Jshell命令

  - 产生背景
    - 像Python和Scala之类的语言早就有交互式编程环境 REPL(read-evaluate-print-loop）了，以交互式的方式对语句和表达式进行求值。开发者只需要输入一些代码，就可以在编译前获得对程序的反馈。而之前的Java版本要想执行代码，必须创建文件、声明类、提供测试方法方可实现。
  - 设计理念
    - 即写即得、快速运行
  - 实现目标
    - Java9中终于拥有了REPL工具：jShell。让Java可以像脚本语言一样运行，从控制台启动jShell，利用iShell在没有创建类的情况下直接声明变量，计算表达式，执行语句。即开发时可以在命令行里直接运行Java的代码，而无需创建Java文件，无需跟人解释”public static void main（Stringll args)”这句废话。
    - jShell也可以从文件中加载语句或者将语句保存到文件中。
    - iShell也可以是tab键进行自动补全和自动添加分号。

- ##### 语法改进：接口的私有方法

  - Java8中规定接口中的方法除了抽象方法之外，还可以定义静态方法和默认的方法。一定程度上，扩展了接口的功能，此时的接口更像是一个抽象类。

  - 在Java9中，接口更加的灵活和强大，**连方法的访问权限修饰符都可以声明为private的了**，此时方法将不会成为你对外暴露的API的一部分。

    ```java
    interface MyInterface{ 
        void normalInterfaceMethod(); 
        default void methodDefault1(){ 
            init();
        } 
        public default void methodDefault2(){ 
            init();
        } 
        private void init(){ 
            System.out.println("默认方法中的通用操作");
        }
    }
    ```

- ##### 语法改进：钻石操作符使用升级

  - 我们将能够与匿名实现类共同使用钻石操作符（diamond operator）在Java 8 中如下的操作是会报错的：

    ```java
    Comparator<Object>com=new Comparator<(){ 
        @Override 
        public int compare(Object ol,Object o2){ 
            return 0;
        }
    };
    ```

  - 编译报错信息：Cannot use“<>”with anonymous inner classes.

- ##### 语法改进：try语句

  - Java9中，用资源语句编写try将更容易，我们可以在try子句中使用已经初始 化过的资源，此时的资源是final的：

    ```java
    InputStreamReader reader = new InputStreamReader(System.in);
    OutputStreamWriter writer = new OutputStreamWriter(System.out);
    try(reader;writer){ 
        //reader是final的，不可再被赋值 
        //reader=null; 
        //具体读写操作省略 
    }catch(IOExceptione){ 
        e.printStackTrace();
    }
    ```

- ##### String存储结构变更

  - 结论：String再也不用char[]来存储啦，改成了byte[]加上编码标记，节约一些空间。

- ##### 集合工厂方法：快速创建只读集合

  - 要创建一个只读、不可改变的集合，必须构造和分配它，然后添加元素，最后 包装成一个不可修改的集合。

    ```java
    List<String> namesList = new Arraylist<>(); 
    namesList.add("Joe"); 
    namesList.add("Bob"); 
    namesList.add("Bill");
    namesList=Collections.unmodifiableList(namesList); 
    System.out.println(namesList);
    ```

  - 缺点：我们一下写了五行。即：它不能表达为单个表达式。

  -  List firsnamesList=List.of("Joe","Bob","Bill");调用集合中静态方法of（），可以将不同数量的参数传输到此工厂方法中。此功能可用于Set和List，也可用于Map的类似形式。**此时得到的集合，是不可变的**：在创建后，继续添加元素到这些集合会导致"UnsupportedOperationException"。

  - 由于Java8中接口方法的实现，可以直接在List，Set和Map的接口内定义这些方法，便于调用。

- ##### InputStream加强

  - InputStream终于有了一个非常有用的方法：transferTo，可以用来将数据直接传输到OutputStream，这是在处理原始数据流时非常常见的一种用法。

- ##### 增强的Stream API

  - Java的**SteamAPI是java标准库最好的改进之一，让开发者能够快速运算，从而能够有效的利用数据并行计算。**Java8提供的Steam能够利用多核架构实现声明式的数据处理。

  - 在Java9中，StreamAPI变得更好，Stream接口中添加了4个新的方法：**takeWhile,dropWhile,ofNullable,还有个iterate方法的新重载方法**，可以让你提供一个Predicate（判断条件）来指定什么时候结束迭代。

  - 除了对Stream本身的扩展，Optional和Stream之间的结合也得到了改进。现在可以**通过Optional的新方法stream（）将一个Optional对象转换为一个（可能是空的）Stream对象。**

  - ##### takeWhile()的使用

    - 用于从Stream中获取一部分数据，接收一个Predicate来进行选择。在有序的 Stream中，takeWhile返回从开头开始的尽量多的元素。

      ```java
      List<Integer>list=Arrays.asList(45,43,76,87,42,77,90,73,67,88);
      list.stream().takewhile(x->x<50).forEach(System.out::println);
      
      System.out.println(); 
      list=Arrays.asList(1,2,3,4,5,6,7,8);
      list.stream().takewhile(x->x<5).forEach(System.out::print1n);
      ```

  - ##### dropWhile()的使用

    -  dropWhile的行为与takeWhile相反，**返回剩余的元素**。

      ```java
      List<Integer>list=Arrays.aslist(45,43,76,87,42,77,90,73,67,88);
      list.stream().dropwhile(x->x<50).forEach(System.out::println); 
      
      System.out.printin();
      list = Arrays.aslist(1,2,3,4,5,6,7,8);
      list.stream().dropwhile(x->x<5).forEach(System.out::println);
      ```

  - ##### ofNullable()的使用

    - Java8中Stream 不能完全为null，否则会报空指针异常。而Java9中的ofNullable方 法允许我们创建一个单元素Stream，可以包含一个非空元素，也可以创建一个空 Stream。

      ```java
      //报Nul1PointerException 
      //Stream<Object>streaml=Stream.of(null); 
      //System.out.println(streaml.count()); 
      //不报异常，允许通过 
      Stream<String> stringstream = Stream.of("AA","BB",null); System.out.println(stringStream.count());//3 
      //不报异常，允许通过 
      List<String> list = new Arraylist<>(); 
      list.add("AA");
      list.add(nul1);
      System.out.println(list.stream().count());//2 
      //ofNullable():允许值为nul1 
      Stream<Object>stream1 = Stream.ofNullable(null);
      System.out.println(streaml.count());//0 
      Stream<String> stream = Stream.ofNullable("hello world"); System.out.println(stream.count());//1
      ```

  - ##### iterate（)重载的使用

    - 这个iterate方法的新重载方法，可以让你提供一个Predicate(判断条件）来指定什么时候结束迭代。

      ```java
      //原来的控制终止方式：
      Stream.iterate(1,i->i+1).limit(10).forEach(System.out::println);
      //现在的终止方式：
      Stream.iterate(1,i->i<100,i->i+1).forEach(System.out::println);
      ```

  - ##### Optional类中stream()的使用

    ```java
    List<String> list = new ArrayList<>();
    list.add("Tom");
    list.add("Jerry");
    list.add("Tim");
    Optional<List<String>> optional = Optional.ofNullable(list); 
    Stream<List<String>> stream = optional.stream(); 
    stream.flatMap(x->x.stream()).forEach(System.out::println);
    ```

### 2.Java10的新特性

- ##### 局部变量类型推断

  - 产生背景

    - 开发者经常抱怨Java中引用代码的程度。**局部变量的显示类型声明，常常被认为是不必须的**，给一个好听的名字经常可以很清楚的表达出下面应该怎样继续。
    - 好处：
      - 减少了啰嗦和形式的代码，避免了信息冗余，而且对齐了变量名，更容易阅读！
    - 举例如下：
      - 场景一：类实例化时
      - 作为Java开发者，在声明一个变量时，我们总是习惯了敲打两次变量类型，第一次用于声明变量类型，第二次用于构造器。
        - LinkedHashSet<Integer>set=new LinkedHashset<>()；
      - 场景二：返回值类型含复杂泛型结构
      - 变量的声明类型书写复杂且较长，尤其是加上泛型的使用
        - Iterator<Map.Entry<Integer,Student>>iterator =set.iterator()；
      - 场景三：我们也经常声明一种变量，它只会被使用一次，而且是用在下一行代码中，比如：
        - URL ur1=new URL("http://www.atguigu.com");
        - URLConnection connection=url.openconnection();
        - Reader reader = new BufferedReader(newInputStreamReader(connection.getInputStream()))；
        - 尽管IDE可以帮我们自动完成这些代码，但当变量总是跳来跳去的时候，可读生还是会受到影响，因为变量类型的名称由各种不同长度的字符组成。而且，有时候开发人员会尽力避免声明中间变量，因为太多的类型声明只会分散注意力，不会带来额外的好处。

  - 适用于以下情况

    ```java
    //1.局部变量的初始化 
    var list=new ArrayList<>();
    //2.增强for循环中的索引 
    for(var v:list){ 
        System.out.println(v);
    }
    //3.传统for循环中 
    for(var i=0;i<100;i++){ 
        System.out.printin(i);
    }
    ```

  - 不适用以下的结构中：

    - 情况1：没有初始化的局部变量声明
    - 情况2：方法的返回类型
    - 情况3：方法的参数类型
    - 情况4：构造器的参数类型
    - 情况5：属性
    - 情况6：catch块

  - 工作原理在处理var时，编译器先是查看表达式右边部分，并根据右边变量值的类型进行推断，作为左边变量的类型，然后将该类型写入字节码当中。

  - 注意

    - var不是一个关键字
    - 你不需要担心变量名或方法名会与var发生冲突，因为var实际上并不是一个关键字，而是一个类型名，只有在编译器需要知道类型的地方才需要用到它。除此之外，它就是一个普通合法的标识符。也就是说，**除了不能用它作为类名，其他的都可以，但极少人会用它作为类名。**
    - 这不是JavaScript
    - 首先我要说明的是，var并不会改变Java是一门静态类型语言的事实。编译器负责推断出类型，并把结果写入字节码文件，就好像是开发人员自己敲入类型一样。

- ##### 集合新增创建不可变集合的方法

  - 从源码分析，可以看出copyOf方法会先判断来源集合是不是AbstractlmmutableList 类型的，如果是，就直接返回，如果不是，则调用of创建一个新的集合。
  - 示例2因为用的new创建的集合，不属于不可变AbstractlmmutablelList类的子类，所以copyof方法又创建了一个新的实例，所以为false。
  - 注意：使用of和copyof创建的集合为不可变集合，不能进行添加、删除、替换、排序等操作，不然会报java.lang.UnsupportedOperationException异常。
  - 上面演示了List的of和copyOf方法，set和Map接口都有。

### 3.Java11新特性

- ##### 字符串处理方法

  | 描述                 | 举例                                          |
  | :------------------- | --------------------------------------------- |
  | 判断字符串是否为空白 | " ".isBlank();  //true                        |
  | 去除首尾空白         | " Javastack ".strip(); //"Javastack"          |
  | 去除尾部空格         | " Javastack ".stripTrailing(); //" Javastack" |
  | 去除首部空格         | " Javastack ".stripLeading(); //"Javastack "  |
  | 复制字符串           | "Java".repeat(3);//JavaJavaJava               |
  | 行数统计             | "A\nb\nC".lines().count();//3                 |

- ##### Optional加强

  - Optional 也增加了几个非常酷的方法，现在可以很方便的将一个Optional 转换成一个Stream,或者当一个空Optional时给它一个替代的。

    | 新增方法                                                     | 描述                                                         | 新增的版本 |
    | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------- |
    | boolean isEmpty()                                            | 判断value是否为空                                            | JDK11      |
    | ifPresentOrElse(Consumer<?<br/>    super T>action,Runnable emptyAction) | value非空，执行参数1功能；如果value为空，执行参数2功能       | JDK9       |
    | Optional<T>or(Supplier<?<br/>    extends Optional<? extends T>>supplier) | value非空，返回对应的Optional；value为空，返回形参封装的Optional | JDK9       |
    | Stream<T>stream()                                            | value非空，返回仅包含此value的Stream；否则，返回一个空的Stream | JDK9       |
    | T orElseThrow()                                              | value非空，返回value；否则抛异常<br/>    NoSuchElementException | JDK10      |

- ##### 局部变量类型推断升级

  - 在var上添加注解的语法格式，在jdk10中是不能实现的。在JDK11中加入了这样的语法。

    ```java
    //错误的形式：必须要有类型，可以加上var
    //Consumer<String>conl=(@Deprecated t)->System.out.printin(t.toupperCase());
    //正确的形式：
    //使用var的好处是在使用1ambda表达式时给参数加上注解。
    Consumer<String>con2=(@Deprecated var t)->System.out.println(t.toUpperCase())；
    ```

- 全新的HTTP客户端API

  -   HTTP，用于传输网页的协议，早在1997年就被采用在目前的1.1版本中。直到2015年，HTTP2才成为标准。

  - HTTP/1.1和HTTP/2的主要区别是如何在客户端和服务器之间构建和传输数据。**HTTP/1.1依赖于请求/响应周期。HTTP/2允许服务器“push”数据：它可以发送比客户端请求更多的数据。**这使得它可以优先处理并发送对于首先加载网页至关重要的数据。

  - 这是Java9开始引入的一个处理HTTP 请求的的HTTP Client API,该API 支持同步和异步，而在Java11中已经为正式可用状态，你可以在java.net包中找到这个APl。

  - 它将**替代仅适用于blocking模式的HttpURLConnection**（HttpURLConnection是在HTTP1.0的时代创建的，并使用了协议无关的方法），并提供对WebSocket和HTTP/2的支持。

    ```java
    HttpClient client = HttpClient.newHttpClient(); 
    HttpRequest request = HttpRequest.newBuilder(URI.create("http://127.0.0.1:8080/test/")).build();
    BodyHandler<String> responseBodyHandler = BodyHandlers.ofString(); HttpResponse<String>response = client.send(request,responseBodyHandler); 
    String body = response.body();
    System.out.println(body);
    
    HttpClient client =HttpClient.newHttpClient(); 
    HttpRequest request= HttpRequest.newBuilder(URI.create("http://127.0.0.1:8080/test/")).build(); BodyHandler<String>responseBodyHandler = BodyHandlers.ofString(); CompletableFuture<HttpResponse<String>> sendAsync = client.sendAsync(request,responseBodyHandler); 
    sendAsync.thenApply(t->t.body()).thenAccept(System.out::print1n); //HttpResponse<String>response=sendAsync.get();
    //String body=response.body();
    //System.out.println(body);
    ```

- ##### 更简化的编译运行程序

  - 看下面的代码。
  - //编译javac Javastack.java
  - //运行java Javastack
  - 在我们的认知里面，要运行一个Java源代码必须先编译，再运行，两步执行动作。
    而在未来的Java11版本中，通过一个java命令就直接搞定了，如以下所示：java Javastack.java
  - 一个命令编译运行源代码的注意点：
    - 执行源文件中的第一个类，第一个类必须包含主方法。
    - 并且不可以使用其它源文件中的自定义类，本文件中的自定义类是可以使用的。

- ##### 废弃Nashorn引擎

  - 废除Nashorn javascript引擎，在后续版本准备移除掉，有需要的可以考虑使用GraalVM。

- ##### ZGC

  - **GC是java主要优势之一。**然而，当GC停顿太长，就会开始影响应用的响应时间。消除或者减少GC停顿时长，java将对更广泛的应用场景是一个更有吸引力的平台。此外，现代系统中可用内存不断增长，用户和程序员希望JVM能够以高效的方式充分利用这些内存，并且无需长时间的GC暂停时间。
  - ZGC,A Scalable Low-Latency Garbage Collector(Experimental)ZGC，这应该是JDK11最为瞩目的特性，没有之一。但是后面带了Experimental，说明这还不建议用到生产环境。
  - ZGC是一个并发，基于region，压缩型的垃圾收集器，只有root扫描阶段会STW(stop the world),因此GC停顿时间不会随着堆的增长和存活对象的增长而变长
  - 优势：
    - GC暂停时间不会超过10ms
    - 既能处理几百兆的小堆，也能处理几个T的大堆（OMG）
    - 和G1相比，应用吞吐能力不会下降超过15%
    - 为未来的GC功能和利用colord指针以及Load barriers优化奠定基础
    - 初始只支持64位系统
  - ZGC的设计目标是：支持TB级内存容量，暂停时间低（<10ms)，对整个程序吞吐量的影响小于15%。将来还可以扩展实现机制，以支持不少令人兴奋的功能，例如多层堆（即热对象置于DRAM和冷对象置于NVMe闪存），或压缩堆。

- ##### 其他新特性

  - Unicode 10 
  - Deprecate the Pack200 Tools and API 
  - 新的Epsilon垃圾收集器 
  - 完全支持Linux容器（包括Docker) 
  - 支持G1上的并行完全垃圾收集 
  - 最新的HTTPS安全协议TLS1.3 
  - Java Flight Recorder
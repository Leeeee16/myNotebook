#### Java

##### 基础

###### 1.Java 中的几种基本数据类型是什么？对应的包装类型是什么？各自占用多少字节呢？

| 基本类型 | 位数 | 字节 | 包装类    |
| -------- | :--- | ---- | --------- |
| int      | 32   | 4    | Integer   |
| short    | 32   | 2    | Short     |
| long     | 64   | 8    | Long      |
| byte     | 8    | 1    | Byte      |
| char     | 16   | 2    | Character |
| float    | 32   | 4    | Float     |
| double   | 64   | 8    | Double    |
| boolean  |      |      | Boolean   |

> Byte、Integer、Short、Long创建了数值[-128, 127]的相应类型的缓存数据
>
> Character创建了[0, 127]的缓存数据
>
> Boolean直接返回 True 或 False



###### 2.`String` `、 StringBuffer` 和 `StringBuilder` 的区别是什么? `String` 为什么是不可变的?

**可变性**

String中使用了`final`关键字修饰 字符数组 来保存字符串，`private final char[] value`，所以String不可变

StringBuffer和StringBuilder继承自AbstractStringBuilder，而在AbstractStringBuilder中没有使用`final`修饰字符数组，因此是可变的

 **线程安全性**

String中的对象是不可变的，可以看做常量，所以线程安全。

StringBuffer对方法加了同步锁synchronized，所以是线程安全的，因此性能较低

StringBuilder没有使用同步锁，因此线程不安全，但性能比StringBuffer高一些









###### 3.`==` 与 `equals`?`hashCode` 与 `equals` ?

`==`：判断两个对象的地址是否相等，判断两个对象是否是同一个对象。基本数据比较值，引用类型比较内存地址

`equals`()：判断两个对象是否相等。一般分为两种情况：

* 类没有重写`equals()`方法，通过`equals`比较时，与`==`一样，比较两个对象
* 类重写了`equals()`方法 。一般是比较两个对象的内容	

> `String`重写了`equals`方法，因此比较的是对象的值
>
> 在创建`String`类型对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相等的对象，若有就把它赋给当前引用；若没有则在常量池中新创建一个String对象

`hashCode()`：获取哈希值，确定该对象在哈希表中的索引位置。

当将对象加入`HashSet`中时，会先用`hashCode()`判断对象加入的位置，如果该位置有对象，就会调用`equals()`方法判断两个对象是否相同，若相同则不会加入。

使用`hashCode()`减少了`equals`的次数，提高执行速度

> 1. 若两个对象相等，`hashCode`一定相同
> 2. 若两个对象相等，对两个对象调用`equals`都返回`true`
> 3. 两个对象有相等的`hashCode`值，他们不一定相等
> 4. `equals`方法被覆盖，`hashCode`方法必须被覆盖

重写equals的原则

1. 自反性：A.equals(A)返回true

2. 对称性：A.equals(B)和B.equals(A)相等
3. 传递性：A.equals(B)返回true，B.equals(C)返回true，则A.equals(C)也返回true
4. 一致性：A、B不变，A.equals(B)的结果也不能变；执行多次结果一样
5. A.equals(null)返回false

###### 4. 解决哈希冲突的方法

1. 拉链法，如HashMap的

   优点

   * 无堆积现象，非同义词不会发生冲突，平均查找长度较短
   * 动态申请节点，适用于无法确定表长的情况
   * 删除操作简单

   缺点：指针需要额外空间

2. 开放地址法，如线性探查、二次探查、伪随机

3. 再哈希：如果冲突就再哈希一次，缺点是再哈希增加计算时间

###### 5.Java 反射？反射有什么缺点？你是怎么理解反射的（为什么框架需要反射）？

程序在运行时，动态加载类并获取类的所有属性和方法，对于任意一个对象，都能调用它的任意一个方法和属性。

**缺点**

1. 性能瓶颈：反射相当于一系列解释操作，需要通知JVM要做的事情，比直接的Java代码慢很多
2. 安全问题：动态获取类的属性和方法，增加了安全隐患

**理解：**

https://www.jianshu.com/p/7885297a1f2b



###### 6.谈谈对 Java 注解的理解，解决了什么问题？

在类、类型、属性、参数、局部变量、方法、构造方法、包、注解本身上面的附属品，它本身没有作用，通过外部程序对其进行解析引起它的作用。

**元注解**：注解的注解。

​	`@Retention`：注解存在的阶段是源码(编译期)、字节码还是运行期

​	`@Target`：注解作用的范围，类、方法、方法参数变量等

​	`@Document`：将注解的元素包含到`Javadoc`中

​	`@Inherited`：继承，若子类没有被其他注解修饰，它的子类也继承了父类的注解

​	`@Repeatable`：这个元注解修饰的注解可以同时作用一个对象多次

**注解属性类型**：基本数据类型、String、枚举、注解、Class、之前类型的一维数组

**注解的本质**：是一个Annotation接口

**作用**：编写文档(Document)、代码分析(反射)、编译检查(Override)，减少了配置文件的代码量

注解流程：定义、使用、读取并执行



###### 7.Java 泛型了解么？什么是类型擦除？介绍一下常用的通配符？

可以在定义类、接口时使用类型参数，到具体使用的时候再用具体的类型来替换，如集合框架，这样大大提高了代码的复用性

**类型擦除：**伪泛型，编译期间会擦除所有的泛型信息，字节码中不包含泛型中的类型信息。字节码中真正的类型是原始类型。

在使用的时候会自动进行强转

继承泛型父类，本意进行重写，实则变为重载。**桥方法：**编译时由编译器生成的方法，用来执行我们重写的方法。解决了类型擦除与多态的冲突

**常用的通配符：**

苹果 is-a 水果，但是 装苹果的盘子 not-is-a 装水果的盘子，也就是容器是没有继承的

`<? extends T>`：上边界限定通配符，可能是T或T的子类，只能get读，不能set写

```java
// Plate<Fruit>以及Plate<Apple>的基类
Plate<? extends Fruit> p = new Plate<Apple>(new Apple());
```

`<? super T>`：下边界限定通配符，可能父类型，直至Object。只能set写，不能get读

```java
List<? super Fruit>
```

###### 8.内部类了解吗？匿名内部类了解吗？

**内部类**，从几个方面讲：作用域，能否访问外部，权限修饰符

**成员内部类**，可以无条件访问外部类的**所有**成员属性和成员方法

> 若成员内部类拥有和外部类同名的成员变量或方法时，会发生**隐藏**现象，即默认情况下访问的成员内部类的成员，如果要访问外部类的同名成员，使用`外部类.this.成员变量/方法`

外部对象访问成员内部类的成员需要先创建一个对象。

成员内部类依附于外部类，若要创建成员内部类的对象，需要先存在一个外部类的对象。

成员内部类可以有权限修饰

**局部内部类**

仅限于该方法内部或该作用域内部，不能有权限修饰符

**匿名内部类**

只能用一次，比如想调用一个类的protected方法时，但又不和这个类在同一个包下，可以用一个匿名类继承这个类，来调用那个方法。

**静态内部类**

内部类优点：方便的访问彼此的私有域、是一种封装、打破单继承局限

###### 9.BIO,NIO,AIO 有什么区别?

同步与异步；阻塞与非阻塞；同步阻塞、同步非阻塞和异步非阻塞(傻等、干会儿别的来看看、干别的等通知)；

**BIO**：同步阻塞IO，一请求一应答，读取写入必须阻塞在一个线程内等待其完成。对于高并发无能为力

伪异步：线程池，减少频繁创建销毁进程的开销，无法从根本上解决问题

**NIO**：同步非阻塞IO，支持阻塞与非阻塞，对于低负载低并发可使用同步阻塞IO，对于高负载高并发可以使用非阻塞。

IO流与NIO流：NIO流是不阻塞的，单线程中从通道读取数据到buffer，同时可以干别的事儿，数据读到buffer中后，再对数据进行处理，不需要等它读完再去做别的事儿。

IO流是阻塞的，当一个线程调用read()或write()时，该线程被阻塞，直到数据完全被读取或写入



###### 10. **Java的锁**

https://tech.meituan.com/2018/11/15/java-lock.html

1. **乐观锁和悲观锁**

   悲观锁：认为自己在使用数据时，一定有别的线程来修改数据，所以悲观锁会先给资源加锁。synchronized和Lock的实现类都是悲观锁

   synchronized底层原理 https://www.cnblogs.com/aspirant/p/11470858.html 

   乐观锁：认为自己在使用数据时，不会有别的线程来修改数据，所以不会加锁，只是在更新数据时判断有没有线程更新了这个数据。如果没有被别的线程更新，则将自己修改的数据成功写入；否则进行别的操作，如报错或重试。如CAS

   > 悲观锁适合写操作多的场景，可以保证写操作正确
   >
   > 乐观锁适合读操作多的场景，不加锁提高了读的性能

   **CAS：Compare And Swap**

   无锁算法，实现多线程间的变量同步。

   **三个操作数：**

   * 需要读写的内存值V

   * 进行比较的值A

   * 要写入的新值B

   循环判断内存中的V与寄存器中的A是否相等，若相等则将B写入内存；若不相等则将V赋给A，再去比较，直到设置成功为止

   **CAS的问题：**

   * ABA问题。若A的值变成B，又从B变回A，CAS检查时会认为没有变化。通过增加一个标志位解决，同时检查引用和标志

   * 循环时间开销大。若不成功，一直自旋

   * 只能保证一个共享变量的原子操作。Java 1.5后可以多变量放在一个对象里进行CAS

2.  **自旋锁和适应性自旋锁**

   阻塞和唤醒线程需要切换CPU状态，需要耗费处理器时间。很多情况下同步自由的锁定时间很短，频繁切换线程的消耗会得不偿失。因此，自旋锁不会立即让线程进入休眠状态，而是不放弃CPU，进行自旋等待，不断尝试获取锁；如果尝试达到阈值，则线程进入休眠，放弃CPU等待锁释放。

   自适应：尝试的次数不固定，如果自旋等待该锁刚刚成功过，那么就允许等待更长的时机；如果某个锁，自旋成功次数很少，则可能省略自旋过程，直接阻塞进程，避免浪费

   实现原理：CAS，通过循环，不断尝试

   缺点：虽然避免了阻塞，但是占用了CPU，长时间占用会造成处理器资源浪费；

3. **无锁、偏向锁、轻量级锁、重量级锁**

   这是四个锁的状态，针对`synchronized`的

   **`Java`对象头**：`synchronized`是悲观锁，操作前先给同步自由加锁，这个锁放在Java对象头中。

   `Java`对象头包括两部分：`Mark Word` 和 `Klass Point`

   **`Mark Word`**：默认存储对象的`HashCode`、分代年龄、锁标志信息等。与对象自身数据无关，因此长度不固定。

   **`Klass Point`**：指向它的类元数据的指针，虚拟机通过这个指针来确定这个对象时哪个类的实例

   **`Monitor`**：一个同步工具或机制，每个Java对象都有一个看不见的锁，成为内部锁或Monitor锁

   Monitor是线程私有的数据结构，每一个被锁住的对象会和一个Monitor关联，同时monitor中会用一个字段记录拥有该锁的线程的唯一标识

   四种状态：

   **无锁**：同时只有一个线程能修改成功，在循环内不断尝试修改，如CAS

   **偏向锁**：如果一段同步代码块一直被一个线程所访问，那么该线程会自动获取锁，降低了获取锁的代价。只有一个线程执行同步代码块时提高性能。

   获取偏向锁后，Mark Word会记录偏向锁的线程ID。偏向锁只有遇到其他线程尝试竞争偏向锁时，持有者才会释放锁，线程不会主动释放偏向锁。释放时会等待全局安全点，即没有字节码正在执行，它首先会暂停所有偏向锁进程，判断锁对象是否处于被锁定状态。撤销偏向锁恢复到无锁(标志位“01”)或轻量级锁(标志位“00”)

   **轻量级锁**：当琐是偏向锁的时候，被另外的线程访问，偏向锁就会升级为轻量级锁，其他线程会通过自旋的形式尝试获取锁，不会阻塞，提高性能

   在代码进入同步块的时候，如果**同步对象**锁状态为**无锁状态**（锁标志位为“01”状态，是否为偏向锁为“0”），虚拟机首先将在**当前线程的栈帧**中建立一个名为**锁记录**（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝，然后拷贝对象头中的Mark Word复制到锁记录中。

   拷贝成功后，虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock Record里的owner指针指向对象的Mark Word。

   如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，表示此对象处于轻量级锁定状态。

   如果轻量级锁的更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行，否则说明多个线程竞争锁。

   若当前只有一个等待线程，则该线程通过自旋进行等待。但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁升级为重量级锁。

   **重量级锁**

   状态是“10”，等待锁的线程都会进入阻塞状态

   **总结**：锁的级别由低到高：无锁、偏向锁、轻量级锁、重量级锁。只能升级，不能降级

4. **公平锁和非公平锁**

   公平锁是多个线程按照申请顺序来获取锁，线程进入队列排队。

   ​	优点：等待锁的线程不会饿死。

   ​	缺点：吞吐效率比较低，CPU唤醒阻塞线程开销较大

   非公平锁多线程加锁时直接尝试获取锁，获取不到的才会等待队列的队尾等待。但如果此时锁刚好可用，那么这个线程可以无需阻塞直接获取锁。也就是后申请的线程可能先获取到锁。

   交接锁的时候，允许其他刚来的线程插队。

   ​	优点：减少唤醒线程的开销，吞吐量较高

   ​	缺点：等待队列中的线程有可能饿死或者等待很久

   **`ReentrantLock`**

   有一个内部类`Sync`，`Sync`继承自`AQS(AbstractQueuedSynchronizer)`，添加和释放锁都在`Sync`中实现。他有公平锁`FairSync`和非公平锁`NonFairSync`，他们唯一的区别是公平锁在获取同步状态时，多了一个条件`hasQueuedPredecessors()`，它是判断该线程是否位于同步队列的第一个，是返回`true`，否则放回`false`

5. **可重入锁和非可重入锁**

   可重入锁又称递归锁，指在同一个线程在外层方法获取锁的时候，再进入线程的内层方法会自动释放锁(前提锁对象得是同一个对象或class)，不会因为之前已经获取过还没释放而阻塞。

   内置锁`synchronized`是可重入锁

   ```java
   public class Widget {
       public synchronized void doSomething() {
           System.out.println("方法1执行...");
           doOthers();
       }
   
       public synchronized void doOthers() {
           System.out.println("方法2执行...");
       }
   }
   ```

   在`doSomething()`内调用`doOthers()`时，可以直接过得当前对象的锁。

   **可重入锁原理：**首先ReentrantLock和NonReentrantLock都继承父类AQS，其父类AQS中维护了一个同步状态status来计数重入次数，status初始值为0。

   当线程尝试获取锁时，可重入锁先尝试获取并更新status值，如果status == 0表示没有其他线程在执行同步代码，则把status置为1，当前线程开始执行。如果status != 0，则判断当前线程是否是获取到这个锁的线程，如果是的话执行status+1，且当前线程可以再次获取锁。而非可重入锁是直接去获取并尝试更新当前status的值，如果status != 0的话会导致其获取锁失败，当前线程阻塞。

   释放锁时，可重入锁同样先获取当前status的值，在当前线程是持有锁的线程的前提下。如果status-1 == 0，则表示当前线程所有重复获取锁的操作都已经执行完毕，然后该线程才会真正释放锁。而非可重入锁则是在确定当前线程是持有锁的线程之后，直接将status置为0，将锁释放。

   ![img](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2018b/32536e7a.png)

6. **独享锁和共享锁**

   读写锁也叫排他锁，锁一次只能被一个线程所持有。

   共享锁是指该锁可以被多个线程所持有。如果线程T对数据A加上共享锁后，则其他线程只能对A再加共享锁，不能加排它锁。获得共享锁的线程只能读数据，不能修改数据。

   ![img](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2018b/762a042b.png)

在独享锁中，state的值通常是0和1，如果是可重入锁，则是重入的次数

在共享锁中，state是持有锁的个数，有读锁和写锁，将state按位切割成两部分，高16位表示读锁个数，低16位表示写锁个数。

```java
protected final boolean tryAcquire(int acquires) {
	Thread current = Thread.currentThread();
	int c = getState(); // 取到当前锁的个数c
	int w = exclusiveCount(c); // 取写锁的个数w
	if (c != 0) { // 如果已经有线程持有了锁(c!=0)
    // (Note: if c != 0 and w == 0 then shared count != 0)
		if (w == 0 || current != getExclusiveOwnerThread()) // 如果写线程数（w）为0（换言之存在读锁） 或者持有锁的线程不是当前线程就返回失败
			return false;
		if (w + exclusiveCount(acquires) > MAX_COUNT)    // 如果写入锁的数量大于最大数（65535，2的16次方-1）就抛出一个Error。
      throw new Error("Maximum lock count exceeded");
		// Reentrant acquire
    setState(c + acquires);
    return true;
  }
  if (writerShouldBlock() || !compareAndSetState(c, c + acquires)) // 如果当且写线程数为0，并且当前线程需要阻塞那么就返回失败；或者如果通过CAS增加写线程数失败也返回失败。
		return false;
	setExclusiveOwnerThread(current); // 如果c=0，w=0或者c>0，w>0（重入），则设置当前线程或锁的拥有者
	return true;
}
```

- 这段代码首先取到当前锁的个数c，然后再通过c来获取写锁的个数w。因为写锁是低16位，所以取低16位的最大值与当前的c做与运算（ int w = exclusiveCount©; ），高16位和0与运算后是0，剩下的就是低位运算的值，同时也是持有写锁的线程数目。
- 在取到写锁线程的数目后，首先判断是否已经有线程持有了锁。如果已经有线程持有了锁(c!=0)，则查看当前写锁线程的数目，如果写线程数为0（即此时存在读锁）或者持有锁的线程不是当前线程就返回失败（涉及到公平锁和非公平锁的实现）。
- 如果写入锁的数量大于最大数（65535，2的16次方-1）就抛出一个Error。
- 如果当且写线程数为0（那么读线程也应该为0，因为上面已经处理c!=0的情况），并且当前线程需要阻塞那么就返回失败；如果通过CAS增加写线程数失败也返回失败。
- 如果c=0,w=0或者c>0,w>0（重入），则设置当前线程或锁的拥有者，返回成功！

###### 11. 引用

强：必不可少，内存不足也不会回收，OOM也不回收

软：有用但非必须，只有内存不足才回收，可以加速JVM对垃圾的回收速度，防止OOM，适用于实现缓存；

弱：非必须，JVM回收时无论内存是否充足，都会回收被弱引用关联的对象。

虚：在任何时候都可能被垃圾回收器回收，用来跟踪对象被垃圾回收的活动。虚引用必须和引用队列关联使用

###### Arrays.sort()

1. 长度大于286且数组接近排序完成(通过一个count记录降序个数，阈值67)，使用归并排序，否则使用快排

2. 长度大于47且小于286，使用快排

3. 长度小于47，插入排序

> 这里快排选了5个索引，5个数不同就是二路快排，否则单路快排，然后递归



###### 12. Java对象创建的方式

1. 使用new关键字，可以使用任意的函数

2. 反射机制：运用反射手段，调用`Java.lang.Class`或者`java.lang.reflect.Constructor`类的`newInstance()`实例方法

   > 区别
   >
   > * Class类的newInstance方法，调用**无参**的、共有的**可见的**构造函数创建对象
   >
   > * Constructor类的newInstance方法，可以调用有**任意参数**的和**私有**的构造函数

3. 使用clone方法：无论何时我们调用一个对象的clone方法，jvm就会创建一个新的对象，将前面对象的内容全部拷贝进去。用clone方法创建对象并不会调用任何构造函数。
   要使用clone方法，我们需要先实现Cloneable接口并实现其定义的clone方法。

4. 使用反序列化：在反序列化时JVM会创建一个单独的对象，不会调用任何构造函数

5. 第三方库：Objenesis







###### 13.代理模式

即通过代理对象方位目标对象，这样可以在不修改原目标对象的前提下，提供额外的操作，实现扩展

* 静态代理

  首先，定义接口和接口的实现类，然后定义接口的代理对象，将接口的实例注入到代理对象中，然后通过代理对象去调用真正的实现类

  缺点：代理类和委托类实现相同的接口，代理类通过委托类实现了相同的方法，因此**代码**出现了大量的**冗余**，**不易维护**；代理对象只服务于**一种类型**的对象

* JDK动态代理

  通过反射机制，在内存中构件对象，实现对目标对象的代理。又称JDK代理

  优点：不需要实现接口，只需目标对象实现接口，

* CGLIB动态代理






#### Java集合框架

###### 1. 说说 List,Set,Map 三者的区别？三者底层的数据结构？

有序？可重复？底层数据结构？

List、Set实现Collection接口

List：允许重复对象、有序、可以多个null、实现类：ArrayList、LinkedList

Set：不允许重复、无序、只能一个null、实现类：HashSet、LinkedHashSet、TreeSet

Map：接口，Entry存key value，key唯一不重复，实现类：HashMap、LinkedHashMap、HashTable

###### 2.有哪些集合是线程不安全的？怎么解决呢？

ArrayList、LinkedList、HashSet、HashMap、TreeMap

解决：

可以使用`Collections.synchronizedList(new ArrayList())`

`CopyOnWriteArrayList()`，加了`sychronized`关键字

`Collections.synchronizedArraySet(new HashSet<>())`

`CopyOnWriteArraySet`，完全利用了 CopyOnWriteArrayList 集合的方法，写时复制，不重复使用遍历比较，效率变低

使用`ConcurrentHashMap`

###### 3.比较 HashSet、LinkedHashSet 和 TreeSet 三者的异同

`HashSet`：无顺序，不同步，可以有null但只有一个

`LinkedHashSet`：用双向链表维护插入次序

`TreeSet`：实现`SortedSet`接口，通过TreeMap实现，元素按规则排序，支持自然排序和定制排序

###### 4.HashMap 和 Hashtable 的区别？

HashMap线程不安全，数组链表红黑树，允许null，哈希算法，扩容2倍，尾插，初始16

Hashtable线程安全，synchronized效率低，数组链表，非null，直接哈希，扩容2倍+1，初始11

###### HashMap 和 HashSet 区别？

HashMap：键值对，键无重复，数组链表红黑树，实现Map接口，

HashSet：集合，无重复，底层是HashMap，`key`存值，value是Object类型的常量PRESENT，实现Set接口，比较两个对象若hashCode同，再比`equals()`。

###### HashMap 和 TreeMap 区别？

HashMap：数组链表红黑树，允许null，通过hash算法定位，效率高

TreeMap：红黑树，不允许null，key有序，通过自定义或默认的key比较器来定位，效率低

###### 5.HashMap 的底层实现

可以对比1.7与1.8

底层数据结构、属性、默认值、构造方法

说一下hash()方法、位移算法求index、哈希碰撞，引出长度为2的幂和如何均匀hash

put、get、扩容的过程，判断是否空？是否需要扩容？是链表还是红黑树？更新键值计数？什么时候链表变树(8)？什么时候树还原链表(6)？

并发问题，为什么会循环链？后插法和前插法？

###### 6.HashMap 的长度为什么是 2 的幂次方

index的计算方法，哈希值  和  长度-1  求与，相当于取余，高位置零，只保留低位，但效率更高

###### 7.ConcurrentHashMap 和 Hashtable 的区别？

底层结构？用的锁有什么不同？哈希值的计算方法？扩容区别？头插法尾插法？分别实现啥接口？默认容量？统计size的方法？

###### 8.ConcurrentHashMap 线程安全的具体实现方式/底层具体实现

**Java 1.7**

初始化：默认容量16，负载因子0.75，默认并发级别16

核心参数：concurrencyLevel：并发级别

存储结构：`segment`数组(不可扩容)，每个`segment`有个类似`HashMap`的结构，放在`HashEntry`数组中，`HashEntry`可扩容

![Java 7 ConcurrentHashMap 存储结构](https://snailclimb.gitee.io/javaguide/docs/java/collection/images/image-20200405151029416.png)

原理上来说：ConcurrentHashMap 采用了分段锁技术，其中 Segment 继承于 ReentrantLock。不会像 HashTable 那样不管是 put 还是 get 操作都需要做同步处理，理论上 ConcurrentHashMap 支持 CurrencyLevel (Segment 数组数量)的线程并发。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment。

**put方法：**通过key定位到Segment，之后在对应的Segment中进行put。虽然value用的volatile修饰，但是不能保证原子性，put时，仍需加锁。

尝试获取自旋锁，如果尝试次数达到阈值，改为阻塞取锁

流程：将当前Segment中的table通过key的hashCode定位到HashEntry，然后遍历该HashEntry，如果不为空，则判断传入的key和当前遍历的key是否相等，相等则覆盖旧的value；HashEntry为空则新建一个，并加入到Segment中，同时判断是否需要扩容，最后释放当前Segment的锁



**Java 1.8**

存储结构：Node数组 + 链表 / 红黑树

**initTable**：通过自旋锁和CAS实现，使用了一变量`sizeCtl`表示状态

`sizeCtl = (1.5 * initialCapacity + 1)`，然后向上取最近的 2 的 n 次方

```java
public V put(K key, V value) {
    return putVal(key, value, false);
}

final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    // 得到 hash 值
    int hash = spread(key.hashCode());
    // 用于记录相应链表的长度
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        // 如果数组"空"，进行数组初始化
        if (tab == null || (n = tab.length) == 0)
            // 初始化数组，后面会详细介绍
            tab = initTable();

        // 找该 hash 值对应的数组下标，得到第一个节点 f
        // hash值 和 长度-1 求'与'
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            // 如果数组该位置为空，
            // 用一次 CAS 操作将这个新值放入其中即可，
            // 这个 put 操作差不多就结束了，可以拉到最后面了
            // 如果 CAS 失败，那就是有并发操作，进到下一个循环就好了
            if (casTabAt(tab, i, null,
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        // hash 居然可以等于 MOVED，这个需要到后面才能看明白，不过从名字上也能猜到，肯定是因为在扩容
        else if ((fh = f.hash) == MOVED)
            // 帮助数据迁移，这个等到看完数据迁移部分的介绍后，再理解这个就很简单了
            tab = helpTransfer(tab, f);

        else { // 到这里就是说，f 是该位置的头结点，而且不为空

            V oldVal = null;
            // 获取数组该位置的头结点的监视器锁
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) { // 头结点的 hash 值大于 0，说明是链表
                        // 用于累加，记录链表的长度
                        binCount = 1;
                        // 遍历链表
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            // 如果发现了"相等"的 key，判断是否要进行值覆盖，然后也就可以 break 了
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            // 到了链表的最末端，将这个新值放到链表的最后面
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) { // 红黑树
                        Node<K,V> p;
                        binCount = 2;
                        // 调用红黑树的插值方法插入新节点
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }
            // binCount != 0 说明上面在做链表操作
            if (binCount != 0) {
                // 判断是否要将链表转换为红黑树，临界值和 HashMap 一样，也是 8
                if (binCount >= TREEIFY_THRESHOLD)
                    // 这个方法和 HashMap 中稍微有一点点不同，那就是它不是一定会进行红黑树转换，
                    // 如果当前数组的长度小于 64，那么会选择进行数组扩容，而不是转换为红黑树
                    //    具体源码我们就不看了，扩容部分后面说
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    // 
    addCount(1L, binCount);
    return null;
}

```

初始化：通过CAS操作尝试初始化，

链表转红黑树：treeifyBin不一定会进行红黑树转换，如果数组长度小于64，会优先数组扩容

扩容：tryPresize：核心是1 次 transfer(tab, null) + 多次 transfer(tab, nt)

transfer：下面这个方法很点长，将原来的 tab 数组的元素迁移到新的 nextTab 数组中。

虽然我们之前说的 tryPresize 方法中多次调用 transfer 不涉及多线程，但是这个 transfer 方法可以在其他地方被调用，典型地，我们之前在说 put 方法的时候就说过了，请往上看 put 方法，是不是有个地方调用了 helpTransfer 方法，helpTransfer 方法会调用 transfer 方法的。

此方法支持多线程执行，外围调用此方法的时候，会保证第一个发起数据迁移的线程，nextTab 参数为 null，之后再调用此方法的时候，nextTab 不会为 null。

阅读源码之前，先要理解并发操作的机制。原数组长度为 n，所以我们有 n 个迁移任务，让每个线程每次负责一个小任务是最简单的，每做完一个任务再检测是否有其他没做完的任务，帮助迁移就可以了，而 Doug Lea 使用了一个 stride，简单理解就是步长，每个线程每次负责迁移其中的一部分，如每次迁移 16 个小任务。所以，我们就需要一个全局的调度者来安排哪个线程执行哪几个任务，这个就是属性 transferIndex 的作用。

第一个发起数据迁移的线程会将 transferIndex 指向原数组最后的位置，然后从后往前的 stride 个任务属于第一个线程，然后将 transferIndex 指向新的位置，再往前的 stride 个任务属于第二个线程，依此类推。当然，这里说的第二个线程不是真的一定指代了第二个线程，也可以是同一个线程，这个读者应该能理解吧。其实就是将一个大的迁移任务分为了一个个任务包。

```java
private final void transfer(Node<K,V>[] tab, Node<K,V>[] nextTab) {
    int n = tab.length, stride;

    // stride 在单核下直接等于 n，多核模式下为 (n>>>3)/NCPU，最小值是 16
    // stride 可以理解为'步长'，有 n 个位置是需要进行迁移的，
    // 将这 n 个任务分为多个任务包，每个任务包有 stride 个任务
    if ((stride = (NCPU > 1) ? (n >>> 3) / NCPU : n) < MIN_TRANSFER_STRIDE)
        stride = MIN_TRANSFER_STRIDE; // subdivide range

    // 如果 nextTab 为 null，先进行一次初始化
    // 前面我们说了，外围会保证第一个发起迁移的线程调用此方法时，参数 nextTab 为 null
    // 之后参与迁移的线程调用此方法时，nextTab 不会为 null
    if (nextTab == null) {
        try {
            // 容量翻倍
            Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n << 1];
            nextTab = nt;
        } catch (Throwable ex) {      // try to cope with OOME
            sizeCtl = Integer.MAX_VALUE;
            return;
        }
        // nextTable 是 ConcurrentHashMap 中的属性
        nextTable = nextTab;
        // transferIndex 也是 ConcurrentHashMap 的属性，用于控制迁移的位置
        transferIndex = n;
    }

    int nextn = nextTab.length;

    // ForwardingNode 翻译过来就是正在被迁移的 Node
    // 这个构造方法会生成一个Node，key、value 和 next 都为 null，关键是 hash 为 MOVED
    // 后面我们会看到，原数组中位置 i 处的节点完成迁移工作后，
    // 就会将位置 i 处设置为这个 ForwardingNode，用来告诉其他线程该位置已经处理过了
    // 所以它其实相当于是一个标志。
    ForwardingNode<K,V> fwd = new ForwardingNode<K,V>(nextTab);


    // advance 指的是做完了一个位置的迁移工作，可以准备做下一个位置的了
    boolean advance = true;
    boolean finishing = false; // to ensure sweep before committing nextTab

    /*
     * 下面这个 for 循环，最难理解的在前面，而要看懂它们，应该先看懂后面的，然后再倒回来看
     * 
     */

    // i 是位置索引，bound 是边界，注意是从后往前
    for (int i = 0, bound = 0;;) {
        Node<K,V> f; int fh;

        // 下面这个 while 真的是不好理解
        // advance 为 true 表示可以进行下一个位置的迁移了
        // 简单理解结局：i 指向了 transferIndex，bound 指向了 transferIndex-stride
        while (advance) {
            int nextIndex, nextBound;
            if (--i >= bound || finishing)
                advance = false;

            // 将 transferIndex 值赋给 nextIndex
            // 这里 transferIndex 一旦小于等于 0，说明原数组的所有位置都有相应的线程去处理了
            else if ((nextIndex = transferIndex) <= 0) {
                i = -1;
                advance = false;
            }
            else if (U.compareAndSwapInt
                     (this, TRANSFERINDEX, nextIndex,
                      nextBound = (nextIndex > stride ?
                                   nextIndex - stride : 0))) {
                // 看括号中的代码，nextBound 是这次迁移任务的边界，注意，是从后往前
                bound = nextBound;
                i = nextIndex - 1;
                advance = false;
            }
        }
        if (i < 0 || i >= n || i + n >= nextn) {
            int sc;
            if (finishing) {
                // 所有的迁移操作已经完成
                nextTable = null;
                // 将新的 nextTab 赋值给 table 属性，完成迁移
                table = nextTab;
                // 重新计算 sizeCtl：n 是原数组长度，所以 sizeCtl 得出的值将是新数组长度的 0.75 倍
                sizeCtl = (n << 1) - (n >>> 1);
                return;
            }

            // 之前我们说过，sizeCtl 在迁移前会设置为 (rs << RESIZE_STAMP_SHIFT) + 2
            // 然后，每有一个线程参与迁移就会将 sizeCtl 加 1，
            // 这里使用 CAS 操作对 sizeCtl 进行减 1，代表做完了属于自己的任务
            if (U.compareAndSwapInt(this, SIZECTL, sc = sizeCtl, sc - 1)) {
                // 任务结束，方法退出
                if ((sc - 2) != resizeStamp(n) << RESIZE_STAMP_SHIFT)
                    return;

                // 到这里，说明 (sc - 2) == resizeStamp(n) << RESIZE_STAMP_SHIFT，
                // 也就是说，所有的迁移任务都做完了，也就会进入到上面的 if(finishing){} 分支了
                finishing = advance = true;
                i = n; // recheck before commit
            }
        }
        // 如果位置 i 处是空的，没有任何节点，那么放入刚刚初始化的 ForwardingNode ”空节点“
        else if ((f = tabAt(tab, i)) == null)
            advance = casTabAt(tab, i, null, fwd);
        // 该位置处是一个 ForwardingNode，代表该位置已经迁移过了
        else if ((fh = f.hash) == MOVED)
            advance = true; // already processed
        else {
            // 对数组该位置处的结点加锁，开始处理数组该位置处的迁移工作
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    Node<K,V> ln, hn;
                    // 头结点的 hash 大于 0，说明是链表的 Node 节点
                    if (fh >= 0) {
                        // 下面这一块和 Java7 中的 ConcurrentHashMap 迁移是差不多的，
                        // 需要将链表一分为二，
                        // 找到原链表中的 lastRun，然后 lastRun 及其之后的节点是一起进行迁移的
                        // lastRun 之前的节点需要进行克隆，然后分到两个链表中
                        int runBit = fh & n;
                        Node<K,V> lastRun = f;
                        for (Node<K,V> p = f.next; p != null; p = p.next) {
                            int b = p.hash & n;
                            if (b != runBit) {
                                runBit = b;
                                lastRun = p;
                            }
                        }
                        if (runBit == 0) {
                            ln = lastRun;
                            hn = null;
                        }
                        else {
                            hn = lastRun;
                            ln = null;
                        }
                        for (Node<K,V> p = f; p != lastRun; p = p.next) {
                            int ph = p.hash; K pk = p.key; V pv = p.val;
                            if ((ph & n) == 0)
                                ln = new Node<K,V>(ph, pk, pv, ln);
                            else
                                hn = new Node<K,V>(ph, pk, pv, hn);
                        }
                        // 其中的一个链表放在新数组的位置 i
                        setTabAt(nextTab, i, ln);
                        // 另一个链表放在新数组的位置 i+n
                        setTabAt(nextTab, i + n, hn);
                        // 将原数组该位置处设置为 fwd，代表该位置已经处理完毕，
                        //    其他线程一旦看到该位置的 hash 值为 MOVED，就不会进行迁移了
                        setTabAt(tab, i, fwd);
                        // advance 设置为 true，代表该位置已经迁移完毕
                        advance = true;
                    }
                    else if (f instanceof TreeBin) {
                        // 红黑树的迁移
                        TreeBin<K,V> t = (TreeBin<K,V>)f;
                        TreeNode<K,V> lo = null, loTail = null;
                        TreeNode<K,V> hi = null, hiTail = null;
                        int lc = 0, hc = 0;
                        for (Node<K,V> e = t.first; e != null; e = e.next) {
                            int h = e.hash;
                            TreeNode<K,V> p = new TreeNode<K,V>
                                (h, e.key, e.val, null, null);
                            if ((h & n) == 0) {
                                if ((p.prev = loTail) == null)
                                    lo = p;
                                else
                                    loTail.next = p;
                                loTail = p;
                                ++lc;
                            }
                            else {
                                if ((p.prev = hiTail) == null)
                                    hi = p;
                                else
                                    hiTail.next = p;
                                hiTail = p;
                                ++hc;
                            }
                        }
                        // 如果一分为二后，节点数少于 8，那么将红黑树转换回链表
                        ln = (lc <= UNTREEIFY_THRESHOLD) ? untreeify(lo) :
                            (hc != 0) ? new TreeBin<K,V>(lo) : t;
                        hn = (hc <= UNTREEIFY_THRESHOLD) ? untreeify(hi) :
                            (lc != 0) ? new TreeBin<K,V>(hi) : t;

                        // 将 ln 放置在新数组的位置 i
                        setTabAt(nextTab, i, ln);
                        // 将 hn 放置在新数组的位置 i+n
                        setTabAt(nextTab, i + n, hn);
                        // 将原数组该位置处设置为 fwd，代表该位置已经处理完毕，
                        //    其他线程一旦看到该位置的 hash 值为 MOVED，就不会进行迁移了
                        setTabAt(tab, i, fwd);
                        // advance 设置为 true，代表该位置已经迁移完毕
                        advance = true;
                    }
                }
            }
        }
    }
}

```



哪些重要的变量？put？get？remove？**initTable**(判空和sizeCtl，循环，判空，CAS？)？扩容？只锁**bucket**



#### 多线程

> 面积博客整理

1. 线程和进程的区别。

   CPU调度、资源分配；

2. 什么是上下文切换?

   从任务保存到再加载的过程，通过CPU的时间片分配实现

3. 什么是线程死锁?如何避免死锁?

   https://blog.csdn.net/ls5718/article/details/51896159

   互斥条件：进程要求对所分配的资源（如打印机）进行排他性控制，即在一段时间内某 资源仅为一个进程所占有。此时若有其他进程请求该资源，则请求进程只能等待。
   不剥夺条件：进程所获得的资源在未使用完毕之前，不能被其他进程强行夺走，即只能 由获得该资源的进程自己来释放（只能是主动释放)。
   请求和保持条件：进程已经保持了至少一个资源，但又提出了新的资源请求，而该资源 已被其他进程占有，此时请求进程被阻塞，但对自己已获得的资源保持不放。
   循环等待条件：存在一种进程资源的循环等待链，链中每一个进程已获得的资源同时被 链中下一个进程所请求。即存在一个处于等待状态的进程集合{Pl, P2, ..., pn}，其中Pi等 待的资源被P(i+1)占有（i=0, 1, ..., n-1)，Pn等待的资源被P0占有，如图2-15所示。

   死锁避免：

   1. 加锁顺序（线程按照一定的顺序加锁）
   2. 加锁时限（线程尝试获取锁的时候加上一定的时限，超过时限则放弃对该锁的请求，并释放自己占有的锁）
   3. 死锁检测

4. 乐观锁和悲观锁了解么？

   乐观锁：**CAS**，总是假设每次资源访问不会冲突，无锁并发

   CAS三个参数：V、E、N；CAS是一种系统原语；

   悲观锁：总是认为每次访问共享资源时会冲突

   **synchronized**

   作用于实例方法，锁的是对象

   作用于静态方法，是当前类的class对象锁

   作用于同步代码块，当前实例对象就是锁对象

   **底层**：对象头的结构、重量级锁和标志位10、指针指向monitor对象(监视器锁)的起始地址、ObjectMonitor有俩队列：EntryList和WaitSet，多个线程访问同步代码，先进EntryList，拿到monitor锁进Owner，计数加1，线程wait后，进WaitSet，计数减1；线程执行完毕，释放monitor锁，复位计数

   **字节码中**：monitorenter和monitorexit，争夺monitor所有权

   **如何判断同步方法？**常量池中的`ACC_SYNCHRONIZED`访问标志

   

   

5. 说说 `sleep()` 方法和 `wait()` 方法区别和共同点?

   sleep是Thread类的方法，wait是Object类的方法

   sleep不会导致锁行为的改变，不会释放锁，线程进入阻塞状态；wait方法释放了锁，等待notify，然后进入锁池，拿到锁后才能继续执行

   sleep和wait都会暂停当前线程；但是调用wait之后，需要notify/notifyAll才能重新获得CPU执行时间

   wait需要在同步代码块中使用，sleep不用

   ![img](https://img-blog.csdnimg.cn/2019050816141738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIwMDA5MDE1,size_16,color_FFFFFF,t_70)

   

   

6. **Java 线程池有哪些参数？阻塞队列有几种？拒绝策略有几种？新线程添加的流程?**

7. ##### Java线程池相关

   https://tech.meituan.com/2020/04/02/java-pooling-pratice-in-meituan.html

   http://www.ideabuffer.cn/2017/04/04/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Java%E7%BA%BF%E7%A8%8B%E6%B1%A0%EF%BC%9AThreadPoolExecutor/

   ###### 什么线程池？

   池化思想管理线程的工具。减少创建、销毁、调度线程的开销。有效避免线程数量膨胀，保证内核的充分利用

   优点：降低资源消耗、提高响应速度、提高线程可管理性、提供功能扩展。

   解决的问题：核心就是资源管理问题。并发环境下，资源分配的不确定性，可能会带来内存消耗、资源耗尽、系统稳定性降低等

   ###### 线程池的核心设计与实现——ThreadPollExecutor类

   1. 总体设计

      <img src="C:%5CUsers%5Clqy98%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210413151758624.png" alt="image-20210413151758624" style="zoom: 50%;" />

      顶层`Executor`思想：任务提交和任务执行进行解耦。用户不用关注咋创建、调度线程，只需提供Runnable对象，将任务的运行逻辑丢到执行器Excutor中即可。

      `ExecutorService`接口：增加了：(1) 扩充了可以为一个或一批异步任务生成Future方法；(2) 提供线程管控方法，如停止线程池运行。

      `AbstractExecutorService`：是上层的抽象类，串起整个流程，保证下层只需关注一个执行任务的方法即可

      `ThreadPoolExcutor`实现类：实现最复杂的运行部分

      <img src="C:%5CUsers%5Clqy98%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210413152237279.png" alt="image-20210413152237279" style="zoom: 200%;" />

      线程池运行机制：

      1. 线程池如何维护自身状态。
      2. 线程池如何管理任务。
      3. 线程池如何管理线程。

   

   2. 生命周期管理

      **通过一个变量维护两个值**：运行状态(runState)和线程数量 (workerCount)。高3位为运行状态，低29位为线程数量。

      这样设计的优点：不必为了维护两者的一致性而消耗锁资源

      ```java
      private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
      ```

      ThreadPoolExcutor的5种**运行状态**

      ![image-20210413152941582](C:%5CUsers%5Clqy98%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210413152941582.png)

      **生命周期转换**

      ![image-20210413153008686](C:%5CUsers%5Clqy98%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210413153008686.png)

   3. 任务执行机制

      3.1 任务调度

      要考虑线程池的运行状态、运行线程数、运行策略等

      1. 首先检测线程池运行状态，如果不是RUNNING，则直接拒绝，线程池要保证在RUNNING的状态下执行任务。
      2. 如果workerCount < corePoolSize，则创建并启动一个线程来执行新提交的任务。
      3. 如果workerCount >= corePoolSize，且线程池内的阻塞队列未满，则将任务添加到该阻塞队列中。
      4. 如果workerCount >= corePoolSize && workerCount < maximumPoolSize，且线程池内的阻塞队列已满，则创建并启动一个线程来执行新提交的任务。
      5. 如果workerCount >= maximumPoolSize，并且线程池内的阻塞队列已满, 则根据拒绝策略来处理该任务, 默认的处理方式是直接抛异常。

      ![image-20210413154453805](C:%5CUsers%5Clqy98%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20210413154453805.png)

      3.2 任务缓冲

      任务缓冲模块是线程池能够管理任务的核心部分。线程池中是以生产者消费者模式，通过一个阻塞队列来实现的。阻塞队列缓存任务，工作线程从阻塞队列中获取任务。

      > 阻塞队列：是一个支持两个附加操作的队列。这两个附加的操作是：在队列为空时，获取元素的线程会等待队列变为非空。当队列满时，存储元素的线程会等待队列可用。

      ![image-20210413155718301](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413155718301.png)

      3.3 任务申请

      线程需要从任务缓存模块中不断地取任务执行，帮助线程从阻塞队列中获取任务，实现线程管理模块和任务管理模块之间的通信。这部分策略由`getTask`方法实现，其执行流程如下图所示：

      ![image-20210413160125393](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413160125393.png)

      `getTask`这部分进行了多次判断，为的是控制线程的数量，使其符合线程池的状态。如果线程池现在不应该持有那么多线程，则会返回null值。工作线程`Worker`会不断接收新任务去执行，而当工作线程`Worker`接收不到任务的时候，就会开始被回收。

      3.4 任务拒绝

      当线程池的线程数达到最大容量(maximumPoolSize)时，会采取拒绝策略，保护线程池

      ```java
      // 可以实现该接口定制拒绝策略
      public interface RejectedExecutionHandler {
          void rejectedExecution(Runnable r, ThreadPoolExecutor executor);
      }
      ```

      JDK默认的4种拒绝策略

      ![image-20210413160420247](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413160420247.png)

   4. Worker线程管理

      4.1 Worker线程

      ```java
      private final class Worker extends AbstractQueuedSynchronizer implements Runnable{
          final Thread thread;//Worker持有的线程
          Runnable firstTask;//初始化的任务，可以为null
      }
      ```

      线程启动时，有firstTask就执行这个初始化任务，无则直接getTask获取任务执行

      ![image-20210413161907133](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413161907133.png)

      线程池通过一张**Hash表**来持有线程的引用，以便通过引用来控制线程的生命周期。

      worker继承了AQS，使用了AQS的独占锁。没有使用可重入锁，是为了实现不可重入的特性去反应线程现在的执行状态(?)

      1. lock方法一旦获取了独占锁，表示当前线程正在执行任务中。

      2. 如果正在执行任务，则不应该中断线程。

      3. 如果该线程现在不是独占锁的状态，也就是空闲的状态，说明它没有在处理任务，这时可以对该线程进行中断。

      4. 线程池在执行shutdown方法或tryTerminate方法时会调用interruptIdleWorkers方法来中断空闲的线程，interruptIdleWorkers方法会使用tryLock方法来判断线程池中的线程是否是空闲状态；如果线程是空闲状态则可以安全回收。

      ![image-20210413162351960](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413162351960.png)

   4.2 Worker线程增加

   通过线程池的addWorker方法，功能就是增加一个线程。不考虑是哪个阶段增加，这是之前步骤的任务。

   两个参数：firstTask、core。firstTask参数用于指定新增的线程执行的第一个任务，该参数可以为空；core参数为true表示在新增线程时会判断当前活动线程数是否少于corePoolSize，false表示新增线程前需要判断当前活动线程数是否少于maximumPoolSize

   ![image-20210413163043485](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413163043485.png)

   4.3 Worker线程回收

   线程池中线程的销毁依赖JVM自动的回收，线程池做的工作是根据当前线程池的状态维护一定数量的线程引用，防止这部分线程被JVM回收，当线程池决定哪些线程需要回收时，只需要将其引用消除即可。Worker被创建出来后，就会不断地进行轮询，然后获取任务去执行，核心线程可以无限等待获取任务，非核心线程要限时获取任务。当Worker无法获取到任务，也就是获取的任务为空时，循环会结束，Worker会主动消除自身在线程池内的引用。

   ```java
   try {
     while (task != null || (task = getTask()) != null) {
       //执行任务
     }
   } finally {
     processWorkerExit(w, completedAbruptly);//获取不到任务时，主动回收自己
   }
   ```

   线程回收通过processWorkerExit方法完成

   ![image-20210413163346334](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413163346334.png)

   4.4 Worker线程执行任务

   在Worker类中的run方法调用了runWorker方法来执行任务，runWorker方法的执行过程如下：

   1. while循环不断地通过getTask()方法获取任务。

   2. getTask()方法从阻塞队列中取任务。

   3. 如果线程池正在停止，那么要保证当前线程是中断状态，否则要保证当前线程不是中断状态。

   4. 执行任务。

   5. 如果getTask结果为null则跳出循环，执行processWorkerExit()方法，销毁线程。

   <img src="D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413163514698.png" alt="image-20210413163514698" style="zoom:50%;" />

   ###### 线程池构造参数

   **核心线程数：corePoolSize**

   线程池中活跃的线程数，即使它们是空闲的，除非设置了allowCoreThreadTimeOut为true。allowCoreThreadTimeOut的值是控制核心线程数是否在没有任务时是否停止活跃的线程，当它的值为true时，在线程池没有任务时，所有的工作线程都会停止。

   **最大线程数：maximumPoolSize**

   线程池所允许存在的最大线程数。

   **多余线程存活时长：keepAliveTime**

   线程池中除核心线程数之外的线程（多余线程）的最大存活时间，如果在这个时间范围内，多余线程没有任务需要执行，则多余线程就会停止。(注意：多余线程数 = 最大线程数 - 核心线程数)

   **时间单位：unit**

   多余线程存活时间的单位，可以是分钟、秒、毫秒等。

   **任务队列：workQueue**

   线程池的任务队列，使用线程池执行任务时，任务会先提交到这个队列中，然后工作线程取出任务进行执行，当这个队列满了，线程池就会执行拒绝策略。

   **线程工厂：threadFactory**

   创建线程池的工厂，线程池将使用这个工厂来创建线程池，自定义线程工厂需要实现ThreadFactory接口。

   **拒绝执行处理器（也称拒绝策略）：handler**

   当线程池无空闲线程，并且任务队列已满，此时将线程池将使用这个处理器来处理新提交的任务。

   ###### 具体应用相关拓展

   动态化线程池

   核心参数为：corePoolSize、maximumPoolSize，workQueue

   实际应用中我们获取并发性的场景主要是两种：（1）并行执行子任务，提高响应速度。这种情况下，应该使用同步队列，没有什么任务应该被缓存下来，而是应该立即执行。（2）并行执行大批次任务，提升吞吐量。这种情况下，应该使用有界队列，使用队列去缓冲大批量的任务，队列容量必须声明，防止任务无限制堆积。

   <img src="D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210413215322759.png" alt="image-20210413215322759" style="zoom:67%;" />

   

8. 实现 `Runnable` 接口和 `Callable` 接口的区别。

   实现Runnable接口无返回值，不能抛出异常，有异常在run内部处理，执行的方法是run，

   实现Callable接口可以返回执行结果，泛型指定返回值类型，执行的方法是call，可以抛出异常，

9. **讲一下 JMM(Java 内存模型)。`volatile` 关键字解决了什么问题？说说 `synchronized` 关键字和 `volatile` 关键字的区别。** 

   https://blog.csdn.net/javazejian/article/details/72772461

   Java内存区域与Java内存模型

   Java内存模型：抽象概念、一组规范、主内存和工作内存、修改数据的过程、数据类型和引用的存储位置

   硬件内存架构与Java内存模型

   硬件：CPU、多级缓存、主内存，可以讲讲缓存命中、更新

   Java线程与硬件处理器：一对一线程模型，轻量级的进程调用内核线程，进而由操作系统内核将任务映射到各个处理器

   Java内核模型与硬件内存架构的关系：JMM是**抽象概念**，不影响硬件内存。无论是主内存还是工作内存，在硬件中都在主内存，也可能是CPU缓存或寄存器，因此是**互相交叉**的

   JMM的必要性：由于JVM运行程序的实体是线程，而每个线程创建时JVM都会为其创建一个工作内存(有些地方称为栈空间)，用于存储线程私有的数据，线程与主内存中的变量操作必须通过工作内存间接完成，主要过程是将变量从主内存拷贝的每个线程各自的工作内存空间，然后对变量进行操作，操作完成后再将变量写回主内存，如果存在两个线程同时对一个主内存中的实例对象的变量进行操作就有可能诱发线程安全问题。
   举个脏读例子

   JMM的承诺：原子性、指令重排、编译器重排、处理器指令重排、可见性、有序性

   JMM提供的解决方案：方法或代码块可以用synchronized、重入锁。可见性可以用synchronized或volatile

   happens-before原则：程序顺序原则、锁规则、volatile规则、线程启动规则、传递性、线程终止规则、线程中断规则、对象终结规则

   volatile：可见性、禁止重排优化、内存屏障

   volatile的可见性：被volatile修饰的变量，对所有线程总是可见的，但是在多线程的情况下，操作该变量并不保证安全性，并不具备原子性，仍然是先读取，后写。因此为了保证原子性，仍然需要使用synchronized修饰

   

   

10. **AQS 原理了解么？AQS 组件有哪些？**

    https://tech.meituan.com/2019/12/05/aqs-theory-and-apply.html

    http://www.ideabuffer.cn/2017/03/15/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3AbstractQueuedSynchronizer%EF%BC%88%E4%B8%80%EF%BC%89/

    AbstractQueuedSynchronizer提供了一个CLH变体的FIFO双端队列，可以看做是一个可以用来实现锁以及其他需要同步功能的框架。

    AQS是一种提供了原子式管理同步状态、阻塞和唤醒线程功能以及队列模型的简单框架。

    这里简称该类为AQS。AQS的使用依靠继承来完成，子类通过继承自AQS并实现所需的方法来管理同步状态。

    AQS的**功能**可以分为两种：**独占和共享**。常用类：`ReentrantReadWriteLock`，其实是通过两个内部类来分别实现这两个功能，提供读锁和写锁的功能。**子类实现时，要么实现独占，要么实现共享**

    

    **数据结构**

    一个FIFO的队列，队列元素为Node

    ```java
    static final class Node {
        int waitStatus;
        Node prev;
        Node next;
        Node nextWaiter;
        Thread thread;
    }
    ```

    - waitStatus：表示节点的状态，其中包含的状态有：
      - *CANCELLED*：值为1，表示当前节点被取消；
      - *SIGNAL*：值为-1，表示当前节点的的后继节点将要或者已经被阻塞，在当前节点释放的时候需要unpark后继节点；
      - *CONDITION*：值为-2，表示当前节点在等待condition，即在condition队列中；
      - *PROPAGATE*：值为-3，表示releaseShared需要被传播给后续节点（仅在共享模式下使用）；
      - *0*：无状态，表示当前节点在队列中等待获取锁。
    - *prev*：前继节点；
    - *next*：后继节点；
    - *nextWaiter*：存储condition队列中的后继节点；
    - *thread*：当前线程。

    其中，队列里还有一个`head`节点和一个`tail`节点，分别表示头结点和尾节点，其中头结点不存储Thread，仅保存next结点的引用。(也就是虚拟头结点)

    AQS中有一个`state`变量，该变量对不同的子类实现具有不同的意义，对ReentrantLock来说，它表示加锁的状态：

    ```java
    private volatile int state;
    ```

    - 无锁时state=0，有锁时state>0；
    - 第一次加锁时，将state设置为1；
    - 由于ReentrantLock是可重入锁，所以持有锁的线程可以多次加锁，经过判断加锁线程就是当前持有锁的线程时（即`exclusiveOwnerThread==Thread.currentThread()`），即可加锁，每次加锁都会将state的值+1，state等于几，就代表当前持有锁的线程加了几次锁;
    - 解锁时每解一次锁就会将state减1，state减到0后，锁就被释放掉，这时其它线程可以加锁；
    - 当持有锁的线程释放锁以后，如果是等待队列获取到了加锁权限，则会在等待队列头部取出第一个线程去获取锁，获取锁的线程会被移出队列；

    **ReentrantLock类的结构**

    ```java
    public void lock() {
        sync.lock();
    }
    ```

    该方法调用了`sync`实例的lock方法，这里要说明一下ReentrantLock中的几个内部类：

    - Sync
    - FairSync
    - NonfairSync

    对于ReentrantLock，有两种获取锁的模式：公平锁和非公平锁。所以对应有两个内部类，都继承自Sync。而Sync继承自AQS

    公平锁FairSync的定义

    ```java
    /**
     * Sync object for fair locks
     */
    static final class FairSync extends Sync {
        private static final long serialVersionUID = -3000897897090466540L;
        final void lock() {
            acquire(1);
        }
        /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            // 获取state
            int c = getState();
            // state=0表示当前队列中没有线程被加锁
            if (c == 0) {
                /*
                 * 首先判断是否有前继结点，如果没有则当前队列中还没有其他线程；
                 * 设置状态为acquires，即lock方法中写死的1（这里为什么不直接setState？因为可能同时有多个线程同时在执行到此处，所以用CAS来执行）；
                 * 设置当前线程独占锁。
                 */
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            /*
             * 如果state不为0，表示已经有线程独占锁了，这时还需要判断独占锁的线程是否是当前的线程，原因是由于ReentrantLock为可重入锁；
             * 如果独占锁的线程是当前线程，则将状态加1，并setState;
             * 这里为什么不用compareAndSetState？因为独占锁的线程已经是当前线程，不需要通过CAS来设置。
             */
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
    }
    ```

    **AQS获取独占锁的具体实现**

    **acquire方法**

    AQS中的方法

    ```java
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
    ```

    尝试获取锁，如果成功，就将当前线程封装成Node，添加到队尾；如果不成功，设置线程中断

    **tryAcquire方法**

    FairSync中重写了AQS中的方法，具体的实现上面列出了，处理逻辑如下：

    ​	获取当前state，若为0，表示无线程被加锁，先判断是否有前驱结点，若`hasQueuedPredecessors`返回false，则表示当前线程可以争取共享资源，那么会继续通过CAS去修改state的状态(使用CAS是因为此时可能有多个线程在修改)，成功则设置当前线程独占锁；

    ​	如果当前state不为0，则表示已有线程独占锁了，判断独占锁线程是否为当前线程(因为是可重入)，如果是，state状态加1。这里不用CAS，因为独占线程已经是当前线程了，只能是一个。

    > 补充一下`hasQueuedPredecessors`的实现：返回false表示当前线程可以争取共享资源；返回true表示队列中有有效节点，当前线程必须加入到等待队列
    >
    > `h != t`：表示当前队列有节点
    >
    > `(s = h.next) == null`：表示有结点正在初始化，并将tail指向了head，此时应该返回true
    >
    > `s.thread != Thread.currentThread()`：说明等待队列中至少有一个有效节点，应该返回true

    ```java
    public final boolean hasQueuedPredecessors() {
    	// The correctness of this depends on head being initialized
    	// before tail and on head.next being accurate if the current
    	// thread is first in queue.
    	Node t = tail; // Read fields in reverse initialization order
    	Node h = head;
    	Node s;
    	return h != t && ((s = h.next) == null || s.thread != Thread.currentThread());
    }
    ```

    

    `tryAcquire`在AQS中的定义如下

    ```java
    protected boolean tryAcquire(int arg) {
        throw new UnsupportedOperationException();
    }
    ```

    > 这里没有使用`abstract`来修饰，为啥？因为AQS提供两种功能：独占和共享，子类只能实现一种功能。如果用`abstract`来修饰，子类就必须实现所有方法了，这对子类不友好

    **addWaiter方法**

    当执行Acquire(1)时，调用tryAcquire尝试获取锁，如果获取失败，就会调用addWaiter方法，封装该线程为一个Node节点，加入队尾

    ```java
    private Node addWaiter(Node mode) {
        // 根据当前线程创建一个Node对象
        Node node = new Node(Thread.currentThread(), mode);
        // Try the fast path of enq; backup to full enq on failure
        Node pred = tail;
        // 判断tail是否为空，如果为空表示队列是空的，直接enq
        if (pred != null) {
            node.prev = pred;
            // 这里尝试CAS来设置队尾，如果成功则将当前节点设置为tail，否则enq
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        enq(node);
        return node;
    }
    ```

    **enq方法**

    如果tail为空，重复尝试：根据当前节点初始化tail节点，直到成功

    ```java
    private Node enq(final Node node) {
        // 重复直到成功
        for (;;) {
            Node t = tail;
            // 如果tail为null，则必须创建一个Node节点并进行初始化
            if (t == null) { // Must initialize
                if (compareAndSetHead(new Node()))
                    tail = head;
            } else {
                node.prev = t;
                // 尝试CAS来设置队尾
                if (compareAndSetTail(t, node)) {
                    t.next = node;
                    return t;
                }
            }
        }
    }
    ```

    **acquireQueued方法(重要)**

    对排队中的线程进行“获取锁”操作

    循环尝试获取锁，直到成功，最后返回中断标志位

    ```java
    final boolean acquireQueued(final Node node, int arg) {
        // 标记是否成功拿到资源
        boolean failed = true;
        try {
            // 中断标志位：记录等待过程中，是否中断过，为了之后获取到锁后补一次中断(Java的协作式中断机制)
            boolean interrupted = false;
            // 开始自旋，要么成功获取锁，要么中断
            for (;;) {
                // 获取前继节点
                final Node p = node.predecessor();
                // 如果前继节点是head，则尝试获取
                if (p == head && tryAcquire(arg)) {
                    // 设置head为当前节点（head中不包含thread）
                    setHead(node);
                    // 清除之前的head
                    p.next = null; // help GC
                    failed = false;
                    return interrupted;
                }
                // 如果p不是head或者获取锁失败，判断是否需要进行park
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    interrupted = true;
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
    ```

    几个问题：

    * 为什么要`park`？
    * 为啥要判断中断状态？
    * 死循环不会引起CPU飙升？

    1. 为什么要park？

    `shouldParkAfterFailedAcquire`的源码

    ```java
    private static boolean shouldParkAfterFailedAcquire(Node pred, Node node) {
        int ws = pred.waitStatus;
        if (ws == Node.SIGNAL)
            /*
             * This node has already set status asking a release
             * to signal it, so it can safely park.
             */
            return true;
        if (ws > 0) {
            /*
             * Predecessor was cancelled. Skip over predecessors and
             * indicate retry.
             */
            do {
                // 循环向前找已被取消的节点，从队列中将他们删除
                node.prev = pred = pred.prev;
            } while (pred.waitStatus > 0);
            pred.next = node;
        } else {
            /*
             * waitStatus must be 0 or PROPAGATE.  Indicate that we
             * need a signal, but don't park yet.  Caller will need to
             * retry to make sure it cannot acquire before parking.
             */
            compareAndSetWaitStatus(pred, ws, Node.SIGNAL);
        }
        return false;
    }
    ```

    * 前一个节点的waitStatus为SIGNAL(-1)时，需要park
    * 前一个节点的waitStatus大于0，表示已被取消，删除前一节点，这里会循环删除前面所有的已被取消的节点
    * 其他情况，会设置前驱节点的waitStatus为SIGNAL

    2. 为什么要判断中断状态？

       `parkAndCheckInterrupt`的源码

       阻塞当前线程，复位中断状态并返回

       调用了Unsafe类的park方法，阻塞进程。

       > 如果当前线程是中断状态，执行wait会报异常；park不会，而是直接返回

       ```java
       private final boolean parkAndCheckInterrupt() {
           LockSupport.park(this);
           return Thread.interrupted();
       }
       ```

       这里的核心目的就是不让循环一直执行，而是让当前线程进入阻塞状态，防止CPU飙升。

    **cancelAcquire方法**

    在acquireQueued方法的finally语句块中，如果在循环的过程中出现了异常，则执行cancelAcquire方法，用于将该节点标记为取消状态。该方法代码如下：

    ```java
    // java.util.concurrent.locks.AbstractQueuedSynchronizer
    
    private void cancelAcquire(Node node) {
      // 将无效节点过滤
    	if (node == null)
    		return;
      // 设置该节点不关联任何线程，也就是虚节点
    	node.thread = null;
    	Node pred = node.prev;
      // 通过前驱节点，跳过取消状态的node
    	while (pred.waitStatus > 0)
    		node.prev = pred = pred.prev;
      // 获取过滤后的前驱节点的后继节点
    	Node predNext = pred.next;
      // 把当前node的状态设置为CANCELLED
    	node.waitStatus = Node.CANCELLED;
      // 如果当前节点是尾节点，将从后往前的第一个非取消状态的节点设置为尾节点
      // 更新失败的话，则进入else，如果更新成功，将tail的后继节点设置为null
    	if (node == tail && compareAndSetTail(node, pred)) {
    		compareAndSetNext(pred, predNext, null);
    	} else {
    		int ws;
        	// 如果当前节点不是head的后继节点
            // 1:判断当前节点前驱节点的是否为SIGNAL
            // 2:如果不是，则把前驱节点设置为SINGAL看是否成功
        	// 如果1和2中有一个为true，再判断当前节点的线程是否为null
        	// 如果上述条件都满足，把当前节点的前驱节点的后继指针指向当前节点的后继节点
    		if (pred != head && ((ws = pred.waitStatus) == Node.SIGNAL || (ws <= 0 && compareAndSetWaitStatus(pred, ws, Node.SIGNAL))) && pred.thread != null) {
    			Node next = node.next;
    			if (next != null && next.waitStatus <= 0)
    				compareAndSetNext(pred, predNext, next);
    		} else {
          // 如果当前节点是head的后继节点，或者上述条件不满足，那就唤醒当前节点的后继节点
    			unparkSuccessor(node);
    		}
    		node.next = node; // help GC
    	}
    }
    ```

    首先，将当前节点的前驱指针指向前面第一个不是取消状态的节点，将当前节点的waitStatus设置为取消状态。

    接下来分为3种情况：

    * 当前节点为尾结点
    * 当前节点是Head的后继节点
    * 当前节点不是Head的后继，也不是tail，即中间的节点

    1. 当前节点为尾结点

       将前驱的后继指针改为null，注意这里并没有改变当前节点的前驱指针！

       <img src="D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210416130125557.png" alt="image-20210416130125557" style="zoom:67%;" />

       2. 当前节点是Head的后继节点

          将当前节点的next指针指向自己，然后unpark后继节点

          <img src="D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210416130656089.png" alt="image-20210416130656089" style="zoom:67%;" />

       3. 当前节点不是Head的后继，也不是tail

          将node前驱结点的next指向node的后继，node的next指向node自己

          ![image-20210416130953939](D:%5CProgram%20Data%5CTypora%5Ctypora-user-images%5Cimage-20210416130953939.png)

       > 这里为什么没有处理pre指针？因为`cancelAcquire`方法在finally中，此时的前一个节点可能已经被移出队列了，因此这块的prev指针是不安全的。
       >
       > 之前try中的如下代码，已经处理过prev指针了
       >
       > ```java
       > do {
       > 	node.prev = pred = pred.prev;
       > } while (pred.waitStatus > 0);
       > ```

    中断后的恢复

    > 如果acquireQueued为True，就会执行selfInterrupt方法。
    >
    > ```java
    >// java.util.concurrent.locks.AbstractQueuedSynchronizer
    > 
    > static void selfInterrupt() {
    > 	Thread.currentThread().interrupt();
    > }
    > ```
    > 
    > 该方法其实是为了中断线程。但为什么获取了锁以后还要中断线程呢？
    >
    > Java的协作式中断
    >
    > 1. 当中断线程被唤醒时，并不知道被唤醒的原因，可能是当前线程在等待中被中断，也可能是释放了锁以后被唤醒。因此我们通过Thread.interrupted()方法检查中断标记（该方法返回了当前线程的中断状态，并将当前线程的中断标识设置为False），并记录下来，如果发现该线程被中断过，就再中断一次。
    >2. 线程在等待资源的过程中被唤醒，唤醒后还是会不断地去尝试获取锁，直到抢到锁为止。也就是说，在整个流程中，并不响应中断，只是记录中断记录。最后抢到锁返回了，那么如果被中断过的话，就需要补充一次中断。

    

    

    **AQS释放独占锁的具体实现**

    通过unlock来解锁

    ```java
    public void unlock() {
        sync.release(1);
    }
    ```

    release是AQS中的定义。首先尝试释放锁，成功后unpark后继的节点

    ```java
    public final boolean release(int arg) {
        // 自定义的tryRelease如果返回true，说明该锁没有被任何线程持有
        if (tryRelease(arg)) {
            // 释放成功后unpark后继节点的线程
            Node h = head;
            // 头结点不为空并且头结点的waitStatus不是初始化节点情况，解除线程挂起状态
            if (h != null && h.waitStatus != 0)
                unparkSuccessor(h);
            return true;
        }
        return false;
    }
    ```

    > h == null Head还没初始化。初始情况下，head == null，第一个节点入队，Head会被初始化一个虚拟节点。所以说，这里如果还没来得及入队，就会出现head == null 的情况。
    >
    > h != null && waitStatus == 0 表明后继节点对应的线程仍在运行中，不需要唤醒。
    >
    > h != null && waitStatus < 0 表明后继节点可能被阻塞了，需要唤醒。


~~~java
```java
// java.util.concurrent.locks.ReentrantLock.Sync

// 方法返回当前锁是不是没有被线程持有
protected final boolean tryRelease(int releases) {
	// 减少可重入次数
	int c = getState() - releases;
	// 当前线程不是持有锁的线程，抛出异常
	if (Thread.currentThread() != getExclusiveOwnerThread())
		throw new IllegalMonitorStateException();
	boolean free = false;
	// 如果持有线程全部释放，将当前独占锁所有线程设置为null，并更新state
	if (c == 0) {
		free = true;
		setExclusiveOwnerThread(null);
	}
	setState(c);
	return free;
}
```

unparkSuccessor源码

```java
// java.util.concurrent.locks.AbstractQueuedSynchronizer

private void unparkSuccessor(Node node) {
	// 获取头结点waitStatus
	int ws = node.waitStatus;
	if (ws < 0)
		compareAndSetWaitStatus(node, ws, 0);
	// 获取当前节点的下一个节点
	Node s = node.next;
	// 如果下个节点是null或者下个节点被cancelled，就找到队列最开始的非cancelled的节点
	if (s == null || s.waitStatus > 0) {
		s = null;
		// 就从尾部节点开始找，到队首，找到队列第一个waitStatus<0的节点。
		for (Node t = tail; t != null && t != node; t = t.prev)
			if (t.waitStatus <= 0)
				s = t;
	}
	// 如果当前节点的下个节点不为空，而且状态<=0，就把当前节点unpark
	if (s != null)
		LockSupport.unpark(s.thread);
}
```

> 为什么要从尾部开始找呢？
>
> ```java
> private Node addWaiter(Node mode) {
> 	Node node = new Node(Thread.currentThread(), mode);
> 	// Try the fast path of enq; backup to full enq on failure
> 	Node pred = tail;
> 	if (pred != null) {
> 		node.prev = pred;
> 		if (compareAndSetTail(pred, node)) {
> 			pred.next = node;
> 			return node;
> 		}
> 	}
> 	enq(node);
> 	return node;
> }
> ```
>
> 1. 之前addWaiter方法时，节点入队操作并不是原子操作，node.prev = pred；compareAndSetTail(pred, node) 这两个地方可以看作Tail入队的原子操作，但是此时pred.next = node;还没执行，如果这个时候执行了unparkSuccessor方法，就没办法从前往后找了，所以需要从后往前找。
> 2. 回想一下cancelAcquire方法的处理过程，cancelAcquire只是设置了next的变化，没有设置prev的变化，在最后有这样一行代码：`node.next = node`，如果这时执行了unparkSuccessor方法，并且向后遍历的话，就成了死循环了，所以这时只有prev是稳定的。
~~~


​    

11. 用过 CountDownLatch 么？什么场景下用的？

    http://www.ideabuffer.cn/2017/03/19/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3AbstractQueuedSynchronizer%EF%BC%88%E4%BA%8C%EF%BC%89/

    CountDownLatch这个类能够使一个线程等待其他线程完成各自的工作后再执行

    **使用**

    初始化创建c个线程，每个线程执行时都会执行`latch.countDown()`使计数器的值减1，主线程在执行到`latch.await()`时会等到计数器的值为0

    ```java
    public class CountDownTest {
        public static void main(String[] args) throws InterruptedException {
            CountDownLatch latch = new CountDownLatch(6);
    
            System.out.println("in" + Thread.currentThread().getName() + "...");
            System.out.println("before latch.await()...");
    
            for (int i = 1; i <= 3; i++) {
                new Thread("T" + i) {
                    @Override
                    public void run() {
                        System.out.println("enter Thread " + getName() + "...");
                        System.out.println("execute countdown...");
                        latch.countDown();
                        System.out.println("exit Thread" + getName() + ".");
                    }
                }.start();
            }
            latch.await();
    
            System.out.println("in " + Thread.currentThread().getName() + "...");
            System.out.println("after latch.await()...");
        }
    }
    /*
    in main...
    before latch.await()...
    enter Thread T1...
    execute countdown...
    enter Thread T3...
    execute countdown...
    exit ThreadT1.
    enter Thread T2...
    execute countdown...
    exit ThreadT3.
    exit ThreadT2.
    in main...
    after latch.await()...
    */
    ```

    使用场景：核心就是多线程异步处理请求，主线程阻塞，等其他线程都处理完了，主线程继续执行。如客户端请求服务端去查询一个用户的风险等级，服务端可以开多个线程，去查询不同的子系统获取数据，多线程异步操作，最后汇总给主线程。如果单线程很可能会超时

    

    **AQS共享模式的实现**

    **调用await时**

    - 共享锁获取失败（计数器还不为0），则将该线程封装为一个Node对象放入队列中，并阻塞该线程；
    - 共享锁获取成功（计数器为0），则从第一个节点开始依次唤醒后继节点，实现共享状态的传播。

    **调用countDown时**

    - 如果计数器不为0，则不释放，继续阻塞，并把state的值减1；
    - 如果计数器为0，则唤醒节点，解除线程的阻塞状态。

    **CountDownLatch构造方法**

    ```java
    public CountDownLatch(int count) {
        if (count < 0) throw new IllegalArgumentException("count < 0");
        this.sync = new Sync(count);
    }
    ```

    传入参数count，内部使用了Sync的构造方法来实现

    ```java
    private static final class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = 4982264981922014374L;
        Sync(int count) {
            setState(count);
        }
        int getCount() {
            return getState();
        }
        protected int tryAcquireShared(int acquires) {
            return (getState() == 0) ? 1 : -1;
        }
        protected boolean tryReleaseShared(int releases) {
            // Decrement count; signal when transition to zero
            for (;;) {
                int c = getState();
                if (c == 0)
                    return false;
                int nextc = c-1;
                if (compareAndSetState(c, nextc))
                    return nextc == 0;
            }
        }
    }
    ```

    可以看到，这里将AQS的state设置为count

    **CountDownLatch的await方法**

    ```java
    public void await() throws InterruptedException {
        // 直接调了sync的方法
        sync.acquireSharedInterruptibly(1);
    }
    ```

    acquireSharedInterruptibly方法

    ```java
    // 这里acquireSharedInterruptibly方法在Sync中被重写
    public final void acquireSharedInterruptibly(int arg)
                throws InterruptedException {
        // 如果线程被中断则抛出异常
        if (Thread.interrupted())
            throw new InterruptedException();
        // 尝试获取共享锁，该方法在Sync类中实现
        if (tryAcquireShared(arg) < 0)
            // 如果获取失败，需要根据当前线程创建一个mode为SHARE的Node放入队列中并循环获取
            doAcquireSharedInterruptibly(arg);
    }
    ```

    **CountDownLatch的tryAcquireShared方法**

    ```java
    protected int tryAcquireShared(int acquires) {
        return (getState() == 0) ? 1 : -1;
    }
    ```

    根据状态来判断，state等于0，说明计数器为0，返回1表示成功；返回-1表示失败

    **doAcquireSharedInterruptibly方法**

    ```java
    private void doAcquireSharedInterruptibly(int arg)
            throws InterruptedException {
        // 创建一个共享模式的节点
        final Node node = addWaiter(Node.SHARED);
        boolean failed = true;
        try {
            for (;;) {
                final Node p = node.predecessor();
                if (p == head) {
                    // 如果 p == head 表示是队列的第一个节点，尝试获取
                    int r = tryAcquireShared(arg);
                    if (r >= 0) {
                        // 设置当前节点为head，并向后面的节点传播
                        setHeadAndPropagate(node, r);
                        p.next = null; // help GC
                        failed = false;
                        return;
                    }
                }
                if (shouldParkAfterFailedAcquire(p, node) &&
                    parkAndCheckInterrupt())
                    throw new InterruptedException();
            }
        } finally {
            if (failed)
                cancelAcquire(node);
        }
    }
    ```

    这里的重点是setHeadAndPropagate方法。

    **setHeadAndPropagate**

    ```java
    private void setHeadAndPropagate(Node node, int propagate) {
        Node h = head; // Record old head for check below
        setHead(node);
        /*
         * Try to signal next queued node if:
         *   Propagation was indicated by caller,
         *     or was recorded (as h.waitStatus either before
         *     or after setHead) by a previous operation
         *     (note: this uses sign-check of waitStatus because
         *      PROPAGATE status may transition to SIGNAL.)
         * and
         *   The next node is waiting in shared mode,
         *     or we don't know, because it appears null
         *
         * The conservatism in both of these checks may cause
         * unnecessary wake-ups, but only when there are multiple
         * racing acquires/releases, so most need signals now or soon
         * anyway.
         */
        if (propagate > 0 || h == null || h.waitStatus < 0 ||
            (h = head) == null || h.waitStatus < 0) {
            Node s = node.next;
            if (s == null || s.isShared())
                doReleaseShared();
        }
    }
    ```

    


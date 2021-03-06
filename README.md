一、
1、要编写正确的并发程序，关键问题在于：在访问共享的可变状态时需要进行正确的管理。
2、为保证线程安全性，“先检查后执行”（例如延迟初始化）和“读取-修改-写入”（例如递增操作）等操作必须是原子的。

二、可见性
1、在没有同步的情况下，编译器、处理器以及运行时等都可能对操作的执行顺序进行一些意想不到的调整。在缺乏足够同步
的多线程程序中，要相对内存操作的执行顺序进行判断，几乎无法得出正确的结论。
2、内置锁可以用于确保某个线程以一种可预测的方式来查看另一个线程的执行结果。当线程A执行某个同步代码快时，线程B随后进入由供一个锁保护的同步代码块，
在这种情况下可以保证，在锁被释放之前，A看到的变量值在B获得锁后同样可以由B看到。换句话说，当线程B执行由锁保护的同步代码块时，可以看到线程A之前在
同一个代码块中的所有操作结果。如果没有同步，那么就无法实现上述保证。

三、对象的共享
1、ThreadLocal类
维持线程封闭性的一种更规范方法是使用ThreadLocal，这个类能使线程中的某个值与保存值的对象关联起来。ThreadLocal提供了get和set等访问接口或方法，这些
方法为每个使用该变量的线程都存有一份独立的副本，因此get总是返回当前执行线程在调用set时设置的最新值。
2、Final域
正如“除非需要更高的可见性，否则应将所有的域都声明为私有域”是一个良好的编程习惯，除非需要某个域是可变的，否则应将其声明为Final域也是一个良好的编程习惯。
3、由于不可变对象是一种非常重要的对象，因此Java内存模型为不可变对象的共享提供了一种特殊的输出化安全保证。我们知道，即使某个对象的引用对其他线程是可见的，也并不意味着对象状态对于使用该对象的线程来说一定是可见的。为了确保对象状态能呈现出一致的视图，就必须使用同步。
另一方面，即使在发布不可变对象的引用时没有使用同步，也仍然可以安全地访问该对象。为了维持这种初始化安全性的保证，必须满足不可变性的所有需求：状态不可修改，所有域都是Final类型，以及正确的构造过程。
总之，任何线程都可以在不需要额外同步的情况下安全地访问不可变对象，即使在发布这些对象时没有使用同步。
这种保证还将延伸到被正确创建对象中所有Final类型的域。在没有额外同步的情况下，也可以安全地访问Final类型的域。然而，如果Final类型的域所指向的是可变对象，那么在访问这些域所指向的对象的状态时仍然需要同步。

4、安全发布的常用模式
可变对象必须通过安全的方式来发布，这通常意味着在发布和使用该对象的线程时都必须使用同步。要安全地发布一个对象，对象的引用以及对象的状态必须同时对其他线程可见。一个正确构造的对象可以通过以下方式来安全地发布：
1）在静态初始化函数中初始化一个对象引用。
2）将对象的引用保存到volatile类型的域或者AtomicReferance对象中。
3）将对象的引用保存到某个正确构造对象的final类型域中。
4）将对象的引用保存到一个由锁保护的域中。
5、对象的发布需求取决于它的可变性：
1）、不可变对象可以通过任意机制来发布。
2）事实不可变对象必须通过安全方式来发布。
3）可变对象必须通过安全方式来发布，并且必须是线程安全的或者由某个锁保护起来。
5、实例封闭
将数据封装在对象内部，可以将数据的访问限制在对象的方法上，从而更容易确保线程在访问数据时总能持有正确的锁。
Java平台的类库中有很多线程封闭的示例，其中有些类的唯一用途就是讲非线程安全的类转化为线程安全的类。一些基本的容器类并非线程安全地，例如ArrayList和HashMap，但类库提供了包装器工厂方法（例如Collection.synchronizedList及其类似方法），使得这些非线程安全地类可以在多线程环境中安全地使用。这些工厂方法通过“装饰器”模式将容器类封装在一个同步的包装器对象中，而包装器能将接口中的每个方法都实现为同步方法，并将调用请求转发到底层的容器对象上。只有包装器拥有对底层容器的唯一引用（即把底层容器对象封闭在包装器中），那么它就是线程安全地。

二、线程池
1、只有当大量独立且同构的任务可以并发进行处理时，才能体现出将程序的工作负载分配到多个任务中带来的真正性能提升。
2、

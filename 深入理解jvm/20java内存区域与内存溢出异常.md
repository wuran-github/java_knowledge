# java内存区域与内存溢出异常

## 运行时数据区域
- java虚拟机把管理的内存划分为若干个不同的数据区域。他们有各自的用途，以及创建和销毁的时间。
- 他们又分为
    - 由所有线程共享的数据区
        - 方法区 Method Area
        - 堆 Heap
    - 线程隔离的数据区
        - 虚拟机栈 VM Stack
        - 本地方法栈 Native Method Stack
        - 程序计数器 Program Counter Register

### 程序计数器
- 一块较小的内存空间，可以看作是当前线程所执行的字节码的行号指示器。
- 为了线程切换后能恢复到正确的执行位置，每条线程都各自拥有独立的程序计数器。
- 如果线程执行的是Java方法，那么计数器记录的是正在执行的虚拟机字节码指令的地址;如果是执行本地方法，那么计数器值为空(Undefined)。

### Java虚拟机栈
- 虚拟机栈的生命周期和线程相同。描述的是Java方法执行的线程内存模型：
    - 每个方法被执行的时候，Java虚拟机都会同步创建一个栈帧`Stack Frame`用于存储局部变量表，操作数栈、动态链接、方法出口等信息。

- C++里说到的栈，在java里一般就是指虚拟机栈。或者更多的情况下就是指局部变量表。

- 局部变量表存放了编译器可知的各种Java虚拟机基本数据类型、对象引用和returnAddress类型（指向了一条字节码指令的地址）。

- 这些数据类型在局部变量中的存储空间以局部变量槽(Slot)来表示，其中64位的占2个变量槽，其余的一个。

### 本地方法栈
- 和虚拟机栈非常相似，区别是本地方法栈为本地方法服务。


### java堆
- java堆是虚拟机管理的内存最大的一块。
- java堆是被所有线程共享的一块内存区域，在虚拟机启动时创建。此内存区域的唯一目的是存放对象实例。几乎所有对象实例都在这里分配内存。

- java堆是垃圾收集器管理的内存区域，因此一些资料也称他为CG堆。

- java堆在逻辑上应该被视为连续的，物理上可以不连续。

- 如果在java堆中没有内存完成实例分配，而且堆也无法扩展时，会抛出OutOfMemoryError异常。

### 方法区
- 方法区和Java堆一样，是各个线程共享的内存区域。
- 用于存储已被虚拟机加载的类型信息、常量、静态变量、即时编译器编译后的代码缓存等数据。


### 运行时常量池
- 运行时常量池(Runtime Constant Pool)是方法区的一部分。Class文件中除了有类的版本、字段、方法、借口等描述信息外，还有一项信息是常量池表(Constant Pool Table)，用于存放编译器生成的各种字面量与符号引用，这部分内容将在类加载后存放到方法区的运行时常量池中。

----

## 虚拟机中的对象

### 对象的创建
- 当Java虚拟机遇到new指令时，首先去检查这个指令参数是否能在常量池中定位到一个类的符号引用，并且检查这个符号银用代表的类是否已被加载、解析和初始化过。如果没有，必须限制性相应的类加载过程。

- 类加载检查通过后，虚拟机为对象分配内存。
- 为对象分配空间的任务实际上就是从Java堆中划出内存块，根据Java堆的情况分为如下两种方法：
    1. java堆是规整的，空闲和被使用的内存在两边，中间有一个指针作为指示器。
        - 这种情况只需要把指针往空闲方向移动对象大小的位置即可。
        - 这种分配方式称为 `指针碰撞`

    2. 如果内存不规整，空闲和被使用的内存交错在一起。
        - 这种情况虚拟机就必须维护一个列表，记录哪些内存可以用。分配时从列表中找到足够大的空间划分给对象实例，并更新记录。
        - 这种分配方式称为 `空闲列表` 

- 内存分配完成后，虚拟机必须把分配到的内存空间都初始化为零值。

- 接下来，Java虚拟机还要对对象进行必要的设置，例如这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的GC分代年龄等信息。这些信息存放在对象的对象头之中。

- 上面的工作都完成后，虚拟机角度来说对象就产生了。从程序角度来说还需执行构造函数。

- 总结创建对象过程如下：
    1. 类加载检查
    2. 对象实例内存空间分配
    3. 对对象进行必要的设置




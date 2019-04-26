# 接口、Lambda表达式和内部类
<!-- TOC -->

- [接口、Lambda表达式和内部类](#接口lambda表达式和内部类)
    - [接口](#接口)
        - [接口概念](#接口概念)
        - [接口的特性](#接口的特性)
        - [接口和抽象类](#接口和抽象类)
        - [静态方法](#静态方法)
        - [默认方法](#默认方法)
        - [解决默认方法冲突](#解决默认方法冲突)
    - [接口示例](#接口示例)
        - [接口与回调](#接口与回调)
        - [comparator接口](#comparator接口)
        - [对象克隆](#对象克隆)
    - [lambda表达式](#lambda表达式)
        - [为什么引入lambda表达式](#为什么引入lambda表达式)
        - [lambda表达式的语法](#lambda表达式的语法)
        - [函数接口](#函数接口)
        - [方法引用](#方法引用)
        - [构造器引用](#构造器引用)
        - [变量作用域](#变量作用域)
        - [处理lambda表达式](#处理lambda表达式)
        - [Comparator](#comparator)
    - [内部类](#内部类)
        - [使用内部类访问对象状态](#使用内部类访问对象状态)
        - [内部类的特殊语法规则](#内部类的特殊语法规则)
        - [内部类是否有用、必要和安全](#内部类是否有用必要和安全)
        - [局部内部类](#局部内部类)
        - [由外部方法访问变量](#由外部方法访问变量)
        - [匿名内部类](#匿名内部类)
        - [静态内部类](#静态内部类)
    - [代理](#代理)
        - [何时使用代理](#何时使用代理)
        - [创建代理对象](#创建代理对象)
        - [代理类的特性](#代理类的特性)

<!-- /TOC -->
- 接口，主要描述类具有什么功能。

- lambda表达式，这是一种表示可以在将来某个时间点执行的代码块的间接方法。

    - 使用lambda表达式，可以用一种精巧而间接的方式表示使用回调或变量行为的代码。

- 内部类(inner class)。内部类有些复杂，定义在另外一个类的内部，其中的方法可以访问包含他们的外部类的域。主要用于设计具有相互协作关系的类集合。

- 最后介绍代理(proxy)，时一种实现任意接口的对象。代理是非常专业的构造工具，可以用来构建系统级的工具。

----

## 接口

### 接口概念
- 接口是对类的一组需求描述。

- 接口所有方法自动属于public，因此不需要提供Public关键字。

- 在接口中还可以定义常量。

- 接口不能含有实例域。

- java se8 之后，可以在接口中提供简单方法。

- 类实现接口使用 `implements` 关键字。
```
public class Employee implements Comparable
```

- 在实现接口时，类必须把方法声明为Public。


### 接口的特性

- 接口不能使用new运算符实例化

- 接口可以声明变量，可以引用一切实现了接口的类对象。

- 可以使用 `instanceof` 检查对象是否实现了接口。

- 接口可以被扩展。

- 接口可以包含常量。(why?)
```
public interface Moveable
{
    double SPEED_LIMIT = 95;
}
```

- 接口中的常量会被自动设为public static final。(C#的常量总是隐式静态的，而且在接口中不允许定义常量)

- 接口中使用常量有点偏离了接口概念的初衷，最好不要这样使用它。

- 类可以实现多个接口。

### 接口和抽象类

- 由于java不支持多继承，所以如果把接口定义为抽象类，虽然看似相同，但是却不能继承多个接口，也不能再继承一个超类。

- 而且，接口主要表达的是类的需求，而父类是表达 is 的意思。

- 实际上，接口可以提供多重继承的大多数好处，还能避免复杂性和低效性。

### 静态方法

- java se 8中，允许在接口中增加静态方法。 理论上讲，没有任何理由认为这是不合法的，只是这有违于将接口作为抽象规范的初衷。(不在其位不谋其职，加那么多功能干啥)

### 默认方法

- 可以为接口方法提供一个默认实现。必须用 `default` 修饰符标记这样的方法。
```
public interface Comparable<T>
{
    default int compareTo(T other){ return 0; }
}
```

- 正常情况下没什么用，但是有些情况默认方法很有用。例如，如果希望在发生鼠标点击事件时得到通知，九幺实现一个包含五个方法的接口：

- 大多数情况下，你只关心其中1、2个事件类型。在java se 8中，你就可以把所有方法声明为默认方法，这样你只需要实现关心的方法即可。

- 默认方法的一个重要用法是 `接口演化` (interface evolution)。例如Collection接口，在java里存在很多年了。假设很久以前你用一个类实现了该接口
```
public class Bag implements Collection
```

- 后来，在JAVA se 8中为接口增加了一个stream方法。

- 假设stream方法不是一个默认方法。那么Bag类将不能编译，因为它没有实现这个新方法。为接口增加一个非默认方法不能保证 `源代码兼容` (source compatible)。

- 不过，假设不重新编译这个类，而只是使用原先的一个包含这个类的JAR文件，这个类仍能正常加载使用。(为接口增加方法可以保证`二进制兼容`)

- 但是，如果在bag实例上调用stream，就会出现一个 `AbstractMethodError`

- 将方法实现为默认方法就可以解决这两个问题。

### 解决默认方法冲突
- 如果先在一个接口将一个方法定义为默认方法，然后又在超类或另一个接口定义了同样的方法，java的规则如下：
    1. 超类优先。如果超类提供了一个具体方法，相同签名的默认方法会被忽略。

    2. 接口冲突。如果一个超接口提供了一个默认方法，另一个接口提供了一个同名且参数类型相同的方法，必须覆盖这个方法来解决冲突。

- 第二种情况，如果两个接口都提供了同样的方法，那实现类必须覆盖这个方法。
    - 可以通过 接口名.super.MethodName调用默认的接口方法。
```
public interface ConflictDemo {
    default void SameName(){}
}
public interface ConflictDemo2  {
    default void SameName() { }
}
public class ConflictDemoImpl implements ConflictDemo, ConflictDemo2 {
    @Override
    public void SameName() {
        ConflictDemo2.super.SameName();
        ConflictDemo.super.SameName();
    }
}
```
---

## 接口示例

### 接口与回调

- 回调(callback) 是一种常见的程序设计模式。在这种模式中，可以指出某个特定事件发生时应该采取的动作。

- 例如，在java.swing中有一个Timer类，可以在到达给定的时间间隔发出通告。

- 在java中，通过把类的对象传递给定时器，定时器调用这个对象的方法来实现通告。对象必须实现ActionListener接口。

### comparator接口

- 对对象数组进行排序的前提是对象所属类实现了Comparable接口。

- 假设我们现在希望按照其他方式对字符串进行排序，肯定不能让String类用两种不同的方式实现compareTo方法，而且String类也不应由我们来修改。

- Array.sort提供了第二个版本，有一个数组和一个比较器作为参数，比较器是实现了Comparator接口的类的实例。
    - 规则和C#一样，0相等，正数大于，负数小于
```
public class ComparatorImpl implements Comparator<String> {
    @Override
    public int compare(String o1, String o2) {
        if(o2 == null && o1 == null){
            return 0;
        }
        if(o1 == null){
            return -1;
        }
        if(o2 == null){
            return 1;
        }
        return o1.length() - o2.length();
    }

    public static void main(String[] args) {
        String[] array = {"123","321","1234","12345",null};
        ComparatorImpl comparator = new ComparatorImpl();
        Arrays.sort(array,comparator);
        System.out.println(Arrays.toString(array));
    }
}
```

### 对象克隆

- Cloneable接口允许我们深层克隆一个对象。

- Object也有clone方法，但是这个方法只会对域逐个拷贝，如果是引用对象的话也只是会拷贝引用。这叫 `浅拷贝`。

- 在这里，Cloneable接口的出现与接口的正常使用并没有关系。具体来说，他没有指定clone方法，这个方法是从Object类继承的。这个接口只是作为一个标记，指示类设计这了解克隆过程。

- 对象对于克隆很偏执，如果一个对象请求克隆，但没有实现这个接口，就会生成一个受查异常。

```
Cloneable接口是java提供的一组标记接口(tagging interface)之一，或称记号接口(marker interface)。标记接口不包含任何方法，唯一作用就是允许在类型查询中使用instanceof
```

- 即使clone的浅拷贝能够满足要求，还是需要实现接口，将clone重新定义为Public，再调用super.clone()。

- 1.4之前，clone方法的返回值总是Object，而现在可以为你的clone方法指定正确的返回值了。这是协变返回类型的一个例子。
    - java的协变指子类可以用子类的返回类型覆盖父类的返回类型

    - C#中
    - 协变针对参数，父类参数可以传递子类型
    - 抗变针对返回类型，父类引用可以接收子类对象

- 要不要在自己的类中实现clone呢？克隆没有你想象中的那么常用。标准库中只有5%的类实现了clone。

---
## lambda表达式

### 为什么引入lambda表达式

- lambda表达式是一个可传递代码块，可以在以后执行一次或多次。（为什么现在才引入

- 例如我们考虑使用比较器的排序，如果想按长度对字符串排序，可以向sort方法传入一个Comparator对象：
```
class LengthComparator implements Comparator<String>
{
    public int compare(String left, String right){
        return left.length() - right.length();
    }
}
Array.sort(strings, new LengthComparator());
```

- 在数组完成排序之前，sort方法会多次调用compare方法完成排序。

- 实际上，sort需要的是一个代码块，我们是要把一个代码块传递给某个对象(一个定时器，或sort方法)，这些代码块会在将来某个时间调用。

- 在此之前，java传递代码段不容易，必须构造一个对象，然后这个对象的某个方法包含所需代码。

- 在此之前，java一致拒绝增加这个特性。现在增加了。

### lambda表达式的语法

- 上一节我们传入代码来比较两个字符串的长短，这里要计算
```
left.length() - right.length()
```

- left和right是什么？他们是字符串，我们需要指定他们的类型:
```
(String left, String right)
-> left.length() - right.length()
```
- 这就是你看到的第一个lambda表达式。lambda表达式是一个代码块，以及必须传入代码的变量规范。

- 带参数变量的表达式就被称为lambda表达式。

- java的一种lambda表达式形式：参数，箭头以及一个表达式。

- 如果代码要完成的计算无法放在一个表达式中，就可以像写方法那样，把代码放在{}中，并包含显式的return语句：
```
Arrays.sort(array,
(String left,String right) -> {
            int i = left.length() - right.length();
            return i;
        });
```

- 即使lambda表达式没有参数，仍要提供空括号，就像无参数方法一样：
```
() -> { }
```

- 如果可以推导出一个lambda表达式的参数类型，则可以忽略其类型：
```
Arrays.sort(array,
(left,right) -> {
            int i = left.length() - right.length();
            return i;
        });
```

- 如果方法只有一个参数，而且这个参数可以推导得出，还可以省略小括号:
```
en -> en.left == en.right
```

- 无需指定lambda表达式的返回类型，lambda表达式的返回类型总是会由上下文推导得出。

### 函数接口

- lambda表达式和封装代码块的接口是兼容的。

- 对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个lambda表达式。这种接口称为函数式接口(functional interface)。
```
java 8 中， 接口可以声明非抽象方法（默认，静态)。
```

- 如之前的sort例子，Comparator接口就只有一个方法，所以可以提供一个lambda表达式替代:

- 在底层，Arrays.sort方法会接收实现了Comparator<String>的某个类的对象，在这个对象上调用compare方法会执行这个lambda表达式的内容。这些对象和类的管理完全取决于具体实现，与使用传统的内联类相比，这样可能要高效得多。最好把lambda表达式看作是一个函数，而不是一个对象。

- 在java中，lambda表达式只能转换为函数式接口。（C#可以用Func

- javaAPI 在java.util.function包中定义了很多非常通用的函数式接口，其中一个接口BiFuncion<T, U, R>描述了参数类型为T和U而且返回类型为R的函数。可以把我们的字符串比较lambda表达式保存在这个类型的变量中。
```
BiFunction<String, String, Integer> lambda = 
                (left, right)-> left.length() - right.length();
```
- 不过sort方法没有接收这个类型的重载。

- 对于java程序员而言，类似于Comparator的接口往往有一个特定的用途，而不只是提供一个有指定参数和返回类型的方法。

- 想用lambda表达式做某些处理，还是要谨记表达式的用途，为他建立一个特定的函数式接口。

- java.util.function包中有一个尤其有用的接口Predicate，
```
public interface Predicate<T>
{
    boolean test(T t);
}
```
- ArrayList类有一个removeIf方法，他的参数就是一个Predicate。这个接口专门用来传递lambda表达式，例如，下面的语句将从一个数据列表删除所有null值。（相当于C#的linq把
```
list.removeIf(e -> e == null);
```

### 方法引用
- 有时，可莪能有现成的方法可以完成你想要传递到其他代码的某个动作。例如，假设你喜欢只要出现又给定时器事件就打印这个事件对象。

- 这是，你可以直接把println方法传递给Timer构造器:
```
Timer t = new Timer(1000, System.out::println);
```

- 表达式`System.out::println`是一个方法引用(method reference)。等价于lambda表达式: `x -> System.out.println(x)`。

- 要用::操作符分割方法名与对象或类名。
    - object::instanceMethod
    - Class::staticMethod
    - Class::instanceMethod

- 前2中情况，方法引用等价于提供方法参数的lambda表达式。

- 第3中情况，第一个参数会成为方法的目标：例如：`String::compareToIgnoreCase`等价于`(x, y) -> x.compareToIgnoreCase(y)`

- 可以在方法引用中使用this参数。例如，`this::equals` 等同于 `x -> this.equals(x)`。(这里的this指的是当前类)

- 使用super也是合法的。
```
class Greet{
    public void greet(Object o){
        System.out.println("Hello");
    }
}

class TimerGreeter extends Greet{
    public void greet(){
        Timer timer = new Timer(1000,super::greet);
    }
}

```

### 构造器引用

- 构造器引用和方法引用很类似，只不过方法名为new。例如，`Person::new`是Person构造器的一个引用。

- 使用哪一个构造器取决于上下文。假设我有一个字符串列表，可以把它转化为一个Person对象数组，为此要在各个字符串上调用构造器：
```
ArrayList<String> names = new ArrayList<>();
Stream<Person> personStream = names.stream().map(Person::new);
//等同于
names.stream().map(x -> new Person(x));
```

- stream\map和collect方法的详细内容会在第二卷讨论。map方法会为各个元素调用Person(map)构造器，如果有多个Person构造器，编译器会选择有一个String参数的构造器。

- 可以用数组类型建立构造器引用。例如：int[]::new是一个构造器引用，他有一个参数：即数组的长度。这等价于lambda `x -> new int[x];`

- java有一个限制，无法构造泛型类型T的数组。数组构造器引用对于客服这个限制很有用。 表达式`new T[n]`会产生错误。(C#就可以直接new)，因为这会改成`new Object[n]`(类型擦除)。

- 假设我们需要一个Person对象数组，Stream接口有一个toArray方法可以返回Object数组:
```
Object[] people = stream.toArray();
```

- 但是用户希望得到一个Person引用数组。可以直接利用构造器引用解决这个问题。
```
Person[] people = stream.toArray(Person[]::new);
```

### 变量作用域

- 通常，你可能希望能够在lambda表达式中访问外围方法或类中的变量。
```
public static void repeatMessage(String text, int delay){
    ActionListener listener = event -> {
        System.out.println(text);
    };
}
```

- 来看这样的一个调用：
```
repeatMessage("Hello", 1000);
```

- 现在来看lambda表达式中的变量text。注意这个变量并不是在这个lambda表达式中定义的，实际上，这是repeatMessage的一个参数变量。

- lambda表达式可能会在repeatMessage调用后很久才运行，这时候变量已经超出作用域了，如何保留该变量呢？

- lambda表达式有3个部分：
    1. 一个代码块

    2. 参数

    3. 自由变量的值。这是指非参数而且不在代码中定义的变量

- 在例子中，text就是自由变量。表示lambda表达式的数据结构必须存储自由变量的指。我们说它被lambda表达式捕获(captured)。下面来看具体的实现细节。
```
关于代码块和自由变量值有一个术语：闭包(closure)。
```
- lambda表达式可以捕获外围作用域变量的值。在Java中，要确保所捕获的值是可以明确定义的，这里有一个重要限制，就是只能引用值不会改变的变量。下面做法是不合法的：
```
public static void repeatMessage(String text, int delay){
    ActionListener listener = event -> {
        delay--;//Error
    };
}
```

- 如果在lambda表达式中改变变量，并发执行多个动作时就会不安全。

- 另外如果在lambda表达式中引用变量，而这个变量可能在外部改变，这也是不合法的。
```
public static void repeatMessage(String text, int delay){
    for(int i = 1;i <= count; i++)
    ActionListener listener = event -> {
        System.out.println(i);//Error
    };
}
```

- lambda表达式中捕获的变量必须实际上是最终变量(effectively final)。实际上的最终变量是指，这个变量初始化后就不会再为他赋新值。

- 在这里text总是指向同一个String对象，所以捕获这个变量是合法的。不过，i的值会改变，因此不能捕获i。

- lambda表达式的体与嵌套块有相同的作用域。这里同样适用于命名冲突和遮蔽的有关规则。
```
int first = 1;
Comparator<String> comp = (first, second) -> first.length;
//Error Variable first already defined
```

- 在一个lambda表达式中使用this关键字，是指创建这个lambda表达式的方法的this参数。

```
public class Application(){
    public void init(){
        TestI i = test ->{
            System.out.println(this.toString());
            //this is Application 
        }
    }
}
```

- 表达式this.toString会调用Application对象的toString方法。

### 处理lambda表达式

- 使用lambda表达式的重点是延迟执行(deferred execution)。可能有如下目的:
    - 在一个单独的线程运行代码
    - 多次运行代码
    - 在算法的适当位置运行代码
    - 发生某种情况时运行代码
    - 只在必要时运行代码

- 假设你想重复一个动作n次，将这个动作和重复次数传递到一个repeat方法：
```
private static void repeatTest(){
    repeat(10, () -> System.out.println("hello"));
}
private static void repeat(int count, Runnable action){
    for (int i = 0; i < count; i++) {
        action.run();
    }
}
```

- 要接受这个lambda表达式，需要选择（偶尔可能需要提供）一个函数式接口。表6-1列出了java API提供的最重要的函数式接口。这里，我们可以使用Runnable接口。P257

- java有很多函数式接口，例如想要告诉这个动作出现在哪一次迭代中，这时候可以自己写一个函数式接口，或者使用基本库给出的接口。

- p258页 表6-2列出了基本类型的函数式接口。

- 最好使用这些特殊化规范来减少自动装箱。

- 如果设计你自己的接口，其中只有一个抽象方法，可以用`@FunctionalInterface`注解来标记这个接口。这样做有两个优点
    1. 无意中增加了另一个非抽象方法，编译器会产生错误消息。

    2. javadoc页中会指出你的接口是一个函数式接口。

### Comparator

- Comparator接口包含很多方便的静态方法来创建比较器。这些方法可以用于lambda表达式或方法引用。

- 静态comparing方法取一个 `键提取器` 函数，他将类型T映射为一个可比较的类型(如String)。对要比较的对象应用这个函数，然后对返回的键完成比较。例如：
```
public static void main(String[] args) {
    Student[] students = {
            new Student("Army"),
            new Student("Mike"),
            new Student("Bill"),
    };
    Arrays.sort(students, Comparator.comparing(s -> s.getName()));
    //method reference
    //Arrays.sort(students, Comparator.comparing(Student::getName));
    System.out.println(Arrays.toString(students));
}
```

- 还可以把比较器与thenComparing方法串起来。如果两个人的姓相同，就使用第二个比较器。
    - 要注意， thenComparing如果使用lambda的话需要手动指定参数类型，因为编译器无法判断出参数类型。
```
static void thenComparingTest(){
    Student[] students = {
            new Student("Army","John"),
            new Student("Mike","Jane"),
            new Student("Bill","Clock"),
            new Student("Bill","Arm"),
    };
    // 报错，因为lambda无法推断出参数类型，需要给参数指定类型
    // Arrays.sort(students, Comparator.comparing(s -> s.getName()).
    //        thenComparing(s -> s.getSecondName()));

    Arrays.sort(students,
            Comparator.comparing((Student s)-> s.getName()).
                    thenComparing((Student s) -> s.getSecondName()));
    System.out.println(Arrays.toString(students));
}
```

- 这些方法有很多变体形式，可以为comparing和thenComapring方法提取的键指定一个比较器。

- 如果键可以返回null，可能就要用到nullsFirst和nullsLast适配器。这两个会修改现有的比较器，从而在遇到Null值时不会抛出异常，而是将这个值标记为小于或大于正常值。

---
## 内部类
- 内部类(inner class)时定义在另一个类中的类。为什么需要使用内部类？原因有三点:
    1. 内部类方法可以访问该类定义所在的作用域中的数据，包括私有的数据。

    2. 内部类可以对同一个包中的其他类隐藏起来。

    3. 当想要定义一个回调函数且不想编写大量代码时，使用匿名(anonymous)内部类比较便捷。

### 使用内部类访问对象状态
- 内部类的语法比较复杂。我们选择一个简单的例子说明内部类的使用方式。
```
public class TalkingClock {
    private int interval;
    private boolean beep;

    public TalkingClock(int interval, boolean beep){
        this.interval = interval;
        this.beep = beep;
    }
    public class TimePrinter{
        
    }
}
```

- 需要注意，虽然TimerPrinter位于TalkingClock类内部，这并不意味着每个TalkingClock都有一个TimePrinter实例域。

- 下面是TimerPrinter类的详细内容。需要注意的时，actionPerformed方法在发出铃声前检查了beep标志。
```
public void actionPerformed(ActionEvent event){
    System.out.println("time:"+new Date());
    if(beep){
        Toolkit.getDefaultToolkit().beep();
    }
}
```
- 可以看到，虽然TimerPrinter没有beep的实例域或变量，但是还是可以使用。这里beep引用了创建TimePrinter的TalkingClock对象的域。(在C#中内部类没有隐含着外部对象)

- 内部类既可以访问自身的数据域，也可以访问创建他的外围类对象的数据域。

- 内部类的对象总有一个隐式引用，指向创建他的外部类对象。这个引用在内部类的定义是不可见的。

- 外围类的引用在构造器中设置。编译器修改了所有的内部类的构造器，添加一个外围类引用的参数。(C#中需要你手动这么做)。
```
public TimePrinter(TalkingClock clock){
    outer = clock;
}
```

- 当在start方法中创建了TimerPrinter对象后，编译器就会将this引用传递给当前语音时钟的构造器：
```
public void start(){
    TimePrinter printer = new TimePrinter();//自动添加this
    //TimePrinter printer = new TimePrinter(this);
}
```
- TimePrinter类声明为私有的。这样一来，只有外围类才能构造该对象。只有内部类可以是四有泪，常规类只可以具有包可见性，或公有可见性。

### 内部类的特殊语法规则

- 事实上，使用外围类引用的正规语法要复杂一些。表达式 `OuterClass.this` 表示外围类引用。
```
public void test(){
    TalkingClock.this.beep = false;
}
```

- 反过来，可以采用下列语法格式更加明确地编写内部对象的构造器：
```
outerObject.new InnerClass(params);
```
```
TimePrinter printer = this.new TimePrinter();
```

- 在这里，最新构造的TimePrinter对象的外围类引用呗设置为创建内部类对象的方法中的this引用。通常，this限定词是多余的。不过，可以通过显式地命名将外围类引用设置为其他的对象。

- 例如，如果TimePrinter是一个公有内部类，对于任意的与饮食中都可以构造一个TimePrinter：
```
TalkingClock clock = new TalkingClock(1000, false);
TalkingClock.TimePrinter printer = clock.new TimePrinter();
```

- 只有静态内部类才能声明静态域和静态方法。


- ？？？？ 内部类中声明的所有静态域都必须是final的。原因很简单，我们希望一个静态域只有一个实例。不过对于每个外部对象，会分别有一个单独的内部类实例。如果这个域不是Final，他可能就不是唯一的。??
```
TalkingClock clock = new TalkingClock(1000, false);
TalkingClock clock2 = new TalkingClock(1000, false);
```

### 内部类是否有用、必要和安全

- p265

- 内部类有更多的访问权限

### 局部内部类

- 可以在方法中定义局部类。
```
public void test(){
    class LocalClass{
            private void print(){
            System.out.println("I am local inner class");
        }
    }
    LocalClass localClass = new LocalClass();
    localClass.print();
}
```

- 局部类不能用public 或private访问修饰符修饰。他的作用域被限定在声明这个局部类的块中。

- 局部类的优势是对外部世界完全隐藏起来。即使该类的其他代码也不能访问他。

### 由外部方法访问变量

- 与其他内部类相比较，局部类还有一个优点。他们不仅能够访问包含他们的外部类，还可以访问局部变量。不过，那些局部变量必须事实上为final(也就是赋值后不会被改变，这个和lambda的闭包是差不多原因)。

- 看下面的例子：
```
interface Print{
    void print();
}
public class SampleInnerClass {
    public void test(int i){
        class LocalClass implements Print{
             public void print(){
                System.out.println("I am local inner class " +i);
            }
        }
        LocalClass localClass = new LocalClass();
        //假设是多线程调用的
        output(localClass);
    }
    public void output(Print print){
        print.print();
    }

    public static void main(String[] args) {
        SampleInnerClass innerClass = new SampleInnerClass();
        innerClass.test(2);
    }
}
```
- 局部类中使用了没有i这个变量，但是使用了外层的参数i。但是这里有一个问题，我们看看整个流程：
    1. 调用test方法

    2. 实例化了一个LocalClass对象

    3. 把该对象传递给了output方法。

    4. 这时候test的i参数不复存在。

    5. output调用LocalClass的print方法，使用变量i。

- 实际上，编译器为局部内部类构造了一个变量，存储了i的值。当创建对象时，i就会被传递给构造器，存储在_i中。编译器必须检测对局部变量的访问，为每一个变量建立相应的数据域，并将局部变量拷贝到构造器中。

```
class LocalClass implements Print{
    int _i;
    public void print(){
        System.out.println("I am local inner class " + _i);
    }
}
```

- 前面曾提到，局部类的方法只可以引用定义为final的局部变量。

- 在java 8 前，必须把局部类访问的局部变量声明为final。例如，test方法原本应道如此声明：
```
    public void test(final int i)
```

### 匿名内部类

- 在局部内部类的使用再深入一步。假如只创建这个类的一个对象，就不必命名了。这种类称为匿名内部类。
```
class AnonymousClass{
    public void start(){
        Print print = new Print() {
            @Override
            public void print() {
                System.out.println("this is anonymous class");
            }
        };
        print.print();
    }
}
```

- 创建一个实现Print接口的类的新对象，需要实现的方法print定义在括号内。一般语法如下：
```
new SuperType(construction parameters){

}
```

- 其中，SuperType可以是Print这样的接口，内部类就要实现这个接口。也可以是一个类，内部类就要扩展它。

- 由于构造器的名字必须与类名相同，而匿名类没有类名，所以，匿名类不能有构造器。取而代之的是，将构造函数传递给超类构造器。在实现接口的时候，不能有任何构造参数。
```
class AnonymousSuper{
    int count;
    public AnonymousSuper(int count){
        this.count = count;
    }
}
class AnonymousExtend{
    public void start(){
        AnonymousSuper sup = new AnonymousSuper(10){
            //没有办法可以调用该方法
            public void start(){
              System.out.println(count);
          }
        };
    }
}
```

- 如果构造参数的闭小括号后面跟一个开大括号，正在定义的就是匿名内部类。

- 多年来，java程序员习惯用匿名内部类实现事件监听器和回调，如今最好还是用lambda表达式。

### 静态内部类
- 有时候，使用内部类只是为了把一个类隐藏在另一个类的内部，并不需要内部类引用外围类对象。为此，可以把内部类声明为static，以便取消产生的引用。

- 下面是一个使用静态的例子，返回一个数组的最大值和最小值。
```
public class StaticInnerClass {

    public static class Pair{

        double min;
        double max;
        public Pair(double min,double max){
            this.min = min;
            this.max = max;
        }
    }

    public static void main(String[] args) {
        
    }
    public static Pair minmax(double[] values){
        double min = values[0];
        double max = values[0];
        for (int i = 0; i < values.length ; i++) {
            if(values[i] < min){
                min = values[i];
            }
            if(values[i] > max){
                max = values[i];
            }
        }
        return new Pair(min, max);
    }
}
```

- java中只有内部类才能声明为静态，静态内部类可以有静态域和静态方法。

---
## 代理
- 代理。(proxy) 利用代理可以在运行时创建一个实现了一组给定接口的新类。对于系统程序设计人员来说，代理的灵活性非常重要。

### 何时使用代理

- 假设有一个表示接口的class对象。他的确切类型在编译时无法知道。这确实有些难度。要想构造一个实现这些接口的类，就需要使用newInstance方法或反射找出这个类的构造器。但是，不能实例化接口，需要在程序处于运行状态时定义一个新类。

- 代理机制是解决这个问题的好方案。代理类可以在运行时创建全新的类，这样的代理类能够实现指定的接口。他具有下列方法：
    - 指定接口所需要的全部方法
    - Object类的全部方法。

- 然而，不能在运行时定义这些方法的新代码。而是要提供一个调用处理器(invocation handler)。调用处理器是实现了InvocationHandler接口的类对象。在这个接口中只有一个方法：
```
Object invoke(Object proxy, Method method, Object[] args);
```

- 无论何时调用代理对象的方法，调用处理器的invoke方法都会被调用，并向其传递Method对象和原始的调用参数。调用处理器必须给出处理调用的方式。

### 创建代理对象
- 想要创建一个代理对象，需要使用Proxy类的newProxyInstance方法。这个方法有三个参数：

    - 一个类加载器(class loader)。作为JAVA安全模型的一部分，对于系统类和从网上下载下来的类，可以使用不同的类加载器。母前，使用null表示使用默认的类加载器。注意，如果使用自己的接口的话使用null时无法加载的。可以使用Interface.class.getClassLoader()来获取类加载器。

    - 一个Class对象数组，每个元素都是需要实现的接口。
    - 一个调用处理器

- 接下来如何定义一个处理器？能够用结果代理对象做些什么？使用代理可能出于很多原因，例如:
    - 路由对远程服务器的方法调用

    - 在程序运行期间，将用户接口事件和动作关联起来

    - 为调试，跟踪方法调用。

- 下面就是跟踪方法调用，打印被调用方法的名字的代码：

```
public class ProxyProgram {
    public static void main(String[] args) {
        Print print = new Print();
        TrackInvoker invoker = new TrackInvoker(print);
        Object proxy = Proxy.newProxyInstance(PrintA.class.getClassLoader(),new Class[]{PrintA.class},invoker);
        ((PrintA)proxy).printA();
    }
}
class Print implements PrintA, PrintB{

    @Override
    public void printA() {
        System.out.println("A");
    }

    @Override
    public void printB() {
        System.out.println("B");
    }
}
class TrackInvoker implements InvocationHandler{
    private Object target;
    public TrackInvoker(Object target){
        this.target = target;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String str = method.getName();

        System.out.println(str);
        return method.invoke(target, args);
    }
}
```


### 代理类的特性
- 代理类是在程序运行过程中创建的，一旦被创建，就成了常规类，和其他类没区别。

- 所有代理类都扩展于Proxy类，一个代理类只有一个实例域——调用处理器。为了履行代理对象的职责，所需要的任何附加数据必须存储在调用处理器中。例如上面的代码，包装了实际的对象。

- 所有的代理类都覆盖了Object类中的toString\equals和hashCode。如同所有代理方法一样，这些方法仅仅调用了调用处理器的Invoke。Object类的其他方法没有被重新定义。

- 对于特定的类加载器和预设的一组接口来说，只能有一个代理类。如果用同一个类加载器和接口数组调用两次newProxyInstance方法的话，之能够得到同一个类的两个对象。也可以利用getProxyClass方法获得这个类：
```
Class proxyClass = Proxy.getProxyClass(null, interfaces);
```
- 代理类一定是public和final的。如果代理类实现的所有接口都是public，代理类就不属于某个特定的包；否则，所有非公有的接口都必须属于同一个包，同时，代理类也属于这个包。

- 可以调用Proxy的isProxyClass检测对象是否一个代理类。


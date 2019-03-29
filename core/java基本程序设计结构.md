# 程序设计结构
## 一个简单的java程序
```
public class FirstSample{
    public static void main(String[] args){
        System.out.println("hello world");
    }
}
```
- 首先，java区分大小写。<code>main</code> 函数一定要是小写，和C#不一样。
- java的标准命名规范，类名使用Pascal,其余部分都使用CamelCase写法。
- java的文件名和类名必须一致。
- 使用命令 <code>java FirstSample</code> 来执行代码。

- 当使用 <code>java ClassName</code>运行已编译程序时，Java虚拟机将从指定类中的main方法开始执行。

- 注意，根据Java语言规范，main方法必须声明为public。

## 数据类型
- java是强类型语言。
- 在java中，一共有8种基本类型(primitive type)。其中有
    - 4种整型 int short long byte
    - 2种浮点型 float double
    - 1种用于表示Unicode编码的字符类型char
    - 1种用于表示真值的 boolean

- java有一种能够表示任意精度的算书包，通常称为"大数值"(big number)。但是他是一个类，不是基本类型。

### 整型
- 整型的4种类型
    - int 4字节
    - short 2字节
    - long 8字节
    - byte 1字节
- 长整型数值后有一个后缀L或l,如40000L.
- 十六进制有一个前缀0x或0X
- 八进制有一个前缀0,例如010代表8。很显然，八进制表示法容易混淆，所以最好不要使用八进制常数。
- 从java7开始，加上前缀0b或0B就可以写二进制。
- 同样，可以为数字字面量加下划线，如 1_000_000;这些下划线只是为了让人更易读。Java编译器会去除这些下划线。

- //注意，java没有任何无符号(unsigned)形式的int\long\short或byte类型。

### 浮点类型
- 浮点型
    - float 4字节
    - double 8字节
- 大部分时候使用double类型，float的精度很难满足需求。
- float类型的数值使用后缀F或f。没有后缀的浮点数值默认double型，也可以加后缀D或d。

- 下面是用于表示溢出和出错情况的三个特殊的浮点数值：
    - 正无穷大 <code>Double.POSITIVE_INFINITY</code>
    - 负无穷大 <code>Double.NEGATIVE_INFINITY</code>
    - NaN(不是一个数字) <code>Double.NaN</code>

- 特别要说明的是,任何一个数值都不等于NaN

- 警告：浮点数值不适用于无法接受舍入误差的金融计算中。
    - 例如：System.out.println(2.0-1.1)将打印出0.8_999_999_999_999_999(15个9)，而不是0.9。这种舍入误差的主要原因是浮点数值采用二进制系统表示，而在二进制系统种无法精确地表示分数1/10，这就好像十进制无法精确地表示分数1/3一样。
    - 如果在数值计算中不允许有任何舍入误差，就应该使用BigDecimal类。

### char类型
- char类型原本用于表示单个字符。但是，现在有些Unicode字符可以用一个char描述，另一些则需要两个char。
- char的字面量需要用单引号引起来， <code>'A'</code> 。
- chr类型的值可以表示为16进制值，其范围从 <code>\u0000</code> 到 <code>\Uffff</code> 。
- 除了转义序列 <code>\u</code> 之外，还有一些用于表示特殊字符的转义序列。
- 所有转义序列都可以出现在加引号的字符字面量或字符串种。
- <code>\u</code> 还可以出现在引号外面。如：
```
public static void main(String\u005B\u005D args)
```
- 这是完全符合语法规则的。

- 警告：Unicode转义序列会在解析代码之前得到处理。
- 例如： <code>"\u0022+\u0022"</code> 并不是一个由引号包围加号构成的字符串 <code>"+"</code> 。实际上，\u0022会在鸡西之前转换为"，这会得到 <code>""+""</code> 也就是一个空串+一个空串。

- 我们强烈建议不要在程序中使用char类型，除非确实需要处理UTF-16代码单元。最好将字符串作为抽象数据类型处理。

### boolean类型
- boolean 取值 true or false。

### 变量
- java的变量名必须是以一个字母开头并由字母或数字构成的序列。
- 注意，相比其他语言，java的字母和数字范围更大
    - 字母包括 <code>'A'~'Z' 'a'~'z' '_' '$'</code> 活在某种语言种表示字母的任何Unicode字符。
    - 同样，数字包括0~9和在某种语言种表示数字的任何Unicode字符。

#### 常量
- 在java中，利用关键字 <code>final</code> 指示常量。
```
public class Constants
{
    public static void main(String[] args){
        final double CM_PER_INCH = 2.54；
    }
}
```
- 关键字 <code>final</code> 表示这个变量只能被赋值一次。一旦被赋值后，就不能再更改了。
- 在java中，常量可以被定义为静态，（C# 中，常量总是隐式静态的）。
- //注意：const是java保留的关键字，但是目前没有使用。final就是java的const

### 运算符
- 在java中，使用算术运算符+、-、*、/,当参与  <code>/</code> 运算的两个操作数都是整数时，表示整数除法；否则，表示浮点除法。
- 注意：整数被0除将会产生一个异常（int/0)，而浮点数被0除将会得到无穷大或NaN结果。
- 为了可移植性，使用 <code>strictfp</code> 关键字标记的方法必须使用严格的浮点计算来生成可再生的结果。不使用的话则允许对中间计算结果采用扩展的精度。

#### 数学函数与常量
- 在Math类中，包含了许多数学函数。
    1. 求平方根： <code>Math.sqrt(x)</code>
    2. 幂运算： <code>Math.pow(x, a)</code> 求x的a次幂
    3. 解决求余问题。 考虑表达式 <code>n%2</code> 。如果n是偶数，表达式为0，n是奇数，表达式为1。如果n为负，表达式则为-1.
        - 数学家几百年来都知道这样一个最优（或：欧几里德）规则：余数总是要>=0。
        - 这个结果是因为设计最早计算机时，制定规则的那个人没有好好翻开数学书看，于是提出了一些看似合理但实际很不方便的规则。
        - 考虑这么一个问题：计算一个时钟时针的位置。这里要做一个时间调整，而且要归一化为一个0~11之间的数。这很简单： <code>(position + adjustment) % 12</code>。不过，如果这个调整数为负会怎么样？你可能会得到一个负数。所以要引入一个分支，或者使用 <code>((position + adjustment) % 12 + 12) % 12</code> ,不管怎样，都很麻烦。
        - floorMod方法让问题变得容易: <code>floorMod(position + adjustment, 12)</code> 总会得到一个0~11之间的数。（遗憾的是，对于负除数，floorMod会得到负数结果。）

    4. 常用三角函数
        - <code>Math.sin</code>
        - <code>Math.cos</code>
        - <code>Math.tan</code>
        - <code>Math.atan</code>
        - <code>Math.atan2</code>
    5. 指数函数和他的反函数——自然对数及以10为底的对数:
        - <code>Math.exp</code> 指数函数
        - <code>Math.log</code> 自然对数（e为底
        - <code>Math.log10</code> 对数（10为底
    6. 表示Π和e常量的近似值:
        - <code>Math.PI</code>
        - <code>Math.E</code>

- 注意，在Math中，为了达到最快性能，所有的方法都是用计算机浮点单元中的例程。如果得到一个完全可预测的结果比运行速度更重要，使用StrictMath。

#### 数值转换
1. 隐式转换
    - 无信息丢失的转换（总的来说就是小的往大不丢，大的往小丢
        - byte -> short
        - short -> int
        - int -> long
        - int -> double
        - char -> int
        - float -> double
    - 有丢失的转换
        - int -> float
        - long -> float
        - long -> double
    - 运算转换规则 a and b
        - 如果其中一个是double ，另一个会转换为double
        - 否则 如果其中一个是float,另一个会转换为float
        - 否则 如果其一是long, 另一转long
        - 否则 都转int

2. 强制转换
    - 在前面加上(type)

#### 自增和自减
- <code>--</code> 和  <code>++</code>
    - 分为前缀和后缀
    - 用在表达式中时，前缀会先完成+1再计算
    - 后缀会使用原来值
    ```
    int n = 1;
    int a = 2 * ++n;
    //a = 4
    int b = 2 * n++;
    // b = 4
    ```

#### 位运算符
- <code>&</code> and
- <code>|</code> or
- <code>^</code> xor
- <code>~</code> not

- <code>>></code> 左移 （用符号位填充高位
- <code><<</code> 右移
- <code>>>></code> 用0填充高位的左移

- 警告：移位运算符的右操作数要完成模32的运算（long 要模64），如 1<<35 = 1<<3


#### 运算符级别
1. [].() 方法调用
2. ! ~ ++ -- +(正号) -(负号) ()(强制类型转换) new
3. <code>* / %</code> 
4. <code>+ -</code> 
5. << >> >>>
6. < <= > >= instanceof
7. == !=
8. &
9. ^
10. |
11. &&
12. ||
13. ?:
14. = += -= *= %= /= ^= <<= >>= >>>=

- 如果不使用圆括号，优先级高的先运算。同一级别的，右结合运算符右边先运算

#### 枚举类型
- enum

### 字符串
- java没有内置的字符串类型。而是在标准java类库中提供了String类。

1. 子串
    - substring可以提取子串
2. 拼接
    - 使用+号拼接字符串
    - 使用String.join方法

3. 不可变字符串
    - java的String类和C#一样，都是不可变的对象。
    - 编译器可以让字符串共享。
    - 为了弄清具体情况，可以看作各种字符串存放在公共的存储池中。字符串变量指向存储池中相应的位置。如果复制一个字符串变量，原始字符串与复制的字符串共享相同的字符。

4. 检测字符串是否相等
    - 一定要使用equals，不要使用==
    - ==只能确定两个字符串是否放值在同一个位置上。当然，如果字符串放在同一个位置上肯定相同。
    - 但是，完全有可能将内容相同的多个字符串拷贝放置在不同的位置上。
    ```
    String greeting = "Hello";
    if(greeting == "Hello")//probably true
    if(greeting.substring(0,3) == "Hel")//probably false
    ```
    - 实际上只有 字符串常量 是共享的。而+或substring等产生的结果并不是共享的。
    
- 字符串有很多坑，得在面试题里慢慢踩
```
String str1 = new String("abc"); //jvm 在堆上创建一个String对象

//jvm 在strings pool中找不到值为“abc”的字符串，因此
//在堆上创建一个String对象，并将该对象的引用加入至strings pool中
//此时堆上有两个String对象
String str2 = "abc";
System.out.println(str1==str2);
//false

String a = new String("abc");
a = a.intern();//intern方法查看常量池中是否有该字符串对象，没有就放进去，返回常量池中的对象
String b = "abc";
System.out.println(a==b);
//true


```    

5. String API
    
6. 构建字符串
    - 有些时候，需要由较短的字符串构建字符串，每次连接字符串都会构建一个新的String对象，这时候可以使用StringBuilder避免这个问题。
    ```
    StringBuilder builder = new StringBuilder();
    builder.append("tes");
    builder.append("t");
    String str = builder.toString();
    ```
7. 输入
```
Scanner in = new Scanner(System.in);
//使用nextLine时因为输入行中可能包含有空格
String name = in.nextLine();
//想要读取单词（以空白符为分隔符
String name = in.next();
//读取整数
int i = in.nextInt();


```

8. 格式化输出

## 控制流程

### 循环
- do While 至少执行一次
- switch case
    - case 标签可以试
        - 类型为char/byte/short/int的常量表达式
        - 枚举常量
        - 从java 7开始，还可以是字符串字面量

### 中断控制流程语句
- break语句 break语句分为带标签和不带标签，不带标签的用法和C#一样
    - 带标签的break语句用于跳出多重嵌套的循环语句。
    - 带标签的break语句会跳到标签那行，然后略过循环体，执行下一条语句
    - 标签必须定义在break之前
    ```
    for1:
    for(int l = 10;l<20;l++) {
        for2:
        for (int i = 0; i < 10; i++) {
            System.out.println(i);
            break for2;//跳到for2后会跳过这个循环体，执行下一行
        }
        System.out.println(l);

    }
    ```
    - 如上语句，输出为0 10 0 11 ... 0 19

## 大数值
- 如果基本的整数和浮点数精度不能够满足需求，那么可以使用java.math包中的两个很有用的类：
    - BigInteger
    - BigDecimal
- 这两个类可以处理包含任意长度数字序列的数值。
- `BigInteger` 类实现了任意精度的整数运算，BigDecimal实现了任意精度的浮点数运算。
- 使用静态的 `valueOf` 可以将普通的数值转换为大数值：
```
BigInteger bigInteger = BigInteger.valueOf(10);
```
- 遗憾的是，不能使用人们熟悉的算术运算符处理大数值。（因为java不支持运算符重载）而需要使用大数值类中的add和multiply方法。
```
BigInteger bigInteger = BigInteger.valueOf(10);
BigInteger c = bigInteger.add(BigInteger.valueOf(20));
bigInteger.multiply(c);
```

## 数组
- 数组是一种数据结构，用来存储同一类型值的集合。
- 声明数组
```
int[] a;
```
- 创建数组,数组长度可以是变量
```
int[] a = new int[100];
```
- 可以使用两种形式声明数组，不过一般采用第一种
    - int[] a;
    - int a[];
- 创建数组的时候所有值都会被初始化为类型的默认值。
    - 如数值类型被初始化为0
    - 对象被初始化为null
    - boolean 为 false

### for each 循环
- java的foreach循环：
```
for(variable : collection){

}
```
- collection这一集合表达式必须是一个数组或是一个实现了Iterable接口的类对象。
```
int[] a = new int[100];
for(int e : a){
    
}
```
- 注意，在foreach里不可以修改元素

### 数组初始化以及匿名数组
- 有一种创建数组对象并同时赋予初始值的简化书写形式：(和C#一致)
```
int[] arrays = {1,2,3,4};
```
- 还可以初始化一个匿名数组
```
small = new int[]{1,2,3,4,5};
```
- 注意，在java中，允许数组长度为0。在编写一个结果为数组的方法时，如果碰巧结果为空，则这种语法形式就显得非常有用。可以通过`array.length`来判断返回值。

### 数组拷贝
- 允许将一个数组变量拷贝给另一个数组变量，这时，两个变量将引用同一个数组。
- 如果希望拷贝值到新数组，则需要使用 `Arrays.copyOf`
```
int[] array = new int[10];
int[] bArray = Arrays.copyOf(array,array.length);
```
- 第二个参数是新数组的长度。这个方法常用来增加数组的大小：
```
array = Arrays.copyOf(array,array.Length * 2);
```
- 多余的元素的默认值和初始化一致。

### 命令行参数
- java的main方法的args参数可以通过命令行输入。
```
java Message -g cruel world
```
- args数组将包含如下内容：
    - -g
    - cruel
    - world

### 数组排序
- 使用 `Arrays.sort()`排序。
- 这个方法使用了优化的快速排序算法。Arrays类还提供了几个使用很便捷的算法。
```
//100选5
int[] numbers = new int[100];
int[] result = new int[5];
for (int i = 0; i < 100; i++) {
    numbers[i] = i + 1;
}
int n = 100;
//抽取
for (int i = 0; i < 5; i++) {
    int r = (int) (Math.random() * n);
    result[i] = numbers[r];
    //把抽取的数替换到最后面
    numbers[r] = numbers[n - 1];
    n--;
}
Arrays.sort(result);
System.out.println(Arrays.toString(result));
```

### 多维数组
- 多维数组将使用多个下标访问数组元素，它适用于表示表格或更加复杂的排列形式(矩阵等)。
- 声明二维数组的方法：
```
double[][] balances;//与C#刚好不一样，C#这种是交错数组(也就是不规则数组)
double[][] example = new double[4][4];
int[][] temp={
    {1,2,3},
    {2,3,4}
}
```

### 不规则数组
- java实际上没有多维数组，只有一维数组。多维数组被解释为`数组的数组`。
- 不规则数组的声明方法：
```
int[][] temp = new int[4][];
for(int i = 0; i < 4;i++)
{
    temp[i] = new int[i+1];
}
```




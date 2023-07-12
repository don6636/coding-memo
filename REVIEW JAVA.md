[TOC]

# REVIEW JAVA

- **标识符** 是由字母、数字、下划线( _ )、和美元符号(**$**)构成的字符序列，~~不能以数字开头~~

- else和if **就近匹配**

- return语句也可以用在void方法中，用来终止方法并返回到调用处；用于在方法中控制程序流程

- 被调用的方法执行到 **return语句** 或 **方法右结束括号** 时，将程序控制返还给调用者

- `a + (int) Math.random() * b` 返回 a ~ a+b 之间的一个随机整数，但不包括 a+b

  **_ps:_** **0.0 <= `Math.random()` < 1.0**

- 可变长参数列表 `void max(int... nums) {}` // 参数类型相同，作数组用

  **_eg:_** `max(23, 233, 2333);` 或 `max(new int[] {6, 66, 666});`

- 查找

  - 线性查找  n - 1
  - 二分查找  ㏒~2~(n) + 1 (已排序)

- Arrays类常用方法

  - `Arrays.sort(array);`
  - `Arrays.binarySearch()`
  - `Arrays.equals()`
  - `Arrays.fill()`
  - `Arrays.toString()`
  - `Arrays.asList(array)` 返回的是Arrays的内部类Arrays.ArrayList，该类未实现集合修改方法add/remove/clear

- 使用new语法创建二维数组时，第一个下标不能省略 `int[][] matrix = new int[7][];`

- 已知循环次数                   for

  未知循环次数                   while

  未知次数且先执行一次   do-while

  遍历数组或集合               foreach

- 实参 argument

  形参 parameter

- **堆**(heap)：存放对象（实例变量存在于所属对象中）

  **栈**(stack)：存放方法和局部变量

- 引用： `new A()` 的值

  **句柄** 的初始化：句柄 = 引用，即把引用赋值给句柄，如语句 `a = new A();`

  对于语句 `A a = new A();` 是对象创建及初始化与句柄初始化的合并

- `System.out.println(new A());` 等价于 `System.out.println(new A().toString());`

  隐式调用对象的 `toString()`，若对象A未覆写 `toString()`，会依次向上寻找父类的 `toString()` 实现。

- 常量大写且以<u>下划线</u>分隔字符：`static final int RESULT_OK = -1;` 或者 `static{}` 块

- 修饰符

|  修饰符   | 同类 | 同包 | 子类 | 不同包 |
| :-------: | :--: | :--: | :--: | :----: |
|  public   |  ✓   |  ✓   |  ✓   |   ✓    |
| protected |  ✓   |  ✓   |  ✓   |   ─    |
| (default) |  ✓   |  ✓   |  ─   |   ─    |
|  private  |  ✓   |  ─   |  ─   |   ─    |

- `private` 和 `protected` 只能修饰类的成员，`public` 和 `(default)` 既能修饰类的成员，也能修饰类

- 属性通常使用 `private` 封装起来；方法一般使用 `public` 用于被调用；会被子类继承的方法通常使用 `protected`

- **封装** 的基本原则：将实例变量标记为 `private`，并提供 `public` 的 **getter/setter** 方法控制读写

- 一个Java文件中可以包含多个类，但只能有一个是public类，编译出的class文件与public类同名

- **实例变量/方法** 属于类的一个实例，它的使用与各自的实例相关联

  **静态变量/方法** 被同一个类的所有实例所共享，可以在不使用实例的情况下调用（**类名.静态/变量方法**）

- **实例方法可以调用static变量/方法；static方法不能调用实例变量/方法**

- 在Java的main方法（静态方法）中调用非静态内部类实例

```java
public class A {
    public static void main(String[] args) {
        A a = new A();// 实例化A
        B b = a.new B();// 实例化B
        b.m();// 调用B类方法
    }
    // 非静态内部类
    class B {
        public void m() {
            System.out.println("Clazz B");
        }
    }
}
```

- **实例变量** 声明在类中，基础类型有默认值（**_0_ / _0.0_ / _false_ / _null_**）

  **局部变量** 声明在方法中，无默认值，使用前必须初始化

- **实例变量** 和 **静态变量** 的作用域是整个类，**局部变量** 的作用域是从声明处到包含此变量的块结束为止

- 若实例变量和局部变量同名，局部变量优先，类变量被隐藏（因此要尽量避免重名）

```java
public class F {
    private int i = 7;
    private static double k = 13.0;
    public void setI(int i) {
        this.i = i;// 隐藏的实例变量通过this引用
    }
    public static void setK(double k) {
        F.k = k;// 隐藏的静态变量通过"类名.静态变量"引用
    }
}
```

- `int i = Integer.parseInt("6");` 返回的是基础类型

  `Double doubleObject = Double.valueOf("13.7");` 返回的是包装类型

- 装箱：基本类型值 -> 包装类对象

  拆箱：包装类对象 -> 基本类型值

- BigInteger类 / BigDecimal类可以用于表示任意大小和精度的整数/十进制数

- **继承:** 子类 **is a** 父类

- `this(参数列表);` 用于调用同一个类中其他重载构造器，必须位于构造函数首行

- `super(参数列表);` 用于调用父类构造器，必须位于构造函数首行

  **_ps:_** 不能同时调用 `super()` 和 `this()`

- `super.方法名(参数列表);` 用于调用父类非构造方法（不限定于构造函数内或首行调用）

- 当提供了有参的构造器，同时又没有显式声明无参构造器，则默认的无参构造器就不存在了

- 子类不能继承父类构造器，构造子类实例时，将依次调用继承链上所有父类的构造器（构造方法链），因此最好为每个类都提供一个无参构造器

  - 若父类不包含无参构造器，子类必须在构造器中通过 **super** 关键字显式地调用父类的有参构造器；
  - 若父类包含无参构造器，系统会在子类中隐式调用父类的无参构造器。

- 可以用构造函数初始化对象状态

- 继承下来的方法可以被覆写，实例变量除外（可重新赋值）

- 仅当实例方法是可访问的，它才能被覆写

- 静态方法也能被继承，但不能被覆写

- 若父类中的静态方法在子类中被重新定义，父类中的静态方法将被隐藏，可用 **父类名.静态方法名** 调用

- **覆写(重写)** 具有同样的签名和返回值类型

  **重载** 具有同样的方法名，不同的参数列表，返回值类型也可以不同

- **覆写** 发生在子类中；**重载** 可以在同一个类中，也可以在子类中

- 子类中覆写方法的访问权限必须与父类中的方法 **相同或更为开放**

  *e.g.* 子类覆写父类的protected方法，应保持可见性为protected或开放为public

- **多态**：使用父类对象的地方都可以使用子类对象

- 变量的 **声明类型** 决定了在编译时能够匹配的方法（向下转型的必要性，父类型匹配不到子类型的方法）；引用变量调用实例方法时，变量的 **实际类型** 在运行时决定使用该方法的哪个具体实现（动态绑定）

  **_eg:_** `Object str = new String();` Object是声明类型，String是实际类型

- 使用 **instanceof** 关键字判断对象类型

- ArrayList 的 `indexOf()` 方法在匹配失败时返回 **_-1_**

- 排序 `Collections#sort`

- `ArrayList<E>`    **_E_** 不能为基本数据类型，但可以是基本数据类型的包装类

- **final** 修饰的 **类/方法/变量** 不能被 **覆写/改变**

- 内部类访问外部成员（局部变量）时，外部成员要用final修饰

- 在Java中，关于内部类：**非静态内部类** 和 **匿名内部类** 都会隐式持有外部类的引用，而静态内部类不会。

- 异常的顶级父类是Throwable，它分为Exception和Error两大类，Exception又分为必检异常和运行时异常(RuntimeException)

- 声明异常    `public void method() throws Exception1, Exception2...`

  **_ps:_** 若方法在父类中未声明必检异常，那么在子类中覆写该方法时也不能声明

- 抛出异常    `throw new IllegalArgumentException();`

- 捕获异常    `try-catch-[finally]`

- catch块中能重新抛出异常（链式异常）

- 若想让方法的调用者处理异常则抛出 **throws**，若方法有返回值也可以 **try/catch**

- 自定义异常最好继承必检异常

- **NPE 空指针异常** 一定位于方法的调用链上（**•**），沿着调用链向前寻找出为null的调用者即可定位问题。

- 扩展（几点使用准则）：

  - try-catch一定要把异常信息打印出来，必要时throw出去
  - try-catch粒度不能太粗，不要对大块的方法体使用
  - 对外抛异常时，错误反馈要明确
  - 弄清return和[try-catch-finally执行顺序](https://www.jianshu.com/p/8c9ea967ffe6)

- Java7 **try-with-resources** 语法自动关闭资源 `try (声明和初始化资源1;资源2..) { 使用资源; }`

  ```java
  try ( PrintWriter output = new PrintWriter(file) ) {
      output.print();// output会自动关闭
  }
  ```
  
  **_ps:_** 资源需实现 `(Auto)Closeable` 接口，声明和初始化必须在同一语句中，声明多个资源时，最终会按照 **声明顺序逆序** 来关闭。


- ```java
  Scanner input = new Scanner(new File("temp.txt"));
  System.out.println(input.nextLine());// 读数据
  ```

- abstract class 抽象类不能 new，子类通过构造方法链调用其构造方法

  `public abstract void eat();`  抽象方法无方法体

- 一个包含抽象方法的类必须被声明为抽象类

- 即使父类是具体的，子类也可以是抽象的

- 必须在抽象类扩展的非抽象子类（具体类）中实现（覆写）抽象类的所有抽象方法；若抽象类的子类没有实现抽象父类的所有抽象方法，则该子类也应定义为抽象类

- 若一个类实现了接口但没有实现接口的方法，那么这个类必须声明为abstract类，该类的具体子类即使没有实现接口也必须实现接口的方法，除非也声明为abstract类

- 接口中所有的数据域都是 public static final 的，所有方法都是 public abstract 的，因此定义接口时可以省略这些修饰符

- 标记接口：空接口    *e.g.* Cloneable / Serializable (反)序列化

- 静态变量不会被序列化

- 当实现了Serializable接口的对象包含非序列化的实例数据域时，该对象就不能被序列化。若要序列化该对象，就要将这些数据域用 **_transient_** 修饰（序列化时会忽略transient修饰的实例变量）

- **SAM** (Single Abstract Method) Interfaces, *e.g.* *java.lang.Runnable*. SAM接口可以通过 **lambda** 表达式 或 函数引用（**::**）实现。

- 接口可以单/多继承 `public interface IMyInterface extends Interface1, ..., InterfaceN {}`

- 若clone对象的数据域是 _基本数据类型_ ，复制的就是它的 **值**

  若clone对象的数据域是 _引用类型_ ，复制的就是它的 **引用**

  **_ps:_** 引用的还是同一个对象（浅复制），除非对这个数据域也进行clone（深复制）


- 递归方法就是直接或间接调用自身。通过递减参数到基础情况（有一个或多个）来终止递归，将问题简化为规模更小的子问题，这些子问题和原问题的情况一样

  *e.g.* _原问题_ : 喝咖啡    _参数_ : 杯中剩余咖啡    _子问题_ : 喝咖啡(杯中剩余)    _基本情况_ : 杯子空了

- 递归设计中定义第二个方法来接收附加的参数是个常用技巧，称为递归辅助方法（如下标判断回文字符串）

- 使用辅助参数将非尾递归转换为尾递归以减少对栈的开销（辅助参数用于构造递归辅助方法）

- 具有构造器的枚举类型，其构造器默认私有，可省略 private 关键字

```java
public enum State {
    CLOSE(0), OPEN(1);

    private int code;

    State(int code) {
        this.code = code;
    }
}
```


- 使用label控制for/while循环嵌套

```java
int i = 0;
outer:
for(; true ;) {
    // inner:
    for(; i < 10; i++) {
        if(i == 1) {
            continue;
        }
        if(i == 2) {
            break;
        }
        if(i == 3) {
            continue outer;
        }
        if(i == 4) {
            break outer;
        }
    }
}
```

- 初始化块：实例初始化块（IIB，Instance Initialization Block）和静态初始化块。
- 父类静态代码块 -> 子类静态代码块 -> 父类构造代码块 -> 父类构造器 -> 子类构造代码块 -> 子类构造器
- 静态代码块只会（在类加载时）执行一次，构造代码块每次创建对象时都会执行。
- 当类包含多个构造器时，无论调用哪个构造器初始化对象，都会先执行构造代码块，因此可以在其中定义公共初始化内容。
- 一个类可以包含多个静态代码块，静态代码块中的变量是局部变量。
- 成员变量初始化是在父类构造函数调用完后，在此之前，成员变量的值均是默认值。[:link:](http://jm.taobao.org/2010/07/21/331/)
- [输出格式化字符串](https://baike.baidu.com/item/printf())（常用）[Format String Syntax](https://docs.oracle.com/javase/6/docs/api/java/util/Formatter.html#syntax)：

```java
System.out.printf(
    "%%<场宽>.<小数位><格式>"
    + "\nInteger:" + "%d"
    + "\nString:" + "hello, %s"
    + "\nChar:" + "%c"
    /*'6' represents total-width-include-decimal-point(场宽);
      '3' represents decimal-places-width-with-rounding*/
    + "\nFloat:" + "%6.3f"
    + "\nBinary:" + 0b111
    + "\nOctal:" + "%o"
    + "\nHex:" + "%x",
    2,
    "world!",
    'I',
    -7.13f,
    07,
    0xFF);

输出:
%<场宽>.<小数位><格式>
Integer:2
String:hello, world!
Char:I
Float:-7.130
Binary:7
Octal:7
Hex:ff
```

- [Operators](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/operators.html)





## String、StringBuilder、StringBuffer

- 都是final类；

  String长度不可变，StringBuffer、StringBuilder长度可变；

  StringBuffer线程安全（synchronized锁），StringBuilder线程不安全（脏数据）；

  执行速度：StringBuilder > StringBuffer > String

- 使用原则：

  - 少量数据    String
  - 单线程大量数据    StringBuilder
  - 多线程大量数据    StringBuffer

- String是不可变的，每次赋值都是一个新的对象。

- String直接赋值时，要看该值在编译期是否确定，若是（即为常量），将在常量池中创建并指向该对象；若否（则为变量），将在运行期的堆内存中创建该对象。

- 多用StringBuilder和StringBuffer类的 **append**，少用String类的 **_+_** 号

- StringBuilder一般用于方法内部来完成类似 **_+_** 的功能，因为是线程不安全的，用完后可以丢弃；StringBuffer主要用于全局变量中

- 如果append的字符超过了当前StringBuilder的capacity，capacity就会扩充为 **2 * (当前capacity + 1)**

- JVM为了提高效率并节约内存，对具有相同序列的 **字符串直接量** 使用同一个实例（共享限定字符串）

- StringJoiner（拼接字符串，内部由StringBuilder实现）

```java
StringJoiner joiner = new StringJoiner(":", "[", "]");
joiner.add("George").add("Sally").add("Fred");
String desiredString = joiner.toString(); // "[George:Sally:Fred]"
```





## 抽象

![抽象](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/abstract.png)

- Java中的抽象有两种实现方式：**抽象类（Abstract Class）** 和 **接口（Interface）**。抽象类描述的是事物的本质，而接口描述的是事物的功能。*e.g.* _保温杯是水杯，所以水杯应该设计成抽象类；保温杯具有保温功能，所以保温应该设计成接口。即 `class 保温杯 extends 水杯 implements 保温`_ 。

![抽象类](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/abstract-class.png)

![接口](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/interface.png)

- 区别

<table>
    <thead>
        <tr>
            <th rowspan="2" style="text-align:center;">类型</th>
            <th colspan="5" style="text-align:center;">区别</th>
        </tr>
        <tr>
            <th style="text-align:center;"><font size="2">抽象的层次</font></th>
            <th style="text-align:center;"><font size="2">与实现类的关系</font></th>
            <th style="text-align:center;"><font size="2">继承</font></th>
            <th style="text-align:center;"><font size="2">方法/属性</font></th>
            <th style="text-align:center;"><font size="2">访问权限</font></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td style="text-align:center;"><font size="2">抽象类</font><br>
                                           <font size="1" color="#68b3fc">(abstract class)</font></td>
            <td style="text-align:center;"><font size="2">对 1整个类 进行抽象</font><br>
                                           <font size="1" color="#68b3fc">(含属性、行为)</font></td>
            <td style="text-align:center;"><font size="2">继承关系</font><br>
                                           <font size="1" color="#68b3fc">(即 is-a 关系)</font></td>
            <td style="text-align:center;">
                <font size="2">单继承</font><br>
                <font size="1" color="red" align="left">
                    <ul><li>子类只可继承1个抽象类</li>
                        <li>抽象类只可继承1个类/实现多个接口</li></ul></font></td>
            <td style="text-align:center;"><font size="2">抽象/不抽象</font><br></td>
            <td style="text-align:center;">
                <font size="1" align="left">
                    <ul><li>对于成员变量：默认friendly，实现类可重新定义/改变其值</li>
                        <li>对于成员函数：可使用修饰符 public、protected、default</li></ul></font></td></tr>
        <tr>
            <td style="text-align:center;"><font size="2">接口</font><br>
                                           <font size="1" color="#68b3fc">(interface)</font></td>
            <td style="text-align:center;"><font size="2">对 类的局部(某个行为) 进行抽象</font></td>
            <td style="text-align:center;"><font size="2">实现类与接口之间 的契约关系</font><br>
                                           <font size="1" color="#68b3fc">(即 like-a 关系)</font></td>
            <td style="text-align:center;">
                <font size="2">多 "继承"</font><br>
                <font size="1" color="red" align="left">
                    <ul><li>子类可实现多个接口</li>
                        <li>接口只可继承接口</li></ul></font></td>
            <td style="text-align:center;"><font size="2">必须是 抽象</font></td>
            <td style="text-align:center;">
                <font size="1" align="left">
                    <ul><li>对于成员变量：属性：static final & 赋初值，实现类不可重新定义/改变其值</li>
                        <li>对于成员函数：必须是 public</li></ul></font></td></tr>
    </tbody>
</table>



- Java8 接口 **default** & **static** 方法

```java
public interface IFoobar {
    //必须被覆写
    void foobar();

    //可选择性覆写（提供默认实现，向老接口追加方法而不会破坏原有实现类）
    default boolean foo() {
        this.foobar(); // 会回调具体实现
        return false;
    }

    //无法覆写，只属于接口本身
    static String bar() { return "static foobar"; }
}
```

```java
public class Foobar implements IFoobar {
    @Override
    public void foobar() {
        IFoobar.bar(); //通过接口名.直接调用静态方法
    }

    @Override
    public boolean foo() {
        return IFoobar.super.foo(); // 这样调用父类默认实现
    }

    //实现类可以和接口具有同名静态方法（调用者不同，不会彼此覆盖）
    public static void bar() {}
}
```





## Collection

- Set    一组无序不重复元素
- List    一组有序可重复元素
- Stack    后进先出
- Queue    先进先出
- Priority Queue    优先级顺序 (最小元素被赋予最高优先级，最先被移除)
- Map    键值对（key/value）
- Iterator（迭代器）

```java
Iterator<T> iterator = new ArrayList<T>().iterator();
while (iterator.hasNext()) {
    System.out.print(iterator.next());
}
```

- `Deque#descendingIterator` 双端队列的降序迭代器

- 无需插删元素，数组最快

  下标随机访问，无需在起始位置插删元素，ArrayList

  下标随机访问，需要在起始位置插删元素，LinkedList    `addFirst()/removeFirst()`

- Comparator 可以用于比较没有实现Comparable接口的对象

- Set（元素唯一）

  - HashSet    无序，初始容量 **16** 负载因子 **0.75**

  - LinkedHashSet    按插入顺序排列

  - TreeSet    Comparable（自然顺序）/ Comparator（比较器顺序）

    ***ps:*** 无参构造器默认自然顺序。若元素对象未实现Comparable接口则会报错；若传入Comparator参数，则优先按照比较器顺序排序。

- 查询合集中是否存在某个元素，Set 比 List 更高效

- Map（键唯一）

  - HashMap    无序

  - LinkedHashMap    插入顺序、访问顺序（无参构造器按插入顺序，有参构造器按从早到晚访问顺序）

  - TreeMap    Comparable（自然顺序）/ Comparator（比较器顺序）

    **_ps:_** 无参构造器默认自然顺序。若key对象未实现Comparable接口则会报错；若传入Comparator参数，则优先按照比较器顺序排序

- 常用Map初始容量速查表

| expectedSize<br/><font size=2; color=red>(需要存储的元素个数)</font> | initialCapacity<br><font size=2; color=red>(int) (expectedSize / 负载因子) + 1</font> | loadFactor | threshold | resize<br><font size=2; color=red>(threshold * loadFactor)</font> |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--------: | :-------: | :----------------------------------------------------------: |
|                              1                               |                              2                               |    0.75    |     2     |                              1                               |
|                              2                               |                              3                               |     --     |     4     |                              3                               |
|                              3                               |                              5                               |     --     |     8     |                              6                               |
|                              4                               |                              6                               |     --     |     8     |                              6                               |
|                              5                               |                              7                               |     --     |     8     |                              6                               |
|                              9                               |                              13                              |     --     |    16     |                              12                              |
|                              16                              |                              22                              |     --     |    32     |                              24                              |
|                              32                              |                              42                              |     --     |    64     |                              48                              |

***ps:*** 若暂时无法确定初始值，请设置为 **16**。

- 算法    大 **_O_** 标记（分析算法性能的理论方法）（增长率）    **时间复杂度/空间复杂度**
- **排序**

  - 选择排序 **_O_**(n²)
    - 在线性表中找到最小元素和首元素交换，然后在剩余元素中找到最小元素和剩余表中首元素交换
  - 插入排序 **_O_**(n²)
    - 新插入的元素与已排好序的数组元素从后往前比较，直到第k个元素 <= 新元素 < 第k+1个元素
  - 冒泡排序 **_O_**(n²)
    - 多次遍历数组，每次遍历连续比较相邻元素顺序，并决定是否交换它们，直到整个数组排序完成
  - 归并排序 **_O_**(n㏒~n~)
    - 将数组分为两半，对每部分递归地应用归并排序；在两部分都排好序后，对它们进行归并
  - 快速排序 **_O_**(n㏒~n~)
    - 选择 **主元**，从左遍历找第一个比主元大的数，从右遍历找第一个比主元小的数，两者交换；当主元大于右遍历第一个小于主元的元素时，两者交换，这样数组就被分为左边元素都小于主元且右边元素都大于主元的两部分；分别对两部分递归地应用快速排序算法，直到整个数组排序完成
  - 堆排序 **_O_**(n㏒~n~)
    - 对于二叉堆 **i** 处的结点，它的父结点位于 **(i-1)/2** 处，左子结点位于 **2i+1** 处，右子结点位于 **2i+2** 处
    - 添加新结点：将新结点加到堆末尾并作为当前结点，当前结点大于它的父结点时两者交换
    - 删除根结点：用末尾结点替换根结点并作为当前结点，当前结点小于子结点时与较大子结点交换（删除末尾结点）
  - 桶排序
    - 
  - 基数排序
    - 
  - 外部排序
    - 



### [Stream APIs][使用Stream API优化代码]

>To perform a computation, stream operations are composed into a *stream pipeline*. A stream pipeline consists of a source (which might be an array, a collection, a generator function, an I/O channel, etc), <u>zero or more</u> *intermediate operations* (which transform a stream into another stream, such as `Stream#filter(Predicate)`), and <u>a</u> *terminal operation* (which produces a result or side-effect, such as `Stream#count()` or `Stream#forEach(Consumer)`). Streams are <u>lazy</u>; computation on the source data is only performed when the terminal operation is initiated, and source elements are consumed only as needed.

**流的生成方式**

------

- 集合（最常用）

```java
List<Integer> integers = Arrays.asList(1, 2, 3);
Stream<Integer> stream = integers.stream();
// 对象流转换成数值流：mapToInt/mapToDouble/mapToLong
IntStream intStream = stream.mapToInt(new ToIntFunction<Integer>() {
    @Override
    public int applyAsInt(Integer value) {
        return value;
    }
});
```

- 数组

```java
int[] ints = {1, 2, 3};
IntStream stream = Arrays.stream(ints);
// 数值流转换成对象流
Stream<Integer> boxed = stream.boxed();
```

***ps:*** 基础类型数组通过 `Arrays.stream()` 生成的流是数值流（如IntStream）而不是对象流（Stream\<T>），数值流可以避免基础类型进行装箱/拆箱的性能损耗。

- 函数：`of()` / `empty()` / `iterate()` / `generate()`

```java
Stream<Integer> stream = Stream.of(1, 2, 3);
// 空流
Stream<Object> empty = Stream.empty();
// Stream#iterate
Stream<Integer> iterateStream = Stream.iterate(0, new UnaryOperator<Integer>() {
    @Override
    public Integer apply(Integer integer) {
        return integer + 2;
    }
}).limit(5);
// Stream#generate
Stream<Double> generateStream = Stream.generate(new Supplier<Double>() {
    @Override
    public Double get() {
        return Math.random();
    }
}).limit(5);
```

***ps:*** `iterate()` 和 `generate()` 函数生成的都是无限流，需要配合 `limit()` 函数使用。

- 文件

```java
Stream<String> lines = Files.lines(Paths.get(), Charset.defaultCharset());// 文件按行生成流
Stream<Path> paths = Files.list(Paths.get());
Stream<Path> walkPaths = Files.walk(Paths.get(), 2, FileVisitOption.FOLLOW_LINKS);
```



**中间操作**

<a href="#Stream#filter">`filter()`</a> / <a href="#Stream#sorted">`sorted()`</a> / <a href="#Stream#distinct">`distinct()`</a> / <a href="#Stream#limit">`limit()`</a> / <a href="#Stream#skip">`skip()`</a> / <a href="#Stream#map">`map()`</a> / <a href="#Stream#flatMap">`flatMap()`</a> / <a href="#Stream#allMatch">`allMatch()`</a> / <a href="#Stream#anyMatch">`anyMatch()`</a> / <a href="#Stream#noneMatch">`noneMatch()`</a> *etc*.

------

- `filter()` <a name="Stream#filter"></a>

- `sorted()` <a name="Stream#sorted"></a>

  ```java
  dataSet = dataSet.stream()
      .sorted((o1, o2) -> o1.getId().compareTo(o2.getId()))
      .collect(Collectors.toList());
  ```

- `distinct()` <a name="Stream#distinct"></a>

  ***ps:*** 引用对象去重，需要对象实现 `hashCode()` 和 `equals()`，否则去重无效。

- `limit()` <a name="Stream#limit"></a>

- `skip()` <a name="Stream#skip"></a>

- `map()` <a name="Stream#map"></a>

  ```java
  String[] names = dataSet.stream()
      .map(Entity::getName)
      .sorted()
      .toArray(String[]::new);
  ```
  
- `flatMap()` <a name="Stream#flatMap"></a>

- `allMatch()` <a name="Stream#allMatch"></a>

- `anyMatch()` <a name="Stream#anyMatch"></a>

- `noneMatch()` <a name="Stream#noneMatch"></a>



**终端操作**

<a href="#Stream#count">`count()`</a> / <a href="#Stream#findFirst">`findFirst()`</a> / <a href="#Stream#findAny">`findAny()`</a> / <a href="#Stream#reduce">`reduce()`</a> / <a href="#Stream#min/max">`min()/max()`</a> / <a href="#Stream#forEach">`forEach()`</a> / <a href="#Stream#">`collect(Collector)`</a> *etc*.

*Collector*：<a href="#Collectors#toList/toSet">`toList()/toSet()`</a> / <a href="#Collectors#counting">`counting()`</a> / <a href="#Collectors#joining">`joining()`</a> / <a href="#Collectors#groupingBy">`groupingBy()`</a> / <a href="#Collectors#partitioningBy">`partitioningBy()`</a> *etc*.

------

- `reduce()` <a name="Stream#reduce"></a>

  ```java
  // equivalent to: 5+4+3+2+1
  Integer reduce = Stream.of(4, 3, 2, 1).reduce(5, new BinaryOperator<Integer>() {
      @Override
      public Integer apply(Integer integer, Integer integer2) {
          return Integer.sum(integer, integer2);
      }
  });
  ```
  
- 分组 `groupingBy()` <a name="Collectors#groupingBy"></a>

  ```java
  Map<String, List<Entity>> groups = dataSet.stream().
      collect(Collectors.groupingBy(Entity::getType));
  List<Entity> typeList = groups.get("type");
  ```

- 分区 `partitioningBy()` <a name="Collectors#partitioningBy"></a>

  ```java
  Map<Boolean, List<Entity>> partitions = dataSet.stream().
      .collect(Collectors.partitioningBy(new Predicate<Entity>() {
          @Override
          public boolean test(Entity entity) {
              return entity.clickable();
          }
      }));
  List<Entity> clickableEntities = partitions.get(true);
  ```

- 最小/大值

  ```java
  // 方式一
  Optional<Integer> min = Stream.of(1, 2, 3, 4).min(Integer::compareTo);
  Optional<Integer> max = Stream.of(1, 2, 3, 4).max(Integer::compareTo);
  ```

  ```java
  // 方式二（推荐）
  OptionalInt min = Stream.of(1, 2, 3, 4).mapToInt(Integer::intValue).min();
  OptionalInt max = Stream.of(1, 2, 3, 4).mapToInt(Integer::intValue).max();
  ```

  ```java
  // 方式三
  Optional<Integer> min = Stream.of(1, 2, 3, 4).reduce(Integer::min);
  Optional<Integer> max = Stream.of(1, 2, 3, 4).reduce(Integer::max);
  ```

- 求和

  ```java
  int sum = Stream.of(1, 2, 3, 4).mapToInt(Integer::intValue).sum();
  ```

- 求平均值

  ```java
  // 方式一
  Double average = Stream.of(1, 2, 3, 4).collect(Collectors.averagingInt(Integer::intValue));
  ```

  ```java
  // 方式二
  OptionalDouble average = Stream.of(1, 2, 3, 4).mapToInt(Integer::intValue).average();
  ```

- 数据统计摘要（包含最小/大值、和、平均值、个数）

  ```java
  // 方式一
  IntSummaryStatistics statistics = Stream.of(1, 2, 3, 4).collect(Collectors.summarizingInt(Integer::intValue));
  ```

  ```java
  // 方式二
  IntSummaryStatistics statistics = Stream.of(1, 2, 3, 4).mapToInt(Integer::intValue).summaryStatistics();
  ```

  ```java
  int min = statistics.getMin();
  int max = statistics.getMax();
  long sum = statistics.getSum();
  double average = statistics.getAverage();
  long count = statistics.getCount();
  ```



## Optional

```java
Optional.ofNullable(object.optJSONArray("data"))
    .ifPresent(array -> {
        // TODO
    });
```

```java
Optional.ofNullable(object.optJSONArray("data"))
    .filter(array -> array.length() > 0)
    .map(array -> array.optJSONObject(0))
    .ifPresent(object -> {
        // TODO
    });
```





## 泛型

> 泛型（Generic）的由来
>
> 现在的程序开发大都是面向对象的，会用到各种类型的对象，一组对象通常需要用集合来存储它们，如List、Map等。这些集合类中装的都是具体类型的对象，若每种类型都去实现显然是不可能的。因此就诞生了**泛型**，将具体的类型泛化，编码时用符号指代，使用时再确定其类型。

- 泛型只能指定类、接口、引用类型（可同时指定多个泛型参数）。

- 声明泛型方法 `public <T> void method(){}` 返回类型前的 `<T>` 不能省略；

  调用泛型方法 `.<T>method();`（注意"•"）。

- 在类中定义具有泛型形参的静态方法，该静态方法必须声明为泛型方法，即使这个类已经声明了该泛型。

- 声明类或接口时，可以使用 `extends/super` 来设置泛型边界，将泛型类型参数限制为某个类型的子/父集（包括自身）：

  `class Monster<T extends Animal> {} //T必须是Animal或其子类型`

  设置多个边界时，用 **&** 符号连接：

  `class Monster<T extends Animal & Food> {} //T必须同时是Animal和Food的子类型`

  ***ps:*** 注意和泛型通配符的区别，这里没有**？**。

- **?**　　　　　　非受限通配

  **? extends T**　上界通配：表示T或T的子类型（T 也可以是间接子类、接口）

  **? super T**　　下界通配：表示T或T的父类型（T 也可以是间接子类、接口）

- 子类泛型不可以转换为父类泛型，泛型只存在于编译时，运行时被**类型擦除**。

```java
// 子类的泛型（List<Button>）不属于泛型（List<TextView>）的子类
TextView textView = new Button(context); // 多态
List<Button> buttons = new ArrayList<Button>();
List<TextView> textViews = buttons; // 报错：incompatible types..
```

- 上界通配（**? extends**）[使泛型具有协变性]

```java
List<Button> buttons = new ArrayList<Button>();
List<? extends TextView> textViews = buttons;
```

一般集合类都包含了 `get` 和 `add` 操作，比如List：

```java
public interface List<E> extends Collection<E> {
    E get(int index);
    boolean add(E e);
    ...
}
```

使用上界通配符时，由于编译器也不确定？的具体类型，它只是个限制条件，所以get得到的对象肯定是TextView的子类型，由于多态，才能赋值给TextView，View也没问题；但add操作时，若`List<? extends TextView>`的类型实际是`List<Button>`，添加TextView就会报错。

```java
List<? extends TextView> textViews = new ArrayList<Button>();
TextView textView = textViews.get(0);
textViews.add(textView); // 报错，no suitable method found for add(TextView)
```

即便使用非受限通配符**？**，也一样不能完全保证类型安全。因此，Java限制上界通配符只能向外提供数据，从该角度讲，称为<u>生产者Producer</u>。

```java
List<Button> buttons = new ArrayList<>();
List<?> list = buttons; // List<?>相当于List<? extends Object>
Object obj = list.get(0);
list.add(obj); // 仍然会报错
```



- 下界通配（**? super**）[使泛型具有逆变性]

```java
List<? super Button> buttons = new ArrayList<TextView>();
```

使用下界通配符时，编译器同样不能确定？的具体类型，get得到的父类型对象一般也没有实际应用场景，因此这种泛型类型声明被称为<u>消费者Consumer</u>。

```java
List<? super Button> buttons = new ArrayList<TextView>();
Object object = button.get(0); // get正常
Button button = ...
button.add(button); // add正常
```



> **PECS法则：*Producer-Extends, Consumer-Super.***

- 使用上界通配符 `? extends` 使泛型支持协变，但是**只能读取不能修改**，这里的修改仅指向泛型集合添加元素，`remove` 和 `clear` 不受影响。
- 使用下界通配符 `? super` 使泛型支持逆变，但是**只能修改不能读取**，这里的不能读取是指不能按照泛型类型读取，若按照Object类型读取再强转当然是可以的。



- 由于泛型的类型擦除，任何在运行时需要知道泛型确切类型的操作都无法使用。比如无法检查一个对象是否为泛型类型T的实例。

```java
<T> void check(Object item) {
    if(item instanceof T) { // IDE报错，illegal generic type for instanceof
        System.out.println(item);
    }
}
```

- 该问题的解决方案通常是额外传递一个 `Class<T>` 类型的参数，然后通过 `isInstance()` 方法来检查。

```java
<T> void check(Object item, Class<T> type) {
    if(type.isInstance(item)) {
        System.out.println(item);
    }
}
```



## Thread

>线程安全问题的本质：在多个线程访问共享资源时，一个线程在对资源 **写** 操作的**过程中**(未结束)，**其它线程**又对这个 **正在写** 的资源进行了读/写操作，就会导致数据错误。
>
>无论是线程安全问题，还是针对线程安全问题衍生出的锁机制，其核心都在于共享的**资源**，而不是某个方法或代码块。

- Java将线程分为user thread和daemon thread。daemon thread通常用来为user thread提供服务。当所有user thread执行完后，不管是否还有daemon thread仍在执行，JVM都会退出（即当一个进程里所有的线程都是守护线程时，结束当前进程，通常用于记录日志，统计性能等）。
- `main()` 方法是一个user thread。
- 新创建的线程和创建它的线程具有相同daemon状态，可以调用 `Thread#setDaemon` 改变，必须在线程 `start()` 之前调用。
- 调用 `Thread#isDaemon` 判断线程是否为守护线程。
- 创建、运行线程的各种方式

1. 普通

```java
Thread thread = new Thread() {
    @Override
    public void run() {
        // If a target Runnable set in Thread's constructor, it'll invoked by below.
        super.run();
        System.out.println("New thread.");
    }
};
thread.start();
```

2. Runnable

```java
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("New runnable thread.");
    }
};
Thread thread = new Thread(runnable);
thread.start();
```

3. ThreadFactory

```java
ThreadFactory threadFactory = new ThreadFactory() {
    @Override
    public Thread newThread(@NonNull Runnable r) {
        return new Thread(r, "thread");
    }
};
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("New thread built by Thread Factory.");
    }
};
Thread thread = threadFactory.newThread(runnable);
thread.start();
```

4. ThreadPool

```java
Runnable runnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("New thread created by Thread Pool.");
    }
};
ExecutorService executor = Executors.newCachedThreadPool();
executor.execute(runnable);
executor.shutdown();
```

5. 返回值：Callable和Future

```java
Callable<String> callable = new Callable<String>() {
    @Override
    public String call() throws Exception {
        Thread.sleep(2000L);
        return "Thread witn return after 2s.";
    }
};
ExecutorService executor = Executors.newCachedThreadPool();
Future<String> future = executor.submit(callable);
try {
    System.out.println(future.get());
} catch (ExecutionException | InterruptedException e) {
    e.printStackTrace();
} finally {
    executor.shutdown();
}
```

- `thread.interrupt();` // 中断线程，需配合以下方法判断线程状态；~~`thread.stop()` 已被弃用~~。

  `Thread.interrupted()` // 静态方法，返回true的同时会重置线程中断状态为false

  `thread.isInterrupted()` // 实例方法，调用后不会重置线程中断状态

```java
Thread thread = new Thread() {
    @Override
    public void run() {
        try {
            Thread.sleep(1000L);
        } catch (InterruptedException e) {
            System.out.println("Thread interrupted.");
            e.printStackTrace();
            // 若此处不return，会继续执行后续代码
            return;
        }
        System.out.println("Running thread.");
    }
};
thread.start();
thread.interrupt();
```

- `thread1.join();` // 先让thread1运行完成

- `Thread.yield();` // 临时让出CPU时间给其他线程

- 同步 **实例方法**，锁 在调用方法的 **对象** 上

  同步 **静态方法**，锁 在方法所属的 **类** 上

- 同步语句    `synchronized(expr) {}` expr必须为对象实例引用

- 任何同步实例方法都能转换成同步语句，因为以下两种写法的monitor都是当前类对象。

```java
public synchronized void method() {
    // statements
}
//等价于
public void method1() {
    synchronized(this) {
        // statements
    }
}
```

- monitor（监视器）

```java
private Object monitor1 = new Object();
private Object monitor2 = new Object();
synchronized (monitor1) {
    synchronized (monitor2) {
        // statements
    }
}
```

- **synchronized** 的本质

  - 资源 **互斥** 访问

    保证方法内部或代码块内部资源（数据）的互斥访问（*i.e.* 同一时间，由同一monitor监视的代码，只能有一个线程在访问）

  - 数据 **同步**

    保证线程之间对监视资源的数据同步（*i.e.* 任何线程在获取monitor后，会第一时间将共享内存中的数据复制到自己的缓存中操作；释放monitor后，会第一时间将缓存中操作后的数据复制更新回共享内存）

- **volatile**

  - 保证操作volatile关键字修饰字段的 **原子性** 和 **同步性**。其中原子性相当于实现了针对单一字段的线程间互斥访问。
  - 只对基本类型（byte、char、int、*etc.*）和对象引用的<u>赋值操作</u>有效。

- `java.util.concurrent.atomic` 包

  包含 `AtomicInteger` `AtomicBoolean` *etc.*，作用和volatile基本一致，可以看做通用版的volatile。

```java
AtomicInteger atomicInteger = new AtomicInteger(9);
atomicInteger.get();
atomicInteger.getAndIncrement(); // i++
atomicInteger.incrementAndGet(); // ++i
```

- 显式调用 lock（更灵活但也更麻烦）

> 本质：通过对共享资源进行访问限制，使得同一时间只有一个线程可以访问资源，保证了数据的准确性。

```java
public void method() {
    lock.lock();// 持有锁
    try {
        // statements 1
    } catch(Exception e) {
        // statements 2
    } finally {
        lock.unlock();// 在finally中调用，保证无论何时都能正常释放锁
    }
}
```

- 读写锁

```java
ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
final ReentrantReadWriteLock.WriteLock writeLock = lock.writeLock();
final ReentrantReadWriteLock.ReadLock readLock = lock.readLock();
// 使用方法同普通锁
// TODO
```

- 使用显式lock和condition以及对象的monitor进行进程间通信

```java
private static Lock lock = new ReentrantLock();// 重入锁
private static Condition condition = lock.newCondition();// 应用条件前必须先持有锁
```

```java
contition.await();// 阻塞当前线程等待唤醒信号
contition.signal()/signalAll();// 唤醒等待的线程
```

```java
while(..) {
    // statements
    condition.await();
    // 唤醒线程后，将从await()下一行继续执行，因此await()应该在循环体中调用（可以重新检验条件）
}
```

- 调用 `wait()`、`notify()`、`notifyAll()` 一定要在synchronized块里

- 阻塞队列（BlockingQueue）

  `put()` // 尾部插入，满则阻塞等待

  `take()` // 头部删除，空则阻塞等待

- 用Semaphore（信号量）来限制访问同一共享资源的线程（并发）数量

  `semaphore.acquire();` // 获得许可

  `semaphore.release();` // 释放许可

- 采用正确的资源排序可以**避免死锁**

- 同步合集 `Collections#synchronizedXxx()`

  **_ps:_** 遍历同步合集时要持有对象上的锁

- 并发编程(Java7)（Fork/Join框架）

  自定义子任务类继承RecursiveAction或RecursiveTask类并覆写 `compute()` 方法实现任务逻辑

  - 继承RecursiveAction执行 **无返回值** 任务

  ```java
  RecursiveAction action = new Action( );
  ForkJoinPool pool = new ForkJoinPool();
  pool.invoke(action);// 并行执行任务类
  
  class Action extends RecursiveAction {
      @Override
      protected void compute() {
          // ..
          // subTasks.fork();
          // 异步执行子任务
          invokeAll(subTasks..);
      }
  }
  ```
  
  - 继承RecursiveTask执行 **有返回值** 任务

  ```java
  RecursiveTask<T> task = new Task( );
  ForkJoinPool pool = new ForkJoinPool();
  T result = pool.invoke(task);// 获取总任务返回结果
  
  class Task extends RecursiveTask<T> {
      @Override
      protected T compute(){
          // ..
          // subTasks.fork();
          // 异步执行子任务
          invokeAll(subTasks..);
          return subTasks.join();// 返回子任务结果
      } 
  }
  ```
  
  **_ps:_** `invoke()` 和 `invokeAll()` 隐式调用 `fork()`



- TCP套接字通信（C/S架构）

  - 服务器Socket

  ```java
  // 绑定端口（0-65536,0-1023为系统预留端口）
  ServerSocket serverSocket = new ServerSocket(port);
  Socket socket = serverSocket.accept();// 调用accept()方法监听客户端请求
  InputStream is = socket.getInputStream();// 连接建立后通过输入流读取Client请求信息
  OutputStream os = socket.getOutputStream();// 通过输出流向Client发送响应信息
  is.close();// 关闭资源
  os.close();
  socket.close();
  ```

  - 客户端Socket

  ```java
  Socket socket = new Socket("127.0.0.1", port);// 创建socket对象，指明服务器IP地址和端口
  OutputStream os = socket.getOutputStream();// 连接建立后通过输出流向Server发送请求信息
  InputStream is = socket.getInputStream();// 通过输入流获取Server的响应信息
  os.close();// 关闭资源
  is.close();
  socket.close();
  ```

  - InetAddress类

  ```java
  InetAddress address = socket.getInetAddress();
  // InetAddress address = InetAddress.getByName("www.google.com");
  address.getHostName();// 获取主机名
  address.getHostAddress();// 获取IP
  ```



## I/O

- InputStream的 `read()` 方法返回 ***-1*** 代表读取结束。
- PrintWriter写数据必须 `close()`，否则数据将不会写入：

```java
PrintWriter output = new PrintWriter(new File("temp.txt"));
output.print("hello world");
output.close();// 必须调用. flush()?
```

- 流应该在finally块中关闭（关闭前先判空再嵌套一层 try/catch）或 使用 **try-with-resource** 语法自动关闭（声明多个资源以**；**间隔，并且按照声明顺序倒序来关闭资源）;
- FileInputStream **读**

```java
try (FileInputStream input = new FileInputStream("temp.dat")) {
    int value;
    while ((value = input.read()) != -1) {
        System.out.println(value);
    }
}
```

- FileOutputStream **写**

```java
try (FileOutputStream output = new FileOutputStream("temp.dat")) {
    output.write(7);
}
```

- DataInputStream **读**

```java
try (DataInputStream input = new DataInputStream(new FileInputStream("temp.dat"))) {
    System.out.println(input.readUTF() + " " + input.readDouble());
}
```

- DataOutputStream **写**

```java
try (DataOutputStream output = new DataOutputStream(new FileOutputStream("temp.dat"))) {
    output.writeUTF("foobar");
    output.writeDouble(100.0);
}
```

- BufferedOutputStream / BufferedInputStream

  ***ps:*** 使用缓存流必须建立在一个已存在的流的基础上，缓存默认大小512字节

- ObjectOutputStream / ObjectInputStream

  ***ps:*** 读取对象流时必须匹配写入时的对象类型（转型）和顺序

- 随机访问文件 `RandomAccessFile raf = new RandomAccessFile("test.dat", "rw");`  *r*：只读 *rw*：读写



- 读

```java
private String read() {
    StringBuilder builder = new StringBuilder();
    try (FileInputStream fileInput = new FileInputStream("filename");
         InputStreamReader inputReader = new InputStreamReader(fileInput);
         BufferedReader reader = new BufferedReader(inputReader)) {
        String line;
        while ((line = reader.readLine()) != null) {
            builder.append(line);
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    return builder.toString();
}
```

- 写

```java
private void write(String data) {
    try (FileOutputStream fileOutput = new FileInputStream("filename");
         OutputStreamWriter outputWriter = new OutputStreamWriter(fileOutput);
         // 缓冲流必须基于已存在流
         BufferedWriter writer = new BufferedWriter(outputWriter)) {
        writer.write(data);
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```



## 反射

1. 获取类对象（3种方式）

   - `Class clazz1 = Class.forName("charactor.Hero");`
   - `Class clazz2 = Hero.class;`
   - `Class clazz3 = new Hero().getClass();`

   **_ps:_** 获取类对象会初始化静态属性，且只会执行一次（方式2不会初始化静态属性）

2. 获取构造器并实例化对象

```java
Constructor c = clazz1.getConstructor(..);
Hero hero = (Hero) c.newInstance();
```

3. 访问或修改字段


```java
Field f = clazz1.getDeclaredField("name");// 获取类Hero的name变量
f.setAccessible(true);// 访问或修改非public变量前需将此标记置为true以抑制IllegalAccessException
f.get(hero);
f.set(hero,"teemo");// 修改变量值
```

**_ps:_** `getDeclaredField()` 可以获取类中所有字段，包括非public字段（继承来的除外）；`getField()` 只能获取public字段，包括从父类继承的。

4. 调用方法

```java
// 获取方法setName，方法参数类型是String
Method m = clazz1.getMethod("setName", String.class);
// m.setAccessible(true);
m.invoke(hero, "盖伦");// 对h对象调用这个方法
```

***ps:*** 同样分为 `getMethod()` 和 `getDeclaredField()`。



## JVM知识

---- 总结自《深入理解Java虚拟机：JVM高级特性与最佳实践 - 周志明》

- **类初始化机制**：父类类构造器 `<clinit>()` -> 子类类构造器 `<clinit>()` -> 父类成员变量和构造(实例)代码块 -> 父类构造函数 -> 子类成员变量和构造(实例)代码块 -> 子类构造函数
- 初始化阶段是执行类构造器 `<clinit>()` 方法的过程。
- 不使用的变量应手动赋null值，进行变量槽(局部变量表)复用，优化GC回收。(可在特殊情况下使用，但非必要依赖，因为编译器可能已经具备自动进行此类优化的功能)
- 使用局部变量必须赋初值，是因为局部变量没有像类变量一样的“准备阶段”(赋系统初始值)，因此必须手动赋值消除歧义，才能通过编译、JVM字节码校验。

- JVM的解释执行引擎被称为“基于栈的执行引擎”，指的是“操作数栈”。



# BEST PRACTICE

- `for (int i = 0, len = list.size(); i < len; i++) {}` 避免每次比较条件都要调用 `list.size()`





# APPENDIX

## QUICK NOTES

- 环境变量配置

  - **JAVA_HOME**

    C:\Program Files (x86)\Java\jdk1.8.0_91　// jdk安装目录

  - **CLASSPATH**　_类文件搜索路径_

    .;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;　// 注意最前面的"•"

  - **PATH**　_操作系统外部命令搜索路径_

    %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;

  **_ps:_**（推荐）把 `%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;` 放在PATH的最前面，javapath(.lnk)放到PATH的最后面。

- Java常用CLI

  - *javac*  `javac <options> <source files>`
  
  ```
  javac Hello.java // 生成Hello.class
  ```
  
  - *java*  `java <class files>`
  
  ```
  java Hello // 执行Hello.class
  ```
  
  - *javap*  `javap <options> <classes>`
  
  ```
  javap Hello // 查看类方法签名
  javap -v Hello // 查看类、方法、变量等详情
  javap -c Hello // Disassemble the code
  ```
  
- Eclipse与JDK源码关联

  Window → Preferences → Java → Installed JREs → (选中项目所用jre) → Edit → (选中rt.jar) → Source Attachment → External location → External File → (jdk/src.zip)



## ERROR ANALYSIS

- Q：Eclipse中Java文件图标异常（空心 **J**）

  **_ps:_** 此类文件表示不被包含在项目中进行编译，而是当做资源存在项目中。

  A：1）单个文件：`右击该文件 --> Build Path --> Include`；

  ​      2）所有文件：`右击该项目 --> Build Path --> Configure Build Path --> Source --> Add Folder... --> 选择相应的文件夹添加到Source`。



## LINKS

[使用Stream API优化代码]:https://juejin.im/post/6844903945005957127
[TOC]

# START KOTLIN

- Kotlin是基于jvm的静态编程语言，可以编译成Java字节码，也可以编译成JavaScript。

- Kotlin文件以 **.kt** 为后缀 `Kotlin.kt`

- 定义常量与变量

  - 可变变量：**var** 关键字 `var <标识符>：<类型> = <初始化值>`
  - 不可变变量（只能赋值一次）：**val** 关键字 `val <标识符>：<类型> = <初始化值>`

  **_ps:_** 变量和常量都可以没有初始化值，但是在引用前必须初始化；编译器支持自动类型判断。

  ```kotlin
  val a:Int = 1
  val b = 2 // 系统自动推断变量类型为Int
  val c:Int // 若声明时未初始化，则必须指明变量类型
  c = 3 // 明确赋值
  ```
  
  ```kotlin
  var x = 6 // 系统自动推断变量类型为Int
  x += 1 // 变量可修改
  ```



- 字符串模板

  - **$** 表示一个变量名或变量值
  - **$varName** 表示变量值
  - **${varName.fun()}** 表示变量方法的返回值

  ```kotlin
  var a = 1
  val s1 = "a is $a"
  a = 2
  val s2 = "${s1.replace("is", "was")}, but now is $a"
  
  // 原生字符串中表示字面值$（它不支持反斜杠转义）
  println("${'$'}9.99")// 输出$9.99
  ```



- null检查机制

  Kotlin的空安全设计对于声明可为空的参数，在使用时要进行空判断处理，有两种方式：

  - 字段后加 **!!**，像Java一样抛出空指针异常
  - 字段后加 **?**，不做处理返回null 或 配合 **?:**（Elvis操作符）做空判断处理

  ```kotlin
  var age:String? = "" // 类型后加？表示可为空
  val ages = age!!.toInt() // 抛出空指针异常
  val ages1 = age?.toIntOrNull() // 不做处理返回null
  val ages2 = age?.toIntOrNull() ?: -1 // age为空返回-1
  ```
  
  - Elvis操作符（**?:**）的另一种常见用法
  
  ```kotlin
  fun validate(user: User) {
      val id = user.id ?: return // 验证user.id是否为空，为空时return
  }
  ```
  
  - 函数参数也有可空的控制，在传递时需要注意：
  
  ```kotlin
  var myName: String? = "shaun"
  fun cook(name: String): Food {}
  cook(myName) // 报错，可空变量传给不可空参数
  ```
  
  ```kotlin
  var myName: String = "shaun"
  fun cook(name: String?): Food {}
  cook(myName) // 正常运行，不可空变量传给可空参数
  ```
  
  ```kotlin
  var myName: String? = "shaun"
  fun cook(name: String?): Food {}
  cook(myName) // 正常运行，可空变量传给可空参数
  ```
  
  ```kotlin
  var myName: String? = "shaun"
  fun cook(name: String): Food {}
  cook(myName) // 正常运行，不可空变量传给不可空参数
  ```
  
  ***ps:*** 关于空安全，最重要的是记住一点：所谓可空不可空，关注的都是使用的时候，即该变量在使用时是否可能为空。



- 类型检测及自动类型转换

  - 使用 **is** 运算符检测一个表达式是否为某类型的一个实例（类似于Java中的 _instanceof_）

  ```kotlin
  fun getStringLength(obj:Any):Int? {
      if (obj is String){
          // 做过类型判断以后，obj会被系统自动转换为String类型
          return obj.length
      }
      // 这里的obj仍然是Any类型的引用
      return null
  }
  ```
  
  ```kotlin
  fun getStringLength(obj:Any):Int? {
      // 与Java中instanceof不同，还可以使用!is
      if (obj !is String) {
          return null
      }
      // 这里obj会被系统自动转换为String类型
      return obj.length
  }
  ```
  
  ```kotlin
  fun getStringLength(obj:Any):Int?{
      // 在'&&'运算符右侧，obj会被系统自动转换为String类型
      if (obj is String && obj.length > 0){
          return obj.length
      }
      return null
  }
  ```
  
  
  
  - **as** 关键字不进行类型检测直接强转
  
  ```kotlin
  fun getStringLength(obj: Any): Int? {
      // 若强转类型操作正确就没问题，但若错误就会抛出类型转换异常，所以不推荐这种写法
      return (obj as String).length
  }
  ```
  
  ```kotlin
  fun getStringLength(obj: Any): Int? {
      // 更安全的写法
      return (obj as? String)?.length
  }
  ```




- 区间 **Range**（IntRange、CharRange、LongRange等）

  区间表达式由 `..` 操作符辅以 **in** 或 **!in** 形成。区间是为任何可比较类型定义的，但对于整型原生类型，它有一个优化的实现。

  ```kotlin
  for(i in 1..3){
      print(i) // 输出123
  }
  ```

  ```kotlin
  for(c in 'a'..'c') {
      print(c) // 输出abc
  }
  ```

  ```kotlin
  for(i in 3 downTo 1){
      print(i) // 输出321
  }
  ```
  
  ```kotlin
  var i = 9
  if (i in 1..10) {// 相当于 1 <= i && i <= 10
      print(i)
  }
  ```
  
  ```kotlin
  //使用step指定步长
  for (i in 1..4 step 2){
      print(i) // 输出13
  }
  ```
  
  ```kotlin
  // 使用until排除结尾元素
  for (i in 1 until 10){// i in [1,10) 排除了10
      print(i) // 输出123456789
  }
  ```
  
    ***ps:*** `downTo`、`step` 叫做中缀表达式。




- 函数定义

  - 使用关键字 **fun**，参数格式为：`参数：类型`

  ```kotlin
  fun sum(a: Int, b: Int): Int{// Int参数，返回值Int
      return a + b
  }
  ```

  - 表达式作函数体，返回类型自动推断

  ```kotlin
  fun sum(a: Int, b: Int) = a + b
  ```
  
  ```kotlin
public fun sum(a:Int, b:Int): Int = a + b // public方法必须明确写出返回类型
  ```

  - 无返回值的函数（类似于Java中的 _void_）
  
  ```kotlin
  fun printSum(a: Int, b: Int): Unit {
      print(a + b)
  }
  ```
  
  ```kotlin
public fun printSum(a: Int, b: Int) {// 若返回Unit类型，可缺省（public方法也可缺省Unit）
      print(a + b)
}
  ```
  
  - 可变长参数函数 [函数的变长参数用 `vararg` 关键字标识]
  
  ```kotlin
  fun vars(vararg v: Int) {
      for (vt in v) {
          print(vt)
      }
}
  // 调用
  fun main() {
      vars(1,2,3,4,5) // 输出12345
  }
  ```
  
  ```kotlin
  //扩展操作符 *：将Array扩展为vararg
  fun sum(vararg numbers: Int): Int {}
  val array = intArrayOf(4, 5)
  sum(1, 2, 3, *array, 6) eq 21
  //vararg传参也要使用 *
  fun first(vararg numbers: Int) {}
  fun second(vararg numbers: Int) = first(*numbers)
  ```
  
  - lambda（匿名函数）
  
  > Kotlin支持函数式编程，以函数作为参数或返回值的函数被称为“高阶函数”。
  >
  > Lambda表达式是一个匿名函数，即没有函数名称的函数，可以表示闭包。
  
  ```kotlin
  fun main() {
      val sumLambda: (Int, Int) -> Int = { x, y -> x + y }
      println(sumLambda(1, 6)) // 输出7
  }
  ```



- Kotlin中没有基础数据类型，只有封装的数字类型对象。

- Kotlin中的Int和Java中的int以及Integer不同，主要是在装箱方面：

  - Java中的int是unbox的，而Integer是box的；
  - Kotlin中的Int是否装箱根据场合决定。

  ```kotlin
  var a: Int = 1 // unbox
  var b: Int? = 2 // box
  var list: List<Int> = listOf(1,2) // box
  ```

  ***ps:*** Kotlin在语言层面简化了Java中的int和Integer。拆装箱主要涉及到程序运行时的性能开销，因此在日常使用中，<u>对于Int这样的基本类型，尽量用不可空变量</u>。

  Java中的基本类型，类比到Kotlin，满足以下条件就不装箱：

  - 不可空类型；
  - 使用IntArray、FloatArray等。



- 可以使用<u>下划线</u>使数字常量更易读

  `val oneMillion = 1_000_000` 、`val creditCardNumber = 1234_5678_9012_3456L`

- 比较两个数字 [在Kotlin中，**==** 表示比较值大小，**===** 表示比较对象地址]

  ***ps:***

  - **==**：对基本类型及String等类型进行内容比较，相当于Java的 `equals()`；
  - **===**：对引用的内存地址进行比较，相当于Java的 **==** 。

- 类型转换 [需调用每种数据类型的类型转换方法，如 `toInt()、toFloat()` 等]

  ```kotlin
  val b:Byte = 1 //字面值是静态检测的
  val i:Int = b.toInt()
  // 在可以根据上下文推断出正确的数据类型而且数据操作符会做相应的重载的情况下，能使用自动类型转化
  val l = 1L + 3 // Long + Int => Long
  ```
  
- 位操作符 [对于Int和Long类型，还有一系列的位操作符可以使用]

  - `shl(bits) // 左移（相当于Java的<<）`
  - `shr(bits) // 右移（相当于Java的>>）`
  - `ushr(bits) // 无符号右移（相当于Java的>>>）`
  - `and(bits) // 与`
  - `or(bits) // 或`
  - `xor(bits) // 异或`
  - `inv() // 反向`
  
- 字符Char [和Java不同，Kotlin中Char不能直接与数字操作，必须使用单引号 `'` ]

- 数组 [不支持协变（Java支持）]

  - **[]** 操作符代表调用成员函数的 `get()` `set()`
  - 除了Array，还有ByteArray、ShortArray、IntArray等用来表示各个类型的数组，省去了装箱操作，效率更高，用法和Array一样。

  ```kotlin
  val arr = arrayOf(1, 2, 3) // [1,2,3]
  val arr1 = Array(4) {i -> i * 2} // [0,2,4,6]
  println(arr[2]) // 输出3
  
  val arri = intArrayOf(1, 2, 3)
  ```
  
  - 不支持协变
  
    Kotlin中的数组编译成字节码时使用的仍然是Java的数组，但在语言层面是泛型实现，这样会失去协变特性，就是子类数组对象不能赋值给父类数组变量。
  
  ```kotlin
  val strs: Array<String> = arrayOf("a", "b", "c")
  val anys: Array<Any> = strs // compile-error: type mismatch
  ```
  
  

- 集合

  Kotlin和Java一样有三种集合类型：

  - List：有序，可重复
  - Set：无序，不重复
  - Map：键-值对，无序，键不可重复，不同键可以对应相同值

  

  - **List** [支持协变（Java不支持）]

    List和数组的区别：Kotlin中数组和MutableList的API是非常像的，主要区别是数组的元素个数不能变。那么什么时候使用数组呢？

    - 这个问题在Java中就存在了，数组和 `List` 的功能类似，`List` 的功能更多一些，直觉上应该用 `List`。但数组也不是没有优势，基本类型（`int[]`、`float[]`）的数组不用自动装箱，性能好一点。
    - 在Kotlin中也是同样的道理，在一些性能需求比较苛刻的场景，并且元素类型是基本类型时，用数组好一点。不过要注意一点，Kotlin中要用专门的基本类型数组类（`IntArray`、`FloatArray` 等）才可以免于装箱。也就是说元素不是基本类型时，相比 `Array`，用 `List` 更方便些。

    ```kotlin
    val strList: List<String> = listOf("a", "b", "c")
    val anyList: List<Any> = strList // 没问题
    ```

    

  - **Set** [支持协变]

  ```kotlin
  val strSet = setOf("a", "b", "c")
  ```

  

  - **Map**

    `mapOf` 的每个参数表示一个键值对，**to** 表示将 *键* 和 *值* 关联，称为中缀表达式。

    ```kotlin
    val map = mapOf("key1" to 1, "key2" to 2, "key3" to 2)
    ```

    - 取值

    ```kotlin
    val value1 = map.get("key1")
    // 或
    val value2 = map["key2"]
    ```

    - 修改

    ```kotlin
    // 只有mutableMapOf()创建的Map才能被修改值。
    val map = mutableMapOf("key1" to 1, "key2" to 2)
    
    map.put("key1", 2)
    // 或
    map["key2"] = 4
    ```

    ***ps:*** 这里用到了 _操作符重载_ 的知识，实现了和数组一样的 _Positional Access Operations_。

    

  - **可变/不可变集合**

    Kotlin中的集合分为：只读和可变的，只读有两层意思：

    - 集合的size不可变
    - 集合中的元素值不可变

    只读的集合（无mutable前缀）可以通过 `toMutable*()` 系列函数转换成对应的可变集合。

    ```kotlin
    val strList = listOf("a", "b", "c")
    // 返回一个新建的集合，原有的集合还是不变的，所以只能修改返回的集合
    val mutableStrList = strList.toMutableList()
    mutableStrList[0] = "a1"
    ```

    

  - **Sequence**（序列，又称*惰性集合操作*）

    Kotlin引入的一个新的容器类型，和Iterable一样用来遍历一组数据并可以对每个元素进行特定的处理。

    - 创建

    ```kotlin
    val seq1 = sequenceOf("a", "b", "c")
    // 使用Iterable创建（List实现了Iterable接口）
    val seq2 = strList.asSequence()
    // 使用Lambda表达式创建['()'内为第一个元素，Lambda表达式负责生成之后的元素，'it'指上一个元素]
    val seq3 = generateSequence(0) { it + 1 }
    ```

    - 惰性

    ```kotlin
    fun main() {
        val sequence = sequenceOf(1, 2, 3, 4)
        val result = sequence.map { i ->
            print("map<$i> ")
            i * 2
        }.filter { i ->
            print("filter<$i> ")
            i % 3 == 0
        }
        /*
        惰性：result被调用之前的赋值语句并不会立即执行，仅定义了一个执行流程；
        只有当result被调用时才会执行，并且执行流程如下：
        取元素i -> map -> filter判断，当出现第一个满足条件的元素时，就停止遍历，即跳过了4的遍历，
        输出：map<1> filter<2> map<2> filter<4> map<3> filter<6> result<6>
        */
        println("result<${result.first()}>") // 此处result被调用
    }
    ```

    而list是没有惰性特性的

    ```kotlin
    fun main() {
        val list = listOf(1, 2, 3, 4)
        val result = list.map { i ->
            print("map<$i> ")
            i * 2
        }.filter { i ->
            print("filter<$i> ")
            i % 3 == 0
        }
        /*
        声明之后立即执行；
        数据处理流程：依次取出所有元素进行map操作 -> 依次filter
        输出：map<1> map<2> map<3> map<4> filter<2> filter<4> filter<6> filter<8> result<6>
        */
        println("result<${result.first()}>") // 此处result是filter后的结果
    }
    ```

    - sequence惰性优点：

      -  一旦满足遍历退出的条件，就可以省略后续不必要的遍历过程；
      -  像 `List` 这种实现 `Iterable` 接口的集合类，每调用一次函数就会生成一个新的 `Iterable`，下一个函数再基于新的 `Iterable` 执行，每次函数调用产生的临时 `Iterable` 会导致额外的内存消耗，而 `Sequence` 在整个流程中只有一个；
      -  同一个sequence只能被消费一次。

      因此，`Sequence` 这种数据类型可以<u>在数据量比较大或者数据量未知的时候，作为流式处理的解决方案</u>。

    

- 数组与集合的操作符

  ```kotlin
  val intArray = intArrayOf(1, 2, 3)
  val strList = listOf("a", "b", "c")
  ```

  - `forEach`：遍历每个元素

  ```kotlin
  intArray.forEach { i -> print("$i ") } // 1 2 3 
  strList.forEach { s -> print("$s ") } // a b c 
  ```

  

  - `filter`：过滤每个元素，若Lambda表达式中的条件不成立就剔除该元素，剩余元素形成新集合

  ```kotlin
  // 注意，返回的是一个List<>
  val newList = intArray.filter { i -> i != 1 } // [2, 3]
  val newStrList = strList.filter { s -> s != "a" } // [b, c]
  ```

  

  - `map`：遍历每个元素并执行给定表达式，最终形成新集合

  ```kotlin
  val newList1 = intArray.map { i -> i + 1 } // [2, 3, 4]
  val newStrList1 = strList.map { s -> s + "0" } // [a0, b0, c0]
  ```

  

  - `flatMap`：遍历每个元素，并为每个元素创建新的集合，最终合并到一个集合中

  ```kotlin
  // [2, a, 3, a, 4, a]
  val newList2 = intArray.flatMap { i -> listOf("${i + 1}", "a") }
  // [a0, 1, b0, 1, c0, 1]
  val newStrList2 = strList.flatMap { s -> listOf(s + "0", "1") }
  ```

  ***ps:*** 与 `flatten` 的区别是 `flatMap` 可以变换后再平铺。`flatten` 相当于 `flatMap` 未进行变换直接平铺的特殊情况。

  

  - `groupBy`

  ```kotlin
  /*
  输出：End with 5: 5 15 25 
       End with 0: 10 20 30 
  */
  fun main() {
      val nums = listOf(-1, 0, 1, 2, 3, 4, 5, 6)
      nums.filter { it > 0 } // 1, 2, 3, 4, 5, 6
          .map { it * 5 } // 5, 10, 15, 20, 25, 30
          .groupBy { it.toString().last() } // ? ?
          .forEach {
              print("End with ${it.key}: ")
              for (num in it.value) {
                  print("$num ")
              }
              println()
          }
  }
  ```

  


- 字符串
  - 方括号语法 **[]** 可以很方便地获取字符串中的某个字符 `println("string"[2]) // 输出r`
  - 原生字符串（多行）可以用三个双引号 **"""** 声明（保留空格，转移字符）
  
  - `trimMargin()` 默认 **|** 作为边界前缀，也可以声明其他字符并作为参数传入 `trimMargin(">")`
  - 边界前缀之前只能有空格，否则不会生效；输出时会删除边界前缀及其之前的空格
  
  ```kotlin
  fun main() {
      val text = """
          |first row \n
            |second row
      """.trimMargin()
      /*
      输出：
      first row \n
      second row
      */
      println(text)
  }
  ```
  
  
  
- **if** 表达式

  ```kotlin
  // 作为表达式将结果赋值给一个变量
  val max = if(a > b) a else b
  ```
  
  ```kotlin
  // 或（多行代码块）
  val max = if(a > b) {
      println("choose a")
      a // 返回值
  } else {
      println("choose b")
      b // 返回值
  }
  ```
  
  ```kotlin
  // 使用区间
  val x = 9
  if (x in 1..10) {// 相当于 1 <= x && x <= 10
      print(x)
  }
  ```



- **when** 表达式 [类似于其他语言的 _switch_ ]

  - when 将它的参数和所有的分支条件顺序比较，直到第一个满足条件的分支（分支语句默认break）
  - when 既可以被当做表达式使用，也可以被当做语句使用
  - 取代 if-else if 链
  - else 相当于 switch 的 default

  ```kotlin
  val validNumbers = intArrayOf(2, 3, 6)
  val x = 6
  // 输出x is valid
  when (x) {
      0, 1 -> println("x == 0 or 1") // 多种情况执行相同代码
      2 -> println("x == 2")
      in 0..5 -> println("x is in the range") // 使用表达式作为分支判断条件
      in validNumbers -> println("x is valid")
      !in 8..10 -> println("x is out of range")
      else -> {
          println("otherwise")
      }
  }
  ```

  ```kotlin
  /*
  作为表达式使用时，必须有else分支，除非列出了所有的情况（保证一定有返回值）;
  检测一个值是（is）或不是（!is）某个特定类型
  */
  fun hasPrefix(x: Any) = when (x) {
      is String -> x.startsWith("!")
    else -> false
  }
  ```
  
  ```kotlin
  /*
  省略when后面的参数，直接跟语句块，
  哪个条件先为true就执行它的分支代码；
  此处输出 猕猴桃(奇异果)
  */
  val items = arrayOf("apple", "banana", "kiwifruit")
  when {
      "kiwifruit" in items -> println("猕猴桃(奇异果)")
      "apple" in items -> println("apple is fine")
  }
  ```



- **for** 循环

```kotlin
// for循环可以对任何提供迭代器（iterator）的对象进行遍历
for (item in ints) {
    print(item)
}
```

```kotlin
// 通过索引遍历一个数组或list
for (i in array.indices) {
    print(array[i])
}
```

```kotlin
// 使用withIndex()库函数
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}
```



- **try-catch**

```kotlin
try {
    //
} catch(e: Exception) {
    //
} finally {
    //
}
```

- 与Java异：

  - 在Java中，调用一个抛出异常的方法时，需要对异常进行处理，否则IDE就会报错；但Kotlin中IDE不会报错，但运行时如果抛出了异常就会报错。
  - Kotlin中的 `try-catch` 可以是一个表达式，允许代码块的最后一行作为返回值。

  ```kotlin
  fun main() {
      val input = "a"
      val a: Int? = try {
          parseInt(input)
      } catch (e: NumberFormatException) {
          e.printStackTrace()
          null // 代码块的最后一行作为返回值
      }
      println(a) // 打印异常信息，输出null
  }
  ```

  



- 返回和跳转

  - _return_ 默认从最直接包围它的 **函数** 或者匿名函数返回
  - _break_ 终止最直接包围它的循环
  - _continue_ 中止本次循环，继续下一次最直接包围它的循环

  ```kotlin
  for (i in 1..10) {
      if (i == 3) continue // i为3时跳过当前循环，继续下一次循环
      print(i) // 输出12456
      if (i > 5) break // i为6时，跳出循环
  }
  ```

  ```kotlin
  // 在Kotlin中任何表达式都可以用标签（label）来标记，标签的格式为标识符后跟@符号，如：abc@
  loop@ for (i in 1..100) {
      for (j in 1..100) {
          // 标签限制的break将跳出标签所指定的循环(i)，而不是只跳出循环(j)
          if (j > 10) break@loop
      }
  }// break到这里，接着运行程序
  ```
  
  ```kotlin
  val ints = arrayOf(6, 7, 0, 9)
  fun each() {
      ints.forEach jump@{
          /*
          不使用标签，当i=0时，将直接return出each()，只输出67
          （注意，这种非局部的返回只支持传给内联函数的lambda表达式）；
          若需要从lambda表达式中返回，则须加标签限制return，
          使用标签后，当i=0时，只跳出forEach循环；
          若仍未遍历结束，会继续遍历至下一次if条件为真或结束，输出679 return explicit label
          */
          if(i == 0) return@jump
          print(i)
      }
      print(" return explicit label")
  }
  ```
  
  ```kotlin
  // 更方便的隐式标签，与接受该lambda的函数同名
  val ints = arrayOf(6, 7, 0, 9)
  fun each() {
      ints.forEach {
          if (it == 0) return@forEach
          print(it)
      }
      print("return implicit label")
  }
  ```
  
  ```kotlin
  // 用匿名函数替代lambda表达式，这时匿名函数内部的return语句将从该匿名函数自身返回，输出679 return
  val ints = arrayOf(6, 7, 0, 9)
  fun each() {
      ints.forEach(fun(value: Int) {
          if (value == 0) return
          print(value)
      })
      print(" return")
  }
  ```
  
  ```kotlin
  // 当要返一个回值的时候，解析器优先选用标签限制的return，即
  return@a -1 // 意为"从标签a@返回-1"
  ```



- 「更方便的Kotlin**语法糖**」

  - 使用 **=** 连接<u>返回值</u>或简化函数体（单句）
  - 参数默认值（用于方法重载）

  ```kotlin
  fun sayHi(name: String = "World") = println("Hello, $name")
  ```

  ```kotlin
  fun main() {
      sayHi() // Hello, World
      sayHi("Shaun") // Hello, Shaun
  }
  ```

  - 命名参数

  ```kotlin
  fun sayHi(name: String = "World", age: Int) = println("Hello, $name. AGE: $age")
  ```

  ```kotlin
  fun main() {
      // [方法签名中可选参数之后还有其他非可选参数时，须使用命名参数向其他参数赋值]
      sayHi(20) // 错误
      sayHi(age = 20) // Hello, World. AGE: 20
      // [按位置参数正常调用]
      sayHi("Shaun", 20) // Hello, Shaun. AGE: 20
      // [调用函数时命名参数必须放在位置参数之后 或者 全部使用命名参数（可不按签名顺序）]
      sayHi(name = "Shaun", 20) // 错误
      sayHi(name = "Shaun", age = 20) // Hello, Shaun. AGE: 20
      sayHi(age = 20, name = "Shaun") // Hello, Shaun. AGE: 20
      sayHi("Shaun", age = 20) // Hello, Shaun. AGE: 20
  }
  ```

  

  - **本地函数（嵌套函数）**

    一个简单的登录函数示例：

  ```kotlin
  fun login(user: String, password: String, illegalStr: String) {
      // 验证 user 是否为空
      if (user.isEmpty()) {
          throw IllegalArgumentException(illegalStr)
      }
      // 验证 password 是否为空
      if (password.isEmpty()) {
          throw IllegalArgumentException(illegalStr)
      }
  }
  ```

  该函数中，检查参数这个部分有些冗余，若不想将这段逻辑作为一个单独的函数对外暴露，就可以使用嵌套函数，在 `login` 函数内部声明一个函数：

  ```kotlin
  fun login(user: String, password: String, illegalStr: String) {
      // 嵌套函数，仅限login函数内访问
      fun validate(value: String) {
        if (value.isEmpty()) {
            // 嵌套函数可以访问它外部的所有变量或常量，例如当前函数的参数与变量、类的属性、顶层声明等
            throw IllegalArgumentException(illegalStr)
        }
      }
      // 验证
      validate(user)
      validate(password)
  }
  ```

  <u>使用Lambda表达式及Kotlin内置函数</u> `require`

  ```kotlin
  fun login(user: String, password: String, illegalStr: String) {
      require(user.isNotEmpty()) { illegalStr }
      require(password.isNotEmpty()) { illegalStr }
  }
  ```





## 类和对象

- **类**

```kotlin
// 空类（无类体，可省略花括号）
class Empty
```

```kotlin
class Person {
    val name:String = "Shaun" // 只读类属性
    var age:Int = 20 // 可变类属性
}
fun main(){
    val person = Person() // Kotlin中没有new关键字
    person.age = 21 // 点 . 操作符
    println("${person.name} is ${person.age} year(s) old.")
}
```



- 修饰符
  - _classModifier_ 类属性修饰符
    - _abstract_      抽象类
    - _final_             不可继承类，默认属性
    - _open_            可继承类，类默认是final的
    - _data_             数据类
    - _enum_           枚举类
    - _annotation_  注解类
  - _accessModifier_ 访问权限修饰符
    - _private_      仅在同一个文件/类中可见（作为内部类时对外部类不可见 [Java可见]）
    - _protected_  同一个文件/类中或子类可见（private + 子类可见，注意与Java的区别）
    - _public_        全部可见
  - _internal_     同一个module内可见 [常见类似形式：Android Studio中的module]
  
  ***ps:*** Kotlin中变量默认是 **public** 的；函数如果不加可见性修饰符时，默认的可见范围和变量一样也是 **public** 的，但有一种例外情况，就是遇到 **override** 关键字时；类是 **public final** 的，省略不写。
  
- **getter** 和 **setter**

  - 属性声明的完整语法（<>中自定义，[]中可选）：

  ```kotlin
  var <propertyName>[:<PropertyType>] [= <property_initializer>]
      [<getter>]
      [<setter>]
  // eg:
  var allByDefault:Int? // 非法，property must be initialized or be abstract
  var initialized = 1 // 合法，类型为Int，默认实现了getter和setter
  val inferredType = 1 // 合法，类型为Int，默认实现getter，不允许设置setter（只读）
  ```
  
  ***ps:*** val所声明的只读变量，在取值的时候仍然可能被修改，这也是和Java中final不同的地方。
  
  
  
  - Kotlin中类不能有字段，提供了 _Backing Field_（后端变量）机制，备用字段使用 **field** 关键字声明，且只能用于属性访问器。
  
  ```kotlin
  class Person {
      var lastName = "Wang"
          get() = field.toUpperCase() // "field"后端变量
      var no = 100
          set(value) {
              field = if (value < 10) value else -1
          }
      var height = 172.5f
          private set // 即使是var变量也无法重新赋值，因为是private的setter
  }
  ```
  
  ```kotlin
  fun main() {
      var person = Person()
      person.lastName = "Wong"
      person.age = 0
      println("Mr.${person.lastName} is ${person.age} year(s) old, ${person.height} tall.")
  }
  ```
  
  **_ps:_** 关于 **field** 关键字（**Remember in Kotlin whenever you write foo.bar = value, it will be translated into a setter call instead of a PUTFIELD.**）也就是说，在 Kotlin 中，任何时候当你写出 <u>“一个变量后边加等号”</u> 这种形式时，比如我们定义 `var no: Int` 变量，当你写出 `no = ..` 这种形式时，这个等号都会被编译器翻译成调用 **setter** 方法；而同样的，在任何位置引用变量时，只要出现 **no** 变量的地方都会被编译器翻译成调用 **getter** 方法。因此，当在 **setter** 方法内出现 `no = ..` 时，相当于在 **setter** 方法中调用 **setter** 方法，形成递归，从而导致死循环。



- 非空属性必须在定义的时初始化，Kotlin提供了一种可以延迟初始化的方案，使用 **lateinit** 关键字描述属性。

```kotlin
class LateInitialize {
    lateinit var person:Person // 延迟初始化必须显式声明变量类型
    fun init() {
        person = Person()
    }
    fun show() {
        println("Mr.${person.lastName} is ${person.age} year(s) old.")
    }
}
fun main() {
    var latePerson = LateInitialize()
    latePerson.init()
    latePerson.show()
}
```

- Kotlin的类可以有一个 **主构造器**，以及 一或多个 **次构造器**，主构造器是类头部的一部分，位于类名称之后：

```kotlin
// 若主构造器在参数声明时加上val/var，就相当于在类中创建了该属性
class Person constructor(var firstName: String) {
    var first = firstName // 主构造器的参数可以在类主体定义的属性初始化代码中使用
    // 主构造器不能包含任何代码，初始化代码可以放在init代码段中，后于主构造器、先于次构造器执行
    init {
        println("$firstName Wong") // 主构造器的参数可以在初始化代码段中使用
    }
    /*
    次构造器需要声明constructor前缀，<直接>或<间接通过另一个次构造器>代理主构造器;
    同一个类中代理另一个构造器使用this关键字
    */
    constructor(firstName: String, lastName: String) : this(firstName) {
        println("Weight: $weight")
    }
}
```

```kotlin
// 若主构造器未被任何可见性修饰符和注解修饰，constructor关键字可缺省
class Person(var firstName:String, val lastName:String) {}
// 如下，constructor关键字必须保留
class Person private constructor(var firstName:String, val lastName:String) {}
```

- 若一个非抽象类没有声明（主或次）构造器，会默认产生一个public的无参构造器。若不想类有公共的构造器，就得显式声明一个空的主构造器。

```kotlin
class DontCreateMe private constructor() {}
```

- 构造器中的参数可以有默认值

```kotlin
class Person constructor(var name:String = "Shaun") // 参数类型不能省略
```

- 抽象类或抽象成员不需要显式声明 **open** 关键字

```kotlin
abstract class Base {
    abstract fun basePrint()
}
```

```kotlin
class Derived: Base() {
    override fun basePrint(){
        println("override abstract method")
    }
}
```



- 嵌套类

```kotlin
class Outer {
    private val exterior = "exterior value from nested class"
    class Nested {
        // 嵌套类可以引用外部类的私有成员变量，但是要先创建外部类的实例，不能直接引用
        var outer = Outer()
        fun nest() = outer.exterior
    }
}
```

```kotlin
fun main() {
    val nest = Outer.Nested().nest() // 外部类.嵌套类().嵌套类方法/属性
    println(nest)
}
```



- 内部类 [ 使用 **inner** 关键字修饰 ]

```kotlin
class Outer {
    private val exterior = "exterior value from inner class"
    var v = "成员属性"
    // 内部类会持有一个外部类对象的引用，所以内部类可以访问外部类的成员属性和函数
    inner class Inner {
        fun interior() = exterior
        fun test() {
            /*
            内部类可以直接通过 this@外部类名 的形式引用外部类的成员变量，无需额外创建外部类的实例；
            为了消除歧义，要访问来自外部作用域的this，可以使用this@label，
            @label是一个代指this来源的标签
            */
            var o = this@Outer
            println(o.v)
        }
    }
}
```

```kotlin
fun main() {
    // 要构造内部类的对象，必须先构造外部类的对象，而嵌套类不需要
    val outer = Outer().Inner().interior() // 外部类().内部类().内部类方法/属性
    println(outer)
    
    Outer().Inner().test()
}
```



- 匿名内部类

```kotlin
class Anonymous {
    var v = "member"
    fun setInterface(i: IAnonymous) {
        i.test()
    }
}

interface IAnonymous {
    fun test()
}
```

```kotlin
fun main() {
    var anonymous = Anonymous()
    /*
    使用对象表达式创建匿名内部类；
    object是Kotlin的关键字，要实现匿名内部类必须声明object关键字，不能随意替换其他单词；
    'object:'修饰的都是接口或者抽象类。
    */
    anonymous.setInterface(object: IAnonymous {
        override fun test() {
            println("anonymous inner class")
        }
    })
}
```



## 继承、接口

- 继承

  - Kotlin中所有类都继承自 **Any** 类，它是所有类的超类。
  - 若一个类要被继承，需要用 **open** 关键字修饰。
  - 子类继承父类时，不能有跟父类同名的变量，除非父类中该变量修饰为private，或者父类中该变量修饰为open并且在子类中使用override覆写。

- 构造函数

  - 子类有主构造函数，则基类必须在主构造函数中立即初始化。

  ```kotlin
  open class Person(var name: String, var age: Int) {}
  ```

  ```kotlin
  // 通过冒号 : 操作符立即初始化基类
  class Student(name:String, age:Int, var no:String, var score:Int): Person(name, age){}
  ```

  - **子类无主构造器**，则必须在每一个次构造器中用 **super** 关键字初始化基类，或者代理另一个次构造器。初始化基类时，可以调用基类的不同构造器。

  ```kotlin
  class Student: Person {// 子类无主构造器，Person后无需加()
      // super关键字初始化基类
      constructor(name:String, age:Int, no:String): super(name, age) {}
      // this关键字代理另一个次构造器（间接代理主构造器）
      constructor(name:String, age:Int, no:String, score:Int): this(name, age, no){}
  }
  ```
  
  ***ps:*** [Kotlin的面向对象编程，深入讨论继承(括号)写法的问题][Kotlin的面向对象编程，深入讨论继承写法的问题]。若父类具有默认的空主构造器，子类次构造器会隐式调用，可以缺省不写。



- 覆写

  - 未声明任何修饰符的fun函数/ 成员变量，默认是 **final** 的，不能被覆写。需要使用 **open** 修饰，才能被子类覆写，子类覆写的方法要用 **override** 修饰。
  - **override** 函数的可见性继承自父类。
  - 覆写基类变量时，不能用val覆写var变量（有点缩小范围的感觉，不能setter了），不能改变变量类型。
  
- 可以在主构造器中使用 **override** 关键字作为属性声明的一部分。
  
  ```kotlin
  open class Person {
      open var height:Float = 174.5f
      open fun study() {
          println("study..")
      }
  }
  ```
  
  ```kotlin
  class Student: Person() {
      override var height = 175.0f
      override fun study() {
          println("learn..")
      }
  }
  ```

  ***ps:*** 子类继承父类，子类仍然是final的，即 **open** 没有父类到子类的遗传性，而 **override** 是有遗传性的，要关闭其遗传性，只需修饰final即可 `final override fun ...`。



- 接口

  - 使用 **interface** 关键字定义，允许方法有默认实现；
  - 接口中的函数/ 属性默认是 open，abstract 的；
  - 接口中的属性不能有初始化值，实现接口时，必须覆写属性。

  ```kotlin
  interface ITest {
      var test: String
      fun bar()
      fun foo() {
          // 默认实现
          println("foo")
      }
  }
  ```

  ```kotlin
  class ConcreteImpl : ITest {
      override var test = "interface test"
      override fun bar() {
          println("bar")
      }
  }
  ```
  
  
  
  - 假设D类继承了A类，实现了B、C接口，它们都有同名的方法 `foo()`，则D只能覆写一个：
  
  ```kotlin
  class D: A(), B, C {
      override fun foo() {
          //此处使用super时要用<>指定具体调用A，B，C中谁的方法
          super<A>.foo()
          // super<B>.foo()
          // super<C>.foo()
      }
  }
  ```
  
  

## 扩展

- **扩展**
- Kotlin可以对一个类的属性和方法进行扩展，且不需要继承或Decorator模式。扩展是一种静态行为，不会影响类代码本身；
  - 扩展函数/属性通常定义在顶级包下，要使用所定义包之外的扩展，通过import导入扩展的函数名即可。

### 扩展函数

- 扩展函数可以在已有类中添加新的方法，不会对原类做修改，定义形式为：

  ```kotlin
fun receiverType.functionName (params) {
      body
  }
  ```
  
  - _receiverType_：函数接收者，也就是函数扩展的对象；
- _functionName_：扩展函数的名称；
  - _params_：扩展函数的参数，可为null。
  
  
  
- 实例

  - 扩展User类

  ```kotlin
  class User(var name: String) {
  }
  
  fun User.printLine() {
      println("用户名：$name")
  }
  ```

  ```kotlin
  fun main() {
      var user = User("Shaun")
      user.printLine()
  }
  ```

  - 为MutableList扩展一个swap函数（调换不同位置的值）

  ```kotlin
  fun MutableList<Int>.swap(index1: Int, index2: Int) {
      // this指接收者对象（调用扩展函数时，点号之前的对象实例）
      val temp = this[index1]
      this[index1] = this[index2]
      this[index2] = temp
  }
  ```

  ```kotlin
  fun main() {
      val l = mutableListOf(1, 2, 3)
      l.swap(0, 1) // 扩展函数'swap()'内部的'this'将指向'l'引用的实例
      println(l) // 输出[2, 1, 3]
  }
  ```

  

- 扩展函数是静态解析的，并不是接收者类型的虚拟成员。在调用扩展函数时，具体被调用的是哪一个函数，由 **调用函数的对象表达式** 决定，而不是动态的类型决定的。

```kotlin
open class C {} // 基类

class D: C() {} // 子类

fun C.foo() = "C" // C类扩展函数foo
fun D.foo() = "D" // D类同名扩展函数foo
```

```kotlin
fun printFoo(c: C) {
    println(c.foo()) // 即使传入D类型实例，实际调用的也是C类的扩展函数foo
}

fun main() {
    printFoo(D()) // 实际输出C
}
```



- 若扩展函数和成员函数完全一致，调用时成员函数优先。

```kotlin
class C {
    fun foo() {
        println("成员函数")
    }
}

fun C.foo(){
    println("扩展函数")
}
```

```kotlin
fun main() {
    var c = C()
    c.foo() // 输出结果：成员函数
}
```



- 扩展一个空对象

  在扩展函数内，可以通过 **this** 来判断接收者是否为null，这样，即使接收者为空也可以调用扩展函数。

  ```kotlin
  fun Any?.toString(): String {
      if (this == null) return "null"
      /*
      空检测之后，'this'会自动转换为非空类型（此处为Any类型）;
      因此下面的toString()解析为Any类的成员函数;
      return this.toString()，'this'可省略
      */
      return toString()
  }
  ```

  ```kotlin
  fun main() {
      var n = null
      println(n.toString())
  }
  ```

  

### 扩展属性

- 扩展属性只允许定义在类或Kotlin文件中；
- 扩展属性只能被声明为 val；
- 扩展属性不能被初始化，因为它没有backing field（只能由显式提供的 getter 定义）

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

```kotlin
fun main() {
    val list = arrayListOf(1, 2, 3)
    println(list.lastIndex)
}
```



### 扩展伴生对象

- 伴生对象内的成员相当于Java中的静态成员，其生命周期伴随类始终，在伴生对象内部可以定义变量和函数，可以通过 **类名.** 的形式直接调用。

- 伴生对象声明的扩展函数，通过类名限定符调用，有两种形式：类内扩展、类外扩展。

  特点：

  - 两种形式的扩展函数可以重名，但相互独立；

  - 当两种函数同名时，类内的其他函数（非伴生对象内的函数）优先引用类内的伴生对象扩展函数；

    （==？？能不能引用类外的呢，how==）

  - 类内的伴生对象扩展函数只能被类内的函数（非伴生对象内的函数）引用；

  - 类外的伴生对象扩展函数可以被伴生对象内的函数引用。（==？？疑问同第2点==）

```kotlin
class Company {
    companion object {
        var obj = "companion object variable"
        fun foo() {
            println("companion object method")
            Company.test() // （优先？只能？）引用类外的伴生对象扩展函数
        }
    }
    // 类内的伴生对象扩展函数（类名.可以省略）
    fun Company.Companion.test() {
        println("companion object extension method in class")
    }
    // 类内的非伴生对象函数
    fun call() {
        Company.test() // （优先？只能？）引用类内的伴生对象扩展函数
    }
}
// 类外的伴生对象扩展函数
fun Company.Companion.test() {
    println("companion object extension method out of class")
}
```

```kotlin
fun main() {
    println(Company.obj) // 调用伴生对象中的变量
    Company.foo() // 调用伴生对象中的方法
    Company.test() // 调用伴生对象的扩展函数
    Company.call() // 通过类内的非伴生对象函数调用类内的伴生对象扩展函数
}
```



### 将扩展声明为成员

- 在一个类内部可以为另一个类声明扩展。在这个扩展中，有多个隐含的接受者，其中扩展方法定义所在的类实例称为分发接受者，扩展方法的目标类型的实例称为扩展接受者；

- 若一个函数 `show()` 在分发接受者和扩展接受者内都存在，默认扩展接受者优先，要引用分发接受者的成员需要使用限定的this语法。

```kotlin
class ExtendReceiver {// 扩展接受者
    fun test() {
        println("extension receiver")
    }
    fun show() {// 同名函数
        println("show method from extension receiver")
    }
}
```

```kotlin
class DistributeReceiver {// 分发接受者
    fun text() {
        println("distribute receiver")
    }
    fun show() {
        println("show method from distribute receiver")
    }

    fun ExtendReceiver.foo() {
        test()
        text() // 在扩展函数内可以调用分发接受者的成员函数
        show() // 默认优先调用扩展接受者的函数
        this@DistributeReceiver.show() // 使用this限定符调用分发接受者的函数
    }

    fun call(e: ExtendReceiver) {
        e.foo()
    }
}
```

```kotlin
fun main() {
    var distributor = DistributeReceiver()
    var extension = ExtendReceiver()
    distributor.call(extension)
}
```



- 以成员的形式定义的扩展函数，可以声明为open，即可以在子类中覆写；也就是说，在这类扩展函数的派发过程中，对分发接受者是虚拟的，对扩展接受者仍然是静态的。

```kotlin
open class D {}

class D1: D() {}
```

```kotlin
open class C {
    open fun D.foo() {
        println("D.foo in C")
    }
    open fun D1.foo() {
        println("D1.foo in C")
    }

    fun call(d: D) {// 扩展接受者解析类型只取决于此处参数声明的类型
        d.foo()
    }
}
```

```kotlin
class C1 : C() {
    override fun D.foo() {
        println("D.foo in C1")
    }
    override fun D1.foo() {
        println("D1.foo in C1")
    }
}
```

```kotlin
fun main() {
    C().call(D()) // 输出'D.foo in C'
    C1().call(D()) // 输出'D.foo in C1' —— 分发接受者虚拟解析
    C().call(D1()) // 输出'D.foo in C' —— 扩展接受者静态解析
}
```



## 数据类、密封类、枚举类

- 数据类：只包含数据的类，关键字为 **data**，如 `data class User(val name: String, val age: Int)`

  编译器会自动从主构造器中根据声明的属性提供以下函数：

  - `equals()/hashCode()`
  - `toString()` // 格式为 `User(name=Shaun, age=20)`
  - `componentN()` // 数据类对应的属性，按声明顺序排列，如 `user.component1()` 值为 `Shaun`
  - `copy()`

  **_ps:_** 若这些函数在数据类中已经被明确定义或者从超类中继承而来，就不会再额外生成。

  

  为了保证生成的代码的一致性且有意义，数据类要满足以下条件：

  - 主构造器至少包含一个参数；
  - 所有的主构造器参数必须标识为 `val` 或 `var`；
  - 数据类不能声明为 _abstract_，_open_，_sealed_，_inner_；
  - 数据类可以实现接口，从 _1.1_ 版本开始还能够继承类。

  

- `copy()` 函数可以复制对象并修改属性

```kotlin
data class User(val name: String, val age: Int) {}
```

```kotlin
fun main() {
    val jack = User("Jack", 20)
    println(jack) // 输出 User(name=Jack, age=20)
    val littleJack = jack.copy(age = 7)
    println(littleJack) // 输出 User(name=Jack, age=7)
    
    println(jack.component1()) // 输出 Jack
    println(jack.component2()) // 输出 20
    println(jack.component3()) // 非法，User数据类主构造器只有两个参数
}
```

- 数据类以及解构声明 [组件函数允许数据类在解构声明中使用]

```kotlin
fun main() {
    val shaun = User("Shaun", 20)
    val(n, a) = shaun
    println("$n, $a years old.") // 输出 Shaun, 20 years old.
}
```

- Kotlin提供了标椎数据类 `Pair` 和 `Triple` 。但大多数情形中，自定义数据类是更好的设计选择，通过指定有意义的名字和属性，提高代码可读性。

```kotlin
fun main() {
    /*
    '<>'可省略；
    Pair同样也提供数据类的默认方法，如println(pair.component1())输出Shaun
    */
    val pair = Pair<String, Int>("Shaun", 20)
    println(pair) // 输出 (Shaun, 20)
    
    val triple = Triple<String, Int, Float>("Shaun", 20, 59.0f)
    println(triple) // 输出 (Shaun, 20, 59.0)
}
```



- 密封类用来表示受限的类继承结构：值 为有限的几种类型，而不能有任何其他类型。在某种意义上，它们是枚举类型的扩展：枚举类型的值集合也是受限的，但每个枚举常量只存在一个实例，而密封类的一个子类可以有包含状态的多个实例。
- 声明一个密封类，使用 `sealed` 关键字修饰类，密封类可以有子类。从 _1.1_ 版本开始，可以在同一个文件中的任何地方定义一个密封类的子类，而不只是以作为密封类嵌套类的方式。
- `sealed` 不能修饰 _interface_，_abstract class_ 。

```kotlin
sealed class Expr

data class Const(val number: Double) : Expr() {}
data class Sum(val e1: Expr, val e2: Expr) : Expr() {}
object NotANumber : Expr()

/*
使用when表达式时，若 验证语句 覆盖了密封类的所有情况，else子句可以省略；
若when表达式的分支语句无需携带额外信息，用object关键字创建单例就行了，此时验证语句可以省略 is 关键字；
只有需要携带额外信息时才定义密封类的子类，且每增加一个子类或单例，编译器就会在when语句中给出提示。
*/
fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number // 子类验证要写 is 关键字
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
```

```kotlin
fun main() {
    val e = eval(Sum(Const(1.0), Const(2.0)))
    println(e)
}
```



- 枚举类最基本的用法是实现一个类型安全的枚举。枚举常量用逗号分隔，每个枚举常量都是一个对象。

```kotlin
enum class Color { RED, GREEN, BLUE }
```

- 每个枚举都是枚举类的实例，它们可以被初始化。

```kotlin
enum class Color(val rgb: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}
```



## 泛型

- 泛型，即“参数化类型”，将类型参数化，用于类、接口、方法上。保证类型安全，消除类型强转的烦恼。

```kotlin
class Box<T>(t: T) {
    var value = t
}
```

- 定义泛型变量，若编译器可以自动推断类型参数，可以省略声明类型参数。

```kotlin
fun main() {
    val iBox: Box<Int> = Box<Int>(1)
    val sBox = Box("Generics")
    println(iBox.value) // 输出 1
    println(sBox.value) // 输出 Generics
}
```

- 泛型函数的声明与Java相似，类型参数要放在函数名称前。调用时，若可以推断出类型参数，也可以省略。

```kotlin
fun <T> doPrintln(content: T) = when (content) {
    is Int -> println("Int: $content")
    is String -> println("String: $content")
    else -> println("neither")
}
```

- 泛型约束可以设定一个给定参数允许使用的类型。Kotlin中使用对泛型的类型上限进行约束。最常见的约束是上界（默认的上界是 `Any?`）：

```kotlin
fun <T : Comparable<T>> sort(list: List<T>) {} // Comparable的子类可以替换T
```

- 对于多个上界约束条件，可以使用 **`where`** 子句（类似Java的 `<T extends A & B>`）：

```kotlin
fun <T> copyWhenGreater(list: List<T>, threshold: T): List<String>
        where T : CharSequence, T : Comparable<T> {
    // 'it' Automatically declared based on the expected type
    return list.filter { it > threshold }.map { it.toString() }
}
```



- **`reified`** 关键字

  使用泛型时，无法检查一个对象是否为泛型类型T的实例，针对该问题，可以像Java一样，额外传递一个 `Class<T>` 类型的参数，然后通过它的 `isInstance()` 方法来检查。不过Kotlin提供更简便的方式，使用关键字 **`reified`** 配合 **`inline`** 来解决：

```kotlin
inline fun <reified T> check(item: Any) {
    if(item is T) {
        println(item)
    }
}
```



- Kotlin泛型与Java的区别
  - Java的数组是支持协变的，而Kotlin的数组Array不支持协变。这是因为在Kotlin中数组是用Array类来表示的，这个Array类使用泛型就和集合类一样，所以不支持协变。
  - Java中的List接口不支持协变，而Kotlin中的List接口支持协变。由于类型擦除，需要使用泛型通配符来解决。实际上在Kotlin中MutableList接口才相当于Java中的List，List接口只实现了只读操作，没有写操作，所以不会发生类型安全问题，自然可以支持协变。



### 型变、星号投射

- Kotlin中没有通配符类型，只有两个其他的东西：**声明处型变** 与 **类型投影**。
- 声明处的类型变异使用协变注解修饰符 **in** 、**out**，消费者 _in_，生产者 _out_（与C#类似）。
  - 使用关键字 `out` 来支持协变（子类协变父类），等价于Java中的上界通配符 `? extends`；
  - 使用关键字 `in` 来支持逆变（父类逆变子类），等价于Java中的下界通配符 `? super`。
  
  ***ps:*** 在Java中 **PECS**「*Producer-Extends, Consumer-Super*」; Kotlin中相应的类似「*Producer-Out, Consumer-In*」
- `out` 协变类型参数只能用作输出，可以作为返回值类型但无法作为入参类型：

```kotlin
class Source<out T>(val a: T) {
    fun foo(): T {
        return a
    }
}
```



- `in` 逆变类型参数只能用作输入，可以作为入参类型但无法作为返回值类型：

```kotlin
class Destination<in T>(t: T) {
    fun foo(t: T) {}
}
```



- 生产者-消费者 示例

```kotlin
class Producer<T> {
    fun produce(): T {}
}
```

```kotlin
val producer: Producer<out TextView> = Producer<Button>() // 注意out
val textView: TextView = producer.produce() // 相当于List的get
```

```kotlin
class Consumer<T> {
    fun consume(t: T) {}
}
```

```kotlin
val consumer: Consumer<in Button> = Consumer<TextView>() // 注意in
consumer.consume(Button(context)) // 相当于List的add
```



- 声明处的 **out** 和 **in**

  在上面的例子中，声明 `Producer` 只用作输出，`Consumer` 只用作输入，但是每次都要显式声明out、in关键字来支持型变。Kotlin提供了另一种写法来简化这种情况：在声明类时就给泛型符号加上out、in，表明泛型参数的用途，这样在使用时就不用额外修饰了。

```kotlin
class Producer<out T> {
    fun produce(): T {}
}
```

```kotlin
val producer: Producer<TextView> = Producer<Button>() // 注意这里可以省略out
```

```kotlin
class Consumer<in T> {
    fun consume(t: T) {}
}
```

```kotlin
val consumer: Consumer<Button> = Consumer<TextView>() // 注意这里可以省略in
```



- ***** 星号投射，`var list: List<*>`，等价于Java中的泛型通配符**？**（*? extends Object*），相当于 `out Any`

  > 有时，我们并不知道类型参数的任何信息，但仍然希望能够安全地使用它。”安全地使用“指，对泛型类型定义一个类型投射，要求这个泛型类型的所有实体实例，都是该投射的子类型。Kotlin这对此问题提供了一种语法，称为_星号投射_。（注意：与Java原生类型（raw type）非常相似，但可以安全使用）

  - 假设类型定义为`Foo<out T>`，其中T是一个协变的类型参数，上界为TUpper，`Foo<>` 等价于 `Foo<out TUpper>`，它表示，当T未知时，可以安全地从 `Foo<>` 中读取TUpper类型的值；
  - 假设类型定义为 `Foo<in T>`，其中T是一个逆变的类型参数，`Foo<>` 等价于 `Foo<in Nothing>`，它表示，当T未知时，不能安全地向 `Foo<>` 写入任何东西；
  - 假设类型定义为 `Foo<T>`，其中T是一个协变的类型参数，上界为TUpper，对于读取值的场合，`Foo<*>` 等价于 `Foo<out TUpper>`，对于写入值的场合，等价于 `Foo<in Nothing>`。



- 若一个泛型类型中存在多个类型参数，那么每个类型参数都可以单独投射。如：

  若类型定义为 `interface Function<in T, out U>`，那么可以出现以下几种星号投射：

  - `Function<*, String>`，代表 `Function<in Nothing, out String>`；
  - `Function<Int, *>`，代表 `Function<Int, out Any?>`；
  - `Function<,>`，代表 `Function<in Nothing, out Any?>`。

  **_ps:_** 星号 ***** 代指所有类型，相当于 **Any?**



- 和Java不同的是，若类型定义中同时存在 out或in 和 \*，那么类型的定义范围会缩小。比如`out T: Number`加上 `<*>` 之后的效果就不是 `out Any`，而是 `out Number` 了。



## 对象表达式、对象声明

Kotlin用对象表达式和对象声明来实现创建一个对某个类做了轻微改动的类的对象，且不需要去声明一个新的子类。

- **对象表达式** `var expression = object {}` 或 `var expression = object: A(1),B {}`

  - 对象可以继承于某个基类，或者实现其他接口，若基类有构造函数，则必须传参给它：

  ```kotlin
  open class A(x: Int) {
      open val y: Int = x
  }
  ```

  ```kotlin
  interface B {}
  ```

  ```kotlin
  fun main() {
      val ab = object : A(1), B {
          override val y = 15
      }
      println(ab.y) // 输出 15
  }
  ```

  - 通过对象表达式可以越过类的定义直接得到一个对象：

  ```kotlin
  fun main() {
      val site = object {
          var name = "Google"
          var url = "www.google.com"
      }
      println("${site.name} https://${site.url}") // Google https://www.google.com
  }
  ```

  - 匿名对象可以用作只在 <u>本地</u> 和 <u>私有作用域</u> 中声明的类型。若 <u>使用匿名对象作为公有函数返回类型或用作公有属性的类型</u>，则该函数或属性的 <u>实际类型会是匿名对象声明的超类型</u>，如果没有声明任何超类型，就会是 <u>Any类型</u>，在匿名对象中添加的成员将无法访问。

  ```kotlin
  // 私有函数，所以其返回类型是匿名对象类型
  private fun foo() = object {
      val x = "x"
  }
  ```

  ```kotlin
  // 公有函数，未声明超类型，所以其返回类型是Any，变量x将无法访问
  fun publicFoo() = object {
      val x = "x"
  }
  // 声明一个超类型A，所以其返回类型是A，假设A类中有个成员变量y，则能访问到y
  fun publicFoo() = object: A() {
      val x = "x"
  }
  ```

  **_todo:_** ==所以如何访问到 x？？==

  ```kotlin
  fun main() {
      val x1 = foo().x
      val x2 = publicFoo().x // 报错，Unresolved reference: x
      val y = publicFoo().y
  }
  ```

  

  - 在对象表达式中可以访问到作用域中的其他变量。

  ```kotlin
  fun main() {
      var i = 0
    val count = object {
          var count = ++i
      }
      println(count.count) // 1
  }
  ```
  
  

- **对象声明** `object Xxx {}` 或 `object Xxx: A() {}`

  Kotlin中可以使用 **object** 关键字声明一个对象。

  - 对象声明不能用作局部变量；
  
- 对象声明可以有超类型；
  - 通过对象声明获得一个单例（饿汉式且线程安全）。

  ```kotlin
  // 单例
  object Site {
      var url = ""
  }
  ```

  ```kotlin
  fun main() {
      var s1 = Site
      var s2 = Site
      s1.url = "www.google.com"
      println(s1.url) // www.google.com
      println(s2.url) // www.google.com
      println(Site.url) // 引用对象时，可以直接使用其名称，同样输出 www.google.com
  }
  ```

  - ==与对象表达式不同（？？）==，当对象声明在另一个类的内部时，并不能通过外部类的实例访问到该对象，而只能通过类名来访问，同样该对象也不能直接访问到外部类的方法和变量。
  
  ```kotlin
  class Site {
      var name = "Google"
      object Desktop {
          var url = "www.google.com"
          fun showName() {
              println("Site name: $name") // 错误，不能访问到外部类的方法和变量
          }
      }
  }
  ```
  
  ```kotlin
  fun main() {
      var site = Site()
      site.Desktop.url // 错误，不能通过外部类的实例访问到该对象
      Site.Desktop.url // 正确
  }
  ```
  
  

- **伴生对象**

  - 使用 **companion** 关键字标记类内部的对象声明能使其与外部类关联在一起，从而直接通过外部类名访问该对象的内部元素。
  - **companion只能在一个类中使用一次，即类只允许关联一个伴生对象**。

  ```kotlin
  interface Factory<T> {
      fun create(): T
  }
  ```

  ```kotlin
  class CompanionClazz {
      var obj = "companion object test"
      // 未显式指定伴生对象名称时，默认为隐式的Companion
      companion object: Factory<CompanionClazz> {
          override fun create(): CompanionClazz = CompanionClazz()
      }
  }
  ```

  ```kotlin
  fun main() {
      /*
      调用伴生对象时，通常省略伴生对象的名称;
      即此处完整的写法为 val instance = CompanionClazz.Companion.create()
      */
      val instance = CompanionClazz.create()
      println(instance.obj) // companion object test
  }
  ```

  

  - 静态初始化

  ```kotlin
  class Sample {
      companion object {
          init {}
      }
  }
  ```

  

- top-level property/function声明

  除了静态函数这种简便的调用方式，Kotlin还可以通过顶层声明（top-level declaration）把属性和函数声明在class外。和静态变量、函数一样是<u>全局</u>的。

  ```kotlin
  package al.app.kotlina
  // 顶层函数属于package，不在class/object内
  fun topLevelFunction() {}
  ```

  

- 在实际使用中，`object`、`companion object` 和 top-level 如何选择：
  - 如果想写 **工具类** 的功能，直接创建文件编写 top-level 顶层函数；
  - 如果<u>需要继承别的类或者实现接口</u>，就用 `object` 或者 `companion object`。



- 对象表达式和对象声明之间的语义差别
  - 对象表达式是在使用他们的地方立即执行的；
  - 对象声明是在第一次被访问到时延迟初始化的；
  - 伴生对象的初始化是在相应的类被加载（解析）时，与Java静态初始化器的语义相匹配。



- **常量**

  - **const** 关键字
  - 必须声明在对象（包括伴生对象）或者 top-level declaration 中，因为常量是静态的
  - <u>只有基本类型和String类型可以声明为常量</u>

  ***ps:*** Kotlin中的常量指的是 *compile-time constant（编译时常量）*，意思是编译器在编译时就知道该变量在每个调用处的实际值，因此可以在编译时就直接把这个值硬编码到代码里使用的地方；而非基本和String类型的变量，可以通过调用对象的方式或变量改变对象内部的值，这样就不是常量了。

  ```kotlin
  class Sample {
      companion object {
          const val CONST_IN_OBJECT = 1
      }
  }
  ```

  或

  ```kotlin
  const val CONST_TOP_LV = 2
  ```

  

## 委托

Kotlin通过 **by** 关键字实现委托。

### 类委托

类的委托即一个类中定义的方法实际上是调用另一个类的对象的方法来实现的。

```kotlin
interface Base {
    fun print()
}
```

```kotlin
// 实现Base接口的被委托类
class BaseImpl(val x: Int) : Base {
    override fun print() {
        println(x)
    }
}
```

```kotlin
/*
派生类Derived继承了Base接口的所有方法，并且委托一个传入的Base类对象来执行这些方法；
在Derived声明中，by字句表示，将b保存在Derived的对象实例内部，
而且编译器会生成继承自Base接口的所有方法，并将调用转发给b。
*/
class Derived(b: Base) : Base by b
```

```kotlin
fun main() {
    val b = BaseImpl(10)
    Derived(b).print() // 10
}
```

***ps:*** 当多个接口委托具有相同成员函数时，则需自行覆写这些函数，其余函数仍会应用委托。



### 属性委托

- 属性委托指的是一个类的某个属性值不是在类中直接进行定义，而是将其托付给一个代理类，从而实现对该类属性的统一管理。

- 属性委托语法格式：`val/var <属性名>: <类型> by <委托代理类表达式>`

  **_ps:_** _by_ 关键字之后的表达式就是委托，属性的 `get()/set()` 方法将被委托给这个对象的 `getValue()/setValue()` 函数。属性委托不必实现任何接口，但必须提供 `getValue()` 函数（对于var属性，还需要 `setValue()` 函数，且必须使用 **operator** 修饰）。

```kotlin
class Example {
    // var属性，Delegate类需要提供getValue()及setValue()函数
    var p: String by Delegate()
}
```

```kotlin
class Delegate {
    /*
    getValue函数至少包含以下2个参数：
    参数thisRef为进行委托的类的对象（此处为Example类）；
    参数property为进行委托的属性的对象（此处为p变量）。
    */
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef,这里委托了${property.name}属性"
    }
    /*
    setValue函数至少包含以下3个参数：
    参数thisRef为进行委托的类的对象（此处为Example类）；
    参数property为进行委托的属性的对象（此处为p变量）；
    参数value为要赋的值（类型与getValue返回值对应 ?）。
    */
    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$thisRef的${property.name}属性赋值为$value")
    }
}
```

```kotlin
fun main() {
    val e = Example()
    e.p = "Shaun" // 调用setValue()函数，输出 xxx.Example@xxx的p属性赋值为Shaun
    println(e.p) // 访问该属性，调用getValue()函数，输出 xxx.Example@xxx，这里委托了p属性
}
```



### 标准委托

Kotlin的标准库中内置了很多工厂方法来实现属性的委托。

- 延迟属性 **Lazy**　`public actual fun <T> lazy(initializer: () -> T): Lazy<T> = ..`

  `lazy()` 函数接受一个Lambda表达式作为参数，返回一个 `Lazy<T>` 实例，返回的实例可以作为实现延迟属性的委托：首次调用 `get()` 会执行已传递给 `lazy()` 的Lambda表达式并记录结果，后续调用只会返回记录的结果。

  ```kotlin
  fun main() {
      val lazyValue: String by lazy {
          println("lazy")
          "Hello" // 此处还可写为 return@lazy "Hello"
      }
      println(lazyValue) // 此处会先执行Lambda表达式中的语句（打印lazy），最后打印返回值Hello
      println(lazyValue) // 再次执行只会输出返回值Hello
  }
  ```

  

- 可观察属性 **Observable**





- **Not Null**





- 把属性储存在映射中





- 局部委托属性





- 属性委托要求





- 翻译规则





### 提供委托







## 协程 ^new^

> 协程(Coroutines)是什么 ~> 一个<u>线程框架</u>
>
> 协程并非Kotlin提出来的新概念，官方文档称其本质上是轻量级的线程。协程设计的初衷是为了解决并发问题，让<u>协作式多任务</u>实现起来更加方便。作为Kotlin协程的初学者，当讨论到协程和线程的关系时，很容易陷入中文的误区，两者虽然都有一个“程”字，但其实是两个概念。从Android开发者的角度去理解它们的关系：
>
> - 所有的代码都是跑在线程中的，而线程是跑在进程中的。
> - 协程没有直接和操作系统关联，但它也是跑在线程中的，可以是单线程，也可以是多线程。
> - ==单线程中的协程总的执行时间并不会比不用协程少。？？==
> - Android系统中，如果在主线程进行网络请求，会抛出 `NetworkOnMainThreadException`，对于在主线程上的协程也不例外，这种场景使用协程还是要切线程的。

[协程指南][协程指南|Kotlin中文站]





## [作用域函数](https://www.kotlincn.net/docs/reference/scope-functions.html)



|  函数   | 对象引用 |         返回值         | 是否为扩展函数           |
| :-----: | :------: | :--------------------: | :----------------------- |
|  `let`  |  **it**  |    Lambda表达式结果    | 是                       |
|  `run`  | **this** |    Lambda表达式结果    | 是                       |
|  `run`  |    ─     |    Lambda表达式结果    | 否：调用无需上下文对象   |
| `with`  | **this** |    Lambda表达式结果    | 否：把上下文对象当做参数 |
| `apply` | **this** | 上下文对象（Receiver） | 是                       |
| `also`  |  **it**  | 上下文对象（Receiver） | 是                       |



- `takeIf`
- `takeUnless`





# BEST PRACTICE





# APPENDIX

## QUICK NOTES





## ERROR ANALYSIS





## LINKS

[Kotlin的面向对象编程，深入讨论继承写法的问题]:https://blog.csdn.net/guolin_blog/article/details/89765754
[协程指南|Kotlin中文站]: https://www.kotlincn.net/docs/reference/coroutines/coroutines-guide.html
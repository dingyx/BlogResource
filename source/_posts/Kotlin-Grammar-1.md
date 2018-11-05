---
layout: post
title: Kotlin 语法 第一章
date: 2018-10-16 20:20:15
tags: Kotlin 语法 函数 变量 
reward: false
---


#### 基础语法

* 函数的定义

  > 使用关键字 fun，参数格式 参数： 类型

  ```kotlin
  fun sum(a: Int, b: Int): Int {
      return a + b
  }
  ```

  > 表达式作为函数体，返回类型自动判断

  ```kotlin
  fun sum(a: Int, b: Int): Int = a + b
  // 省略后如下
  fun sum(a: Int, b: Int) = a + b
  ```

  > 无返回值的函数 Unit返回类型可以省略

  ```kotlin
  fun printLog(info : String): Unit{
      println(info)
  }
  // 省略后如下
  fun printLog(info : String){
      println(info)
  }
  ```

  * 可变长参数函数-使用关键字vararg

  ```kotlin
  fun vars(vararg v: Int) {
      for (vt in v)
      	print(vt)
  }

  // 如下调用输出 123456
  vars(1,2,3,4,5,6)
  ```

  * lambda(匿名函数)

  ```kotlin
  val add: (Int, Int) -> Int = { x, y -> x + y }
  // 如下调用 返回结果3
  add(1,2)
  ```



* 定义常量与变量

  * 可变变量定义 var关键字

    > var <标识符> ： <类型> = <初始值>

    ```kotlin
    var str : String = "Java"
    // 编译器支持自动类型判断 声明时可以不指定类型 由编译器判断。
    var str = "Kotlin"
    str = "Android"
    ```


  * 不可变变量(常量)定义 val关键字 智能赋值一次的变量(类似java中final修饰)

    > val <标识符> :  <类型> = <初始化值>

    ```kotlin
    val a : Int = 1
    // 编译器支持自动类型判断 声明时可以不指定类型 由编译器判断。
    val b = 2
    // 如果不在声明时初始化则必须提供变量类型
    val c : Int
    c = 3
    ```

    注：变量与常量都可以没有初始化，但在引用前必须初始化



* 注释

  > Kotlin与Java一样支持单行与多行注释

  ```kotlin
  // 单行注释

  /* 多行
     注释 */
  ```



* 字符串模版

  * $ 使用变量名作为模版

  ```kotlin
  var a = 1

  val str = "a is $a"	// "a is 1"
  ```

  * ${....} 使用表达式作为模版

  ```kotlin
  var a = 1
  val s = "a is $a"
  a = 2
  // str 表示 a was 1,but now is 2
  val str = "${s.replace("is","was")},but now is $a"
  ```



* NULL 检查机制 --- 使用可空变量以及空值检查

  > Kotlin 当空值可能出现时应该明确指出该引用可空
  >
  > 当参数声明可为空时，使用时要进行空判断处理

  ```kotlin
  // 类型后面加？ 表示可为空
  var age: String? = "23"
  // age声明可为空 使用时字段后加 !! 和java一样抛异常
  val ages1 = age!!.toInt()
  // 使用时字段后加 ? 不做处理返回值为null
  val ages2 = age?.toInt()
  // 使用字段后加 ? 再配合 ?: 做空判断处理
  val age3: Int = age?.toInt() ?: -1
  ```



* 类型检测及自动类型转换

  > 使用 is 运算符检测一个表达式是否是某个类型实例   
  >
  > 如果对不可变的局部变量或属性进行过了类型检查，就没必要明确转换

  ```kotlin
  fun getStringLength(obj: Any): Int?{
      if(obj is String){
          // Smart cast to kotlin.String
          return obj.length
      }
      // 这里的 obj 仍然是 Any 类型的引用
      return null
  }

  // 也可以写成
  fun getStringLength(obj: Any): Int?{
      if(obj !is String)
      return null
      // 在这个分支中 'obj' 会自动转换为 'String'
      return obj.length
  }

  // 或者写成
  fun getStringLength(obj: Any): Int? {
      // 在 && 运算符右侧  obj 的类型会被自动转换为 String
      if (obj is String && obj.length > 0)
      return obj.length
      return null
  }
  ```



* 区间

  > 区间表达式具有操作符的形式 ... 的 rangeTo 函数辅以 in 和 !in 形成
  >
  > 区间是为任何可比较类型定义的，但对于整型原生类型。它有一个优化的实现

  ```kotlin
  // 输出 12345
  for (i in 1..5)
  	print(i)

  // 没有输出
  for (i in 5..1)
  	print(i)

  // 输出 135
  for (i in 1..5 step 2)
  	print(i)

  // 输出 531
  for(i in 5 downTo 1 step 2)	
  	print(i)

  // 输出 123456789
  for (i in 1 until 10)	
  	print(i)
  ```





#### 基本类型

> Kotlin 所有变量的成员方法和属性都是一个对象。一些类型是内建的，它们的实现是优化过的，使用时就像普通的类一样。以下介绍会到大多数类型：数值、字符、布尔及数组

* 数字

  > Kotlin 提供了如下的内置类型来表示数字

  |  Type  |  字节  |
  | :----: | :--: |
  |  Byte  |  8   |
  | Short  |  16  |
  |  Int   |  32  |
  |  Long  |  64  |
  | Float  |  32  |
  | Double |  64  |

  * 字面常量

    * 字面常量的表示：

      十进制:123    长整型:123L    16进制:0x0F    2进制:0b00001011

      浮点型  double:123.6 , 123.5e10    float:123.6f

      > 为了使数字常量更易读，可以使用下划线分隔

      ```kotlin
      val a = 1_000_000
      val b = 123_456_789_000L
      val c = 0xFF_EC_DE_5C
      val d = 0b11010010_01101001_10010100_10010010
      ```

    * 数字大小比较

      Kotlin没有基础数据类型，只有封装的数字类型。定义一个变量时，其实是Kotlin封装了一个对象。

      三个等号 === 表示比较对象地址，两个等于号 == 表示比较值

      ```kotlin
      val a: Int = 10000
      println(a === a) // 输出“true”
      val boxedA: Int? = a
      val anotherBoxedA: Int? = a
      println(boxedA == anotherBoxedA)  // 值相同 输出true
      println(boxedA === anotherBoxedA) // 对象地址不同 输出false
      ```

    * 类型转换

      由于不同的表达方式，较小的类型不是较大类型的子类。不进行显示转换的话，不能将Byte型值赋给Int变量

      ```kotlin
      val a : Byte = 1
      val b : Int = a			// error---Type mismatch
      val c : Int = a.toInt()	// OK
      ```

      > 每种数据离线都有对应方法转换为其他类型：toByte()，toShort()，toInt()，toLong()，toFloat()，toDouble()，toChar()

      算术运算时会有重载做适当转换

      ```
      val a = 1L + 2	// Long + Int -> Long
      ```

      ​
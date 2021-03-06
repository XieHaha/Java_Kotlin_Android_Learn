## 一、Object 关键字

### 1.1、对象声明

在 Kotlin 的世界中，可以通过**对象声明**这一功能来实现 Java 中的单例模式，将类声明与该类的单一实例声明结合到一起

与类一样，一个对象声明可以包含属性、方法、初始化语句块等的声明，唯一不被允许的是构造方法。与普通类的实例不同，对象声明在定义的时候就被立即创建了，不需要在代码的其它地方调用构造方法，因此为对象声明定义构造方法是没有意义的

```kotlin
object SingleClass {
    val names = arrayListOf<String>()
}

fun main(args: Array<String>) {
    SingleClass.names.add("leavesC")
    for (name in SingleClass.names) {
        println(name)
    }
}
```

对象声明同样可以继承类和接口

```kotlin
open class SuperClass {

}

object SingleClass : SuperClass(), Runnable {

    override fun run() {

    }

    val names = arrayListOf<String>()
    
}
```

Kotlin 中的对象声明被编译成了通过静态字段来持有它的单一实例的类，这个字段名字始终都是 INSTANCE

例如，对于 Kotlin 中的如下两个对象声明

```kotlin
class Test {

    object SingleClass {
        val names = arrayListOf<String>()
    }

    object SingleClass2 {
        val names = arrayListOf<String>()
    }

}
```

在 Java 代码中来访问这两个对象

```java
    public static void main(String[] args) {
        Test.SingleClass.INSTANCE.getNames();
        Test.SingleClass2.INSTANCE.getNames();
    }
```

### 1.2、伴生对象

如果需要一个可以在没有类实例的情况下调用但是需要访问类内部的函数（类似于 Java 中的静态变量/静态函数），可以将其写成那个类中的对象声明的成员

通过关键字 companion ，就可以获得通过容器类名称来访问这个对象的方法和属性的能力，不再需要显式地指明对象的名称

```kotlin
class Test {

    companion object {

        const val NAME = ""

        fun testFun() {

        }
    }

}

fun main(args: Array<String>) {
    Test.NAME
    Test.testFun()
}
```

#### 1.2.1、工厂模式

可以利用伴生对象来实现工厂模式

```kotlin
private class User private constructor(val name: String) {

    companion object {
        fun newById(id: Int) = User(id.toString())

        fun newByDouble(double: Double) = User(double.toString())
    }

}

fun main(args: Array<String>) {

    //构造函数私有，无法创建
    //val user1 = User("leavesC")

    val user2 = User.newById(10)

    val user3 = User.newByDouble(1.3)
}
```

#### 1.2.2、指定名称

伴生对象既可以为其指定名字，也可以直接使用其默认名 Companion，在引用伴生对象时，可以自由选择是否要在类名后加上伴生对象名

如果使用的是其默认名 Companion（没有自定义名称），则以下两种引用方式都是等价的

``` kotlin
    val user2 = User.Companion.newById(10)

    val user3 = User.newByDouble(1.3)
```

如果为伴生对象声明了自定义名称，引用方式等同

```kotlin
private class User private constructor(val name: String) {

    companion object UserLoader {
        fun newById(id: Int) = User(id.toString())

        fun newByDouble(double: Double) = User(double.toString())
    }

}

fun main(args: Array<String>) {

    //构造函数私有，无法创建
    //val user1 = User("leavesC")

    val user2 = User.UserLoader.newById(10)

    val user3 = User.newByDouble(1.3)

}
```

#### 1.2.3、实现接口

伴生对象也可以实现接口，且可以直接将包含它的类的名字当做实现了该接口的对象实例来使用

```kotlin
private class User private constructor(val name: String) {

    companion object UserLoader : Runnable {

        override fun run() {

        }
    }

}

fun newThread(runnable: Runnable) = Thread(runnable)

fun main(args: Array<String>) {
    //User 会直接被当做 Runnable 的实例
    val thread = newThread(User)
	
    val thread2 = newThread(User.UserLoader)
}
```

### 1.3、对象表达式

object 能用来声明匿名对象，可用于替代 Java 中的匿名内部类，且对象表达式中的代码可以访问并修改其外部的非 final 型的变量

```kotlin
fun newThread(runnable: Runnable) = Thread(runnable)

fun main(args: Array<String>) {
    var count = 0
    val thread = newThread(object : Runnable {
        override fun run() {
            count++
        }
    })
}
```


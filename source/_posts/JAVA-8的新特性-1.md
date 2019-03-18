---
title: JAVA 8的新特性 - 1
date: 2019-02-22 17:34:48
tags:
- JAVA
- Lambda 表达式
---
Java 8 相比之前主要变化？

# 1. Lambda 表达式

可传递的一种匿名函数的方式：它没有名称，但是它有参数列表、函数体、返回类型，可能还有一个可抛出的异常列表。下面我们用例子来解释:

* 匿名------我们说匿名，是因为它不像普通的方法那样有一个明确的名称：写得少而想  
得多！

* 函数------我们说它是函数，是因为Lambda函数不像方法那样属于某个特定的类。但和方法一样， Lambda有参数列表、函数主体、返回类型，还可能有可以抛出的异常列表。

* 传递------Lambda表达式可以作为参数传递给方法或存储在变量中。

* 简洁------无需像匿名类那样写很多模板代码。

我们通过一段代码来发现Lambda 表达式的简洁之处：

1：找出Person列表中 比传入age 更老的Person 列表，不通过Lambda表达式，写法如下：
```
    public static void printPersonsOlderThan(List\<Person\> roster, int age){
        for (Person p : roster) {
            if (p.getAge() \>= age) {
        
                 p.printPerson();
            }
        }
    }
```
2：接下来如果我们使用Lambda 表达式重写上段代码：

```
roster
    .stream()
    .filter(p -> {p.getAge() > age}).forEach(p -> p. p.printPerson());
```
使用Lambda 表达式之后让代码更整洁，读起来更清晰

Lambda 表达式词法：
```
* 在括号中参数列表用逗号分隔
* 使用 ->
* 如果有return声明，在Lambda 表达式中， 就必要用花括号来括起语句
```
p -> {
    return p. p.printPerson()
}

然而如果是一个void方法就不必使用{}

email -> System.out.println(email)


# 2. 方法的引用

重复使用现有的方法定义，并像Lambda一样传递它们函数式数据处理。下面就是四种方法的引用：


    | **Kind**      | **Example**                          |
    | 静态方法引用        | ContainingClass::staticMethodName    |
    | 特定对象的方法引用     | containingObject::instanceMethodName |
    | 特定类型的任意对象方法引用 | ContainingType::methodName           |
    | 构造引用          | ClassName::new                       |

下面我们通过代码来解释一下：

```
public class Person {
    public enum Sex {

        MALE, FEMALE

    }
    String name;
    LocalDate birthday;
    Sex gender;
    String emailAddress;
    public int getAge() {
        // ...
    }
    
    public Calendar getBirthday() {
        return birthday;
    }    

    public static int compareByAge(Person a, Person b) {
        return a.birthday.compareTo(b.birthday);
    }

}
```

* 静态方法引用
```
Person::compareByAge
```

* 特定对象的方法引用

跟着一个例子来解释特定对象的方法引用

```
class Student {

    public int compareByAge(String a, String b) {

        return a.compareTo(b);

    }

    public static void main(String[] args) {

        Student stu = new Student();

        String[] arr = {"ABC","abc","sdf","ddddd","rrrttt"};

        Arrays.sort(arr,(a, b) -> stu.compareByAge(a,b));

        Arrays.stream(arr).forEach(System.out::println);

    }

}
```

方法接收的是一个String对象


* 特定类型的任意对象方法引用

```
String[] stringArray = { "Barbara", "James", "Mary", "John",
"Patricia", "Robert", "Michael", "Linda" };
Arrays.sort(stringArray, String::compareToIgnoreCase);
```
这个方法引用将调用a.compareToIgnoreCase(b)方法
```
 构造引用
使用Lambda 表达式 （） -\> {return new HashMap\<\>();}
```
使用构造引用 HashSet::new 让Lambda 表达式有了一种快捷的写法
```
 特定对象的方法引用 和特定类型的任意对象方法引用的区别
String::compareToIgnoreCase 中，当你引用对象的方法时，这个对象本身就是一个Lambda表达式的参数Lambda表达式(String s) -\> s. compareToIgnoreCase ()可以写作String:: compareToIgnoreCase。而特定对象的方法引用则是(a, b) -\> stu.compareByAge(a,b),等价stu::compareByAge
```
![](JAVA-8的新特性-1/1550828240.jpg)



# 3. 接口的默认方法和静态方法

Java 8 允许在接口中添加默认方法，这个默认方法和之前的抽象方法不一样，主要在于抽象方法实现者必要实现，默认方法实现者都包含，如果需要可以重写这个方法。具体我们通过例子来解释：

创建一个car 接口，声明一个默认方法和一个静态方法


```
public interface Car {

    default void print() {

        System.out.println("default------------");

    }

    static Car create( Supplier<Car> supplier ) {

        return supplier.get();

    }

}
```
实现newCar类
```
public class NewCar implements Car {
    public static void main(String[] args) {

        Car newCar = Car.create(NewCar::new);

        newCar.print();

    }
}
```

覆盖默认方法 OverriddenCar


```
public class OverriddenCar implements Car {

    @Override

    public void print() {

        System.out.println("OverriddenCar------------------");

    }

    public static void main(String[] args) {

        Car overriddenCar = Car.create(OverriddenCar::new);

        overriddenCar.print();

        Car newCar = Car.create(NewCar::new);

        newCar.print();

    }

}
```
打印结果
```
OverriddenCar------------------
default------------
```

在JVM中，默认方法的实现是非常高效的，并且通过字节码指令为方法调用提供了支持。默认方法允许继续使用现有的Java接口，而同时能够保障正常的编译过程。这方面好的例子是大量的方法被添加到java.util.Collection接口中去：stream()，parallelStream()，forEach()，removeIf()，......


尽管默认方法非常强大，但是在使用默认方法时我们需要小心注意一个地方：在声明一个默认方法前，请仔细思考是不是真的有必要使用默认方法，因为默认方法会带给程序歧义，并且在复杂的继承体系中容易产生编译错误。

参考文档：

  [Java 8新特性终极指南](http://www.importnew.com/11908.html)

  Java+8 实战书籍

  [Java tutorial ](https://docs.oracle.com/javase/tutorial/)
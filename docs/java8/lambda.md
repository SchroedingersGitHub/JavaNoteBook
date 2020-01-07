## 01 Lambda 简介

>  Lambda表达式是Java 8推出的一个新特性。从本质上讲，**Lambda表达式是一个匿名函数。**

**为什么使用Lambda表达式?**

使⽤Lambda表达式可以对⼀个接口进⾏非常简洁的实现。

> ```java
> package com.max.lambda;
> 
> public class Program {
>     public static void main(String[] args) {
> 
>         // i 使用接口实现类
>         TestInterface myTestInterface = new MyTestInterface();
> 
>         // ii 使用匿名内部类
>         TestInterface testInterface = new TestInterface() {
>             @Override
>             public int test(int a, int b) {
>                 return 0;
>             }
>         };
>       
>         // iii Lambda表达式
>         TestInterface testInterface2 = (a, b) -> (a - b);
>     }
> }
> 
> class MyTestInterface implements TestInterface {
> 
>     @Override
>     public int test(int a, int b) {
>         return a - b;
>     }
> }
> 
> interface TestInterface {
>     int test(int a, int b);
> }
> ```

### Lambda对接⼝口的要求?

> 1. 接口的要实现抽象方法只有一个  （**不包括被default修饰的方法**）
> 2. **@FunctionalInterface** 修饰的函数式接口 -> 表示该接口有且只能有一个必须实现的抽象方法

## 02 Lambda 基础语法

> 一个方法包括:   返回值 方法名 参数 方法体     -- 经Lambda后 -->     **返回值 参数**  （我们在写Lambda表达式的时候，只需要关⼼参数和方法体即可 ）

* 参数： 用 () 包括起来

* 方法体：用 {} 包围起来

*  lambda运算符 用  -> 分割参数和方法体

  
  
  
  
  ```java
  //i 无参 无返回
  LambdaNoneReturnNoneParameter lambda1 = () -> {
      System.out.println("hello World");
  };
  lambda1.test();
  
  // ii 无返回值 单个参数
  LambdaNoneReturnSingleParmeter lambda2 = (int a) -> {
      System.out.println(a);
  };
  lambda2.test(10);
  
  // iii  无返回值 多个参数
  LambdaNoneReturnMultipleParameter lambda3 = (int a,int b) -> {
      System.out.println(a + "  "+ b);
  };
  lambda3.test(10,40);
  
  
  // i 有返回值 无参数
  LambdaSingleReturnNoneParmeter lambda4 = () -> {
      return 10;
  };
  System.out.println(lambda4.test());
  
  // ii 有返回值 一个参数
  LambdaSingleReturnSingleParmeter lambda5 = (int a) -> {
      return a;
  };
  System.out.println(lambda5.test(30));
  
// iii  有返回值 多个参数
  LambdaSingleReturnMultipleParameter lambda6 = (int a,int b) -> a+b;
  System.out.println(lambda6.test(10,40));
  ```
  
  

## 03 语法精简

> 概念：接⼝中定义的方法，已经声明了方法的参数类型、数量和返回值类型。所以，使用Lambda表达式在 实现的时候，对应的部分可以省略

### 1. 参数类型

>  由于在接口抽象方法中定义了参数类型，所以来Lambda表达式中可以省略
>  ps: 如果省略 则每一个参数类型都要省略

```java
 LambdaNoneReturnMultipleParameter Lambda = (a, b) -> {
            System.out.println("hello world");
  };
```

### 2. 参数括号

> ps: 参数只有一个情况下可以

```java
LambdaNoneReturnSingleParmeter lambda2 = a -> {
            System.out.println("hello world");
};
```

###  3. 方法大括号

>  ps: 方法体只有一条语句时

```java
LambdaNoneReturnSingleParmeter lambda3 = a -> System.out.println("hello world" + a);
```

### 4. 返回值

> 如果方法体中唯一的一条语句是返回值，在精简掉⼤大括号后，return也必须省略略 

```java
// int test();
LambdaSingleReturnNoneParmeter lambda4 = () -> 10;

// int test(int a, int b);
LambdaSingleReturnMultipleParameter lambda5 = (a, b) -> a + b;
```

## 04 方法引用

> 概念：可以快速将一个Lambda表达式中的实现指向一个 已经实现的方法
>
> * 语法：方法隶属者 :: 方法名  
>   * 方法隶属者：
>    		 	方法分为静态方法(通过类名直接调用)   类名::方法名
>    		 	非静态方法(需要创建对象引用的方法)   对象::方法名
> *  ps:
>   * 方法的 参数类型 和 参数数量 要和接口定义的方法一致
>   * 返回值的类型 也要和接口中定义的方法一致

 **静态方法：**

```java

package com.max.lambda.syntax;

import com.max.lambda.interfaces.LambdaSingleReturnSingleParmeter;

public class Syntax3 {

    public static void main(String[] args) {

      // 解耦 : 只需要改change方法就行了
      LambdaSingleReturnSingleParmeter lambda2 = a -> change(a); 
      // 解耦 : 可以通过类名调用 隶属者就是类(Syntax3)
      LambdaSingleReturnSingleParmeter lambda3 = Syntax3::change; 
      
      // 输出语句引用
      Consumer<Integer> consumer = System.out::println;
      consumer.accept(10);

    }

    private static int change(int a){
        return a*10;
    }

}
```

 **非静态方法：**

```java
package com.max.lambda.test;

import java.util.function.Consumer;

public class TestMain {

    public static void main(String[] args) {
        Precess precess = new Precess();

        TestInterface testInterface = precess::test;
        testInterface.test1();
    }

    @FunctionalInterface
    interface TestInterface {
        void test1();
    }

}

class Precess {
    public void test() {
        System.out.println("调用了test");
    }

}

```

**构造方法：**

>  如果需要引⽤⼀个构造方法，需要使用 类::new 进⾏引⽤

```java
package com.max.lambda.syntax;

import com.max.lambda.data.Person;

public class Syntax4 {

    public static void main(String[] args) {

        PersonCreate personCreate = () -> new Person();
        Person person = personCreate.getPerson();

        // 构造方法的引用
        PersonCreate personCreate1 = Person::new;  // 无参引用
        Person person1 = personCreate1.getPerson();

        PersonCreate2 personCreate2 = Person::new;  // 有参引用
        Person person2 = personCreate2.getPerson("zs", 23);

    }

    interface PersonCreate {
        Person getPerson();
    }
    interface PersonCreate2 {
        Person getPerson(String name,int age);
    }

}
```

## 05 综合案例

###  集合排序  -> Comparator<? super E> 

```java
package com.max.lambda.exercise;

import com.max.lambda.data.Person;

import java.util.ArrayList;
import java.util.Comparator;

public class Exercise1 {

    // 集合排序
    // ArrayList
    public static void main(String[] args) {
        // 需求: 已知一个ArrayList中有若干个Person对象 将这些对象按照年龄降序排序

        ArrayList<Person> list = new ArrayList<>();
        list.add(new Person("zs", 20));
        list.add(new Person("ls", 19));
        list.add(new Person("ww", 21));
        list.add(new Person("wc", 23));

        System.out.println(list);

        list.sort((o1, o2) -> o2.getAge() - o1.getAge());

        System.out.println(list);

    }

}

```

###  TreeSet  -> Comparator<? super E>

```java
package com.max.lambda.exercise;

import com.max.lambda.data.Person;

import java.util.TreeSet;

public class Exercise2 {

    public static void main(String[] args) {

        // TreeSet 排序  该实现 防止去重
        TreeSet<Person> set = new TreeSet<>((o1, o2) -> {
            if (o2.getAge() < o1.getAge()) {
                return -1;
            } else {
                return 1;
            }
        });
        set.add(new Person("zs", 20));
        set.add(new Person("ls", 19));
        set.add(new Person("ms", 19));
        set.add(new Person("ww", 21));
        set.add(new Person("wc", 23));
        set.add(new Person("wc", 23));
        System.out.println(set);
    }

}

```

### forEach -> Consumer<? super E> 

```Java
package com.max.lambda.exercise;

import java.util.ArrayList;
import java.util.Collections;

public class Exercise3 {

    public static void main(String[] args) {

        // 集合遍历
        ArrayList<Integer> list = new ArrayList<>();
        Collections.addAll(list, 1, 2, 3, 4, 5, 6, 7, 8, 9);

      // 将集合中的每一个元素都带⼊到方法accept中。
        list.forEach(System.out::println);

      //输出集合中所有的偶数
        list.forEach(ele -> {
            if (ele % 2 == 0) {
                System.out.println(ele);
            }
        });

    }

}

```

###  removeIf -> Predicate<? super E> 

```Java
package com.max.lambda.exercise;

import com.max.lambda.data.Person;
import com.max.lambda.syntax.Syntax;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Iterator;

public class Exercise4 {

    public static void main(String[] args) {

        //需求：集合中删除满足条件的元素
        ArrayList<Person> list = new ArrayList<>();
        list.add(new Person("zs", 20));
        list.add(new Person("ls", 19));
        list.add(new Person("zc", 18));
        list.add(new Person("ww", 21));
        list.add(new Person("wc", 23));

        // 删除年龄大于20的 传统方式
        /* Iterator<Person> iterator = list.iterator();
        while (iterator.hasNext()) {
            Person person = iterator.next();
            if (person.getAge() > 20) {
                iterator.remove();
            }
        }*/


        // lambda实现
        // 将集合中每一个元素都带入test方法中，若返回值为true则删除这个元素
        list.removeIf(ele -> ele.getAge() > 20);
        list.forEach(System.out::println);
    }

}

```

###  线程实例化 Runnable接口

```java
package com.max.lambda.exercise;

import java.util.Arrays;

public class Exercise5 {
    public static void main(String[] args) {

        // 开启一个线程
        Thread t = new Thread(() -> {
            for (String s : Arrays.asList("aa", "bb", "cc")) {
                System.out.println(s);
            }
        });
        t.start();
    }

}

```

## 06 系统内置函数式接⼝

|                  函数式接口                  | 描述                                          |                  实现方法                   |                            子接口                            |
| :------------------------------------------: | --------------------------------------------- | :-----------------------------------------: | :----------------------------------------------------------: |
|              <br />Predicate<T>              | <br />参数T  返回值boolean                    |          <br />boolean test(T t);           | IntPredicate -> boolean test(int value);<br />LongPredicate -> boolean test(long value);<br />DoublePredicate -> boolean test(double value); |
|              <br />Consumer<T>               | <br />参数T  返回值void                       |           <br />void accept(T t);           | IntConsumer -> void accept(int value);<br />    LongConsumer -> void accept(long value); <br />  DoubleConsumer -> void accept(double value); |
| <br /><br /><br /><br /><br />Function<T, R> | <br /><br /><br /><br /><br />参数T   返回值R | <br /><br /><br /><br /><br />R apply(T t); | IntFunction<R> -> R apply(int value); <br />      LongFunction<R> -> R apply(long value);<br />      DoubleFunction<R> -> R apply(double value);  <br />  IntToLongFunction -> long applyAsLong(int value); <br />    IntToDoubleFunction  -> double applyAsDouble(int value);<br /> LongToIntFunction  -> int applyAsInt(long value);<br />   LongToDoubleFunction -> double applyAsDouble(long value); <br />DoubleToIntFunction -> int applyAsInt(double value);<br /> DoubleToLongFunction -> long applyAsLong(double value); |
|                 Supplier<T>                  | 参数无  返回值T                               |                  T get();                   |                                                              |
|               UnaryOperator<T>               | 参数T    返回值T                              |        R apply(T t) (Function<T, T>)        |                                                              |
|              BinaryOperator<T>               | 参数T, T  返回值T                             |  R apply(T t, U u);<br />BiFunction<T,T,T>  |                                                              |
|             BiFunction<T, U, R>              | 参数T, U  返回值R                             |             R apply(T t, U u);              |                                                              |
|              BiPredicate<T, U>               | 参数T, U  返回值boolean                       |           boolean test(T t, U u);           |                                                              |
|               iConsumer<T, U>                | 参数T, U  返回值void                          |           void accept(T t, U u);            |                                                              |

### 常用

```Java
// 参数T  返回值boolean   boolean test(T t);
Predicate<T>
  
// 参数T  返回值void   void accept(T t);
Consumer<T> 

// 参数T 返回值R    R apply(T t);
Function<T, R>

// 参数无 返回值T   T get();
Supplier<T>
```

## 07 闭包

>  什么是闭包?
>
>  > 在一个方法中 在方法执行结束后(Return) 方法的局部变量也就被销毁了 但在做返回时（返回值是一个是接口的实现）却引用了该变量，使得变量的作用域增加了  这种情况就是闭包。
>
>   闭包的注意事项：
>
>  > 引用的变量必须被 final 所修饰 
>
>  ```java
>  package com.max.lambda.closure;
>  
>  import java.util.function.Consumer;
>  
>  public class ClosureDemo2 {
>      public static void main(String[] args) {
>          int a = 10;
>          Consumer<Integer> c = ele -> {
>              System.out.println(a);
>          };
>  
>          // a++;
>          c.accept(1);
>      }
>  }
>  
>  ```


## 01 集合流式操作简介

### 1: 什么是流式操作

**Stream** 是JAVA8的⼀一个新特性，是对集合操作的增强。流不是集合元素，也不是⼀个数据结构，不负责数据的存储。流更像是⼀一个迭代器器，可以单向的遍历⼀个集合中的每⼀个元素，并不可循环(集合中的元素只能遍历使用一次) 

将集合中的所有数据读取到⼀个流中，并且可以对流中的数据进行⼀些操作(例如:过滤、映射、排序、截取等)，⽽每次操作的结果都是返回的⼀个流对象，以便于其他操作的执行 。

### 2:  集合流式操作的构成

 通常情况下，使⽤用集合的流式操作有三个步骤: 
 	1. 获取数据源，即需要对哪些数据进⾏处理 
 	2. 数据处理理过程:中间操作
 	3. 结果的整合:最终操作

## 02 最终操作

**最终操作:** 将流中的元素整理理成我们想要的各种数据



### 1: collect

**作用:** 将流中的所有的元素转成指定类型

```java
// 获取数据源
Stream<Person> s = Data.getData().stream();
List<Person> list = s.collect(Collectors.toList()); // 转List
Set<Person> set = s.collect(Collectors.toSet()); // 转Set
Map<String, Integer> map = s.collect(Collectors.toMap(Person::getName, Person::getScore)); // 转map
```



### 2: reduce

**作用:** 将数据按照一定**规则**统计在一起

**参数：**`BinaryOperator<T>`

```java
// 需求1：求若干数字的和
Stream<Integer> s1 = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9);
Integer integer= s1.reduce((n1, n2) -> n1 + n2).get();
System.out.println(integer);

// 需求2：求若干Person对象成绩的和
Person tmp = new Person();
Optional<Person> person1 = s.reduce((n1, n2) -> tmp.setScore(n1.getScore() + n2.getScore()));
System.out.println(tmp.getScore());
```



### 3: count

**作用:**  统计流中的元素数量 

```java
Stream<Person> s = Data.getData().stream();
long count = s.count();
System.out.println(count);
```



### 4: max  min

**作用：**  按照比较规则获取 阈值 

**参数：`Comparator<? super T> `**

```java
Stream<Person> s = Data.getData().stream();
// 需求1：找到集合中成绩最高人的信息
Person person = s.max((o1, o2) -> o1.getScore() - o2.getScore()).get();
System.out.println(person);

 // 需求2：找到集合中年龄最小人的信息
Person person = s.min((o1, o2) -> o1.getAge() - o2.getAge()).get();
System.out.println(person);
```



### 5: anyMatch  allMatch  noneMatch

**作用：** 1. 判断流中是否有满足条件的元素    2. 判断流中的所有元素是否都满足指定的条件  3. 判断流中的元素是否都不不满足指定的条件 

**参数：**`Predicate<T> `

```java
Stream<Person> s = Data.getData().stream();
// 需求1：判断集合中是否包含成绩大于80的学员
boolean res = s.anyMatch(person -> person.getScore() > 80);
System.out.println(res);

// 需求2：判断集合中是否所有成员都及格
boolean res2 = s.allMatch(person -> person.getScore() >= 60);
System.out.println(res2);

// 需求3：判断集合中是否有不及格的学员
boolean res2 = s.noneMatch(person -> person.getScore() < 60);
System.out.println(res2);
```



### 06: forEach

**作用：**依次获取流中的每⼀一个元素，实现元素的迭代，并将元素带入到某⼀个逻辑代码段中指定指定的逻辑 

**参数:** `Consumer<T> `

```java
Stream<Person> s = Data.getData().stream();
s.forEach(System.out::println);
```



### 07: 注意事项

```java
// 最终操作会关闭一个流，如果一个流被关闭了，再进行其他的操作会抛异常
Person max = s.max((ele1, ele2) -> ele1.getScore() - ele2.getScore()).get();
Person min = s.min((ele1, ele2) -> ele1.getAge() - ele2.getAge()).get();
```

​	

## 03  中间操作

### 1. filter

**作用：**是一个过滤器 ，可以自定义过滤条件 满足的被留下

**参数：`Predicate<? super T> `**

```java
// 需求：保留成绩大于80的学员
 Data.getData().stream().filter(person -> person.getScore() > 80).forEach(System.out::println);
```

### 2. distinct

**作用:**去重操作

**逻辑:** 先判断HashCode,    如果hashcode相等 再判断equals

```java
Data.getData().stream().distinct().forEach(System.out::println);
```

### 3. sorted

**作用:**排序

**逻辑：** `sorted() ` 无参类需要实现`Comparator<T>` 接口
			`sorted(Comparator<? super T> comparator) `自定义排序规则  优先级最高

```java
// 无参
public class Person implements Comparable<Person> {
    @Override
    public int compareTo(Person o) {
        return this.getScore() - o.getScore();
    }
}

// 有参
Data.getData().stream().
  sorted((o1, o2) -> o1.getAge() -> o1.getAge()- o2.getAge()).forEach(System.out::println);
```

### 4. limit

**作用：**截取流，只获取指定位置的元素

```java
Data.getData().stream().limit(3).forEach(System.out::println);
```

### 5. skip

 **作用:**跳过流中前指定位的元素 

```java
Data.getData().stream().skip(3).forEach(System.out::println);
```

### 6. map

**作用：**元素映射，将指定元素替换流中元素

**参数：** `Function<T,  R> `

**逻辑：** 将流中元素带入Function方法参数中，返回值来代替流中的元素

```java
Data.getData().stream().map(person -> person.getName()).forEach(System.out::println);
```

### 7. flatMap

**作用：** 扁平化为一个流

```java
//各个数组并不是分别映射一个流，而是映射成流的内容，所有使用map(Array::stream)时生成的单个流被合并起来，即扁平化为一个流
Arrays.stream(array).map(s -> s.split("")).flatMap(Arrays::stream).
forEach(System.out::println);
```

## 04 Collectors 工具类

* `toList`, `toSet`, `toMap` 

* `maxBy`, `minBy`: 按照指定的规则获取最大值, 最小值

  ```java
  Optional<Person> optional = Data.getData().stream()
  .collect(Collectors.maxBy((o1, o2) -> o1.getScore() - o2.getScore()));
  ```

* `joining`  合并, 将流中的元素, 以字符串的形式拼接起来

  ```java
  // joining() 无参  xiaomingxiaolixiaowangxiaozhangxiaomeixiaoheixiaohei
           String names = Data.getData().stream().map(Person::getName).collect(Collectors.joining());
           System.out.println(names);
  
           // joining(CharSequence delimiter) xiaoming-xiaoli-xiaowang-xiaozhang-xiaomei-xiaohei-xiaohei
           String names_ = Data.getData().stream().map(Person::getName).collect(Collectors.joining("-"));
           System.out.println(names_);
  
           // joining(CharSequence delimiter,CharSequence prefix,CharSequence suffix)  {xiaoming-xiaoli-xiaowang-xiaozhang-xiaomei-xiaohei-xiaohei}
           String names__ = Data.getData().stream().map(Person::getName).collect(Collectors.joining("-", "{", "}"));
           System.out.println(names__);
  ```

* `summingInt`   将流中的元素映射成一个int类型的元素, 最后进行求和

    ```java
     Integer sumScore = Data.getData().stream()
       .collect(Collectors.summingInt(Person::getScore));
     System.out.println(sumScore);
    ```
    
* `averagingInt`  求平均值
  
  ```java
   Double avgScore = Data.getData().stream()
   .collect(Collectors.averagingInt(Person::getScore));
  ```

* `summarizingInt` 将流中的元素映射成一个int类型的元素, 最后获取这些数据的描述信息

  ```java
  // IntSummaryStatistics{count=7, sum=505, min=50, average=72.142857, max=100}
  IntSummaryStatistics statistics = Data.getData().stream().collect(Collectors.summarizingInt(Person::getScore));
  ```
  
---
title: 由Java-String.intern引申的思考
copyright: true
date: 2019-03-14 22:14:49
categories: JAVA
tags:
	- String
	- 字符串
---
### 前言

闲来翻翻API文档，看到了String类的intern()方法，稍加研究，引发了Java对字符串处理的思考。由此看来，对String类的了解还尚不透彻。

### String.intern方法的作用

*Returns a canonical representation for the string object. A pool of strings, initially empty, is maintained privately by the class String. When the intern method is invoked, if the pool already contains a string equal to this String object as determined by the equals(Object) method, then the string from the pool is returned. Otherwise, this String object is added to the pool and a reference to this String object is returned. It follows that for any two strings s and t, s.intern() == t.intern() is true if and only if s.equals(t) is true. All literal strings and string-valued constant expressions are interned. String literals are defined in section 3.10.5 of the The Java? Language Specification.*

上面是jdk源码中对intern方法的详细解释。简单来说就是intern用来返回常量池中的某字符串，如果常量池中已经存在该字符串，则直接返回常量池中该对象的引用。否则，在常量池中加入该对象，然后 返回引用。下面的一个例子详细的解释了intern的作用过程:

*Now lets understand how Java handles these strings. When you create two string literals:*

*String name1 = "Ram";*

*String name2 = "Ram";*

*In this case, JVM searches String constant pool for value "Ram", and if it does not find it there then it allocates a new memory space and store value "Ram" and return its reference to name1. Similarly, for name2 it checks String constant pool for value "Ram" but this time it find "Ram" there so it does nothing simply return the reference to name2 variable. The way how java handles only one copy of distinct string is called String interning.*

### String.intern方法的演变

在jdk1.7之前，字符串常量存储在方法区的PermGen Space。在jdk1.7之后，字符串常量重新被移到了堆中。

### String类的设计思路

Java中的String被设计成不可变的，出于以下几点考虑：

1. 字符串常量池的需要。字符串常量池的诞生是为了提升效率和减少内存分配。可以说编程的多数时间在处理字符串，而处理的字符串中有很大概率会出现重复的情况。正因为String的不可变性，常量池很容易被管理和优化。

2. 安全性考虑。正因为使用字符串的场景如此之多，所以设计成不可变可以有效的防止字符串被有意或者无意的篡改。从java源码中String的设计中我们不难发现，该类被final修饰，同时所有的属性都被final修饰，在源码中也未暴露任何成员变量的修改方法。（当然如果我们想，通过反射或者Unsafe直接操作内存的手段也可以实现对所谓不可变String的修改）。

3. 作为HashMap、HashTable等hash型数据key的必要。因为不可变的设计，jvm底层很容易在缓存String对象的时候缓存其hashcode，这样在执行效率上会大大提升。

### 深入讨论

```java
String s1 = new String("aaa");
String s2 = "aaa";
System.out.println(s1 == s2);    // false

s1 = new String("bbb").intern();
s2 = "bbb";
System.out.println(s1 == s2);    // true

s1 = "ccc";
s2 = "ccc";
System.out.println(s1 == s2);    // true

s1 = new String("ddd").intern();
s2 = new String("ddd").intern();
System.out.println(s1 == s2);    // true

s1 = "ab" + "cd";
s2 = "abcd";
System.out.println(s1 == s2);    // true

String temp = "hh";
s1 = "a" + temp;
// 如果调用s1.intern 则最终返回true
s2 = "ahh";
System.out.println(s1 == s2);    // false

temp = "hh".intern();
s1 = "a" + temp;
s2 = "ahh";
System.out.println(s1 == s2);    // false

temp = "hh".intern();
s1 = ("a" + temp).intern();
s2 = "ahh";
System.out.println(s1 == s2);    // true

s1 = new String("1");    // 同时会生成堆中的对象和常量池中1的对象，但是此时s1指向堆中的对象
s1.intern();            // 常量池中的已经存在
s2 = "1";
System.out.println(s1 == s2);    // false

String s3 = new String("1") + new String("1");    // 此时生成了四个对象：常量池中的"1" + 2个堆中的"1" + s3指向的堆中的对象（注此时常量池不会生成"11"）
s3.intern();    // jdk1.7之后，常量池不仅仅可以存储对象，还可以存储对象的引用，会直接将s3的地址存储在常量池
String s4 = "11";    // jdk1.7之后，常量池中的地址其实就是s3的地址
System.out.println(s3 == s4); // jdk1.7之前false， jdk1.7之后true

s3 = new String("2") + new String("2");
s4 = "22";        // 常量池中不存在22，所以会新开辟一个存储22对象的常量池地址
s3.intern();    // 常量池22的地址和s3的地址不同
System.out.println(s3 == s4); // false
```

 对于什么时候会在常量池存储字符串对象，我想我们可以基本得出结论:

1. 显示调用String的intern方法的时候;
2. 直接声明字符串字面常量的时候，例如: `String a = "aaa";`。
3. 字符串直接常量相加的时候，例如: `String c = "aa" + "bb";  `其中的aa/bb只要有任何一个不是字符串字面常量形式，都不会在常量池生成"aabb". 且此时jvm做了优化，不会同时生成"aa"和"bb"在字符串常量池中。

### 应用实例

```java
Integer[] DB_DATA = new Integer[10];
Random random = new Random(10 * 10000);
for (int i = 0; i < DB_DATA.length; i++) {
    DB_DATA[i] = random.nextInt();
}
long t = System.currentTimeMillis();
for (int i = 0; i < MAX; i++) {
    arr[i] = new String(String.valueOf(DB_DATA[i % DB_DATA.length])); // 每次都要new一个对象
    // arr[i] = new String(String.valueOf(DB_DATA[i % DB_DATA.length])).intern(); // 其实虽然这么多字符串，但是类型最多为10个，大部分重复的字符串,大大减少内存
}

System.out.println((System.currentTimeMillis() - t) + "ms");
System.gc();
```

**部分引自 <https://www.cnblogs.com/Kidezyq/p/8040338.html>**


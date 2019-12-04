---
title: 关于Java字符串拼接
copyright: true
date: 2019-03-14 22:45:02
categories: JAVA
tags:
	- String
	- 字符串
---
### 前言

敲了几行关于字符串拼接代码，看了下字节码，发现跟Thinking in Java中描述的并不相同，随即查了一下资料，发现jdk9之后，对字符串的处理方式做出了一些优化。

### 代码

下面写了一段循环执行字符串拼接的代码

```java
public class StrTest {
	public static void main(String[] args) {
		for(int i = 0; i<5; i++) {
			String str = "str";
			String s = "abc" + str + "def";
			System.out.println(s);
		}
	}
}
```

### jdk9之后的字节码

```assembly
Compiled from "StrTest.java"
public class StrTest {
  public StrTest();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: iconst_0
       1: istore_1
       2: iload_1
       3: iconst_5
       4: if_icmpge     30
       7: ldc           #2                  // String str
       9: astore_2
      10: aload_2
      11: invokedynamic #3,  0              // InvokeDynamic #0:makeConcatWithConstants:(Ljava/lang/String;)Ljava/lang/String;
      16: astore_3
      17: getstatic     #4                  // Field java/lang/System.out:Ljava/io/PrintStream;
      20: aload_3
      21: invokevirtual #5                  // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      24: iinc          1, 1
      27: goto          2
      30: return
}
```

### Java9之前的字节码

```assembly
Compiled from "StrTest.java"
public class StrTest {
  public StrTest();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: iconst_0
       1: istore_1
       2: iload_1
       3: iconst_5
       4: if_icmpge     48
       7: ldc           #2                  // String str
       9: astore_2
      10: new           #3                  // class java/lang/StringBuilder
      13: dup
      14: invokespecial #4                  // Method java/lang/StringBuilder."<init>":()V
      17: ldc           #5                  // String abc
      19: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      22: aload_2
      23: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      26: ldc           #7                  // String def
      28: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      31: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      34: astore_3
      35: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
      38: aload_3
      39: invokevirtual #10                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      42: iinc          1, 1
      45: goto          2
      48: return
}
```

### 未完待续。。。
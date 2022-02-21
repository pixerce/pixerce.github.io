---
layout: post
title:  "String In Pool"
date:   2022-02-20 00:38:16 +0900
categories: Java
---

Java에서 String은 불변 객체이다.  
이러한 특성 때문에 JVM은 스트링을 생성하는 방식에 따라서 다르게 취급을 한다.  
* 리터럴 스트링 (literal String):    

```java
String txt = "JetStream";
```  
Heap 영역에 있는 String Pool에 메모리를 확보한 후에 리터럴 스트링을 생성한다.  
같은 리터럴 스트링을 사용하는 곳이 있다면 처음에 생성한 리터럴 스트링을 가리키도록 한다.  
(이를 intern 이라고 한다.)  
따라서,  

```java
String txt1 = "JetStream";
String txt2 = "JetStream";
// 두 String 변수 모두 같은 스트링을 가리킨다

txt1 == txt2의 결과는 true 이다. 
```

* new String (String Constructor 사용):  

```java
String txt1 = new String("JetStream");
String txt2 = new String("JetStream");

txt1 == txt2 의 결과는 false 이다.
```

new 를 사용할 때 마다 Heap에 매번 새로운 객체를 생성한다.  
이전에 같은 스트링을 사용하는 변수의 유무는 중요하지 않다.


출처:  
[geeksforgeeks]: String Constant Pool in Java  
[baeldung]: Guide to Java String Pool


[geeksforgeeks]: https://www.geeksforgeeks.org/string-constant-pool-in-java/
[baeldung]: https://www.baeldung.com/java-string-pool
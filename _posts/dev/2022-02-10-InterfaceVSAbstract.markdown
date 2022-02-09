---
layout: post
title:  "Interface vs Abstract"
date:   2022-02-10 00:07:16 +0900
categories: Java
---
Interface와 Abstract는 언제 사용해야 할까?<br>

우선 interface의 특징을 보면,
- 메서드는 body를 갖을 수 없다<br>
- 객체 변수를 갖을 수 없으며 변수 선언은 public static final 만 가능하다.
- 모든 메서드는 public이다, 다른 메서드 접근 제어자는 사용할 수 없다 e.g. private, protected
<br>

abstract의 특징을 보면,
- 객체 변수와 body가 있는 메서드를 선언할 수 있다.<br>
- body가 없는 abstract 메서드가 최소한 하나 이상 있어야 한다.
- 다양한 메서드 접근 제어자를 사용할 수 있다<br>
<br>

그리고 interface와 abstract의 공통점으로는 자체적으로는 객체화가 불가능하여 구현을 하거나 상속을 받아야만 가능한다. 
<br><br>

다시 질문으로 돌아가서, "interface와 abstract는 언제 사용해야 할까?"<br>
- 확장성을 대비하고, 최소한의 인터페이스를 제공하여 구현하는 클래스들 끼리의 독립성을 보장하고 싶다면 => interface<br>
- 기본적인 구현을 제공하여 향후에 추가하는 클래스들에서도 abstract의 구현사항을 재사용하지만 변경이 필요한 여지가 있다면 => abstract

interface의 예를 든다면,
```java
interface Payment {
    public pay();
}

class Card implements Payment {
    public void pay() { 
        System.err.println("This is Card"); 
    }
}

class SamsungPay implements Payment {
    public void pay() { 
        System.err.println("This is SamsungPay"); 
    }
}
```
abstract의 예를 든다면,
```java
abstract class AbstractFeeCalculator {
    private double ratio = 1.2;
    protected abstract boolean needRound();
    public void calculate(long price) {
        double result = ratio * price;
        System.err.println("fee = " + (needRound())? Math.round(result) : result);
    }
}

class FeeCalculator {
    @Override
    protected boolean needRound() {
        return true;        
    }
}

class TheOtherFeeCalculator {
    @Override
    protected boolean needRound() {
        return false;
    }
}
```
<br>
그리고 java 1.8 부터 interface에 default가 추가되면서, body가 있는 메서드 선언이 가능해졌다.<br>
abstract와의 경계가 모호해졌다고 생각했는데, default의 탄생 배경을 보면 꼭 그렇지만도 않다.<br>
왜냐하면 interface에도 의외로 치명적인 단점이 있는데 바로 확장성이다.<br>

```java
interface Display {
    void print();
}

class Led implements Display {
    public void print() {
        System.err.println("this is LED");
    }
}

class Oled implements Display {
    public void print() {
        System.err.println("this is OLED");
    }
}
// 수 많은 Display를 구현한 디스플레이 클래스들
```
위 예제에서 Display를 구현한 Led와 OLED외에 수 많은 Display를 구현한 클래스들이 있다고 가정하자.<br>
Display 인터페이스에 새로운 메서드를 추가한다면.... Display를 구현한 모든 클래스에 찾아서 구현체를 만들어 줘야 한다.<br>
이는 생각보다 꽤 번거로운 작업인데 이러한 interface의 단점을 보완하기 위해서 나온게 default 함수이다.

따라서, interface와 abstract를 두고 결정할 때는 default를 끼워 넣기 보다는
기존의 장단점을 고려하여 결정하자!



[https://stackoverflow.com/a/26328555]: https://stackoverflow.com/a/26328555
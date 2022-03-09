---
layout: default
title: "Entity Life Cycle"
date: 2022-01-17 00:03:16 +0900
categories: JPA
---

**Persistence Context**  
Persistence Context는 managed 상태의 엔터티들의 집합으로 이들의 라이프 사이클을 관리한다. 찾고자 하는 엔터티가 Persistence Context 에 있다면 디비 조회 없이 Context 내의 엔터티를 리턴하며, db 조회가 필요하다면 refresh()를 사용하면 된다.

Entity는 4가지의 상태가 있는데 transient, managed, removed, detached가 있다.  
<br>

**Transient**

> 새로 생성한 객체로서 순수 Java 객체이기에 아직은 데이터베이스나 JPA와의 연관성이 없다. 따라서 Persistence Context도 Transient 상태의 객체를 관리하지 않는다.
>
> ```java
>    Order order = new Order(); // transient
>    order.setId(1);
> ```

<br>

**Managed**

> Persistence Context에 persist 된 엔터티의 상태를 Managed라고 한다. Persistence Context는 엔터티의 상태를 모니터링 하는데 변경상항이 있다면 context를 flush 할 때 이를 DB에 반영할 수 있도록 필요한 쿼리 (insert or update)를 생성한다.
>
> ```java
>     Employee employee = new Employee();
>     employee.setName("Json");
>
>     entityManager.persist(employee);
> ```
>
> 이 밖에,
>
> - EntityManager의 find메서드나 쿼리를 사용하여 데이터베이스에서 엔터티를 로드 하는 경우
> - EntityManager의 merge 나 하이버네이트의 세션의 update를 사용하여 detached 한 객체를 Managed 상태로 변경할 수 있다.

<br>

**Detached**

> Persistence Context에서 관리하는 객체 였으나, 현재는 관리 밖에 있는 상태.  
> 트랜잭션 커밋 후에 Persistence Context가 닫히면서(close) 엔터티를 detached 상태로 변경 한다. EntityManager::detach()로도 가능하며,
> detached 된 엔터티는 update 나 merge 메서드를 사용하여 다시 managed 상태로 변경 할 수 있다. (Session::update, EntityManager::merge)

<br>

**Removed**

> Entity의 상태를 removed로 변경하며 Persistence Context에서 flush 시점에 SQL Delete 명령문을 실행한다.
>
> ```java
>     Employee employee = new Employee();
>     employee.setName("Json");
>     entityManager.remove(employee);
> ```

<br>

이밖에,

**JPA와 Hibernate 어떤 관계인가?**

> - JPA: ORM에 대한 Java API 인터페이스
> - Hibernate: JPA 구현체  
>   (이 밖에 OpenJPA, TopLink Essentials 등이 있다, JPA 보다 hibernate가 먼저 나와서 JPA를 정립할 때 Hibernate가 많은 영향을 끼쳤다고 한다. )

<br>

**Hibernate의 Session은 언제 열리는 것인가?**

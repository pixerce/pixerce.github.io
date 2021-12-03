---
layout: post
title:  "Optional"
date:   2021-12-02 01:48:16 +0900
categories: jekyll update
---

Optional은 어떻게 사용해야 할까?

Kotlin 이라면 ?. 같은 문법을 지원하고 있지만, Java에서는 if (obj == null) 로 null 여부를 확인한 뒤에 사용해야 한다.

Optional.ofNullable(obj), of 로 감싸서 isPresent나 map, orElse 등의 Optional에서 지원하는
여러 메소드를 사용하여 null 체크를 했는데, 사용하다보니 이게 맞나 싶은 의문이 들었다

예를 들어 기존의 null 체크가 다음과 같다면,

```java
void fun(String param) {
    if (param == null)
        throw new IllegalArgumentException("not acceptable");
}
```
Optional을 사용하면,
```java
void fun(String param) {
    Optional.ofNullable(param).orElseThrow(()-> new IllegalArgumentException("not acceptable"));
}
```

Optional을 사용했을 때보다 if 조건으로 Null 체크하는 문의 가독성이 더 좋아보인다.

물론 가끔씩 사용 하긴 했다, 요렇게..
```java
void fun(String param) {
    SimpleObject().builder()
        .txt(Optional.ofNullable(param).orElseGet("default value"))
        .build();
}
```

이처럼 Optional에 대해서 고민을 하던 중에 Optional에 대한 흥미로운 글을 찾았다

https://stackoverflow.com/a/26328555

요약을 하자면,
Optional을 Java에 추가한 이유는 메소드의 리턴 값이 없음을 명확하게 표현하기 위해서 였으나,
Optional을 어떻게 사용하던지 그건 개발자의 몫이라는 거다.

'역시'.. Optional은 아무데나 사용하도록 만든게 아니었다.

요렇게 리턴에 특화된 녀석이었다.
```java
public Optional<Member> findMember(final long srl) {
    final Member member = this.repository.find(srl);
    return Optional.ofNullable(member).orElseThrow(RuntimeException::new);
}
```

무작정 null을 리턴하기 보다는 Optional을 사용함으로써  
리턴 값이 없을 수도 있다는 것을 caller에게 명확하게 전달할 수 있다.


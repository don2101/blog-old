# 3장. 모든 객체의 공통 메서드

- Object에서 final이 아닌 메서드(equals, hashCode, toString) 등은 모두 재정의를 염두에 두고 설계된 것
- 따라서 이런 메서드를 규약에 맞게 재정의 해야 한다.





## Item 10. equals는 일반 규약을 지켜 재정의 하라

- equals는 재정의 하기 쉬워보이지만 위험이 많아 재정의 하지 않는 것이 좋다.



### 재정의 하지 않는 상황

1. 각 인스턴스가 본질적으로 고유하다.
   - 값을 표현하는 게 아니라 동작하는 개체를 표현하는 클래스
     - ex)Thread
2. 인스턴스의 '논리적 동치성'을 검사할 일이 없다.
3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 들어맞는다.
4. 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없다.



#### equals 호출을 막는 방법

```java
@Override
public boolean equals(Object object) {
    throw new AssertionError();
}
```



### 재정의 하는 상황

1. 논리적 동치성을 확인해야 하는 상황



### equals 메서드를 재정의 할 때는 일반 규약을 따라야 한다.

- equals는 동치 관계를 구현하며 다음을 만족한다.
  - 동치관계 : 집합을 서로 같은 원소들로 이루어진 부분집합으로 나누는 연산

- 반사성 : 객체는 자신과 같아야 한다.
- 대칭성 : 두 객체는 서로에 대한 동치 여부에 똑같이 답해야 한다.
- 추이성 : 첫 번째 객체 = 두 번째 객체, 두 번째 객체 = 세 번째 객체 이면 첫 번째 객체 = 세 번째 객체
- 일관성 : 두 객체가 같다면(그리고 수정되지 않는다면) 앞으로 영원히 같아야 한다.
- null 아님 : 모든 객체가 null과 같이 않아야 한다.



### 올바른 equals 메서드를 구현하는 방법

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
3. 입력을 올바른 타입으로 형변환 한다.
4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사한다.



### Google's AutoValue

- equals를 작성하고 테스트 하는 일은 지루할 수 있다.
- 구글인 만든 프레임 워크로 annotation 추가로 메서드를 작성해준다.





## Item 11. equals를 재정의 하려면 hashCode도 재정의하라

- equals를 재정의한 클래스 모두에서 hashCode도 재정의 해야 한다.

- 그렇지 않으면 hashCode 일반 규약을 어겨 문제를 발생시킨다.



### 좋은 hashCode를 작성하는 요령





- Guava : 해시 충돌이 더욱 적은 방법

- 클래스가 불변이고 해시 코드 계산 비용이 크다면, 캐싱하는 방법을 고려해볼 수 있다.

- 성능을 높이겠다고 해시 코드를 계산할 때 핵심 필드를 생략해서는 안된다.
  - 속도를 빨라 지더라도 해시 품질이 나빠진다.

- hashCode가 밚환하는 값의 생성 규칙을 API 사용자에게 자세히 공표하지 말자.
  - 그래야 클라이언트가 이 값에 의지하지 않고, 추후 계산 방식을 바꿀 수 있다.





## Item 12. toString을 항상 재정의하라

- Obejct의 기본 toString 메서드는 우리가 작성한 클래스에 적합한 문자열을 반환하지 않는다.
- 간결하면서 사람이 읽기 쉬운 형태의 유익한 정보를 반환하도록 작성해야 한다.

##### toString 규약 : 모든 하위 클래스에서 이 메서드를 재정의하라



- toString은 그 객체가 가진 주요 정보 모두를 반환하는 게 좋다.
- toString을 구현할 때 반환값의 포맷을 문서화 할지 정해야 한다.
  - 단점으로 포맷을 한번 명시하면, 평생 그 포맷에 얽매이게 된다.



- 정적 유틸리티 클래스는 toString을 제공할 이유가 없다.
- 열거 타입 또한 완벽한 toString을 제공하니 재정의 하지 않아도 된다.
- google의 AutoValue는 toString까지 생성해준다.





## Item 13. clone 재정의는 주의해서 진행하라

- Cloneable을 구현하는 것만으로는 외부 객체에서 clone 메서드를 호출할 수 없다.
- clone 메서드를 호출하면 그 객체의 필드들을 하나하나 복사한 객체를 반환한다.
- 클래스가 가변 객체를 참조하는 순간 코드상의 문제가 발생한다.
- clone 메서드는 사실생 생성자와 같은 효과를 낸다.
  - 즉, clone은 복제된 객체의 불변식을 보장해야 한다.



### Item 14. Comparable을 구현할지 고려하라

- compateTo : 단순 동치성 비교 뿐만 아니라 순서까지 비교할 수 있으며 제네릭하다.
- Camparable을 구현했다는 것은 그 클래스의 인스턴스들에서 자연적인 순서가 있음을 뜻한다.
  - Comparable을 구현한 객체 들의 배열은 손쉽게 정렬할 수 있다.
  - 순서가 명확한 값 클래스를 작성한다면 반드시 comparable 인터페이스를 구현할 것

```java
public interface Comparable<T> {
    int compareTo(T t);
}
```



### compareTo 규약

1. 두 객체 참조의 순서를 바꿔 비교해도 예상한 결과가 나와야 한다.
2. x < y, y < z 이면 x < z 를 만족해야 한다.
3. 크기가 같은 객체들 끼리는 어떤 객체와 비교하더라도 항상 같아야 한다.



### compareTo 메서드 작성 요령

- 각 필드가 동치인지를 비교하는 게 아니라 그 순서를 비교한다.
- Comparable을 구현하지 않은 필드나 표준이 아닌 순서로 비교해야 한다면 Comparator를 사용



- compareTo 메서드에서 연산자 <, >를 사용하는 방식은 오류를 유발하니 이제는 사용하지 않도록 한다.
- 대신 정적 메서드 compare를 사용한다.
- 값의 차를 기준으로 크기를 비교하는 방식은 사용하면 안된다.
  - 정수 오버플로우나 부동소수점 계산 오류가 발생할 수 있음
  - 대신, 정적 compare 메서드나 비교자 생성 메서드를 사용하자

#### 정적 compare 메서드 사용

```java
static Comarator<Object> order = new Comparator<>() {
    public in compare(Object o1, Object o2) {
        return Integer.compare(o1.hashCode(), o2.hashCode());
    }
}
```

#### 비교자 생성 메서드 활용

```java
static Comparator<Object> order = Comparator.comparingInt(o -> 0.hashCode());
```



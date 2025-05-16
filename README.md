# 📌 코틀린을 코틀린답게 사용하기 

<details>
<summary><strong>9.1 산술 연산자를 오버로드해서 임의의 클래스에 대한 연산을 더 편리하게 만들기</strong></summary>
  
- 코틀린에서 관례를 사용하는 가장 단순한 예는 산술 연산자

## 9.1.1 plus, times, divide 등: 이항 산술 연산 오버로딩

```kotlin
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point): Point {
        return Point(x + other.x, y + other.y)
    }
}
```

```kotlin
fun main() {
	val p1 = Point(10, 20)
	val p2 = Point(30, 40)
	println(p1 + p2) // 기호를 쓰면 plus 함수가 호출 
	// Point(x=40, y=60)
}
```

- `plus` 함수 앞에 `operator` 키워드를 붙여야 한다는 점
- 연산자를 오버로딩하는 함수 앞에는 반드시 `operator` 가 있어야함
- `operator` 키워드를 붙임으로 써 어떤 함수가 관례를 따르는 함수임을 명확히 할 수 있음
- 실수로 관례에서 사용하는 함수 이름을 사용하는 경우를 막아줌
- **함수 이름**(`plus`, `minus`, `times`, `div` 등)과 **`operator`** 키워드를 맞추면, 표준 연산자(`+`, `-`, `*`, `/`)를 그대로 사용 가능

## 9.1.2 연산을 적용한 다음에 그 결과를 바로 대입: 복합 연산자 오버로딩

- `plus` 와 같은 연산자를 오버로딩하면 코틀린은 `+` 연산자뿐 아니라 그와 관련 있는 연산자인 `+=` 도 자동으로 함께 지원 `+=` , `-=` 등의 연산자는 `복합 대입 연산자`라고 부름

## 9.1.3 피연산자가 1개뿐인 연산자: 단한 연산자 오버로딩

- 단항 연산자를 오버로딩하는 절차도 이항 연산자와 마찬가지
- 미리 정해진 이름의 함수를 선언하면서 `operator` 로 표시하면 됨

```kotlin
data class Point(val x: Int, val y: Int) {
    // -point
    operator fun unaryMinus(): Point =
        Point(-x, -y)

    // ++point
    operator fun inc(): Point =
        Point(x + 1, y + 1)

    // --point
    operator fun dec(): Point =
        Point(x - 1, y - 1)
}
```

```kotlin
fun main() {
    val p = Point(3, 5)

    val neg = -p
    println(neg)   // Point(x=-3, y=-5)

    val p2 = p.inc()    // 혹은 ++p
    println(p2)   // Point(x=4, y=6)

    var p3 = p
    p3++
    println(p3)   // Point(x=4, y=6)

    val p4 = p3.dec()   // 혹은 --p3
    println(p4)   // Point(x=3, y=5)
}
```
</details>

<details>
<summary><strong>9.2 비교 연산자를 오버로딩해서 객체들 사이의 관계를 쉽게 검사 </strong></summary>
	
- 코틀린에서는 산술 연산자와 마찬가지로 기본 타입 값뿐 아니라 모든 객체에 대해 비교 연산(`==`, `!=`, `>`, `<`) 등를 수행할 수 있음
- `equals`, `compareTo` 를 호출해야 하는 자바와 달리 코틀린에서는  `==` 비교 연산자를 직접 사용할 수 있어 비교 코드가 더 간결하며 이해하기 쉬움

## 9.2.1 동등성 연산자: equals

- 코틀린의 `==` 연산자는 내부적으로 `a?.equals(b) ?: (b === null)` 을 호출
- **참조 동일성**을 직접 비교하려면 `===` ,`!==` 사용

## 9.2.2 순서 연산자: compareTo (<, >, ≤, ≥)

- **`operator fun compareTo(other): Int`** 을 구현
- **양수/0/음수** 반환에 따라 <, ==, > 같은 연산자 지원
- **멤버 함수**로 직접 구현하거나, **확장 함수**로도 활용 가능
</details>


<details>
<summary><strong>9.3  컬렉션과 범위에 대해 쓸 수 있는 관례</strong></summary>
- 컬렉션을 다룰 때 가장 많이 쓰는 연산은 인덱스를 사용해 원소를 읽거나 쓰는 연산과 어떤 값이 컬렉션에 속해 있는지 검사하는 연산임

## 9.3.1 인덱스로 원소 접근: get과 set

- 코틀린 컬렉션(`List`, `Map` 등)는 `operator fun get(index)` 와 `operator fun set(index, value`) 를 제공해, 인덱스 연산자([]) 문법을 사용할 수 있게 해 줌
- **불변 컬렉션 vs 가변 컬렉션**
    - `List<T>` 는 읽기 전용이므로 `get(index)` 만 지원
    - `MutableList<T>` 는 `get` 과 `set` 둘 다 지원해 요소 변경이 가능

## 9.3.2 어떤 객체가 컬렉션에 들어있는지 검사: in 관례

- **`contains` 연산자 함수**
- 컬렉션(List, Set, Map 등)에는 `operator fun contains(element: T): Boolean` 가 정의되어 있어, 특정 원소가 컬렉션에 포함되어 있는지를 검사할 수 있음
- `in` 연산자는 내부적으로 `contains`호출

```kotlin
val nums = listOf(1, 2, 3)
println(2 in nums)    // nums.contains(2) → true
println(5 !in nums)   // !nums.contains(5) → true
```

- **불변 vs 가변 컬렉션**
    - `List<T>` / `Set<T>` 등 읽기 전용 컬렉션에서도 `contains` 만 있어 `in` 연산이 가능
    - `MutableList<T>` / `MutableSet<T>` 에도 동일하게 `in` / `!in` 사용 가능.
- **Map의 키 검사**
    - `Map<K, V>` 의 경우 `operator fun contains(key: K): Boolean` 가 키 검사용으로 정의되어 있음
    
    ```kotlin
    val map = mapOf("a" to 1, "b" to 2)
    if ("a" in map) { /* true */ }
    ```
    
- **문자열과 범위에도 적용**

```kotlin
"ell" in "Hello"  // true
```

## 9.3.3 객체로부터 범위 만들기: rangeTo와 rangeUtil 관례

- `a..b` 구문은 내부적으로 `a.rangeTo(b)`를 호출
- ex) `a..b` → `a.rangeTo(b)` (끝값 포함)
- `a until b` → 끝값 미포함 범위
- 커스텀 타입에 rangeTo/until을 정의하면 for (x in …) 같은 범위 반복을 직접 지원할 수 있습니다.

## 9.3.4 자신의 타입에 대해 루프 수행: iterator 관례

- **for-루프와 iterator**
    - Kotlin의 for (item in collection) 구문은 내부적으로 다음 과정을 거침
        - 대상 객체에 `operator fun iterator(): Iterator<T>` 가 있는지 확인
        - 반환된 `Iterator<T>`에서 `hasNext()`와 `next()`를 반복 호출
- **Iterator 인터페이스**
    
    ```kotlin
    interface Iterator<out T> {
      fun hasNext(): Boolean
      fun next(): T
    }
    ```
    
    - `hasNext()`가 `true`인 동안 `next()`를 호출해 순차적으로 요소를 꺼냄
</details>

<details>
<summary><strong>9.4 component 함수를 사용해 구조 분해 선언 제공</strong></summary>
	
- 구조 분해 선언를 사용하면 복합적인 값을 분해해서 별도의 여러 자역 변수를 한꺼번에 초기화할 수 있음
- 복합적인 값을 **여러 변수로 한꺼번에 분해**해서 초기화하는 문법
- 예: `val (name, age) = person`

---

- **작동 원리**
    - 구조 분해 선언은 `componentN()` 함수들을 호출하여 동작함.
    - 예를 들어, `val (a, b) = obj` → 내부적으로 `obj.component1()`과 `obj.component2()` 호출됨.

---

- **사용 조건**
    - 클래스에 `componentN()` 함수가 정의되어 있어야 함.
    - **`data class`**는 자동으로 `componentN()` 함수를 생성해줌.

```kotlin
class Point(val x: Int, val y: Int) {
    operator fun component1() = x
    operator fun component2() = y
}

val (x, y) = Point(10, 20)
```
</details>

<details>
<summary><strong>9.5 프로퍼티 접근자 로직 재활용: 위임 프로퍼티</strong></summary>
	
## 9.5.1 위임 프로퍼티의 기본 문법과 내부 동작

- **프로퍼티의 getter/setter 로직을 다른 객체에 위임**하는 방식
- 공통된 로직을 재사용할 수 있어 중복 제거와 코드 간결화에 유리함

```kotlin
val property by delegate
```

• `delegate`는 `getValue` / `setValue` 함수를 가진 객체여야 함

## 9.5.2 위임 프로퍼티 사용: by lazy()를 사용한 지연 초기화

- 지연 초기화는 객체의 일부분을 초기화하지 않고 남겨뒀다가 실제로 그 부분의 값이 필요할 경우 초기화할 때 흔히 쓰이는 패턴

```kotlin
val name: String by lazy {
    println("계산 중...")
    "Kotlin"
}
```

- **표준 위임 프로퍼티 종류**
    - `lazy` → 지연 초기화
    - `observable` → 값 변경 감지
    - `vetoable` → 변경 조건 검사
    - `Delegates.notNull<T>()`→ 반드시 나중에 초기화돼야 할 값에 사용
</details>

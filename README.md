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

<hr>

<details>
<summary><strong>10.1 다른 함수를 인자로 받거나 반환하는 함수 정의: 고차 함수 </strong></summary>
	
- 코틀린에서는 람다나 함수 참조를 사용해 함수를 값으로 표현 할 수 있음

## 10.1.1 함수 타입은 람다의 파라미터 타입과 반환 타입을 지정한다.

- 함수를 인자로 받거나, 함수를 반환하는 함수를 고차 함수라고 함
- 코틀린에서는 람다식이나 함수 참조를 통해 함수를 값처럼 사용할 수 있음
- 람다의 매개변수 타입과 반환 타입을 명시하여 사용.

```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y }
```

## 10.1.2 인자로 전달 받은 함수 호출

- 고차 함수에서 함수를 인자로 전달받았다면, 해당 함수를 일반 함수처럼 호출하면 됨
- 전달받은 함수도 ()를 붙여 호출할 수 있음

```kotlin
fun twoAndThree(operation: (Int, Int) -> Int): Int {
    return operation(2, 3)
}

val sum = twoAndThree { a, b -> a + b }     // 5
val product = twoAndThree { a, b -> a * b } // 6
```

- `operation`이라는 파라미터는 `(Int, Int) -> Int` 타입의 함수
- `operation(2, 3)`처럼 일반 함수처럼 호출 가능

## 10.1.3 자바에서 코틀린 함수 타입 사용

- 코틀린의 함수 타입은 Java의 함수형 인터페이스와 호환됨.
- Java에서는 코틀린 함수 타입을 직접 사용할 수는 없지만, Function 인터페이스나 람다로 전달 가능함.

```kotlin
// kotlin 
fun process(operation: (Int, Int) -> Int): Int {
    return operation(4, 2)
}
```

```java
//java
int result = KotlinKt.process((a, b) -> a + b);
```

- SAM 변환 (Single Abstract Method)
    - 자바에서는 함수형 인터페이스(메서드가 하나인 인터페이스)를 사용하여 코틀린 함수 타입을 전달받을 수 있음
    - 자바의 람다는 이런 인터페이스를 구현한 익명 객체로 자동 변환됨 → SAM 변환

```kotlin
// kotlin 
fun interface IntBinaryOp {
    fun apply(x: Int, y: Int): Int
}

fun compute(op: IntBinaryOp): Int = op.apply(10, 5)
```

```java
// java
int result = KotlinKt.compute((x, y) -> x * y);
```

## 10.1.4 함수 타입의 파라미터에 대해 기본값을 지정할 수 있고, 널이 될 수도 있다.

- 함수 타입 파라미터도 기본값 지정 가능
    - 일반 파라미터처럼 함수 타입 파라미터에도 기본값을 설정할 수 있음
    - 함수 인자를 생략하면 기본으로 지정된 함수가 사용됨

```kotlin
fun greet(message: String, formatter: (String) -> String = { it.uppercase() }) {
    println(formatter(message))
}

greet("hello")                     // HELLO (기본값 사용)
greet("hello") { it.reversed() }  // olleh
```

- 함수 타입 파라미터도 nullable 가능
    - 함수 타입 파라미터에 `null`을 허용할 수도 있음.
    - 이 경우에는 `null` 체크 후 호출 필요

```kotlin
fun greetNullable(message: String, formatter: ((String) -> String)? = null) {
    val result = formatter?.invoke(message) ?: message
    println(result)
}

greetNullable("hello")                        // hello
greetNullable("hello") { it.capitalize() }    // Hello
```

## 10.1.5 함수를 함수에서 반환

- 코틀린에서는 **함수를 반환값으로 사용할 수 있음**
- 즉, **고차 함수는 함수를 반환**할 수도 있음
- 반환 타입은 (파라미터) -> 반환값 형태의 **함수 타입**

```kotlin
fun operation(): (Int) -> Int {
    return { it * 2 }
}

val result = operation()(3)  // 6
```

- `operation()`은 `(Int) -> Int` 타입의 함수를 반환
- 반환된 함수를 즉시 호출: `operation()(3)`

## 10.1.6 람다를 활용해 중복을 줄여 코드 재사용성 높이기

- *공통된 코드 흐름(템플릿)*은 유지하고, 다른 동작만 람다로 전달하여 중복 제거
- 템플릿 메서드 패턴을 람다로 간결하게 구현하는 방식

```kotlin
fun <T> withFileReader(file: File, block: (BufferedReader) -> T): T {
    return file.bufferedReader().use { reader ->
        block(reader)
    }
}
```

- `file.bufferedReader()`와 `use {}`는 **공통 로직**
- 실제 동작은 `block` 람다로 전달 받음

```kotlin
val lines = withFileReader(File("data.txt")) { reader ->
    reader.readLines()
}

val firstLine = withFileReader(File("data.txt")) { reader ->
    reader.readLine()
}
```
</details>

<details>
<summary><strong>10.2 인라인 함수를 사용해 람다의 부가 비용 없애기 </strong></summary>
	
- 보통 람다를 익명 클래스로 컴파일한다고 설명
- 그렇지만 람다식마다 새로운 클래스가 생기고 람다가 변수를 캡처한 경우 람다 정의가 포함된 코드를 호출하는 시점마다 새로운 객체가 생긴다는 뜻이라는 사실
- 이로 인해 부가 비용이 듬
- 따라서 람다를 사용하는 구현은 똑같은 코드를 직접 실행하는 함수보다 절 효율 적

## 10.2.1 인라이닝이 작동하는 방식

- 어떤 함수를 `inline`으로 선언하면 . 그 함수의 본문이 인라인이 됨
- 다른 말로 하면 함수를 호출하는 코드를 함수를 호출하는 바이트코드 재신에 함수 본문을 번역한 바이트코드로 컴파일 한다는 뜻

```kotlin
inline fun <T> synchronized(lock: Lock, action: () -> T): T {
  lock.lock()
  try {
    return action()
  } finally {
    lock.unloack()
  }
}

fun main() {
   val l = ReentrantLock()
   synchronized(1) {
   // 
   }
}
```

- 자바에서는 임의의 객체 대해 `synchronized` 를 사용할 수 있지만 이함수는 `Lock` 클래스의 인스턴스를 요구한다는 점

## 10.2.2 인라인 함수의 제약

- **재귀 호출(recursion) 금지**
    - 직접(recursive) 혹은 간접(recursive)으로 자기 자신을 호출할 수 없음
    - 컴파일러가 호출 지점에 함수 본문을 무한히 복사하게 되어 코드 팽창(infinite inlining)이 발생하기 때문
- **가상(virtual) 함수로 사용할 수 없음**
    - `open`, `abstract`, `override` 키워드를 붙인 함수는 `inline`으로 선언할 수 없음
    - 인터페이스나 추상 클래스의 멤버로도 선언할 수 없으며, 항상 `final` 상태여야 함
- **리플렉션(reflection) 제한**
    - 인라이닝된 함수 본문은 실제 바이트코드에 함수 호출 형태로 남아 있지 않기 때문에, 런타임에 :: 연산자로 참조하거나 `kotlin.reflect API`로 호출할 수 없음
- **람다 파라미터의 반환(return) 제약**
    - `inline` 함수 안에서 넘어온 람다 내에서는 “*비지역(non-local) 반환*” (return without label) 이 가능
    - 하지만 `noinline` 으로 표시된 람다에는 비지역 반환이 불가능하며, `crossinline` 을 붙이면 “non-local return” 자체가 금지됩니다.
- **지역(local) 클래스·함수 인라이닝 불가**
    - `inline` 함수 내부에 정의된 지역 클래스나 지역 함수는 인라이닝되지 않음
    - `inline` 함수는 어디까지나 호출 지점에 “본문”만 복사해 넣으므로, 지역 선언부 전체를 가져갈 수는 없음
</details>


<details>
<summary><strong>10.3 람다에서 반환: 고차 함수에서 흐름 제어</strong></summary>
	
## 10.3.1 람다 안의 retrun 문: 람다를 둘러싼 함수에서 반환

```kotlin
inline fun perform(times: Int, action: (Int) -> Unit) {
    for (i in 1..times) {
        action(i)
    }
    println("perform 끝")  // 이 코드는, 람다에서 비지역 반환이 일어나면 실행되지않음 
}

fun foo() {
    perform(5) { i ->
        if (i == 3) return        // non-local return: foo()를 즉시 종료
        println("i = $i")
    }
    println("foo 끝")             // 이 줄은 호출되지 x 
}

fun main() {
    foo()
    println("main 계속 실행")    // foo()가 return으로 바로 나가버렸기 때문에, 여전히 실행
}
```

## 10.3.2 람다로부터 반환: 레이블을 사용한 return

- `inline 고차 함수`에 넘긴 람다에서 `return`을 하면 기본적으로 **비지역 반환(non-local return)** 으로 동작해, 람다를 감싼 함수 전체가 종료됨
- 그런데 “람다 내부만 종료시키고 싶을 때” 는 **레이블(label)** 을 붙인 return 을 사용함

```kotlin
inline fun perform(times: Int, action: (Int) -> Unit) {
    for (i in 1..times) {
        action(i)
    }
    println("perform 끝")
}

fun exampleLabelReturn() {
    perform(5) lambda@{ i ->
        if (i == 3) return@lambda   // 이 return은 람다(lambda@)만 종료
        println("i = $i")
    }
    println("exampleLabelReturn 끝")  // 이 줄도 실행됨
}

fun main() {
    exampleLabelReturn()
}
```

## 10.3.3 익명 함수: 기본적으로 로컬 return

- `inline` 함수에 넘겨줄 때 람다 대신 **익명 함수**(anonymous function)를 사용하면, 그 안의 `return`은 언제나 **로컬 반환(local return)** 으로 동작
- 익명 함수 내부에서 return이 호출되면 **익명 함수 자체만** 빠져나가고, 이를 감싼 호출 함수나 상위 함수 전체는 종료되지 않음

```kotlin
inline fun perform(times: Int, action: (Int) -> Unit) {
    for (i in 1..times) {
        action(i)
    }
    println("perform 끝")
}

fun exampleAnonymousFunction() {
    perform(5, fun(i: Int) {
        if (i == 3) {
            println("i == 3: 익명 함수에서 return")  
            return    //  로컬 반환: 익명 함수만 종료
        }
        println("i = $i")
    })
    println("exampleAnonymousFunction 끝")  // 이 줄도 정상 실행
}

fun main() {
    exampleAnonymousFunction()
}

/*
i = 1
i = 2
i == 3: 익명 함수에서 return
i = 4
i = 5
perform 끝
exampleAnonymousFunction 끝
*/
```
</details>

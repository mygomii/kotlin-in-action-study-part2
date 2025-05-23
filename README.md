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

<hr>
<details>
<summary><strong>11.1 타입 인자를 받는 타입 만들기: 제네릭 타입 파라미터</strong></summary>
	
- 제네릭스를 사용하면 타입 파라미터를 받는 타입을 정의할 수 있음

## 11.1.1 제네릭 타입과 함께 동작하는 함수와 프로퍼티

- 일반 클래스와 마찬가지로, 제네릭 클래스 안의 멤버 함수 시그니처에 T를 마음껏 쓸 수 있음

```kotlin
class Box<T>(private val value: T) {
    // T를 반환하는 함수
    fun getValue(): T = value

    // T를 파라미터로 받는 함수
    fun replace(newValue: T): Box<T> = Box(newValue)
}
```

## 11.1.2 제네릭 클래스를 홑화살괄호 구문을 사용해 선언한다

- 클래스·인터페이스·함수 이름 뒤에 <T>(또는 <A, B> 등)로 타입 파라미터 선언. 생성 시 <Int>처럼 명시하거나, 타입 추론으로 생략 가능

```kotlin
// 쌍(pair)을 담는 제네릭 클래스
class PairBox<A, B>(val first: A, val second: B)

// 함수에도 제네릭 선언 가능
fun <T> singleton(item: T): List<T> = listOf(item)

// 사용 예
val p: PairBox<String, Int> = PairBox("age", 30)
val list = singleton(true)     // List<Boolean>
```

## 11.1.3 제네릭 클래스나 함수가 사용할 수 있는 타입 제한: 타입 파라미터 제약

- SuperType 문법으로 T가 특정 타입(또는 인터페이스)을 상속/구현하도록 제한. where 절로 다중 제약도 가능.

```kotlin
// Comparable을 구현한 타입만 허용하는 정렬 리스트
class SortedList<T : Comparable<T>> {
    private val elements = mutableListOf<T>()
    fun add(item: T) {
        elements.add(item)
        elements.sort()
    }
    fun all(): List<T> = elements
}

// Number 타입만 허용하는 함수
fun <T : Number> half(value: T): Double = value.toDouble() / 2

// 사용 예
val sl = SortedList<Int>()
sl.add(5); sl.add(2); sl.add(8)
println(sl.all())           // [2, 5, 8]
println(half(9))            // 4.5
```

## 11.1.4 명시적으로 타입 파라미터를 널이 될 수 없는 타입으로 표시해서 널이 될수 있는 타입 인자 제외시키기

- 본 <T>는 nullable(Any?)도 허용. T : Any로 제한해 non-null 타입만 받도록 설정

```kotlin
// nullable 허용 버전: Box<String?> 가능
class NullableBox<T>(val value: T)

// non-null 전용 버전: Box<String?> 불가
class NotNullBox<T : Any>(val value: T)

// 사용 예
val nb1 = NullableBox<String?>(null)   // 허용
// val nb2 = NotNullBox<String?>(null) // 컴파일 에러!
val nb3 = NotNullBox("Hello")         // OK
```
</details>

<details>
<summary><strong>11.2 실행 시점 제네릭스 동작: 소거된 타입 파라미터와 실체화된 타입 파라미터</strong></summary>
	
## 11.2.1 실행 시점에 제네릭 클래스의 타입 정보를 찾을 때 한계: 타입 검사와 캐스팅

- 제네릭은 컴파일 시점에만 타입 정보를 유지하고, 실행 시점에는 타입 정보가 지워짐 이걸 `타입 소거(Type Erasure)` 라고 한다.
- 따라서 `List<String>`과 `List<Int>`는 실행 시점에는 동일한 타입(List) 으로 간주됨
- 이로 인해 발생하는 대표적인 제약
    - 타입 검사 시 구체적인 타입 파라미터를 사용할 수 없음
    
    ```kotlin
    if (value is List<String>) // ❌ 경고 발생: 실행 시점에 타입 정보 없음
    ```
    
    - **캐스팅** 시에도 타입 파라미터는 무시됨
    
    ```kotlin
    val strings = value as List<String> // 경고는 없지만, 런타임에 타입 체크 안됨
    ```
    

**???*캐스팅 시 타입 파라미터 무시됨 — 왜 문제가 되나???***

- Kotlin의 **제네릭 타입 파라미터는 실행 시점에 지워짐** → **타입 소거(Type Erasure)**

```kotlin
val strings = value as List<String>
```

- `value`가 실제로 `List<Any>`이거나 `List<Int>`라도 컴파일러는 경고 없이 통과함
- 하지만 런타임에는 `ClassCastException`이 발생할 수 있음.
    
    왜냐면 *JVM은 value가 List인지까지만 알고*, 그 안에 어떤 타입이 들어있는지는 모름.
    

- `as List<String>` 같이 **구체적인 제네릭 타입으로 캐스팅하는 건 매우 위험**함.
- *타입 안전성을 확보하려면 → reified 키워드 사용하거나, 타입 체크를 피해 로직을 설계해야 함*

## 11.2.2 실체화된 타입 파라미터를 사용하는 함수는 타입 인자를 실행 시점에 언급할 수 있다

- 앞에서 본 것처럼, 일반적인 제네릭은 타입 소거(Type Erasure) 때문에 실행 시점에 타입 정보를 알 수 없음.
- 그러나 inline 함수의 타입 파라미터에 `reified`를 붙이면, 실행 시점에도 타입 정보를 사용할 수 있음.
- 이를 통해 다음과 같은 작업이 가능해짐:
    - `is T`
    - `T::class`
    - `T::class.java`

```kotlin
inline fun <reified T> printTypeName(value: Any) {
    if (value is T) {
        println("value is of type ${T::class.simpleName}")
    } else {
        println("value is NOT of type ${T::class.simpleName}")
    }
}
```

- ***왜 inline 이 필요할까?***
    - `reified`는 **타입 정보를 바이트코드에 포함시켜야 하므로**, 반드시 함수가 `inline` 이어야 함
    - `inline` 없이 `reified`만 쓰면 컴파일 오류 발생

## 11.2.3 클래스 참조를 실체화된 타입 파라미터로 대신함으로써 java.lang.Class 파라미터 피하기

- 자바 스타일의 함수에서 타입 정보를 실행 시점에 사용하려면 보통 이렇게 함

```java
fun <T> loadService(clazz: Class<T>): T {
    return clazz.getDeclaredConstructor().newInstance()
}
```

- 코틀린에서는 `reified`를 쓰면 `Class<T>` 없이도 가능

```kotlin
inline fun <reified T> loadService(): T {
    return T::class.java.getDeclaredConstructor().newInstance()
}
```

- `T::class.java`는 **실체화된 타입 파라미터 T가 있어야만 가능**.
- 더 간결하고 타입 안전하며, **불필요한 인자 전달 없이 구현 가능**.

## 11.2.4 실체화된 타입 파라미터가 있는 접근자 정의

- Kotlin에서는 프로퍼티의 getter나 setter에 inline 함수와 reified 타입 파라미터를 사용할 수 있음
- 이를 통해 특정 타입인지 검사하거나 타입에 따라 동작을 다르게 하는 로직을 프로퍼티처럼 정의할 수 있음

```kotlin
val <T> List<T>.firstElementType: String
    inline get() = T::class.simpleName ?: "Unknown"
```

- 하지만 위 코드는 **컴파일되지 않음**.
- 왜냐하면 **`reified`는 inline ``함수에서만 사용할 수 있는데**, 일반 프로퍼티의 `getter`는 `inline`이 아니기 때문

```kotlin
inline val <reified T> List<T>.typeName: String
    get() = T::class.simpleName ?: "Unknown"
```

- 실제로는 **프로퍼티처럼 보이지만 `inline` 함수 형태**로 만들어야 함
</details>

<details>
<summary><strong>11.3 변성은 인자를 함수에 넘겨도 안전한지 판단하게 해준다</strong></summary>

- 변성 개념은 `List<String>`과 `List<Any>` 같이 기저 타입이 같고 타입 인자가 다른 여러 타입이 어떤 관계가 있는지 설명하는 개념

## 11.3.1 변성은 인자를 함수에 넘겨도 안전한지 판단하게 해준다.

- 코틀린의 **제네릭은 기본적으로 공변(covariant)하지 않음**
    
    예를 들어 List<String>은 List<Any>의 하위 타입이 아님
    
- 하지만 우리가 알고 있는 일반적인 타입 상속 관계(예: String → Any)와 달리, 제네릭 타입은 **타입 인자가 달라지면 무관한 타입으로 간주됨**

```kotlin
val strings = listOf("a", "b", "c")
printContents(strings) // 컴파일 오류 발생!
```

- 이유는 List<Any>와 List<String>은 별개의 타입으로 간주되므로, 타입 안전성 보장을 위해 허용되지 않음.

## 11.3.2 클래스, 타입, 하위 타입

- 클래스와 타입은 다름 코틀린에서는 한 클래스가 여러 타입을 가질 수 있음
- 특히 제네릭 클래스의 경우, 타입 인자에 따라 서로 다른 “타입”으로 간주됨

```kotlin
class Box<T>(val value: T)
```

- `Box<Int>` , `Box<String>` , `Box<Any>`

## 11.3.3 공변성은 하위 타입 관계를 유지한다.

- 공변성(covariance)
    - 하위 타입 관계가 유지되는 것을 의미함.
    - 즉, `String`이 `Any`의 하위 타입이라면, `Producer<String>`이 `Producer<Any>`의 하위 타입이 되도록 허용하는 것.

```kotlin
interface Producer<out T> {
    fun produce(): T
}
```

- `out T`는 *T를 반환(출력)*하는 데만 사용된다는 뜻.
- 코틀린 컴파일러는 T가 출력 전용(produced-only) 으로만 쓰인다는 걸 보장할 수 있으면 공변성을 허용함
- 이로 인해 하위 타입 관계가 유지될 수 있음
- 왜 공변성이 필요한가?
    - 타입 인자가 *읽기 전용(read-only)* 으로 사용될 때는 공변성 적용이 완전 안전
    - 따라서 `List<out T>`, `Sequence<out T>`처럼 코틀린의 표준 라이브러리에서도 적극적으로 사용됨

## 11.3.4 반공변성은 하위 타입 관계를 뒤집는다.

- 반공변성(Contravariance)이란?
    - 일반적으로 `String`은 `Any`의 하위 타입이야.
    - 그런데 `Consumer<Any>`가 `Consumer<String>`의 하위 타입이 되는 걸 반공변성이라 한다.
    - 즉, 타입 관계가 뒤집힌다는 뜻.
- 왜 타입 관계가 뒤집힐까?
    - `Consumer<T>`는 값을 받기만(consumes) 하는 역할이기 때문에
        
        → 더 일반적인 타입이어야 안전함
        
    - 예를 들어 `Consumer<Any>`는 모든 타입을 받을 수 있으니, `Consumer<String>`이 필요한 곳에 넣어도 문제 없이 동작할 수 있어.

```kotlin
interface Consumer<in T> {
    fun consume(item: T)
}
```

- `in T`는 *T를 입력 전용(consume-only)* 으로 사용하겠다는 의미
- 반공변성 적용을 통해 타입 관계를 뒤집을 수 있게 허용함

| **구분** | **키워드** | **방향성** | **설명** |
| --- | --- | --- | --- |
| 공변성 | out | 하위 → 상위 | 읽기 전용: Producer<String> → Producer<Any> |
| 반공변성 | in | 상위 → 하위 | 쓰기 전용: Consumer<Any> → Consumer<String> |
| 무변성 | 없음 | 관계 없음 | Box<String> ≠ Box<Any> |
</details>

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


> 문제시 연락주시면 삭제하겠습니다.

### 1. 식이 본문인 함수는 무엇인가? 블록이 본문인 함수 대신 식이 본문인 함수를 쓰면 어떤 경우 더 좋을까?

본문이 중괄호로 둘러싸인 함수를 블록이 본문이 함수라 부르고, 등호와 식으로 이뤄진 함수를 식이 본문이 함수라고 부른다.

```kotlin
fun circleArea2(radius: Double) = PI*radius*radius // 반환값이 Double로 추론됨
```

컴파일러가 함수 본문 식을 분석해 식의 결과 타입을 함수 반환 타입으로 정해준다. (타입 추론)

---

### 2. 디폴트 파라미터와 함수 오버로딩 중 어느쪽을 써야 할지 어떻게 결정 할 수 있을까?

1. 파라미터 개수와 타입을 기준으로 호출 할 수 있는 모든 함수를 찾는다.
2. 덜 구체적인 함수를 제외 시킨다.덜 구체적인 함수가 모두 제외 될때까지 이 단계를 반복한다.
3. 후보가 하나로 압축되면 함수를 호출하고 후보가 2개 이상일 경우 오류가 발생한다.

```kotlin
fun mul(a: Int, b: Int) = a*b            // 1
fun mul(a: Int, b: Int, c: Int) = a*b*c  // 2
fun mul(s: String, n: Int) = s.repeat(n) // 3
fun mul(o: Any, n: Int) = Array(n) { o } // 4

mul(1, 2)   // Int가 Any의 하위타입이므로 1과 4중에 1을 선택
// mul(1, 2L) // error: none of the following functions can be called with the arguments supplied
            // 오류: (Int, Long) 타입을 받을 수 있는 함수가 없음
mul(1L, 2)  // Long, Int 타입을 받을 수 있는 함수는 4번뿐이므로 4를 선택
mul("0", 3) // String이 Any의 하위타입이기 때문에 3과 4중에 3을 선택

---------------------------------------------------------------------------------

fun mul2(a: Int, b: Int = 1) = a*b           // 1
fun mul2(a: Int, b: Long = 1L) = a*b         // 2
fun mul2(a: Int, b: Int, c: Int = 1) = a*b*c // 3

//mul2(10)         // error: overload resolution ambiguity
                 // 오류: 1과 2 사이에 어느 쪽을 호출할지 결정할 수 없음
mul2(10, 20)     // 인자가 더 적기 때문에 1과 3 중에 1을 선택
mul2(10, 20, 30) // 적용 가능한 함수가 3번뿐이어서 3을 선택
```

---

### 3. 이름 붙은 인자를 사용할 경우 장단점은 무엇인가?

장점으로는 사용할 경우 어떤 인자에 어떤 인자가 쓰이는지 가시성이 좋아지고, 모든 인자를 이름 붙은 인자로 사용할 경우 함수의 인자의 순서에 영향을 받지 않는다.

단점으로는 가독성과 안전성의 향상에는 비용이 따르기 때문에 이름 붙은 인자도 예외가 아니다. 또한 인자가 많아 지게되면 코드가 길어지고 장황스러워 지고, 인수 이름을 변경하면 함수를 호출하는 이름도 바뀌어야 합니다.

---

### 4. 인자 개수가 가변적인 함수를 정의하는 방법은 무엇인가? kotlin과 java에서 vararg 함수는 어떻게 다른가?

```kotlin
fun printSorted(vararg items: Int) {
  items.sort()
  println(items.contentToString())
}

printSorted(6, 1, *intArrayOf(3, 8), 2) // 6,1,3,8,2 순서로 원소가 들어있는 배열이 전달되고, [1, 2, 3, 6, 8]이 반환됨

printSorted(items = intArrayOf(1, 2, 3)) // vararg의 이름을 지정할 때는 배열을 그냥 넘기면 됨
```

코틀린의 varargs는 자바의 vargs와 동일하지만 구문은 약간 다르다. 세 개의 점이 있는 대신 파라미터에 코틀린에서는 vararg를 사용 한다.

Kotlin과 Java의 또 다른 차이점은 Java에서는 배열을 그대로 전달하지만 Kotlin에서는 배열을 명시적으로 풀어야 한다.

---

### 5. Unit과 Nothing 타입은 어디서 사용하는가? void와 비교해서 설명하라. Nothing이나 Unit인 함수를 정의해 사용할 수 있는가?

### Unit

Unit의 가장 큰 이유는 제네릭이다.

모든 함수는 값을 반환해야 한다. 코틀린은 자바에서처럼 특별한 형태의 보이드를 갖는 대신 클래스로 표현하기로 결정했다. 클래스를 사용하는 이유는 형식 시스템을 형식 계층의 일부로 만들어 보다 일관되게 만들 수 있기 때문이다.

예를 들어, `Worker<T>` 라는 일반 인터페이스가 있다고 하자. 이 인터페이스의 doWork() 함수는 값 T를 반환해야 한다.

```kotlin
interface Worker<T> {
    fun doWork(): T
}
```

그러나 Worker 인터페이스를 확장하는 LogWorker 클래스에서 값을 반환할 필요가 없는 작업(예: 로그 작업)에 이 인터페이스를 사용할 수도 있다.

```kotlin
class LogWorker : Worker<Unit> {
    override fun doWork() {
        // Do the logging
    }
}
```

이것은 원래 값을 반환하도록 설계된 기존 인터페이스를 사용할 수 있는 Unit의 마법이다. doWork() 함수가 반환할 것이 없는 목적을 위해 단위 값을 반환하도록 한다. 따라서 일반 매개 변수를 반환하는 함수를 재정의할 때 유용하다.

또한 doWork() 함수에 대한 Unit return type 언급은 생략했고 Return 구문을 작성할 필요도 없다.

### Nothing

#### Nothing은 return value가 절대 존재 하지 않는 다는걸 명시적으로 표현한다.
```kotlin
fun error(message: Any): Nothing = throw IllegalStateException(message.toString())
```

또한 Any?가 모든 타입의 슈퍼타입이듯이 Nothing은 모든 타입의 서브타입이다. 따라서 다음과 같이 작성이 될 수 있다.

```kotlin
val user: User = request.user ?: error("User not found")

fun getUser(request: Request): User {
    return request.user ?: error("User not found")
}
```


#### Nothing은 Null Object 패턴이다.

```kotlin
fun deleteFiles(files: List<File>? = null) {
    if (files != null) files.forEach { it.delete() }
}
```

이 함수의 설계상의 문제는 `List<File>`이 비어 있는지 null인지 또는 요소가 있는지 여부를 전달하지 않는다는 것 이다. 또한 사용하기 전에 리스트가 null인지 확인해야 한다.

이 문제를 해결하기 위해 Null Object 패턴을 사용한다. Null Object 패턴 패턴에서는 객체의 부재를 전달하기 위해 null 참조를 사용하는 대신 인터페이스를 구현하지만 메서드 본문은 비워두는 객체를 사용한다.

따라서 Nothing을 쓴다면 다음과 같이 깔끔하게 작성이 가능하다.

```kotlin

// This function is already defined in the Kotlin standard library
fun emptyList() = object : List<Nothing> {
    override fun iterator(): Iterator<Nothing> = EmptyIterator
    ...
}

fun deleteFiles(files: List<File> = emptyList()) {
    files.forEach { it.delete() }
}
```


### Void

```kotlin
interface Worker<T> {
    T doWork();
}

class LogWorker implements Worker<Void> {
    @Override public Void doWork() {
        // Do the logging
        return null;
    }
}
```

Void를 사용할 때, 우리는 Void를 반환 유형으로 사용해야 하고(넘길 수 없음) 반환문을 작성해야 하는 반면, Unit의 경우에는 둘 다 건너뛸 수 있다. 이것은 코틀린 코드에서 보이드를 사용하지 않는 또 다른 이유이다.


---

### 9. 공개와 비공개 최상위 함수는 어떤 차이가 있는가?

공개 최상위 함수는 프로젝트 어디서에도 호출이 가능하지만, 비공개 최상위 함수는 파일 내에서만 호출이 가능하다.

---

### 13. 코틀린 when vs 자바 switch

자바의 경우 조건에 해당되는 모든 케이스를 실행을 시키므로 break를 걸어야 하지만(이런 경우를 폴스루라고 한다.) 코틀린은 조건에 해당되는 경우만 실행시키고 빠져 나간다.

---

### 17. 예외 처리 과정을 전체적으로 설명하라. 자바와의 차이점은 무엇인가? try문이 어떻게 다른지 설명하라.


* 프로그램은 예외를 잡아내는 핸들러를 찾는다. 예외와 일치하는 핸들러가 있다면 예외 핸들러가 처리한다.
* 현재 함수 내부에서 핸들러를 찾을 수 없으면 함수 실행이 종료되고 함수가 스택에서 제거 된다. 그리고 호출한 쪽에서 핸들러 검색을 수행한다. 이런 경우를 호출자에게 전파했다고 말한다.
* 프로그램 진입접에 이를 때까지 예외를 잡아내지 못하면 현재 스레드가 종료된다.

코틀린은 `catch (FooException || BarException e) ` 와 같은 구문을 사용할 수 없다. 또한 자바는 try-catch문에서 도달 할 수 없는 죽은 코드를 허용하지 않지만 코틀린은 허용하며, 코틀린에서는 검사 예외와 비검사 예외를 구분하지 않는다.

그리고 코틀린에서는 try도 식으로 쓸 수 있다.

```kotlin
fun readInt(default: Int) = try {...}
```



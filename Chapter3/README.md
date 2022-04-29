# 3장 예제

3장은 별도의 프로젝트 없이 몇가지 코틀린 스크립트로 되어 있습니다.

다음과 같이 스크립트를 실행할 수 있습니다. 책에서 에러 메시지가 나오는 코드 예제가 있는 경우 주변을 주석으로 둘러싸서 전체 실행에 지장을 주지 못하게 했습니다. 오류를 보고 싶다면 주석을 풀어주세요.
또, 변수 이름이 겹치거나 하는 경우에도 어느 한쪽을 주석처리하거나, 변수 이름을 바꾼 경우가 있습니다. 그리고 `fun main()` 정의가 있는 스크립트의 경우 스크립트 맨 마지막에 `main()`을 실행하는 코드가 추가됐습니다.

chapter3.kts의 주석을 보면 본문에서 각 스크립트가 어떤 순서인지를 알 수 있습니다.

```
$ kotlin -script chapter3.kts
```

코틀린 소스파일(.kt로 끝남)은 컴파일 후 실행해야 합니다.

```
$ kotlinc CircleArea.kt
$ 
```

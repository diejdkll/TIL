# **SideEffect**
> - Composable 함수(의 scope)를 벗어난 곳에서 앱의 state 변경이 일어나는 것을 뜻한다.
> - 한번만 일어나는 UI 이벤트, 다른 Screen 으로 이동하는 Navigation, system services 들과 interacting, Coroutine 을 이용한 네트워킹이나 디스크 IO 에서 사용된다.

<br>

## **SideEffect 특징**
### 비동기 작업 처리
- 주로 비동기 작업을 수행하는 데 사용된다.
- 예를 들어, API 호출, 데이터베이스 쿼리, 파일 다운로드, 또는 기타 비동기 작업을 처리할 때 사용한다.

### 컴포저블 내 사용
- 주로 Jetpack Compose의 컴포저블 함수 내에서 사용된다. 
- 컴포저블은 UI 구성 요소를 나타내며, Composable 내에서 발생하는 동작이나 상태 업데이트를 처리하기에 적합하다.

<br>

## **SideEffect 장점**
### 상태 관리 분리
- `SideEffect` 를 사용하여 비동기 작업을 처리하면 UI 상태 업데이트와 동작 분리가 가능하다.
- 이렇게 하면 코드가 더 관리하기 쉬워지며, 예기치 않은 상태 변화를 방지할 수 있다.

### 예외 처리 간소화
- `SideEffect` 를 사용하면 예외 처리를 더 간단하게 할 수 있다.
- 예외가 발생하면 해당 예외를 처리하거나 오류 상태를 UI에 나타내는 것이 용이해진다.

<br>

## LaunchedEffect
```kotlin
val data = remember { mutableStateOf<String?>(null) }

LaunchedEffect(Unit) {
    // 비동기 작업 실행 (예: 네트워크 호출)
    val result = fetchData()
    data.value = result
}
```
- Composable 함수 안에서, Coroutine 의 suspend 함수를 실행할 수 있도록 해준다.
- 해당 작업은 백그라운드에서 실행되며, 컴포저블이 재구성되더라도 작업은 완료된다.
- UI 시작, Recomposition, 인자로 들어오는 key 값이 변경되면 작동된다.

<br>

## DisposableEffect
```kotlin
DisposableEffect(Unit) {
    val request = networkRequest() // 네트워크 리퀘스트 실행

    onDispose {
        request.cancel()
    }
}
```
- 화면이 종료가 되게 전 처리해야 하는 것들을 처리할때 사용한다.
- Composable 이 Composition에서 제거, key 값이 변경되면 작동된다.
- onDispose 를 사용해서 리소스를 회수하거나, 리스너 등록을 취소한다.

***
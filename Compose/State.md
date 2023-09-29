# **State**
> - Jetpack Compose에서 state는 UI 컴포넌트의 상태 정보를 저장하고 관리하는 데 사용되는 중요한 개념이다.
> - Compose의 state는 변경 가능한 데이터를 포함하며, 데이터의 변경이 UI에 자동으로 반영된다.

<br>

## **State 특징**
### 선언적 UI (Declarative UI)
- Compose에서 UI를 정의할 때 상태를 변경하는 대신 UI의 현재 상태를 설명하는 방식으로 UI를 작성한다. 
- 상태가 변경되면 Compose가 자동으로 UI를 업데이트한다.

### 불변성(Immutability)
- Compose에서의 상태(State) 객체는 불변(immutable)하다.
- 이것은 상태 객체가 한 번 생성되면 그 값을 변경할 수 없음을 의미한다. 
- 상태의 변경이 필요한 경우, 변경된 새로운 상태 객체를 생성해야 한다.
- 이러한 불변성은 예측 가능하고 안정적인 앱을 만드는 데 도움이 된다.

<br>

## **State 장점**
### 예측 가능한 상태 관리
- 상태의 불변성과 선언적 프로그래밍 모델은 상태 관리를 예측 가능하고 버그를 줄이는 데 도움이 된다.

### 리액티브 프로그래밍 지원
- Compose에서는 `remember`, `mutableStateOf`, `viewModel` 등과 같은 상태 관리 도구를 제공하여 리액티브 프로그래밍을 지원한다. 
- 이를 활용하여 상태를 더욱 강력하게 관리할 수 있다.

### UI 테마 및 구성 속성 관리
- Compose에서는 UI 테마 및 구성(configuration) 상태를 관리할 수 있으며, 화면 방향 변경 등과 같은 구성 변경에 대한 대응이 용이하다.

<br>

## remember
```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }

    Column(
        modifier = Modifier.padding(16.dp)
    ) {
        Text(text = "Count: $count", style = MaterialTheme.typography.h4)

        Button(
            onClick = { count++ },
            modifier = Modifier.padding(top = 16.dp)
        ) {
            Text(text = "Increment")
        }
    }
}
```
- `remember` 함수를 사용하여 `count` 라는 정수 값을 초기화하고, 버튼을 클릭할 때마다 이 값을 증가시킨다.
- `remember` 함수로 보존된 상태는 Composable 내에서만 사용할 수 있다. 따라서 해당 Composable 내에서만 상태를 공유하고 관리할 수 있다.

<br>

## rememberSaveable
```kotlin
@Composable
fun Counter() {
    var count by rememberSaveable { mutableStateOf(0) }

    Column(
        modifier = Modifier.padding(16.dp)
    ) {
        Text(text = "Count: $count", style = MaterialTheme.typography.h4)

        Button(
            onClick = { count++ },
            modifier = Modifier.padding(top = 16.dp)
        ) {
            Text(text = "Increment")
        }
    }
}
```
- `rememberSaveable` 함수를 사용하여 `count` 라는 정수 값을 초기화하고, 버튼을 클릭할 때마다 이 값을 증가시킨다.
- `rememberSaveable` 은 앱의 상태를 안정적으로 보존하고 구성 변경(화면 회전) 시에도 상태를 복원하는 데 사용된다.
- `rememberSaveable` 함수를 사용하여 보존된 상태는 ViewModel 또는 Composable 외부에서도 공유하고 관리할 수 있다. 이렇게 함으로써 다른 Composable 또는 Android 아키텍처 구성 요소와 상태를 공유하고 상태를 더 넓은 범위에서 관리할 수 있다.

<br>

## state hoisting
```kotlin
@Composable
fun ParentComposable() {
    var count by remember { mutableStateOf(0) }

    ChildComposable(count) { newCount ->
        count = newCount
    }
}

@Composable
fun ChildComposable(count: Int, onUpdateCount: (Int) -> Unit) {
    Column {
        Text("Count: $count")

        Button(onClick = { onUpdateCount(count + 1) }) {
            Text("Increment")
        }
    }
}
```
- `ParentComposable` 에서 `count` 라는 상태를 관리하고 있다.
- `ChildComposable` 은 `count` 상태를 받아와 사용하며, 상태 업데이트를 위한 함수 `onUpdateCount` 를 인자로 받는다.
- 버튼 클릭 시, `ChildComposable` 에서 `onUpdateCount` 를 호출하여 상태를 업데이트한다.
- 부모 Composable 에서 상태를 관리하고, 자식 Composable에게 필요한 상태 및 업데이트 함수를 전달하여 상태를 공유하고 관리할 수 있다.

***
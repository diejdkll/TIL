# **Slot API**
> - Composable 한 함수, 즉 UI 요소를 매개변수로 하여 또 다른 Composable 함수를 만들고 싶을 때 사용한다.
> - 레이아웃 내에서 Composable 을 재사용하면서 해당 Composable 의 일부를 다른 컴포넌트로 대체하는 방법을 제공한다.

<br>

## **Slot API 특징**
### 커스터마이징
- Slot API 를 사용하면 Composable 을 호출할 때 내용을 변경할 수 있다. 
- 이를 통해 동일한 컴포저블을 재사용하면서도 그 일부분을 다른 컨텐츠로 대체할 수 있다.

### 파라미터화된 컴포넌트
- Slot API 를 파라미터로 받는 함수나 컴포넌트를 정의하여, 해당 슬롯에 다양한 컨텐츠를 삽입 할 수 있다.

<br>

## **Slot API 장점**
### 재사용성
- 일부 내용을 커스터마이징 하고자 하는 컴포넌트에 Slot API 를 사용하여, 해당 컴포넌트를 여러 상황에서 재사용 할 수 있다.

### 모듈화
- 코드를 모듈화하고, 컴포넌트를 재사용 가능한 단위로 만들 수 있어서 코드의 가독성과 유지보수성을 향상시킨다.

<br>

## Slot API 사용
```kotlin
@Composable
fun MySlot(content: @Composable () -> Unit)

@Composable
fun MyScreen() {
    MySlot {
        Button(onClick = {}) {
            Text("Click Me")
        }
    }
}
```
- `MySlot` 함수가 전달된 `content` 를 수신하여 내부에서 실행한다.
- `MyScreen` 에서 `MySlot` 에 포함된 컨텐츠(Button과 Text)를 사용할 수 있다.

***
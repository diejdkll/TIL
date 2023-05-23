# **ViewModel**
> - 안드로이드 앱의 UI 관련 데이터를 저장하고 관리하기 위한 클래스

<br>

## **ViewModel 장점**
1. 수명 주기 인식(Lifecycle-aware) 
-  ViewModel은 안드로이드의 수명 주기를 인식하고, 액티비티나 프래그먼트의 생명주기와 연동된다. 이를 통해 활성 상태에 있는 구성 요소에게만 데이터를 전달하고, 비활성 상태에서는 자원을 정리할 수 있다.

2. 데이터 유지
- ViewModel은 데이터를 메모리에 유지한다. 따라서 화면 회전, 구성 변경 등의 상황에서도 데이터가 유지되며, 사용자가 화면을 다시 보게 될 때까지 데이터를 재사용할 수 있다.

3. UI와의 독립성
- ViewModel은 UI 컴포넌트와 완전히 분리되어 있다. 이를 통해 ViewModel은 UI의 변경 없이도 다양한 UI 컴포넌트 (액티비티, 프래그먼트, 서비스 등)에서 동일한 데이터를 공유할 수 있다.

<br>

## **ViewModel 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    val lifecycle_version = "2.5.1"
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:$lifecycle_version")
}
```
- 최신버전은 [*android developers*](https://developer.android.com/jetpack/androidx/releases/lifecycle?hl=ko#declaring_dependencies)에서 확인 가능

<br>

## **ViewModel 사용하기**

### 1. ViewModel 클래스 작성
```kotlin
class MyViewModel : ViewModel() {
    // 데이터를 저장하고 관리하는 코드 작성
}
```
- ViewModel을 상속하는 클래스를 작성
- ViewModel 클래스는 androidx.lifecycle.ViewModel을 상속해야 한다.

<br>

### 2. 데이터 관리
```kotlin
class MyViewModel : ViewModel() {
    private var data: String = ""

    fun setData(newData: String) {
        data = newData
    }

    fun getData(): String {
        return data
    }
}
```
- ViewModel 내부에 데이터를 저장하고, 필요에 따라 데이터를 업데이트하거나 가져올 수 있는 메서드를 작성한다.

<br>

### 3. ViewModel 사용
```kotlin
class MyActivity : AppCompatActivity() {
    private lateinit var viewModel: MyViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        viewModel = ViewModelProvider(this).get(MyViewModel::class.java)

        // 데이터 설정
        viewModel.setData("Hello ViewModel")

        // 데이터 가져오기
        val data = viewModel.getData()
    }
}
```
- UI 컴포넌트(예: 액티비티, 프래그먼트)에서 ViewModel을 사용하여 데이터를 가져오거나 업데이트한다.

***
# **LiveData**
> - 수명 주기를 인식하는 데이터 홀더 클래스

<br>

## **LiveData 장점**
1. 수명 주기 인식 (Lifecycle-aware)
- LiveData는 수명 주기를 인식하여 액티비티, 프래그먼트, 서비스 등의 수명 주기와 연동된다. 이를 통해 활성 상태에 있는 구성 요소에게만 데이터를 전달하고, 비활성 상태에서는 데이터 업데이트를 건너뛰어 자원을 절약할 수 있다.

2. 자동 업데이트
- LiveData는 데이터가 변경될 때마다 관찰자에게 자동으로 업데이트를 알린다. 이를 통해 UI 컴포넌트가 데이터 변경을 수동으로 처리하지 않아도 된다.

3. 메모리 누수 방지
- LiveData는 수명 주기를 인식하여 적절한 시기에 자동으로 연결을 해제하여 메모리 누수를 방지한다.

<br>

## **LiveData 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    val lifecycle_version = "2.5.1"
    implementation "androidx.lifecycle:lifecycle-livedata-ktx:$lifecycle_version"
}
```
- 최신버전은 [*android developers*](https://developer.android.com/jetpack/androidx/releases/lifecycle?hl=ko#declaring_dependencies)에서 확인 가능

<br>

## **ViewModel에서 LiveData 사용하기**

### 1. ViewModel 클래스 생성
```kotlin
class MyViewModel : ViewModel() {
    // MutableLiveData를 사용하여 변경 가능한 데이터 선언
    private val _data = MutableLiveData<String>()

    // 데이터를 읽기 전용으로 노출하는 LiveData 선언
    val data: LiveData<String> get() = _data

    // 데이터 업데이트 메서드
    fun updateData(newData: String) {
        _data.value = newData
    }
}
```
- `_data`라는 이름의 MutableLiveData 객체를 생성한다.
- `data`라는 이름의 LiveData 속성을 생성하여 `_data`를 읽기 전용으로 노출시킨다.


<br>

### 2. UI 컴포넌트에서 ViewModel 인스턴스 생성
```kotlin
class MyActivity : AppCompatActivity() {
    private lateinit var viewModel: MyViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // ViewModel 인스턴스 생성
        viewModel = ViewModelProvider(this).get(MyViewModel::class.java)

        // LiveData 관찰
        viewModel.data.observe(this, Observer { value ->
            // 데이터 변경 시 처리할 로직 작성
            updateUI(value)
        })

        // 데이터 업데이트 호출
        viewModel.updateData("New Data")
    }

    private fun updateUI(data: String) {
        // UI 업데이트 로직 작성
        textView.text = data
    }
}
```
- `viewModel.data.observe` 메서드를 사용하여 LiveData를 관찰한다.
- observe 메서드는 LifecycleOwner (여기서는 this, 즉 MyActivity)와 Observer를 인자로 받는다.
- Observer 내에서는 데이터가 변경될 때마다 실행되는 로직을 작성한다.
- `viewModel.updateData("New Data")`를 호출하여 데이터를 업데이트한다.

***
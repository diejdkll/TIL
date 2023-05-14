# **Binding**
> - 레이아웃 내의 뷰들(Button, TextView, ImageView와 같은)과 Activity/Fragment에서 레이아웃 내의 뷰들을 참조하기 위한 변수들을 **연결**해주는 행위

<br>

## **ViewBinding**
- 안드로이드 앱에서 View와 관련된 코드를 작성할 때 findViewById() 메소드를 대체하는 방법 중 하나이다.
- 이를 통해 개발자는 코드를 더욱 간결하고 가독성 높게 작성할 수 있다.

<br>

## **ViewBinding 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
android {
	...
    buildFeatures {
    	viewBinding = true
    }
}
```

<br>

## **Activity에서 ViewBinding 사용하기**
```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.textView.text = "Hello, World!"

        binding.button.setOnClickListener {
            // 버튼 클릭 이벤트 처리
        }
    }
}
```
- 각 view의 id 가 속성으로 제공된다.
- xml 전체를 감싸는 최상단의 부모를 root라는 속성으로 제공한다.

<br>

## **Fragment에서 ViewBinding 사용하기**
```kotlin
class MainFragment : Fragment() {

    private var _binding: FragmentMainBinding? = null
    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentMainBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        binding.textView.text = "Hello, World!"
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```
- `onCreateView()` 메소드에서 ViewBinding 클래스를 초기화하고, `onViewCreated()` 메소드에서 View들을 참조하여 작업을 수행한다.
- `onDestroyView()` 메소드에서는 ViewBinding 클래스 인스턴스를 해제한다.

***

## **DataBinding**
- UI 요소와 데이터를 프로그램적 방식으로 연결하지 않고, 선언적 형식으로 결합할 수 있게 도와주는 라이브러리
- 데이터와 뷰를 연결하는 작업을 레이아웃에서 처리하는 기술

<br>

## **DataBinding 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
android {
    ...
    buildFeatures {
        dataBinding true
    }
}
```

<br>

## **DataBinding 사용하기**
1. XML 파일에 `<layout>` 태그 안에 XML 레이아웃을 작성
```xml
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="viewModel"
            type="com.example.myapp.MyViewModel" />
    </data>

    <LinearLayout
        android:id="@+id/main_layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{viewModel.text}" />

        <Button
            android:id="@+id/button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Click me!"
            android:onClick="@{viewModel::onButtonClick}" />

    </LinearLayout>

</layout>
```
- `<data>` 태그 안에 ViewModel을 선언
- TextView의 텍스트는 ViewModel의 text 속성을 참조하도록 설정
- Button의 onClick 속성에는 ViewModel의 `onButtonClick()` 메소드를 지정하여 이벤트를 처리할 수 있도록 설정

<br>

2. Activity에서 기존의 `setContnetView()` 함수를 `DataBindingUtil.setContentView()` 로 교체
```kotlin
class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding
    private val viewModel: MyViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        binding.lifecycleOwner = this
        binding.viewModel = viewModel
    }
}
```
- `DataBindingUtil.setContentView()` 메소드를 사용하여 activity_main.xml 파일을 연결
- ViewModel을 바인딩에 설정
- lifecycleOwner에 현재 Activity를 지정하여 LiveData가 업데이트될 때 UI가 자동으로 업데이트되도록 함

<br>

3. ViewModel 만들기
```kotlin
class MyViewModel : ViewModel() {

    val text = MutableLiveData<String>()

    fun onButtonClick() {
        text.value = "Button clicked!"
    }
}
```
- MutableLiveData를 사용하여 text 속성의 값을 업데이트
- `onButtonClick()` 메소드에서 버튼 클릭 이벤트를 처리

***

## **ViewBiding vs DataBinding**
DataBinding과 ViewBinding은 모두 안드로이드에서 제공하는 뷰바인딩 기술   
이 둘을 비교하면 다음과 같은 차이점이 있다.

1. 작업방식   
ViewBinding은 각 뷰의 ID를 사용하여 뷰를 참조한다. 이에 반해 DataBinding은 XML 파일에서 뷰와 데이터를 바인딩하여 코드 작성량을 줄이고 생산성을 높이는 방식이다.

2. 기능
ViewBinding은 뷰를 참조하는 기능만을 제공한다. 이에 반해 DataBinding은 뷰와 데이터를 바인딩하여 UI를 업데이트하고 이벤트 처리 등의 기능을 제공한다.

3. 사용 대상
ViewBinding은 단순한 레이아웃에 적합하다. 이에 반해 DataBinding은 데이터와 UI를 바인딩해야 하는 복잡한 레이아웃에 적합하다.

4. 레이아웃 구성
ViewBinding은 XML 파일에서 뷰를 참조하기 때문에 레이아웃 구성이 단순하다. 이에 반해 DataBinding은 XML 파일에서 뷰와 데이터를 바인딩하기 때문에 레이아웃 구성이 복잡하다.

따라서, 단순한 뷰를 처리하는 경우 ViewBinding을 사용하고, 복잡한 뷰와 데이터 처리를 해야하는 경우 DataBinding을 사용하는 것이 좋다.   

***
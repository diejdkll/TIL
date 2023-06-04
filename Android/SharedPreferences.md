# **SharedPreferences**
> - 간단한 설정 값이나 문자열 같은 데이터를 저장
> - 내부적으로 내장 메모리의 앱 폴더에 XML 파일로 데이터가 저장
> - Application이 삭제되기 전까지 저장한 데이터가 보존된다.
> - 데이터를 key-value 형태로 저장

<br>

## **SharedPreferences 사용하기**

### 1. SharedPreferences 초기화
```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var sharedPreferences: SharedPreferences

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // SharedPreferences 초기화
        sharedPreferences = getSharedPreferences("sp", MODE_PRIVATE)

        // ...
    }
}
```
- `sharedPreferences` 객체를 클래스 변수로 선언하여 액티비티 전체에서 사용할 수 있도록 한다.
- `onCreate()` 메서드에서 `sharedPreferences`를 초기화한다.

#### **MODE의 종류**
- MODE_PRIVATE : 생성한 Application에서만 사용 가능
- MODE_WORLD_READABLE : 외부 App에서 사용 가능, 읽기만 가능
- MODE_WORLD_WRITEABLE : 외부 App에서 사용 가능, 읽기/쓰기 가능

<br>

### 2. 데이터 저장
```kotlin
binding.saveBtn.setOnClickListener {
    val editor: SharedPreferences.Editor = sharedPreferences.edit()
    editor.putString("hello", "안녕")
    editor.putString("good bye", "잘가")

    editor.apply() // 변경 사항을 비동기적으로 저장
}
```
- 저장 버튼(`saveBtn`)을 누르면 `sharedPreferences.edit()`를 호출하여 `SharedPreferences.Editor` 객체를 얻는다.
- `putString()` 메서드를 사용하여 키(hello, good bye)와 값을 저장한다.
- `apply()` 메서드를 호출하여 변경 사항을 비동기적으로 저장한다.

<br>

### 3. 데이터 불러오기
```kotlin
binding.loadBtn.setOnClickListener {
    val value1 = sharedPreferences.getString("hello", "기본값 설정")
    val value2 = sharedPreferences.getString("good bye", "기본값 설정")

    // 데이터 사용
    binding.textView.text = "Value 1: $value1\nValue 2: $value2"
}
```
- 로드 버튼(`loadBtn`)을 누르면 `sharedPreferences.getString()`을 사용하여 값을 불러온다.
- 해당 키에 대한 값이 없을 경우에는 기본값으로 "기본값 설정"을 사용한다.
- 데이터를 사용하여 UI를 업데이트한다.

<br>

### 4. 데이터 삭제하기
```kotlin
binding.deleteBtn.setOnClickListener {
            val sharedPreference = getSharedPreferences("sp", MODE_PRIVATE)
            val editor = sharedPreference.edit()

            editor.remove("hello")
            // 전체 삭제는 editor.clear()
            editor.commit()
        }
```
- 삭제 버튼(`deleteBtn`)을 누르면 `sharedPreferences.edit()`를 호출하여 `SharedPreferences.Editor` 객체를 얻는다.
- `remove()` 메서드를 사용하여 해당 키에 대한 데이터를 삭제한다.
- 전체 데이터를 삭제할 경우에는 `editor.clear()`를 호출한다.
- 변경 사항을 비동기적으로 저장한다.

***
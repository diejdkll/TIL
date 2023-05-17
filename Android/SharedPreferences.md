# **SharedPreferences**
> - 간단한 설정 값이나 문자열 같은 데이터를 저장
- 내부적으로 내장 메모리의 앱 폴더에 XML 파일로 데이터가 저장
- Application이 삭제되기 전까지 저장한 데이터가 보존된다.
- 데이터를 key-value 형태로 저장

<br>

## **SharedPreferences 사용하기**

### 1. 애플리케이션 Context로부터 SharedPreference에 값 저장하기
```kotlin
binding.saveBtn.setOnClickListener {
            val sharedPreference = getSharedPreferences("sp", MODE_PRIVATE)
            val editor : SharedPreferences.Editor = sharedPreference.edit()
            editor.putString("hello", "안녕")
            editor.putString("good bye", "잘가")

            editor.commit() // data 저장
        }
```
#### **MODE의 종류**
- MODE_PRIVATE : 생성한 Application에서만 사용 가능
- MODE_WORLD_READABLE : 외부 App에서 사용 가능, 읽기만 가능
- MODE_WORLD_WRITEABLE : 외부 App에서 사용 가능, 읽기/쓰기 가능

<br>

### 2. 데이터 불러오기
```kotlin
binding.loadBtn.setOnClickListener {
            val sharedPreference = getSharedPreferences("sp", MODE_PRIVATE)
            val value1 = sharedPreference.getString("hello", "기본값 설정")
            val value2 = sharedPreference.getString("good bye", "기본값 설정")
        }
```

<br>

### 3. 데이터 삭제하기
```kotlin
binding.deleteBtn.setOnClickListener {
            val sharedPreference = getSharedPreferences("sp", MODE_PRIVATE)
            val editor = sharedPreference.edit()

            editor.remove("hello")
            // 전체 삭제는 editor.clear()
            editor.commit()
        }
```

***
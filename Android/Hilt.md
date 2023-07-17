# **Hilt**
> - Android 앱의 의존성 주입(Dependency Injection)을 쉽게 구현할 수 있도록 도와주는 라이브러리

<br>

## **Hilt 장점**
**1. 간편한 설정**
- Hilt는 Dagger를 기반으로 한며, Dagger의 복잡한 설정을 단순화 하고 간소화했다.
- Dagger를 직접 구현하는데 필요한 많은 코드를 줄일 수 있다.
- 주요 구성 요소에 대한 의존성 주입을 간편하게 설정할 수 있다.

**2. 안전한 주입**
- Hilt는 컴파일 타임에 의존성 그래프를 생성하고 검증하기 때문에 런타임 시 의존성 주입에 대한 안전성이 보장된다.
- 주입 대상을 찾지 못했거나 잘못된 객체의 타입을 주입하려고 할 때 컴파일 오류를 발생시킨다.

**3. 안드로이드 수명 주기 통합**
- Hilt는 Android의 수명 주기 컴포넌트와 통합되어 객체의 수명 주기를 자동으로 관리한다.
- 액티비티, 프래그먼트, 서비스 등의 수명 주기와 일치하는 범위 내에서 객체를 생성하고 소멸시켜 이를 통해 메모리 누수와 같은 문제를 방지할 수 있다.

**4. 모듈화 지원**
- Hilt는 멀티모듈 프로젝트를 지원하여 모듈화된 의존성 주입 설정을 제공한다.
- 각 모듈은 자체적인 의존성 그래프를 가지고 있으며, 필요한 경우 다른 모듈로부터 의존성을 주입받을 수 있다.
- 앱을 모듈 단위로 분리하고 테스트 가능한 모듈을 작성 할 수 있다.

**5. 테스트 용이성**
- Hilt를 사용하면 테스트하기 쉬운 의존성 주입 코드를 작성할 수 있다.
- Hilt는 테스트용 의존성 그래프를 생성하고, 테스트 환경에서 모의 객체(Mock) 또는 가짜 객체(Fake)를 주입하여 테스트를 수행할 수 있다.
- 의존성 주입이 없는 상태에서의 단위 테스트 및 통합 테스트를 수행할 수 있다.

<br>

## **Hilt 사전 준비**
### build.gradle(Project:ProjectName)에서 의존성 추가
```
plugins {
  ...
  id("com.google.dagger.hilt.android") version "2.44" apply false
}
```

### build.gradle(Module:app)에서 의존성 추가
```
plugins {
  kotlin("kapt")
  id("com.google.dagger.hilt.android")
}

android {
  ...
}

dependencies {
  implementation("com.google.dagger:hilt-android:2.44")
  kapt("com.google.dagger:hilt-android-compiler:2.44")
}
```
- 최신버전은 [*android developers*](https://developer.android.com/training/dependency-injection/hilt-android?hl=ko)에서 확인 가능

<br>

## **Hilt 사용하기**

### 1. Application 클래스 설정
```kotlin
@HiltAndroidApp
class HiltApplication : Application() {
    ...
}
```
- `@HiltAndroidApp` 어노테이션을 `HiltApplication` 클래스에 추가하여 Hilt를 초기화한다.

<br>

### 2. Manifest에서 Application 클래스 명시
```xml
<application
    android:name="com.example.myapp.HiltApplication"
    >
    ...
</application>
```
- `AndroidManifest.xml` 파일의 `<application>` 요소에 `android:name` 속성을 추가하여 `HiltApplication` 클래스를 설정한다.

<br>

### 3. 주입 모듈 작성 및 추가
```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    fun provideApiService(): ApiService {
        return ApiService()
    }
}
```
- `@Module` 어노테이션을 사용하여 주입 모듈을 작성한다.
- 필요한 의존성 객체를 `@Provides` 어노테이션을 사용하여 제공하는 메서드를 작성한다.
- 작성한 주입 모듈을 `@InstallIn` 어노테이션을 사용하여 컴포넌트에 추가한다.

<br>

### 4. 주입 받을 클래스에 어노테이션 추가
```kotlin
@AndroidEntryPoint
class MainActivity : AppCompatActivity() {
    @Inject
    lateinit var apiService: ApiService

    ...
}
```
- 주입을 받을 클래스에 `@AndroidEntryPoint` 어노테이션을 추가한다.

***
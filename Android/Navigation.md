# **Navigation**
> - 안드로이드 JetPack 라이브러리 중 하나로 사용자의 상호작용에 따라 화면간의 이동을 구현하는데 도움을 준다.

### **Android Jetpack**
- 개발자가 관심 있는 코드에 집중할 수 있도록 권장사항 준수, 상용구 코드 제거, 모든 Android 버전과 기기에서 일관되게 작동하는 코드 작성을 돕는 라이브러리 모음

<br>

## **사용해야 되는 이유**
기존 Fragment 간 전환 코드를 작성할 때는 Fragment Manager 를 활용할 때가 많았는데, Fragment 백스택에 대한 관리, 전달할 파라미터 등 많은 요소를 코드로 직접 구현해주어야 했기 때문에 **컴포넌트의 코드 양이 길어지고 난잡해지는 경우가 많았다.**

Jetpack Navigation은 기존과 다르게 **화면 이동은 물론, 스택 관리, 데이터 전송까지 손쉽게 구현**할 수 있게 됐다.

<br>

### 장점
- Fragment 트랜잭션 처리
- **손쉬운 화면 이동** 처리
- 손쉬운 **애니메이션 구현** 지원
- **딥링크 구현** 및 처리 지원
- **`Safe args` 와 함께 안전한 데이터 전달** 지원
- `Bottom Navigation`, `Navigation Drawer` 와 같은 요소들을 손쉽게 구현할 수 있도록 지원
- `ViewModel` 을 이용, 그래프 내의 컴포넌트 간에 UI 관련 데이터를 공유할 수 있도록 지원
- 안드로이드 스튜디오의 네비게이션 에디터를 통해 네비게이션 그래프를 한 눈에 보고 쉽게 편집할 수 있도록 지원


<br>

## **Navigation의 주요 요소**
### **NavGraph** 
![NavGraph](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FpL1P6%2FbtqK2rNaFAm%2FtiilMpV2xxUL0ZqYp8i39k%2Fimg.png)
- Navigation Graph 는 res/navigation/ 폴더에 추가한 xml 파일이다.   
- 화면이동에 대한 모든정보( Action, 화면이동시 파라미터, 화면단위)를 정의하는 곳이다.    
- 위와 같은 화면 흐름을 시각적으로 보고 관리할 수 있는 파일이며, Android Studio 3.3 이상에서 지원한다.
- NavGraph에는 아래의 기본 요소에 대한 태그들을 사용할 수 있다.
    - **\<navigation>** : NavGraph의 기본 태그 
    - **\<fragment>** or **\<Activity>** or **\<Dialog>등**의 Destination(목적지가 되는 하나의 화면)
    - **\<action>** :  화면 이동에 대한 액션을 정의할 수 있다.( 그래프에서는 화살표로 표현된다.)
    - **\<argument>** : 화면 이동에 대한 파라미터를 정의한다.
    - **\<deeplink>** : 딥링크에 대한 내용을 정의한다.

<br>

### **NavHost**
- NavHost는 NavGraph에 정의된 화면들을 보여주는 컨테이너의 역할을 한다.
- 화면이동에 대한 액션은 모두 NavHost라는 Fragment에게 위임된다.
- 일반 Fragment의 Name에 NavHostFragment를 정의해주어야하고, 
연결되는 navGraph 또한 정의해주어야한다.
- NavHost에는 NavController도 포함한다.

<br>

### **NavController**
- NavController는 화면이동에 대한 컨트롤러 역할을 한다.
- NavController는 NavHost에서 얻을수있다.

<br>

## **Navigation 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    implementation 'androidx.navigation:navigation-fragment-ktx:2.5.3'
    implementation 'androidx.navigation:navigation-ui-ktx:2.5.3'
}
```
- 최신버전은 [*Navigation developer*](https://developer.android.com/jetpack/androidx/releases/navigation?hl=ko)에서 확인 가능

***

## **Navigation 사용하기**

### 1. NavGraph 만들기
- res - new - Android Resource File - navigation폴더를 생성 - nav_graph.xml을 생성
- NavGraph를 통해 앱 내에서 **사용자가 이용 가능한 모든 경로**가 표시된다.

### nav_graph.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/nav_graph"
    app:startDestination="@id/fragmentOne">

    <fragment
        android:id="@+id/fragmentOne"
        android:name="com.eun.myappkotlin02.nav.OneFragment"
        android:label="fragment_one"
        tools:layout="@layout/fragment_one" >
        <action
            android:id="@+id/action_fragmentOne_to_fragmentTwo"
            app:destination="@id/fragmentTwo" />
    </fragment>

    <fragment
        android:id="@+id/fragmentTwo"
        android:name="com.eun.myappkotlin02.nav.TwoFragment"
        android:label="fragment_two"
        tools:layout="@layout/fragment_two" >
        <action
            android:id="@+id/action_fragmentTwo_to_fragmentThree"
            app:destination="@id/fragmentThree" />
    </fragment>

    <fragment
        android:id="@+id/fragmentThree"
        android:name="com.eun.myappkotlin02.nav.ThreeFragment"
        android:label="fragment_three"
        tools:layout="@layout/fragment_three" >
        <action
            android:id="@+id/action_fragmentThree_to_fragmentOne"
            app:destination="@id/fragmentOne" />
    </fragment>

</navigation>
```
- `app:startDestination="@id/fragmentOne"`
    - 사용자가 앱을 처음 열 때 기본적으로 실행되는 대상을 지정하는 속성
- `tools:layout`
    - 그래픽 편집기에서 표시해야 하는 레이아웃을 지정한다.

<br>

### 2. nav_graph.xml에 Fragment추가
![네비게이션](https://velog.velcdn.com/images%2Feoqkrskfk94%2Fpost%2F367e7ac6-4184-4e0d-b674-084f12adf5e9%2Fimage.png)
- 우리가 미리 만든 페이지들을(프레그먼트와 레이아웃 파일) 추가

<br>

### 3. Navhost 만들기
### activity_main.xml
```xml
<fragment
    android:id="@+id/nav_host_fragment"
    android:name="androidx.navigation.fragment.NavHostFragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    
    app:defaultNavHost="true"
    app:navGraph="@navigation/nav_graph"/>
```
- `android:name="androidx.navigation.fragment.NavHostFragment"`
    - 프래그먼트 대상을 표시하는 기본 NavHost 구현
- `app:defaultNavHost="true"`  
    - 시스템의 백버튼 등의 이벤트가 그래프와 연동이 되도록한다. 
    - true 로 설정하지 않을 경우 백버튼 시 앱이 종료된다.
- `app:navGraph="@navigation/nav_graph"`
    - 어떤 Navigation 탐색 그래프를 사용하는지 명시해준다.

<br>

### 4. NavController
### MainActivity.kt
```kotlin
class MainActivity : AppCompatActivity() {

    lateinit var navController: NavController

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_sign_up)

        navController = nav_host_fragment.findNavController()
    }
}
```
- onCreate에 findNavController 메소드를 사용하여 NavController를 검색
- 액티비티에서 발견한 NavHost를 찾는 메소드

### Fragment.kt
```kotlin
class FragmentOne : Fragment() {

    lateinit var navController : NavController

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        return inflater.inflate(R.layout.fragment_one, container, false)
    } 
    
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        navController = nav_host_fragment.findNavController()

        btn_next.setOnClickListener {
            navController.navigation(R.id.action_fragmentOne_to_fragmentTwo)
        }
    }
}
```
- NavController를 선언해주고 onViewCreated에서 받아온 view를 통해 NavController를 정의
- 버튼의 아이디를 가져와, setOnClickListener를 사용
- R.id.action_출발_to_도착

***
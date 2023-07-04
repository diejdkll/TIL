# **Flow**
> - 비동기적인 데이터 스트림을 표현하는 Kotlin의 라이브러리

<br>

## **Flow 장점**
**1. 비동기적인 데이터 스트림**
- 비동기 연산의 결과를 표현하는 스트림이다.
- 여러 개의 값을 연속적으로 반환할 수 있으며, 이를 통해 비동기 작업의 중간 결과나 실시간 데이터를 처리할 수 있다.

**2. 선언적인 처리**
- 선언적인 방식으로 데이터를 처리한다.
- 연산자를 사용하여 데이터를 변형하고 조작할 수 있으며, 데이터 흐름을 지정하는 DSL(Domain Specific Language) 형태로 표현할 수 있다.

**3. 취소 가능성**
- 취소 가능한 연산이므로, 필요한 경우 데이터 흐름을 중단하고 취소할 수 있다.
- 비동기 작업의 실행을 중단시키고 리소스를 효율적으로 관리하는 데 도움을 준다. 

<br>

## **Flow 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.2")
}
```
- 최신버전은 [*Coroutines Github*](https://github.com/Kotlin/kotlinx.coroutines)에서 확인 가능

<br>

## **Flow 사용하기**

### 1. 데이터 모델 정의
```kotlin
data class Post(
    val id: Int,
    val title: String,
    val body: String
)
```
- `Post` 는 게시물을 나타내는 데이터 모델이다.

<br>

### 2. Retrofit 인터페이스 정의
```kotlin
interface ApiService {
    @GET("posts")
    suspend fun getPosts(): List<Post>
}
```
- `ApiService` 는 Retrofit을 사용하여 서버와 통신하는 역할을 한다.
- `getPosts` 함수는 서버로부터 게시물 목록을 가져온다.

<br>

### 3. Repository 생성
```kotlin
class Repository(private val apiService: ApiService) {
    suspend fun getPosts(): List<Post> {
        return apiService.getPosts()
    }
}
```
- `Repository` 는 데이터를 관리하고 가져오는 역할을 한다.
- `getPosts` 함수는 ApiService를 통해 게시물 목록을 가져온다.

<br>

### 4. ViewModel 생성
```kotlin
class MainViewModel(private val repository: Repository) : ViewModel() {
    private val _posts = MutableStateFlow<List<Post>>(emptyList())
    val posts: StateFlow<List<Post>> = _posts.asStateFlow()

    fun fetchPosts() {
        viewModelScope.launch {
            try {
                val result = repository.getPosts()
                _posts.value = result
            } catch (e: Exception) {
                // 에러 처리
            }
        }
    }
}
```
- `MainViewModel` 은 데이터를 관리하고 UI에 업데이트하는 데 사용된다.
- `posts` 는 게시물 목록을 유지하는 `StateFlow` 이다.
- `fetchPosts` 함수는 비동기적으로 게시물을 가져와 `_posts` 값을 업데이트한다.

<br>

### 5. UI 구현
```kotlin
class MainActivity : AppCompatActivity() {
    private val viewModel: MyViewModel by viewModels()
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)
        binding.lifecycleOwner = this
        binding.viewModel = viewModel

        val adapter = PostAdapter()
        binding.recyclerView.adapter = adapter

        lifecycleScope.launchWhenStarted {
            viewModel.posts.collect { posts ->
                adapter.submitList(posts)
            }
        }

        viewModel.fetchPosts()
    }
}
```
- `MainActivity` 는 UI를 담당하는 액티비티이다.
- DataBindingUtil을 사용하여 XML 파일과 액티비티를 연결한다.
- `lifecycleScope.launchWhenStarted` 를 사용하여 `posts` 를 관찰하고, 게시물 목록이 업데이트될 때마다 어댑터에 데이터를 전달한다.
- `viewModel.fetchPosts` 를 호출하여 서버에서 게시물 목록을 가져온다.

***
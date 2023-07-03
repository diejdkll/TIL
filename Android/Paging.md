# **Paging**
> - 데이터를 일정한 덩어리(페이지)로 나눠서 가져오는 라이브러리

<br>

## **Paging 구성요소**
### **PagingSource**
- 데이터 원본과 상호작용하여 데이터를 가져오는 역할을 한다.
- 네트워크 요청, 데이터베이스 쿼리 등을 통해 데이터를 로드하는 로직이 포함된다.
- 데이터를 페이지 단위로 로드하고 필요한 경우 추가 데이터를 로드 할 수 있도록 한다.

### **PagingConfig**
- 페이징 동작을 구성하는 설정 객체
- 페이지 크기, 초기 로드 크기, 프리패치 크기, 에러 핸들링등을 설정 할 수 있다.

### **PagingData**
- 페이징된 데이터를 나타내는 객체
- RecyclerView Adapter에서 사용되며, 페이지 단위로 로드된 데이터를 관리하고 표시한다.
- LiveData, Flow등을 통해 관찰 가능한 형태로 제공되어 UI와의 연동이 용이하다.

### **PagingDataAdapter**
- RecyclerView Adapter의 구현체
- PagingData를 표시하기 위해 사용한다.
- 페이지 단위로 로드된 데이터를 RecyclerView에 바인딩하고 화면에 표시한다.
- 새로운 데이터를 자동으로 감지하고 업데이트하는 기능을 제공한다.

### **LoadState**
- 데이터 로딩 상태를 나타내는 객체
- 로딩 중, 로드 완료, 로드 실패 등의 상태를 나타낸다.
- UI에서 로딩 상태를 처리하고 표시하는 데 사용된다.
- PagingDataAdapter와 함께 사용하여 로딩 상태를 처리할 수 있다.

<br>

## **Paging 장점**
**1. 메모리 효율성**
- 메모리 효율성을 높이기 위해 페이지 단위로 데이터를 로드한다.
- 대량의 데이터를 처리할 때 특히 유용하며, 필요한 만큼의 데이터만 로드하여 메모리 사용을 최적화할 수 있다.

**2. 네트워크 효율성** 
- 페이징된 데이터를 효율적으로 로드하기 위해 미리 가져오기(프리패치) 기능을 제공한다.
- 사용자가 스크롤할 때 추가 데이터를 미리 로드하여 네트워크 트래픽을 최소화한다.

**3. 강력한 에러 핸들링**
- 데이터 로딩 중 발생하는 에러를 처리하기 위한 강력한 에러 핸들링 기능을 제공한다.
- 에러 상태를 관찰하고 적절한 에러 처리 로직을 적용하여 사용자에게 적절한 메시지를 표시하거나 재시도 옵션을 제공할 수 있다.

**4. 유연한 데이터 소스**
- 다양한 데이터 소스에 대한 지원을 제공한다.
- 네트워크 요청, 데이터베이스 쿼리, 메모리 캐시 등 다양한 소스에서 데이터를 로드할 수 있다.
- 커스텀 데이터 소스를 구현하여 특정 데이터 소스에 대한 페이징을 지원할 수 있다.

**5. 쉬운 구현 및 통합**
- 간편한 API와 쉬운 구현 방법을 제공한다.
- RecyclerView와의 통합이 용이하며, PagingDataAdapter를 사용하여 데이터를 표시하고 업데이트할 수 있다.
- LiveData, Flow 등과 함께 사용하여 간편한 UI 업데이트를 구현할 수 있다.  

<br>

## **Paging 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    implementation "androidx.paging:paging-runtime:3.1.1"
}
```
- 최신버전은 [*android developers*](https://developer.android.com/topic/libraries/architecture/paging/v3-overview?hl=ko)에서 확인 가능

<br>

## **Paging 사용하기**

### 1. 데이터 모델 정의
```kotlin
data class Repository(
    val id: Int,
    val name: String,
    val description: String?,
    val owner: String,
    val stars: Int
)
```
- `Repository` 는 GitHub 리포지토리를 나타내는 데이터 모델이다.
- 각 속성은 리포지토리의 ID, 이름, 설명, 소유자, 별점을 나타낸다.

<br>

### 2. Retrofit 인터페이스 정의
```kotlin
interface GitHubService {
    @GET("repositories")
    suspend fun getRepositories(@Query("page") page: Int): List<Repository>
}
```
- `GitHubService` 는 Retrofit을 사용하여 서버와 통신하는 역할을 한다.
- `getRepositories` 함수는 페이지별 `Repository` 목록을 가져오기 위해 서버와 통신한다.

<br>

### 3. 데이터 소스 생성
```kotlin
class RepositoryDataSource(private val service: GitHubService) : PagingSource<Int, Repository>() {
    override suspend fun load(params: LoadParams<Int>): LoadResult<Int, Repository> {
        try {
            val page = params.key ?: 1
            val repositories = service.getRepositories(page)
            val prevKey = if (page > 1) page - 1 else null
            val nextKey = page + 1
            return LoadResult.Page(repositories, prevKey, nextKey)
        } catch (e: Exception) {
            return LoadResult.Error(e)
        }
    }
}
```
- `RepositoryDataSource` 는 `PagingSource` 를 상속받아 Retrofit을 통해 Repository 목록을 가져오는 역할을 한다.
- `load` 함수는 페이지별 데이터를 로드하고, `LoadResult.Page` 를 반환한다.
- 페이지 번호에 따라 API 서비스를 호출하여 데이터를 가져온다.
- 가져온 데이터와 이전 페이지 및 다음 페이지에 대한 키를 `LoadResult.Page` 에 담아 반환한다. 예외가 발생한 경우 `LoadResult.Error` 를 반환한다.

<br>

### 4. ViewModel 구현
```kotlin
class RepositoryViewModel(private val repository: RepositoryDataSource) : ViewModel() {
    private val _repositories = MutableStateFlow<PagingData<Repository>>(PagingData.empty())
    val repositories: StateFlow<PagingData<Repository>> = _repositories

    fun getRepositories() {
        viewModelScope.launch {
            repository.getRepositories()
                .cachedIn(viewModelScope)
                .collectLatest { pagingData ->
                    _repositories.value = pagingData
                }
        }
    }
}
```
- `RepositoryViewModel` 은 `RepositoryDataSource` 를 사용하여 데이터를 가져오는 뷰모델이다.
- `repositories` 는 `PagingData<Repository>` 를 유지하는 `StateFlow` 이다.
- `getRepositories` 함수는 데이터를 가져와서 `_repositories` 값을 업데이트한다.

<br>

### 5. Adapter 구현
```kotlin
class RepositoryAdapter : PagingDataAdapter<Repository, RepositoryAdapter.RepositoryViewHolder>(DIFF_CALLBACK) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RepositoryViewHolder {
        val view = LayoutInflater.from(parent.context).inflate(R.layout.item_repository, parent, false)
        return RepositoryViewHolder(view)
    }

    override fun onBindViewHolder(holder: RepositoryViewHolder, position: Int) {
        val repository = getItem(position)
        repository?.let { holder.bind(it) }
    }

    inner class RepositoryViewHolder(view: View) : RecyclerView.ViewHolder(view) {
        fun bind(repository: Repository) {
            // 뷰에 데이터 바인딩 및 표시
        }
    }

    companion object {
        private val DIFF_CALLBACK = object : DiffUtil.ItemCallback<Repository>() {
            override fun areItemsTheSame(oldItem: Repository, newItem: Repository): Boolean {
                return oldItem.id == newItem.id
            }

            override fun areContentsTheSame(oldItem: Repository, newItem: Repository): Boolean {
                return oldItem == newItem
            }
        }
    }
}
```
- `RepositoryAdapter` 는 PagingDataAdapter를 상속받아 RecyclerView에 데이터를 표시하는 어댑터이다.
- onCreateViewHolder에서 ViewHolder를 생성하고, onBindViewHolder에서 데이터를 ViewHolder에 바인딩한다.
- inner class로 `RepositoryViewHolder` 를 정의하고, `bind` 함수에서 데이터를 뷰에 바인딩하고 표시한다.
- `DIFF_CALLBACK` 을 사용하여 아이템이 동일한지 비교하고 콘텐츠가 동일한지 비교한다.

<br>

### 6. UI 구현
```kotlin
class RepositoryFragment : Fragment() {
    private val viewModel: RepositoryViewModel by viewModels()
    private lateinit var repositoryAdapter: RepositoryAdapter
    private lateinit var binding: FragmentRepositoryBinding

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        binding = FragmentRepositoryBinding.inflate(inflater, container, false)
        repositoryAdapter = RepositoryAdapter()
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        binding.recyclerView.adapter = repositoryAdapter

        viewModel.repositories.observe(viewLifecycleOwner) { pagingData ->
            repositoryAdapter.submitData(viewLifecycleOwner.lifecycle, pagingData)
        }
    }

    override fun onStart() {
        super.onStart()
        viewModel.getRepositories()
    }
}
```
- `RepositoryFragment` 는 데이터를 표시하는 UI를 담당하는 프래그먼트이다.
- onCreateView에서 데이터 바인딩을 사용하여 프래그먼트의 레이아웃을 인플레이트한다.
- `repositoryAdapter` 를 초기화한다.
- onViewCreated에서 데이터 바인딩을 통해 뷰를 참조하고, binding.recyclerView와 같이 바인딩된 뷰를 사용할 수 있다.
- repositories를 observe하여 `PagingData<Repository>` 가 업데이트될 때마다 어댑터에 데이터를 전달한다.
- onStart에서 viewModel의 `getRepositories` 함수를 호출하여 데이터를 가져온다.

***
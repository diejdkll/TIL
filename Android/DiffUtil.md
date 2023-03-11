# **DiffUtil**
> - RecyclerView 어댑터에 대한 업데이트를 알리는데 사용된다.
> - RecyclerView에서 데이터의 업데이트 처리를 효율적으로 작업하기위해 개발됬다.

<br>

## **사용해야 되는 이유**
RecyclerView에 표현할 데이터를 업데이트하기 위해 주로 **notifyDataSetChanged()를** 호출한다.

notifyDataSetChanged() 는 사실상 **리스트를 싹 지우고 다시 처음부터 끝까지 객체를 하나하나 만들어 새로 렌더링하는 과정**을 거치게 된다. 때문에 **비용이 매우 크게 발생**한다. 따라서 효율적으로 동적인 리사이클러뷰를 구성하는 방법이 필요했다.

이러한 이유로 등장한 것이 바로 DiffUtil 클래스이다. 이전 데이터 상태와 현재 데이터간의 상태 차이를 계산하고, 반드시 업데이트해야 할 최소한의 데이터에 대해서만 갱신하게 된다. 데이터 업데이트 횟수를 최소한으로 가져가는 것이다.

### DiffUtil을 이용하여 데이터를 효율적으로 업데이트 할 수 있다.

<br>
<br>

## **DiffUtil**
- RecyclerView의 성능 향상을 위해 사용하는 DiffUtil은 old list와 new list를 비교하여 어떤 것이 다른지 알아낸다.
- DiffUtil이 어떤 것이 변경되었는지 알아내면 RecyclerView는 이 정보를 사용하여 변경, 추가, 제거 또는 이동된 항목만 업데이트 한다.

<br>

### DiffUtil.Callback 구현
```kotlin
class UserDiffCallback(private val oldList: List<User>, private val newList: List<User>) :
    DiffUtil.Callback() {
     override fun getOldListSize() = oldList.size

    override fun getNewListSize() = newList.size

    override fun areItemsTheSame(oldItemPosition: Int, newItemPosition: Int) =
        oldList[oldItemPosition].id == newList[newItemPosition].id

    override fun areContentsTheSame(oldItemPosition: Int, newItemPosition: Int) =
        oldList[oldItemPosition] == newList[newItemPosition]
}
```

#### 함수 기능
- **getOldListSize()** : 이전 목록의 개수를 반환한다.
- **getNewListSize()** : 새로운 목록의 개수를 반환한다.
- **areItemsTheSame(int oldItemPosition, int newItemPosition)** : 두 객체가 같은 항목인지 여부를 결정한다.
- **areContentsTheSame(int oldItemPosition, int newItemPosition)** : 두 항목의 데이터가 같은지 여부를 결정한다.   
areItemsTheSame()이 true를 반환하는 경우에만 호출된다.
- **getChangePayload(int oldItemPosition, int newItemPosition)** : 만약 areItemTheSame()이 true를 반환하고 areContentsTheSame()이 false를 반환하면 이 메서드가 호출되어 변경 내용에 대한 페이로드를 가져온다.   
해당 메소드는 추상 메소드가 아니기 때문에 꼭 오버라이드할 필요는 없다.

<br>

### RecyclerView.Adapter의 리스트 업데이트 하는 함수에 추가
```kotlin
class UserDiffAdapter : RecyclerView.Adapter<UserViewHolder>() {
    private val user = mutableListOf<User>()
    
    ...
    
    fun replaceItems(newUser: List<User>) {
        val diffCallback = UserDiffCallback(user, newUser)
        val diffResult = DiffUtil.calculateDiff(diffCallback)
        
        user.clear()
        user.addAll(newUser)
        
        diffResult.dispatchUpdatesTo(this)
    }
}
```
- calculateDiff()에서 Diff 알고리즘을 통해 변경된 아이템을 감지한다.
- Diff 계산에서 반환된 DiffResult 객체가 dispatchUpdatesTo()를 통해 Adapter로 업데이트 이벤트를 전달한다.
- 목록이 크면 이 작업에 상당한 시간이 걸릴 수 있으므로 **백그라운드 스레드에서 이 작업을 실행**하고 DiffUtil.DiffResult를 가져온 다음 기본 스레드의 RecyclerView에 적용하는 것이 좋다.

<br>

## **AsyncListDiffer**
- DiffUtil은 아이템 수가 많으면 연산에 필요한 시간이 길어질 수 있기 때문에 백그라운드 스레드에서 처리하는 것이 좋다.
- AsyncListDiffer은 내부적으로 Diff 계산을 백그라운드 스레드로 처리한 뒤 리스트 업데이트까지 해 준다.

<br>

### DiffUtil.ItemCallback 구현
```kotlin
class UserDiffItemCallback : DiffUtil.ItemCallback<User>() {
    override fun areItemsTheSame(oldItem: User, newItem: User) =
        oldItem.id == newItem.id

    override fun areContentsTheSame(oldItem: User, newItem: User) =
        oldItem == newItem
}
```

<br>

### RecyclerView Adapter에서 AsyncListDiffer를 생성해 사용
```kotlin
class UserAsyncDifferAdapter : RecyclerView.Adapter<UserViewHolder>() {
    private val asyncDiffer = AsyncListDiffer(this, UserDiffItemCallback())

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = UserViewHolder(
        ItemUserBinding.inflate(LayoutInflater.from(parent.context), parent, false)
    )

    override fun onBindViewHolder(holder: UserViewHolder, position: Int) =
        holder.bind(asyncDiffer.currentList[position])

    override fun getItemCount() = asyncDiffer.currentList.size

    fun replaceItems(newUser: List<User>) {
        asyncDiffer.submitList(newUser)
    }
}
```
- currentList로 현재 아이템을 확인하고, submitList로 리스트 데이터를 변경한다. AsyncListDiffer에서 넘어오는 currentList는 READ ONLY 리스트로 변경이 불가능하기 때문에 currentList의 아이템의 변경은 submitList()를 통해서만 가능하다.

<br>

## **ListAdapter**
- AsyncListDiffer를 더 쓰기 편하도록 랩핑한 클래스(사실상 이것만 쓴다.)
- ListAdapter는 사용자를 위해 목록을 추적하고 목록이 업데이트 될 때 어댑터에 알린다.

<br>

### ListAdapter를 사용한 RecyclerView Adapter
```kotlin
class UserListAdapter : ListAdapter<User, UserViewHolder>(diffUtil) {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = UserViewHolder(
        ItemUserBinding.inflate(LayoutInflater.from(parent.context), parent,false)
    )

    override fun onBindViewHolder(holder: UserViewHolder, position: Int) {
        holder.bind(getItem(position))
    }

    fun replaceItems(items: List<User>) {
        submitList(items)
    }

    companion object {
        val diffUtil = object: DiffUtil.ItemCallback<User>() {
            override fun areContentsTheSame(oldItem: User, newItem: User) =
                oldItem == newItem

            override fun areItemsTheSame(oldItem: User, newItem: User) =
                oldItem.name == newItem.name
        }
    }
}
// Activity or Fragment ...
adapter.submitList(list)
```

- getItemCount() 오버라이딩 메서드가 없다.

#### 함수 기능
- **getItem(position: Int)** : ListAdapter 내부 List Indexing을 할 때 활용된다.
- **getCurrentList()** : ListAdapter가 가지고 있는 리스트를 가져올 때 사용한다.
- **submitList(MutableList<T> list)** : 리스트 항목을 변경하고 싶을 때 사용한다.

***
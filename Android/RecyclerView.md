# **RecyclerView**
> - 스크롤 가능한 항목을 나타낼 때 사용되는 뷰 그룹
> - 기존에 사용하던 ListView와 비슷하지만, 정확히는 ListView의 확장판이라고 이야기할 수 있다. **ListView의 확장판이니 당연히, 대부분 RecyclerView로 대체**된 상태이다.
- **RecyclerView는 ListView보다 향상된 성능을 제공**하며, Adpater의 ViewHolder를 이용, RecyclerView의 View를 **재활용**하여 사용한다.   
- ViewHolder를 사용하는 이유는?    
-> 맨 처음 화면에 보이는 뷰 객체를 홀딩(기억)하고 있어야 하기 때문에 ViewHolder를 사용한다.

![listView vs RecyclerView](https://res.cloudinary.com/practicaldev/image/fetch/s--MUx-LV8y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/4h2wjwo0dlzkenw9ewnm.png)

### ListView 는 사용자가 스크롤 할 때마다 위에 있던 뷰는 삭제되고, 맨 아래의 뷰는 생성되길 반복하여 cost가 매우 높아지게 되는 반면,

### RecyclerView에서는 아이템의 수가 많더라도 **화면에 보이는 정도의 View만 생성**하고, **스크롤 할 때마다 삭제하지 않고 가장 아래의 아이템쪽으로 객체를 이동시켜 재사용**하게 된다.

<br/>

## **1. RecyclerView 만들기**
- 리사이클러뷰에 들어갈 각 아이템의 레이아웃을 XML로 정의
### activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".BoardActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

<br>

## **2. 모델객체 만들기**
- 코틀린 파일(.kt) 생성
- 리스트에 들어갈 데이터에 어떤 요소가 들어갈 것인지 정의해주는 것
```kotlin
// 클래스 모델 객체
class RecyclerViewItem {
    var icon: Drawable? = null
    var title: String? = null
}
```

<br>

## **3. item 만들기**
- Layout에 xml파일 생성    
    
### recyclerview_item.xml
```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="8dp"
    android:orientation="horizontal">
    <ImageView
        android:id="@+id/imageView"
        android:layout_width="60dp"
        android:layout_height="60dp"
        android:src="@mipmap/ic_launcher"/>
    <TextView
        android:id="@+id/textView"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="8dp"
        android:textSize="24dp"
        android:text="text"/>
</LinearLayout>
```

<br/>

## **4. Adapter 생성**
- recyclerView_item이 적용된 리스트뷰의 어댑터를 정의한다.

### RecyclerViewAdapter.kt
```kotlin
class RecyclerViewAdapter(
    // outer class
    var recyclerViewItem: MutableList<RecyclerViewItem>,
    var inflater: LayoutInflater,
    val context: Context
) : RecyclerView.Adapter<RecyclerViewAdapter.ViewHolder>() {

    inner class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {

        // inner class
        // 아이템 뷰의 상세 뷰 컴포넌트를 홀드한다
        val imageView: ImageView
        val textView: TextView

        init {
            imageView = itemView.findViewById(R.id.imageView)
            textView = itemView.findViewById(R.id.textView)
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = inflater.inflate(R.layout.recyclerView_item, parent, false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.imageView?.setImageDrawable(context.resources.getDrawable(
            recyclerViewItem.get(position).icon, 
            context.theme)
        )
        holder.textView.text = recyclerViewItem.get(position).title
    }

    override fun getItemCount(): Int {
        return recyclerViewItem.size
    }
}
```

#### 함수 기능
- **onCreateViewHolder()** : ViewHolder 객체를 생성하고 초기화한다.
- **onBindViewHolder()** : 데이터를 가져와 ViewHolder안의 내용을 채워준다. 
- **getItemCount()** : 총 데이터의 갯수를 반환하도록한다.

<br/>

## **5. Adapter 연결**

### **5-1. 리스트 선언**
```kotlin
// 데이터 준비
var list = arrayListOf<RecyclerViewItem>(
        ListViewItem(R.drawable.이미지, "1"),
        ListViewItem(R.drawable.이미지, "2"),
        ListViewItem(R.drawable.이미지, "3")
    )
```

### **5-2. 리스트 활용**
```kotlin
val recyclerView = findViewById<RecyclerView>(R.id.recyclerView)
// 리사이클러뷰에 어댑터 장착
recyclerView.adapter = RecyclerViewAdapter(
        list, 
        LayoutInflater.from(this), 
        this
    )
// 리사이클러뷰에 레이아웃 매니저 장착
recyclerView.layoutManager = LinearLayoutManager(this, LinearLayoutManager.VERTICAL, true)
```

<br>

## RecyclerView와 ListView의 차이점

|항목|RecyclerView|ListView|
|:---:|:---:|:---:|
|ViewHolder|ViewHolder 패턴을 이용한다|ViewHolder 패턴을 이용하지 않는다.|
|Item Layout|가로, 세로, Grid 형식 모두 지원|세로 방향만 지원|
|Item Animation|아이템 애니메이션 처리 클래스 존재|아이템 추가/제거시에 적용가능한 애니메이션이 없다.|
|Decoration|RecyclerView.ItemDecoration 객체를 사용하여 구분선을 설정해야한다.|Android:divider 속성을 이용하여 리스트에 있는 아이템을 쉽게 구분할 수 있다.|
|Adapter|데이터를 제공하기 위한 사용자 정의 구현이 필요하다.|배열/DB결과에 대한 ArrayAdapter/CursorAdapter와 같은 다양한 소스에 대한 어댑터가 존재한다.|
|Click Detection|개별 터치 이벤트를 관리하지만 클릭 처리 기능이 내장되어 있지 않다.|목록의 개별 항목에 대한 클릭 이벤트에 바인딩하기 위한 AdapterView.OnItemClickListener 인터페이스가 있다.|

***
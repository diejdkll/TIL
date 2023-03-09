# **ListView**
> - 스크롤 가능한 항목을 나타낼 때 사용되는 뷰 그룹
> - ListView에 먼저 View를 배치한 다음, 데이터가 저장된 곳에서 데이터를 View의 형식에 맞게 변환하여 가져온다.

<br/>

## **1. ListView 만들기**
- 리스트뷰에 들어갈 각 아이템의 레이아웃을 XML로 정의
### activity_main.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ListView
        android:id="@+id/listView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```
<br/>

## **2. 모델객체 만들기**
- 코틀린 파일(.kt) 생성
- 리스트에 들어갈 데이터에 어떤 요소가 들어갈 것인지 정의해주는 것
```kotlin
// 클래스 모델 객체
class ListViewItem {
    var iconDrawable: Drawable? = null
    var titleStr: String? = null
    var descStr: String? = null
}
```

<br/>

## **3. item 만들기**
- Layout에 xml파일 생성    
    
### listview_item.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/imageView1"
        android:layout_width="52dp"
        android:layout_height="52dp"
        android:layout_marginBottom="4dp"
        android:layout_marginStart="8dp"
        android:layout_marginTop="4dp" />

    <TextView
        android:id="@+id/textView1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:textColor="@color/black"
        android:textSize="20dp"
        android:background="@android:color/holo_purple" />

    <TextView
        android:id="@+id/textView2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="2"
        android:textColor="@color/black"
        android:textSize="20dp"
        android:background="@android:color/holo_blue_light" />

</LinearLayout>
```

<br/>

## **4. Adapter 생성**
- listView_item이 적용된 리스트뷰의 어댑터를 정의한다.

### ListViewAdapter.kt
```kotlin
class ListViewAdapter(
    val listViewitem: MutableList<ListViewItem>,
    val layoutInflater: LayoutInflater,
    val context: Context
) : BaseAdapter() {

    override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {
    }

    override fun getItem(position: Int): Any {
    }

    override fun getItemId(position: Int): Long {
    }

    override fun getCount(): Int {
    }
}
```

#### 함수 기능
- **getView()** : position에 위치한 데이터를 화면에 출력하는데 사용될 View를 리턴
- **getItem()** : 지정한 위치(position)에 있는 데이터 리턴
- **getItemId()** : 지정한 위치(position)에 있는 데이터와 관계된 아이템(row)의 ID를 리턴 
- **getCount()** : Adapter에 사용되는 데이터의 개수를 리턴

<br/>

### **4-1. getView** 
```kotlin
override fun getView(position: Int, convertView: View?, parent: ViewGroup?): View {

        // LayoutInflater는 item을 Adapter에서 사용할 View로 부풀려주는(inflate) 역할을 한다.
        val view: View = inflater.inflate(R.layout.listview_item, null)

        val iconImageView = view!!.findViewById(R.id.imageView1) as ImageView
        val titleTextView = view.findViewById(R.id.textView1) as TextView
        val descTextView = view.findViewById(R.id.textView2) as TextView
      
        // Data Set(listViewItemList)에서 position에 위치한 데이터 참조 획득
        val list = listViewitem[position]
 
        // 아이템 내 각 위젯에 데이터 반영
        iconImageView.setImageDrawable(listViewItem.icon)
        titleTextView.setText(listViewItem.title)
        descTextView.setText(listViewItem.desc)

        return view
    }
```

### **4-2. getItem, getItemId, getCount**
```kotlin
override fun getItem(position: Int): Any {
        return listViewItem.get(position)
    }
override fun getItemId(position: Int): Long {
        return position.toLong()
    }
override fun getCount(): Int {
        return listViewItem.size
    }
```

<br/>

## **5. Adapter 연결**

### **5-1. 리스트 선언**
```kotlin
// 데이터 준비
var list = arrayListOf<ListViewItem>(
        ListViewItem(R.drawable.이미지, "1", "01"),
        ListViewItem(R.drawable.이미지, "2", "02"),
        ListViewItem(R.drawable.이미지, "3", "03")
    )
```

### **5-2. 리스트 활용**
```kotlin
// Adapter 준비
val adapter = ListViewAdapter(
            list,
            LayoutInflater.from(this),
            this
        )

// Adapter 장착 방법
val listview = findViewById<ListView>(R.id.listView)
        listview.adapter = adapter
```

<br/>

## **ListView의 단점**
- 아이템의 애니매이션 처리가 쉽지 않다.
- 리스트에는 한 개 이상의 View가 필요한 경우가 있지만 커스텀으로 작업하기 쉽지 않다.
- ViewHolder 패턴을 강제적으로 사용하지 않으므로 고비용의 findViewById가 매번 호출될 수 있다.

### 위와 같은 이유로 **RecyclerView**사용이 권장된다.

***
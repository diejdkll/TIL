# **Room**
> - 안드로이드에서 SQLite 데이터베이스를 쉽게 사용할 수 있도록 도와주는 라이브러리

<br>

## **Room 구성요소**
### **Entity**
- Room의 데이터베이스 테이블을 나타내는 클래스
- 각각의 Entity 클래스는 데이터베이스 테이블의 열(Column)을 멤버 변수로 가지며, 주석을 통해 테이블 이름과 열 속성을 정의할 수 있다.

<br>

### **DAO(Data Access Object)**
- 데이터베이스와 상호작용하기 위한 메서드를 포함하는 인터페이스
- 데이터베이스의 CRUD(Create, Read, Update, Delete) 작업을 정의하고 실행하는 역할을 수행한다.

<br>

### **Database**
- Room 데이터베이스를 나타내는 추상 클래스
- 데이터베이스 버전, 엔티티 클래스, DAO 인터페이스 등을 정의할 수 있다.
- 실제 데이터베이스 인스턴스를 나타내는 객체는 이 클래스를 상속한 클래스에서 생성된다.

<br>

## **Room 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    implementation "androidx.room:room-runtime:2.5.0"
    annotationProcessor "androidx.room:room-compiler:2.5.0"
}
```
- 최신버전은 [*android developers*](https://developer.android.com/training/data-storage/room?hl=ko)에서 확인 가능

<br>

## **Room 사용하기**

### 1. Entity 클래스 생성
```kotlin
@Entity(tableName = "users")
data class User(
    @PrimaryKey val id: Int,
    val name: String,
    val age: Int
)
```
- 반드시 PrimaryKey가 있어야 한다.
- 기본적으로 클래스 이름이 테이블명이지만 따로 지정은 @Entity(tableName = " ")을 사용한다.

<br>

### 2. DAO 인터페이스 생성
```kotlin
@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun getUsers(): List<User>

    @Query("SELECT * FROM users WHERE id = :userId")
    fun getUserById(userId: Int): User?

    @Insert
    fun insertUser(user: User)

    @Delete
    fun deleteUser(user: User)
}
```
- 앱 데이터베이스의 데이터와 상호작용하는 메소드를 하나 이상 정의해야한다.

<br>

### 3. Database 클래스 생성
```kotlin
@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao

    companion object {
        private var instance: AppDatabase? = null

        fun getInstance(context: Context): AppDatabase {
            if (instance == null) {
                instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "my-database"
                ).build()
            }
            return instance as AppDatabase
        }
    }
}
```
- version 은 Entity 의 구조 변경해야 하는 일이 생겼을 때 이전 구조와 현재 구조를 구분해주는 역할이다.

<br>

### 4. 데이터베이스 사용하기
```kotlin
// 데이터베이스 인스턴스 생성
val database = AppDatabase.getInstance(context)

// UserDao 인터페이스를 통해 데이터베이스 작업 수행
val userDao = database.userDao()

// 사용자 추가
val user = User(1, "John Doe", 30)
userDao.insertUser(user)

// 사용자 조회
val retrievedUser = userDao.getUserById(1)
Log.d("User", "Name: ${retrievedUser?.name}, Age: ${retrievedUser?.age}")

// 사용자 삭제
userDao.deleteUser(user)
```

***
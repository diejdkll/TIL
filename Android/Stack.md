# **Activity Stack**

## **Stack**
> - **Activity가 순서대로 저장되어 관리되는 공간**
> - **후입 선출 구조(LIFO)**

***

## **Activity Stack 확인**
![stack 확인](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbTN1go%2FbtqB1mT770W%2FeGn5sjGhvsQ5YFj24Uij21%2Fimg.png)
> - 안드로이드 스튜디오에서 Tools > Layout Inspector > 실행 시킨 avd or 모바일 기기
선택

***

## **Activity Stack 관리**
1. **LaunchMode**로 관리   
2. **Intent Flag**로 관리

## **1. LaunchMode로 관리**
> - Activity를 어떠한 방식으로 스택에 쌓을 건지 LaunchMode를 통해 기술할 수 있다.
> - Manifest의 activity 태그 내에서 설정할 수 있다.

### **standard**
![standard](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbwXX4M%2FbtrawBQT47h%2FV3jAHdFQx4haGNZMVVZaHK%2Fimg.png)
```xml
<activity android:launchMode="standard"
``` 
- Default로 설정되는 모드
- 별도의 Task를 생성하지 않고 해당 Task에 계속 쌓아나감

### **singleTop**
![singleTop](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcTW5H9%2Fbtrav64OGCF%2F445pKlrfXufudPJb2tWUGK%2Fimg.png)
```xml
<activity android:launchMode="singleTop"
```
- Task의 Top에 생성하려는 Activity가 존재하는 경우 새로운 Activity를 Top에 올리지않고 기존의 Activity를 재사용

### **singleTask**
![singleTask](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FCgKfZ%2Fbtrax7h8OiD%2FkDj8L1VYK7NxZq0Jxo3YA1%2Fimg.png)
```xml
<activity android:launchMode="singleTask"
```
- RootActivity로만 존재하며 하나의 인스턴스만 생성 가능
- 다른 Activity 실행시 동일 Task내에서 실행 가능

### **singleInstance**
![singleInstance](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdadU9P%2Fbtraoa1oAYd%2FXa3Zoof9229KwCKzK8Le80%2Fimg.png)
```xml
<activity android:launchMode="singleInstance"
```
- RootActivity로만 존재하며 하나의 인스턴스만 생성 가능
- Task내에 해당 Activity 하나만 속할 수 있어 다른 Activity를 실행시키면 새로운 Task가 생성

***

## **2. Intent Flag로 관리**

> - Activity나 service 같은 컴포넌트를 Intent를 통해 실행
> - **flag 값을 주어 액티비티를 어떠한 방식으로 실행시킬 지 결정**

### **Intent.addFlags()**
> - 기존에 적용된 flag를 삭제하고 다시 설정
### **Intent.setFlags()**
> - flag를 추가함

-> ex) 사용예시
```kotlin
val intent = Intent(this, MainActivity::class.java)
intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK or FLAG_ACTIVITY_NEW_TASK)
startActivity(intent)
```

### **FLAG_ACTIVITY_BROUGHT_TO_FRONT**
- 시스템에서 설정하는 값
- 같은 Task에 Activity가 있을 경우에 Activity 실행모드가 singleTask이면 자동으로 설정

### **FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET**
- Task가 리셋될 때 flag가 사용된 Activity부터 위의 Activity가 **모두 삭제**   
-> ex) ABCD -> B call - > AB

### **FLAG_ACTIVITY_RESET_TASK_IF_NEEDED**
- 시스템에 의해 홈에서 사용자에 의해 백그라운드에 있던 Task가 포그라운드로 전환될때 항상 붙음

### **FLAG_ACTIVITY_CLEAR_TOP**
- 호출한 Activity가 현재 stack에서 실행 중이면 해당 Activity의 스택을 제거하고 새로운 Activity를 top으로 설정

### **FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS**
- **최근 실행목록에 표시가 되지 않게 됩니다.**

### **FLAG_ACTIVITY_FORWARD_RESULT**
- **startActivityForResult를 이용하여서 Activity를 호출할 경우에, 호출하는 쪽이 아닌 한번 더 거쳐서 Result를 받고 싶을 경우에 사용**   
-> ex) ABC -> C에서 setResult를 설정 -> B에서 finish -> **A에서는 C의 값을 받을수 있음**

### **FLAG_ACTIVITY_LAUNCHED_FROM_HISTORY**
- 시스템에 의해서 설정되는 flag
- **최근실행목록에서 실행되게 되면 자동으로 설정**

### **FLAG_ACTIVITY_MULTIPLE_TASK**
- FLAG_ACTIVITY_NEW_TASK와 함께 사용
- 두개의 flag를 동시에 사용할 경우 새로운 Task는 재활용되지 않고 무조건 새로 생성되며 피호출되는 Activity는 이 새로운 Task의 최상위 Activity가 됩니다.

### **FLAG_ACTIVITY_NEW_TASK**
- singleTask와 유사
- **새로운 Task를 생성하여 그 Task안에 Activity를 추가할때 사용**
- 이미 실행 중인 작업이 있다면 그 작업을 마지막으로 포그라운드로 이동

### **FLAG_ACTIVITY_NO_ANIMATION**
- Activity 전환시 애니메이션을 무시

### **FLAG_ACTIVITY_NO_HISTORY**
- Activity가 스택에 존재하지 않게 되도록 합니다.
- 로딩화면등에 이용

### **FLAG_ACTIVITY_NO_USER_ACTION**
- 자동적으로 Activity가 호출될 경우에 자동 호출되는 onUserLeaveHint()가 실행되는 것을 차단
- onUserLeaveHint() 콜백 메서드는 **어플리케이션 사용중에 전화가 온다거나 하는 등의 사용자의 액션없이 Activity가 실행/전환되는 경우에 호출되는 메서드**

### **FLAG_ACTIVITY_REORDER_TO_FRONT**
- 호출하려는 Activity가 stack에 존재하면 최상위로 올려줌   
-> ex) ABCDE -> C call -> ABDEC

### **FLAG_ACTIVITY_SINGLE_TOP**
- signTop과 유사
- 호출되는 Activity가 최상위에 존재할 경우에는 해당 Activity를 다시 생성하지 않고, 존재하던 Activity를 다시 사용   
-> ex) ABC -> C call -> ABC
- **onPause() -> onNewIntent() -> onResume()**

***

## **Task**
![Task](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F99D1E13E5CA162A311)
> - **사용자의 입장에서 하나의 어플리케이션 처럼 보여지는 Activity들의 집합**   
- 하나의 Task에는 Activity 집합을 열린 순서대로 정렬해놓는 **Back Stack**이 존재
- Task 는 Activity를 **Stack형태**로 관리
    - Root Activity : Task를 시작한 Activity
    - Top Activity : Task Stack의 가장 상위 Activity

***

## **Back Stack**
![Back Stack](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FyQtX9%2FbtqCKgLy4lz%2F2h7tBqNOYbF23rd2PK5VVk%2Fimg.png)
> - Stack의 성질을 가짐
- 뒤로가기를 누를 경우 Top Activity를 제거(pop)
- Task의 모든 Activity는 백그라운드에 있는 동안 모두 중지되지만, Task의 Back Stack은 그대로 유지

***

## **Affinity**
-> ex) 사용예시
```xml
<activity
    android:taskAffinity="string"/>
```
> - Activity 실행 시 신규 Task를 만들 경우 어느 Task에 속할 지 지정
> - Manifest의 activity 태그 내에서 설정할 수 있다.
- Activity들은 하나의 Affinity를 가지고 있음
- 값을 명시하지 않을 시 디폴트로 앱의 패키지명   
    -> 즉 기본적으로 한 Application안의 모든 Activity들은 같은 Affinity를 가지고 있다.
- **2가지 상황에서 작용**
    - Activity를 실행한 인텐트에 **FLAG_ACTIVITY_NEW_TASK**플래그가 포함되어 있을 경우
    - Activity의 **allowTaskReparenting** 속성이 true로 설정되어 있을 경우   

***
# **LifeCycle**
> - Life + cycle의 합성어, 삶의 순환   
> -> 탄생하고 성장하여 죽음에 이르기까지의 과정
***

## **Fragment LifeCycle**

![Fragment LifeCycle](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcDyVCU%2Fbtq9CtTEtoA%2FkpOuUqYRAw8aVmbyKT7jpk%2Fimg.png)

### onAttach()
> - Fragment가 Activity에 붙을때 호출됨
- 아직 완벽하게 생성된 상태가 아님
- 인자로 Context가 주어짐
### onCreate()
> - Fragment가 Activity의 호출을 받아 생성됨
- **UI 초기화 불가능**
- Bunddle로 Activity로부터 데이터가 넘어옴
### onCreateView()
> - Fragment가 자신의 UI를 처음 그리기 위해 호출됨
- **Layout을 Inflate하는 곳**   
-> Inflate : xml에 써져 있는 view의 정의를 실제 view 객체로 만드는 역할
- 다른 매개 변수인 savedInstanceState는 Bundle 객체로 Fragment가 재개되는 경우 이전 상태에 대한 데이터를 제공
### onViewCreate()
> - Fragment의 View(레이아웃)이 생성된 후에 호출됨
- onCreateView()에서 반환된 View객체는 onViewCreated() 파라미터로 전달됨
- View의 초기값 설정, LiveData 옵저빙, RecyclerView 또는 ViewPager에 사용될 Adepter 세팅등을 함
### onViewStateRestored()
> - 저장해둔 모든 State 값이 Fragment의 View의 계층구조에 복원됬을때 호출됨   
-> CheckBox가 현재 체크되어 있는지의 상태 값 체크
- View lifecycle owner : **INITIALIZED → CREATED 변경**
### onStart()
> - Fragment가 사용자에게 보여질 수 있을 때 호출됨
- 해당 Fragment가 attach 되어 있는 Activity의 OnStart()시점과 유사
- Fragment의 child FragmentManager을 통해 FragmentTransaction을 안전하게 수행가능
- View lifecycle owner : **CREATED → STARTED 변경**
### onResume()
> - Fragment가 보이는 상태에서 사용자와 상호작용할 수 있을 때 이 호출됨
- Fragment가 가려지기 전까지 이 상태가 유지됨
### onPause()
> - 사용자가 Fragment를 떠나기 시작했지만 Fragment는 여전히 visible일때 onPause()가 호출됨
- UI 관련 처리를 정지, 중요한 데이터 저장
### onStop()
> - Fragment가 더 이상 화면에 보여지지 않게 되면 호출됨   
> - 부모 Activity나 Fragment가 중단됐을 뿐만 아니라, 부모 Activity나 Fragment의 상태가 저장될 때도 호출
- API 28버전을 기점으로 onSaveInstanceState() 함수와 onStop() 함수 호출 순서가 달라짐, 따라서 onStop()이 **FragmentTransaction을 안전하게 수행하는 마지막 지점**이 됨
![호출 순서 변경](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbaA64V%2FbtrqAcgcRE5%2FLKM8K1MQdXoTiv13dcQr21%2Fimg.png)

### onSaveInstanceState()
> - Fragment의 상태 값(State)이 저장된 이후에 호출됨
### onDestroyView()
> - 모든 Exit Animation, Transaction이 완료되고 Fragment가 화면으로부터 벗어났을 경우 호출됨
- 가비지 컬렉터에 의해 수거될 수 있도록 **Fragment View에 대한 모든 참조가 제거되어야 함**
### onDestroy()
> - view가 제거된 후 Fragment가 완전히 소멸되기 전에 호출됨
- Fragment Lifecycle의 끝을 알림
### onDetach()
> - Fragment가 Activity로부터 해제되어질때 호출됨
***
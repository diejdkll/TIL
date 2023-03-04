# **LifeCycle**
> - Life + cycle의 합성어, 삶의 순환   
> -> 탄생하고 성장하여 죽음에 이르기까지의 과정
***

## **Activity LifeCycle**

![Activity LifeCycle](https://velog.velcdn.com/images%2Fits-mingyu%2Fpost%2Fef359ab0-12d5-442c-8349-60119eecfe25%2Fimage.png)

### onCreate()
> - Activity가 생성되면 가장 먼저 호출됨
- 화면 Layout 정의, View 생성, Databinding 등은 이곳에 구현
- **단 한번만 실행**   
-> 따라서 Activity 최초 실행에 해야하는 작업을 수행하기 적합
### onStart()
> - Activity가 화면에 표시되기 직전에 호출됨
- 화면에 진입할 때마다 실행되어야 하는 작업을 이곳에 구현
- 매우 빠르게 완료됨
- Activity을 포그라운드에 보내 상호작용 할 수 있게 준비   
-> 포그라운드 : 사용자 눈에 보일 때 또는 사용중일 때
### onResume()
> - Activity가 화면에 보여진 직후 호출됨
- 현재 Activity가 사용자에게 포커스인 되있는 상태
- 앱이 사용자와 **상호작용**
### onPause()
> - Activity가 화면에 보여지지 않은 직후에 호출됨   
-> 이벤트가 앱 실행 방해, 반투명한 Activity(대화상자)
- 현재 Activity가 사용자에게 포커스아웃 되있는 상태
- 다른 Activity가 호출되기 전에 실행되기 때문에 **무거운 작업을 수행하지 않도록 주의**해야함
- 사용자가 Activity를 떠나는 것을 나타내는 첫번째 신호   
-> Activity가 포그라운드에 있지 않다는 신호
### onStop()
> - Activity가 다른 Activity에 의해 100% 가려질 때 호출됨   
-> 새로 시작된 Activity가 화면의 전체를 가릴경우
- **필요하지 않은 리소스를 해제하거나 조정**해야 함   
-> 애니메이션 일시중지, 세밀한 위치 -> 대략적인 위치
- 이 상태에서 Activity가 다시 시작되면 onRestart()가 호출됨
### onDestroy()
> - Activity가 소멸되기 전에 호출됨   
-> 활동이 종료되는 경우(finish(), Activity 종료),
일시적으로 Activity를 소멸 시키는 경우(기기 회전, 멀티 윈도우 모드)
- onStop()에서 해제되지 않은 **모든 리소스를 해제**해야 함
- Activity가 수신하는 마지막 호출
### onRestart()
> - onStop()이 호출된 이후 다시 기존 Activity로 돌아올때 호출 됨
- onRestart()가 호출된 이후 onStart()가 호출됨
***


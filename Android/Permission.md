# **Permission**
> - Permission은 AndroidManifest.xml에 들어가는 설정이다.
> - (API 23)부터는 중요한 권한들을 분류하여 **설치 시점이 아니라 앱을 실행했을 때** 사용자로부터 권한을 부여받도록 변경

<br>

### 일반권한
- **일반 권한(Normal Permission)** : 앱을 설치할 때 사용자에게 권한이 부여되어야 함을 알려주고 설치할 것인지 물어본다.
- 사용자가 부여할 권한들을 보고 수락하면 앱이 설치되고 앱에는 해당 권한들이 부여된다.

### 위험권한
- **위험 권한(Dangerous Permission)** : 앱 실행 시 사용자에게 권한을 부여할 것인지 물어본다.
- 사용자가 권한을 부여하지 않으면 해당 기능은 동작하지 않는다.
- 앱을 설치했다고 하더라도 권한에 따라 실행할 수 있는 기능에 제약이 생기는 것

<br>

## **권한종류[(Permission)](https://developer.android.com/reference/android/Manifest.permission)**

|<center>Permission</center>|<center>내용</center>|
|:---|:---|
|ACCESS_FINE_LOCATION|정확한 위치 정보 액세스|
|ACCESS_NETWORK_STATE|네트워크에 대한 정보 액세스|
|ACCESS_WIFI_STATE|와이파이 네트워크에 대한 정보 액세스|
|BATTERY_STATS|배터리 통계 수집|
|BLUETOOTH|연결된 블루투스 장치에 연결|
|BLUETOOTH_ADMIN|블루투스 장치를 검색하고 페어링|
|CALL_PHONE|다이얼 UI를 거치치 않고 전화를 시작|
|CAMERA|카메라 장치에 액세스|
|INTERNET|네트워크 연결|
|READ_CONTACTS|사용자의 연락처 데이터 읽기|
|READ_EXTERNAL_STORAGE|외부 저장소에서 파일 읽기|
|READ_PHONE_STATE|장치의 전화번호, 네트워크 정보, 진행 중인 통화 상태 등 전화 상태에 대한 읽기|
|READ_SMS|SMS 메시지 읽기|
|RECEIVE_BOOT_COMPLETED|부팅 완료 시 수행|
|RECORD_AUDIO|오디오 녹음|
|SEND_SMS|메시지 발신|
|VIBRATE|진동 울리기|
|WRITE_CONTACTS|사용자의 연락처 데이터 읽기|
|WRITE_EXTERNAL_STORAGE|외부 저장소에 파일 쓰기|

<br>

## **권한 요청**
### 1. manifest 파일에 권한을 사용할 것을 명시해야 한다.
```xml
<manifest ...>
    <uses-permission android:name="android.permission.CAMERA"/>
    <application ...>
        ...
    </application>
</manifest>
```
- manifest.xml 파일에 manifest 태그 하위, application 태그와 동일 뎁스에 위 코드를 넣으면 된다.

<br>

권한이 없는 경우에 해당 앱을 설치하지 못하게 하려면 다음과 같이 required 옵션을 주면 된다.
```xml
<manifest ...>
    <application>
        ...
    </application>
    <uses-feature android:name="android.hardware.camera"
                  android:required="true" />
<manifest>
```

<br>

권한이 없지만 권한을 사용하는 동작만 실행하지 못하게 하려면 required 옵션을 false로 주고, 다음과 같이 하면 된다
```kotlin
// 기기에 전면 카메라가 있는지 확인하는 코드
if (applicationContext.packageManager.hasSystemFeature(
        PackageManager.FEATURE_CAMERA_FRONT)) {
    // 전면 카메라를 이용한 기능 수행
} else {
    // 전면 카메라가 필요한 기능 수행 못하도록 처리
}
```

<br>

### 2. 앱의 특정 작업과 일치하는 UX를 설계한다.

<br>

### 3. 사용자가 (권한이 필요한) 기능을 동작시킬 것을 기다린다.
- **권한은 사용자 사용 흐름 중 늦게 요청되는 것이 권장된다.**

<br>

### 4. 이미 이전에 권한을 허가했는지 확인한다.
- 허가 했다면 6-1번으로, 허가한 적이 없다면 5번으로.
```kotlin
val permission = ContextCompat.checkSelfPermission(this, android.Manifest.permission.READ_EXTERNAL_STORAGE)

when(permission) {

	PERMISSION_GRANTED -> {
    	// 해당 권한이 부여됨
    }
    PERMISSION_DENIED -> {
    	// 해당 권한이 거부됨
    }
}
```

<br>

### 5. 권한을 요청하고, 권한이 부여됐는지 거부됐는지 확인한다. 
- 부여됐다면 6-1로, 부여되지 않았다면 6-2번으로.
```kotlin
private val permissionList = Manifest.permission.ACCESS_FINE_LOCATION

private  val requestPermission = registerForActivityResult(
    ActivityResultContracts.RequestPermission()) {
    when(it) {
        true -> { 
            Toast.makeText(this,"권한 허가",Toast.LENGTH_SHORT).show()
            }
        false -> {
            Toast.makeText(this,"권한 거부",Toast.LENGTH_SHORT).show()
        }
    }
}


override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    val request_btn = findViewById<Button>(R.id.request_permission_btn)

    request_btn.setOnClickListener {
        requestPermission.launch(permissionList)
    }
}
```

<br>

### 6-1. 권한이 부여됐다면 주어진 권한 내에서 작업을 수행한다.
- 권한이 주어졌어도 카메라나 마이크와 같은 민감한 데이터나 하드웨어에 엑세스 할 때는 앱에 지속적으로 알림을 표시해야 한다.

<br>

### 6-2. 권한이 부여되지 않아도 사용자가 권한이 필요한 기능이 아닌 다른 기능들은 정상적으로 사용할 수 있도록 사용성을 제공한다.
- 사용자에게 앱에 필요한 권한이 없어 기능이 제한된 UI의 특정 부분을 강조 표시하고, 권한이 없기 때문에 어떤 기능을 사용할 수 없는지 정확하게 설명한다.
- 또한 권한 요청을 거부했다는 이유로 권한이 필요없는 다른 기능들도 사용하지 못하게 하면 안되고, 전체 화면 경고 메시지를 표지하지 않아야 한다.

<br>

## Android11(API 30) 부터 **달라진 점**
- Android11(API 30) 부터는 사용자가 앱이 설치된 동안 특정 권한을 두 번 이상 거부한다면 앱에서 그 권한을 다시 요청해도 사용자에게 권한 요청 대화상자가 표시되지 않는다. (다시 묻지 않음과 동일)   
    -> 이 경우 사용자가 해당 권한을 허가하려면 설정 - 애플리케이션 - 해당 앱에서 권한을 직접 켜줘야 한다.

<br>

-  또한 Android11(API 30)부터 위치, 마이크, 카메라와 관련된 권한을 요청하면 **이번만 허용**이라는 옵션이 주어진다.  
![권한](https://bluedot.io/wp-content/uploads/2019/09/Droid-10-4-1-1.png)

    - 앱을 사용하는 동안 데이터에 엑세스 할 수 있다.
    - 앱을 백그라운드에 보내면 짧은 시간 동안 데이터에 계속 엑세스 할 수 있다.
    - 앱을 사용하는 동안 포그라운드 서비스가 실행되고 사용자가 앱을 백그라운드로 이동하면 포그라운드 서비스가 중지될 때까지 데이터에 계속 엑세스 할 수 있다.
    - 사용자가 시스템 설정 등에서 이번만 허용 권한을 취소하면 앱에서 데이터에 엑세스할 수 없다. 또한 앱의 프로세스가 종료된다.

***

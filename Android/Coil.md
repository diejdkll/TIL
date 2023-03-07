# Coil

> - **Co**routine **I**mage **L**oader의 약자
> - Kotlin Coroutines(코루틴)으로 만들어진 가벼운 Android 백앤드 이미지 로딩 라이브러리
> - 코루틴자체가 내장이 되어 있어 코루틴 라이브러리를 별도로 설치하지 않아도 되는 장점

<br/>

## 굳이 Glide, Picasso를 두고 써야할까?
### 이미 성능과 신뢰성이 검증되고 확실하게 자리잡은 이미지 라이브러리가 많다.
### 하지만 그럼에도 불구하고 Coil에는 여러 장점이 있다.

<br/>

### 장점
- Glide, Picasso보다 상대적으로 가볍다.
- 빠르다 : Coil은 메모리와 디스크의 캐싱, 메모리의 이미지 다운 샘플링, Bitmap 재사용, 일시정지/취소의 자동화 등등 수 많은 최적화 작업을 수행한다.
- 가볍다 : Coil은 최대 2000개의 method들을 APK에 추가합니다(이미 OkHttp와 Coroutines을 사용중인 앱에 한하여), 이는 Picasso 비슷한 수준이며 Glide와 Fresco보다는 적다.
- 사용하기 쉽다 : Coil API는 심플함과 최소한의 boilerplate를 위하여 Kotlin의 기능을 활용한다.
- 현대적이다 : Coil은 Kotlin 우선이며 Coroutines, OkHttp, Okio, AndroidX Lifecycles등의 최신 라이브러리를 사용한다.

#### **하지만 단점도 명확하다.**

### 단점
- 버전이 아직 정식버전이라고는 할 수 없어서 신뢰성이 낮아 실무에서 사용하는 기업이 많이 없다.
- 최소 Android SDK 버전 14부터 지원을 하나 최신버전에서 사용을 하는 것을 권장한다.(AndroidX)
- Kotlin을 모른다면 사용하기 어렵다.
- 일부 기능 사용을 위해 Coroutines이 무엇인지 개념적으로 어느정도 알아야 한다.

***

## **Coil 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    implementation("io.coil-kt:coil:2.2.2")
}
```
- 최신버전은 [*Coil Github*](https://github.com/coil-kt/coil)에서 확인 가능

<br/>

## **Coil 사용하기**

1. 이미지 로드하기
```kotlin
// URL
imageView.load("<https://imageofworld.com/image/phonewhite.png>")
// Resource
imageView.load(R.drawable.image)
// File
imageView.load(File("/path/to/image.jpg"))
// And more...
```
- 특별한 옵션 없이 단순히 이미지만 로드하는 경우

<br/>

2. 이미지 간단히 편집하기(후처리)
```kotlin
imageView.load("<https://imageofworld.com/image/phonewhite.png>") {
    crossfade(true)
    placeholder(R.drawable.place_holder_image)
    transformations(CircleCropTransformation())
}
```
- 이미지 간단히 편집하는 경우
#### 함수 기능
- **CircleCropTransformation()** : 이미지를 원형으로 잘라주는 함수
- **RoundedCornersTransformation()** : 이미지의 가장자리를 둥글게 처리해주는 함수
- **BlurTransformation()** : 이미지를 흐릿하게 보이게 하는(Gaussian Blur)를 적용해주는 함수
- **GrayscaleTransformation()** : 이미지에 음영처리(Greyscale)효과를 씌워주는 함수

***
# **Picasso**

> - 외부로부터 이미지를 불러와야 할 경우 유용하게 사용할 수 있는 라이브러리이다.
> - 매우 간단한 코드 몇 줄로 이미지 로딩, 메모리 & 디스크 캐싱, 변형(Transforming)을 가능하게 한다.
> - 비슷한 [*글라이드(Glide)*](https://github.com/diejdkll/TIL/blob/main/Android/Glide.md)라는 라이브러리도 있으며, 사용법 또한 거의 같다.

<br />

## **Picasso 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    implementation 'com.squareup.picasso:picasso:2.8'
}
```
- 최신버전은 [*Picasso Github*](https://github.com/square/picasso)에서 확인 가능
### 네트워크에서 이미지를 가져온다면 Manifest.xml 파일에 인터넷 권한 추가
```xml
<uses-permission android:name="android.permission.INTERNET" />
```

***

## **Picasso 사용하기**

1. 이미지 로드하기
```kotlin
Picassso.get(View의 Context)
    .load(로드할 이미지)
    .into(ImageView)
```
- 특별한 옵션 없이 단순히 이미지만 로드하는 경우
#### 함수 기능
- **get()** : View,Fragment 혹은 Activity로부터 Context를 가져오는 함수
- **load()** : 이미지를 로드하는 함수. 다양한 방법으로 이미지를 로드할 수 있다.(Bitmap, Drawable, Uri, String, File, ResourceId, ByteArray)
- **into()** : 이미지를 보여줄 View를 지정하는 함수

<br/>

2. 이미지 에러처리
```kotlin
Picassso.get(View의 Context)
    .load(로드할 이미지)
    .placeholder(R.drawable.picasso_example_place_holder)
    .error(R.drawable.picasso_example_error)
    .into(ImageView)
```
- 이미지 로드가 실패했을 경우
#### 함수 기능
- **placeholder()** : Picasso로 이미지 로딩을 시작하기 전에 보여줄 이미지를 선정하는 함수
- **error()** : 리소스를 불러오다가 에러 발생 시 보여줄 이미지 설정하는 함수

<br/>

3. 이미지 크기 변경
```kotlin
Picassso.get(View의 Context)
    .load(로드할 이미지)
    .resize(horizontal, vertical)
    .centerCrop()
    .centerInside()
    .rotate(90f)
    .into(ImageView)
```
- 이미지 크기를 변경해주는 함수
#### 함수 기능
- **resize()** : 이미지 사이즈를 조절해주는 함수
- **centerCrop()** : 실제 이미지가 이미지뷰의 사이즈보다 클 때, 이미지뷰의 크기에 맞춰 이미지 중간부분을 잘라서 스케일링하는 함수
- **centerInside()** : 이미지뷰의 크기와 같거나, 작도록 크기를 조정해준다, 이미지를 완전히 채우지 못할수 있다.
- **rotate()** : 이미지를 회전시켜주는 함수

<br/>

## Glide와 Picasso의 차이점

|항목|Glide|Picasso|
|:---:|:---:|:---:|
|속성 및 옵션|Picasso에 비해 많음|Glide에 비해 적음|
|메모리 사용량|이미지를 리사이징 하여 적은 메모리 사용|원본 이미지 사용으로 많은 메모리 사용|
|캐싱|이미지를 리사이징하여 저장|원본 이미지를 저장|
|속도|네트워크상에서 리사이즈하여 로딩하므로 느리지만 캐시 이미지 로딩 속도는 빠름|원본 이미지 로딩은 빠르나 캐시 이미지 로딩 속도는 느림|
|품질|낮은 비트 사용으로 품질 낮음|높은 비트 사용으로 품질 높음|
|GIF|지원|미지원|

***
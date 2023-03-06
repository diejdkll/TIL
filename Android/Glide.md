# **Glide**

> - **Android에서 추천**하는 이미지 로드 라이브러리
> - 이미지 로드 라이브러리는 보다 쉽게 이미지를 로드하고 더 나아가서는 이미지 캐싱까지 해주는 편리한 라이브러리이다.
> - 사용법이 간단하고 성능이 좋다는 것이 큰 장점

***

## **사용해야 되는 이유**
### 안드로이드 개발을 하면서 이미지를 띄우고자 할때, 여러 방법이 있다.

1. 안드로이드 프로젝트의 drawable폴더의 리소스를 보여주는 경우
2. 안드로이드 디바이스 안에 저장되어있는 사진(갤러리 등)을 보여주는 경우
3. 서버에 있는 이미지(이미지 URL)를 보여주는 경우

### Glide는 위의 3가지의 방법의 이미지를 빠르고 효율적으로 불러올 수 있게 도와준다.

***

## **Glide 사전 준비**
### build.gradle(Module:app)에서 의존성 추가
```
dependencies{
	...
    implementation 'com.github.bumptech.glide:glide:4.15.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.15.0'
}
```
- 최신버전은 [Glide Github](https://github.com/bumptech/glide)에서 확인 가능
### 네트워크에서 이미지를 가져온다면 Manifest.xml 파일에 인터넷 권한 추가
```xml
<uses-permission android:name="android.permission.INTERNET" />
```

***

## **Glide 사용하기**

1. 이미지 로드하기
```kotlin
Glide.with(View의 Context)
    .load(로드할 이미지)
    .into(ImageView)
```
- 특별한 옵션 없이 단순히 이미지만 로드하는 경우
#### 함수 기능
- with() : View,Fragment 혹은 Activity로부터 Context를 가져오는 함수
- load(): 이미지를 로드하는 함수. 다양한 방법으로 이미지를 로드할 수 있다.(Bitmap, Drawable, Uri, String, File, ResourceId, ByteArray)
- into(): 이미지를 보여줄 View를 지정하는 함수

***

2. 에러처리, 후가공
```kotlin
Glide.with(this)
    .load(R.drawable.glide_example)
    .placeholder(R.drawable.glide_example_place_holder)
    .error(R.drawable.glide_example_error)
    .fallback(R.drawable.glide_example_null)
    .into(imageView)
```
- 에러상황에 대한 대처, 후가공이 필요할 경우
#### 함수 기능
- placeholder() : Glide로 이미지 로딩을 시작하기 전에 보여줄 이미지를 선정하는 함수
- error() : 리소스를 불러오다가 에러 발생 시 보여줄 이미지 설정하는 함수
- fallback() : load할 url이 null인 경우 보여줄 이미지를 설정하는 함수

***

3. 메모리, 디스크 캐싱
```kotlin
Glide.with(this)
    .load(R.drawable.glide_example)
    .skipMemoryCache(true)
    .diskCacheStrategy(DiskCacheStrategy.NONE)
    .into(imageView)
```
- 캐싱 : 파일 사본을 캐시 혹은 임시 저장소에 저장해서, 보다 빠르게 접근할 수 있도록 하는 하나의 프로세스   
    -> 캐시 : 파일 또는 데이터 사본을 임시 저장하는 위치
- 이미지의 URL을 이용해 **메모리와 디스크에 캐싱**하여 추후에 불러올 시 빠른 이미지 로딩을 지원
#### 함수 기능
- skipMemoryCache() : 메모리에 캐싱하지 않으려면 true, 메모리에 캐싱하려면 false
- diskCacheStrategy() : 디스크에 캐싱을 수행하기 위한 함수.
    -  DiskCacheStrategy.NONE : 디스크 캐싱을 하지 않는다.
    -  DiskCacheStrategy.SOURCE : 원본 이미지만 캐싱
    -  DiskCacheStrategy.RESULT : 변형된 이미지만 캐싱
    -  DiskCacheStrategy.ALL : 모든 이미지를 캐싱(기본값)

***

4. GIF 로딩
```kotlin
Glide.with(this)
    .load(R.drawable.glide_example)
    .asGif()
    .into(imageView)
```
- GIF 이미지 로딩
#### 함수 기능
- asGif() : GIF이미지 로딩을 위한 함수

***

5. 썸네일 이미지
```kotlin
Glide.with(this)
    .load(R.drawable.gilde_example)
    .thumbnail(0.1f)
    .into(imageView)
```
- 원본 이미지를 썸네일로 사용
#### 함수 기능
- thumnail() : 원본 이미지의 크기의 배수값을 줌으로써 썸네일의 크기를 지정할 수 있는 함수

***

6. 이미지 크기 변경
```kotlin
Glide.with(this)
    .load(R.drawable.gilde_example)
    .override(Int,Int)
    .centerCrop()
    .fitCenter()
    .into(imageView)
``` 
- 이미지 크기를 변경해주는 함수
#### 함수 기능
- override() : 이미지 사이즈를 조절해주는 함수
- centerCrop() : 실제 이미지가 이미지뷰의 사이즈보다 클 때, 이미지뷰의 크기에 맞춰 이미지 중간부분을 잘라서 스케일링하는 함수
- fitCenter() : 실제 이미지가 이미지뷰의 사이즈와 다를 때, 이미지와 이미지뷰의 중간을 맞춰서 이미지 크기를 스케일링하는 함수

***
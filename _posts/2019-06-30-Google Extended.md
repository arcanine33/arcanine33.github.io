---
title: "2019.06.30 Google Extended"
categories: seminar
classes: wide
toc: true
toc_label: "목차"
toc_icon: "laugh-wink"
---

# 1. C hall - 모두를 위한 안드로이드 새소식
Q 버전부터는 재설정 할 수 없는 고유식별자는 더이상 사용할 수 없다.
대안 API가 있다. SERTal(?), getSeries() 등

백그라운드 액티비티 실행 금지!
- 사용자의 사용 여부에 critical하지 않느냐! 대신 notification을 사용하라!

사용자의 개인정보를 쓴다면 사용자가 어디에 쓰이는지, 그 정보를 잘 전달하고 있는지 고민해보아야 한다.



## System UI
Dark Theme가 추가 되었는데, 원래 있었지만, 편하게 UI를 바꿀수 있는 기능이 생겼다.
앱 개발자는 Dark theme 따로 적용해야 한다. 어차피 적용해야하기 때문에 최대한 빨리 하도록 하자.

Force Dark
- 강제로 색을 반전해주는 기능이다.

-night
Resource qualifier 를 활용하면 쉽게 dark theme를 적용할 수 있다.

Web view도 dark theme를 적용할 수 있다.(웹 뷰라서 안돼! 이런건 없다)

시스템 제스쳐 UI
System Insets이라는 영역을 침범하면 UI가 생각한 대로 동작하지 않을 수 있다. Gesture Navigation에서는 이런 영향이 더 커질 수 있다.

Gentle Notifications의 추가. 


## Paltform
앱의 성능이 좋아지거나 나빠질 수 있는 가능성이 있다!
art runtime  = interpreter + JIT + AOT
처음에는 interpreter를 쓰다가 그 다음은 JIT 그 다음은 AOT를 사용하게 된다.

Profiles in the cloud
컴파일을 하기 위해서는 최적화된 정보가 필요하다. 이걸 cloud상으로 하자. 
초창기 앱을 처음 다운받아서 쓰는 사용자들의 정보를 받아서 이를 구글 cloud에 올린다.
그 이후 다른 사람이 처음 앱을 내려받을 때 profile 정보를 같이 받아서 실행을 하면 15%정도 더 빨리 첫 실행을 할 수 있다.

No more Zygote 
Process에서 pool을 받아서 바로 실행. 5ms 정도 더 빨라진다. 매우 큰 변화!


Project mainline in Q
-Security
-Privacy
-Consistency

permission controller 가 updateable 하다는 것은 버전에 관계없이 동일한 권한 정책을 갖게 될 수 있다.
지금까지는 버전마다 권한이 조금씩 달랐다.

## CameraX Security
안드로이드에서 Camera앱을 만들기 쉽지 않다. 이제는 사용하기 쉽고 믿고 쓸수 있는 라이브러리를 만들겠다. 그것이 Z 라이브러리.
- Preview
- Capture
- Picture Analyze
Camera X 라이브러리를 사용하면 좋을 것이다.

Jetpack Library
file Encryption - 쉽게 쓸 수 있는 보안 라이브러리를 만들겠다.


First 10 year - APK
안드로이드 앱 번들을 통해 앱을 배포하겠다. 사용자에게 맞게 앱을 배포할 수 있다. 성능 증가 및 최적화.
타겟 단말에 맞는 리소스만 뽑아서 배포할 수 있다.

Early Stage 아니다. 당장 써도 된다!! 믿고 써도 된다. 베타 아니다. 정식이다.

vitals
앱사이즈를 줄이는거에 대해 한번 살펴보아라.

In-app update
앱 내에서 바로 업데이트 할 수 있다.

Internal app sharing 을 통해 apk 버전 필요없이 더 쉽게 만들 수 있다.

앱을 다운받은 이후 딱 한번 app key(서명 key)를 바꿀수 있도록 허용해주었다.새로운 install에만 서명키를 바꿀 수 있다.

google play billing 2.0이 발표되었다.
막 업데이트가 되었다.(규칙 없이) 앞으로는 정기적인 절차에 따라서 배포를 하도록 하겠다.
메이저 버전에 대해 매년 정기적으로 배포할 것 이기에 major 한 구현 방법으로 사용할 것이다.
2021이 되면 billing 4.0 버전이 나올텐데, 그러면 이전 버전은 전부 사용할 수 없다.



# 2. A Hall - Flutter 를 소개합니다.
## Legacy Cross, Hybrid Platform

### Native App 
My apps   <---------------> Platform
my code,                    widget <---> canvas event   
xml                                 
java
kotlin     <--------------> location, bluthooth
Obj-c
swift


### react native
my apps

js <---> jsBridge widget<---> canvas events
                               location, bluetooth, network


### flutter
2017년 5월 google이 발표했다.
모바일 오픈소스 플랫폼

Fuchsia 2016년 google에서 갑자기 발표한 OS 오픈소스 프로젝트
전통적인 리눅스 커널 OS가 아닌 지르콘 마이크로커널을 사용
2019년 1월 안드로이드 어플리케이션에서 구동 가능


dart <---> render <---------> canvas events
           플랫폼 채널 <-----> locations, bluetooth 등
           (skia사용)
           
           
flutter에 있는 render가 자동으로 그리면 하위 호환성은?
젤리 빈 4.1 부터 가능하고 IOS 8 부터 가능하다!
IOS 디바이스는 iphone 4부터 가능!

flutter widget
stateless 위젯이 한번 그려지면 변경이 안된다. 그릴 때 비용이 적게 든다.
stateful 위젯이 변경 될 때 사용가능, setState()으로 변경 사용 가능.

```
if(platform.isandroid){
  return run app(
     materialApp()
   );
}
```
이렇게 안드로이드랑 ios를 구별해서 디자인을 줄 수 있다.

복잡한 뷰를 그릴 수록 따로 모듈화를 자주 해야 한다.

왜 위젯을 사용 할까?
- 캔버스에 그린다 뿐이지 그냥 네이티브의 위젯들을 사용하면 되는거 아닌가?
- 왜 계층구조를 쓰게 만들었을까?
 
---> 효과적으로 쓸 수 있을것이다. 간단하게 레이아웃을 그릴 수 있다.

scaffold라는 위젯은 머터리얼 디자인을 그리는 제일 기본적인 위젯이다.


비동기 함수 await, async 
future와 함께 사용한다.

### flutter 생명주기
(create)->initstate->(dirty)->build->dispose->(defunct)


statefulWidget을 빌드하게 되면 즉시 createState()를 호출합니다.
initState() 위젯이 생성될 때 사용되는 함수입니다.
build 필수로 작성해야 합니다.
setState() 데이터가 변경될때, 해당 데이터가 변경 되었다는 것을 알립니다.
비동기가 될수 없는 콜백을 처리할 때 호출 가능합니다.

### refresh
async에 대한 메소드를 정의하고 setState()안에 처리할 함수를 집어 넣으면 된다.

### flutter 특징
- fase development
- expressive and flexible ui
- native perfomrance

### 사이트!
- itsallwidgets
- pub.dev
- flutter.dev


gdgincheon.com 을 통해 만날 수 있다.


2d 그래픽을 그리는 demo가나왔다. 2d 애니메이션을 플러터에 넣어서 게임을 만들 수 있다!!

# 3. C hall - what's new in android studio?
## Android 3.4 
### resource manager 
구조는 기존 프레임워크와 비슷
동일한 프로젝트에서 비슷한 이미지끼리 그룹핑해서 보여준다.
이미지, 칼라, 레이아웃 들을 리소스 매니저로 볼 수 있다.


#### Color picker
-resourse xml의 color double click
-rgv, hsb 지원

### Project Structure Dialog
- add library dependency
- variants

### unresolved reference
- 라이브러리 추가 제안
- jetpack, firebase 지원

## Adnroid 3.5
### project marble
- 대규모 프로젝트의 메모리 관리
- UI hang
- lint 기능 강화

#### System health
- DataBinding UI Freezes 해결
- Improve Android Lint : 퍼포먼스가 더 빨라져 alert를 더 빨리 알 수 있게 되었다.
- Speed & CPU Usage
- Build Speed
  - Kotlin incremental annotation processing 
  - Liget R class generation
  - benchmarking
  
  
도입 해 볼 만한 것
- 안드로이드 gradle 플러그인 최신 버전
- gradle profile
- 빌드 구성 및 task 최적화

- I/o File Access for Windows


#####  Foldable Support
- 안드로이드 Q 부터 사용가능
- gradle sync

##### App deployment flow

##### increase heap
- 메모리가 충분하다면 안스가 메모리를 늘리라고 추천해주기 시작했다.

#### Apply Change
##### Instant Run
- 빌드 중 apk 수정이 없음
- 플랫폼 api로 빠르게 앱 실행
- 호환되지 않는 변경 발생 시 app 실행

.
.
.

(겁나 내용 많은데 쓸 시간이 없음....)



# 4. A hall - Flutter 와 Dart 2.0
언어를 소개하는 시간.
1. Dart 란?
2. Kotlin/Swift/Dart의 문법 비교
3. Dart의 매력
4. 왜 Flutter는 Dart를 선택했나?

## 1. Dart 란?
### 1. 흑역사: Google의 Activity X?
### 2. 브라우저 전용?
### 3. 강한 타입 시스템
### 4. UI를 표현하기 편리함


## 2. Kotlin/Swift/Dart의 문법 비교
## 3. Dart의 매력
### 1. 1급 함수 지원
```
typedef operation(val1, val2);
operation oper;
if(operation = '+'){
  oper = add;
}
// 동작을 선택한 다음에 그 값을 넘겨준다.
// 자바는 값을 먼저 확보하고 동작을 선택한 다음 넘겨주기 때문에 불편하다.
```

### 2. 상수 단일화(Constant canonicalisation)
### 3. Mixin
상속이랑 비슷한데, 타입을 재활용하는데 용도를 둔 기능이다.
Mixin은 코드를 재활용하는데 목적을 두었다.

상속에는 한계가 있어서 이를 극복하기 위해 throw UnsupportOperationException()이 쓰인다.
그러나 애초에 쓰이지 않는 함수가 노출되는 것이 문제가 아닐까?


Dart 의 Mixin으로 구현한 동물 체계 코드

## 4. 왜 Flutter는 Dart를 선택했나?
### 1. JIT/AOT 모두 지원
#### a. JIT - Hot (stateful) reload
#### b. AOT - Production 최적화

JIT : 프로그램이 실행되는 동안에 점점 빨라지는 기법
AOT : 프로그램이 실행되기 전에 먼저 최적화해서 실행하는 기법

플러터는 개발은 JIT로 해도 실행은 AOT로 한다.
hot reload가 좋다!


### 2. UI 표현에 최적화된 문법

### 3. 기타 등등

## 맺으며...
### Dart 2.x : 환골탈태 중
### Dart on Flutter, Kotlin on JVM, Swift on Darwin




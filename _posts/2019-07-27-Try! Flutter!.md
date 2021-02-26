---
title: "[Flutter Seminar]Try! Flutter! 세미나"
categories: seminar
toc: true
toc_label: "목차"
toc_icon: "cog"
---

# 1. 한번 쓰고 우려먹는 플러터
플러터의 구조는 선언적 UI - swift와 비슷
Fuchsia 에 어플리케이션 형태로 사용하게 될 것이다.
윈도우, 맥, 리눅스, 크롬 등에서 사용된다.

Dart? 
==> 모던-멀티 패러다임 언어
자바스크립트, 자바, 씨샵 언어를 해보면 어색하지 않으며, flutter는 UI부터 로직까지 dart로 구성하고 있다.
객체지향 : 클래스 객체, 상속, 캡슐화, 인터페이스를 두며
함수형 언어처럼 동적으로 다룰 수 있다?

크로스 플랫폼 

flutter web은 skia엔진을 직접 다루지 않고, DOM/HTML5 Canvas를 이용하여 렌더링.
<br><br>


# 2. State 관리 어렵지 않아요
* setState
* inheritedWidget & ScopedModel
* Provider

### setState()
=> Stateful Widget에서 사용된다. State<T> Object에서 사용 됨.

### inheritedWidget
* 상태관리를 위한 Dluter Package
=> page라는 widget을 한번 감싸준다. 

* 단점
.of()를 통해서 상태에 접근 하면 buildcontext에 대해 위젯이 다시 빌드됨
만약 .of()가 호출되는 위젯의 하위 위젯이 무거우면 성능이 떨어 질 수 있음
직접 짜야하는 코드의 양이 많아짐



### provider
커뮤니티를 통해서 만들어진 상태 관리 패키지
구글이 provider 패키지 사용을 장려
inheritedWidget의 syntax suger와 비슷

주의사항
.of()를 통해 상태에 대해 접근하면 build Context 대해 widget이 다시 build
잘못 사용하면 성능적면이 inheritedWidget와 비슷
rebuild에 민감한 widget들 주의


# 3. Bloc..? 안드로이드스럽게 이해하기
### 01
business logic component
=> 유저의 행동에 따라 서비스에서 보여주고자 하는 결과를 나타내기 위해 데이터를 가공하는 로직

UI -> BLoC -> Repository
   <-      <-
View -> ViewModel
     <-
     
     
BLoC는 ViewModel이 아니다!
Kotiln, Java        Dart

Viewmodel == Presentation logic (o)
          == Business Logic (x)
BLoc == Presentation logic (x)
     == Business logic (o)
   
   
BLoC == UseCase(or service) == true? => service에 더 가깝다.
 

### 02 Deep Dive

BLoC =>  

UI ->  BLoC -> Repository
   <-       <-
   Rx       Rx
   
 
 Stream -> 물을 흘려보내는 파이프
 Subscription -> 물을 떠 마시는 동작
 
Stream
Obervable
Subject

Subject -> Stream -> Subscription
                 Listen
                 
                 
안드로이드에서 rx를 잘 활용하고 어떤 플랫폼에서든 활용할 수 있기 때문에 어떤 언어로도 비슷하게 짤 수 있다.

### 03 Advanced
* DI
* Sub
* 

DI => android는 dagger, coin 사용
      flutter 는 Provider, inject.dart 시용
      

DI - Provider 
* 가장 기본적인 DI 방식
* inheritedWidget 사용
* Package로도 
 
 Navigation(Route)코드의 위치 의문

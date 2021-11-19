---
title: "[Flutter]Firebasae Dynamic Links"
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"
---

현재 만들고 있는  서비스를 페이스북 마케팅으로 광고를 내보내고 있다. 이를 위해 준비할 작업이 많았는데, 첫 번째는 페이스북에 앱을 등록하는 작업이었고, 두 번째는 앱에 dynamic link를 붙이는 작업이었다. 오늘은 두 번째의 firebase의 dynamic link를 붙이는 작업에 대해 알아보자!

## 서막

사실 dynamic link에 대한 개념을 몰랐었다. 기획자 겸 마케터님에게 페이스북에 광고를 올린다는 말을 듣고 '음... 그렇군' 하고 내 일 아닌것 처럼 넘겼지만, dynamic link로 앱을 연결해야 한다는 소릴 듣고는 부랴부랴 이것이 뭔가에 대해 찾아봤다.

## dynamic link? deep link?

내가 dynamic link를 공부하면서 제일 헷갈렸던게 이 두 용어에 대한 차이점이었다. ?^?

다른 블로그에서 이미지도 넣어가며 자세히 설명해줄 것이기 때문에, 여기서는 내가 이해한 두 용어에 대해서만 간략히 서술하겠다. (만약 제가 틀리게 이해했다면 말해주세요!)

dynamic link : 우리가 페이스북이나 인스타 등 SNS의 피드를 내리다보면 중간중간 광고가 삽입되어 나온다. 이 광고를 클릭하면 어딘가로 연결이 되는데, 만약 광고한 앱이 안깔려 있다면 깔 수 있도록 앱스토어나 구글스토어로 연결 되고, 깔려 있다면 앱을 실행 시킨다. 

deep link : 앱을 실행시켰을 때, 이 앱의 화면이 어디로 연결되어야 할 지를 알려주는 path 값이다. 예를 들어 '/main' 이런식으로 path가 넘어오면 메인화면을 보여주고, '/profile' 이렇게 Path가 넘어오면 프로필 화면을 처음에 띄워준다. dynamic link 를 앱에 붙일 때, deep link 경로를 작성하는 란이 있기 때문에 활용할 수  있다.

그렇다면 dynamic link에서 제공하는 deep link 기능을 활용하려면, deep link기능을 따로 붙여할까? 나는 이 부분이 제일 헷갈렸었다. 어디에서도 명확히 설명해주는 부분이 없었어서;;

결론부터 말하자면 아니오! (하단의 deep link 설정 링크의 내용이 일부 겹치기에, firebase_dynamic_links 라이브러리에서 제공하는 설명과 중복으로 세팅할 필요가 없다)

먼저 flutter 에서 deep link를 붙이는 방법을 보자 

[Deep linking](https://docs.flutter.dev/development/ui/navigation/deep-linking)

사실 안봐도 된다. dynamic link에 있는 deep link 기능을 활용하기 위해 굳이 위에 있는 포스팅을 따라하지 않아도 된다. 위에 있는 포스팅은 예를 들어 카카오톡으로 앱을 공유 하고, 사용자가 그 앱을 눌렀을 때, 개발자가 의도한 화면으로 넘어가게 할 때 사용하는 것이다. 만약 앱이 안깔려 있으면, 안 열리거나 scheme가 겹치는 다른 앱을 열겠냐고 할 것이다(아마도...그럴 것이다. 잘 아시는 분은 댓글을...)  

그리고 요즘에는 카톡에 공유하는 앱들도 다 dynamic link로 공유시키기 때문에(그래야 앱이 안깔려있을 때, 마켓으로 이동시켜 깔게 만들 수 있으니까) deep link만 단독으로 쓰이는 경우는 거의 없을 것 같다.

그렇다면 dynamic link 대체 어떻게 해야 연동 할 수 있는 걸까? 

## dynamic link 연동하기

### Firebase

먼저 firebase 부터 살펴 보자 

![firebase](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/1.png)

먼저 탭의 dynamic link 를 눌러 시작하기를 누른다. 이번 포스팅의 가장 쉬운 부분.

그리고 사용자들이 눌러 들어올 기본 도메인을 만든다. 개인적으로 가지고 있는 도메인이 없다면 (거의 대부분 그럴 것이다) 구글에서 ~~~.page.link 라는 도메인 형식을 제공해준다. 앞의 ~~~를 본인이 원하는 이름으로 넣고 계속 버튼을 누른다. 한번 만들면 이 도메인으로 사용자가 보거나 누르게 되기 때문에, 이왕이면 신중하게 작성하자. 물론 삭제하고 다시 만들 수 있다. 아니면 추가할 수도 있다. 

도메인을 만들었다면, 사용자가 누르고 들어올 url을 만들자. 상단의 '새동적링크' 버튼을 누르면 단축URL 링크를 설정하라고 한다.

![firebase step1](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/2.png)

여기서 도메인 뒤쪽에 사용자가 혹은 개발자가 구분할 수 있는 path를 적는다. 그러면 사용자에게 공유할 url이 만들어진다. 

![firebase setp2](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/3.png)

다음 동적링크 설정에서 deep link 관련 path를 설정한다. 여기에 deep link path를 적고 앱에도 같은 path를 작성해주면, 사용자가 dynamic link를 누르고 앱이 실행될 때(앱이 설치되어 있다면), 그 path로 화면이 이동한다. 동적 링크 이름은 구분값일 뿐이니 본인이 원하는 대로 작성하면 된다.

그리고 dynamic link의 deep link는 오로지 http:// 또는 https:// 로 시작해야 한다. 그렇게 안적으면 다음 화면으로 넘어가지도 않는다.

참고로 deep link 는 마지막 슬래시 이후의 path 값만 보기 때문에, 굳이 길게 할 필요없다. 만약 main으로 넘기고 싶다면 [https://main](https://main) 이렇게만 해도 충분하다. 

그 다음은 해당 링크를 눌렀을 때, 동작하는 방식이다. 당연히 Apple 앱에서 딥 링크 열기를 선택한다(안드로이드도 마찬가지) 이를 누르기 위해선 앱이 firebase에 등록이 되어야 하는데, 이는 다른 블로그를 참조하자! 

그 외 캠페인추적, 소셜태그 등은 필요하다면 작성해도 되고 생략해도 된다.

이렇게 했다면, 여러분은 dynamic link를 만든 것이다!

이제 골 때리는 앱 설정으로 넘어가보자 

[firebase_dynamic_links | Flutter Package](https://pub.dev/packages/firebase_dynamic_links)

사실 내가 설명하는 것보다 여기서 설명하는 것으로 따라하는게 더 좋다.

**당연히 firebase_dynamic_links 라이브러리를 적용하자.** 

### Android

안드로이드는 step5를 보면 알겠지만, AndroidManifets,xml과 app/build.gradle만 수정해주면 된다. 이걸 수정하는 이유는 dynamic link의 deep link가 https:// 또는 https:// 로 시작하기 때문에, 이렇게 시작하는 인자를 받아줄 것이라고 작성해햐 한다. 

![android](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/8.png)

host 는 qwerasdf.page.link(본인이 파베에서 작성한 도메인) 를 적으면 된다.

만약 flavor로 dev, prod 가 분기되어 있는 경우 `android:host="${host}"` 라고 적고 app/build.gradle에서 favor 값 작성란에 `manifestPlaceholders.host = "qwerasdf.page.link"` 라고 적으면 된다.

ex ) 

```dart
productFlavors{
	dev{
				dimension "environment"
        applicationIdSuffix ".dev"
        versionNameSuffix "-dev"
        manifestPlaceholders.host = "qwerasdfdev.page.link"
	}
	prod{
				dimension "environment"
       manifestPlaceholders.host = "qwerasdf.page.link"
	}
}
```

참, 도메인을 구글이 제공해주는 도메인이 아닌 커스텀도메인(개인도메인)으로 작성했다면, 따로 또  설정해주어야 하는게 있다. 이건 다른 블로그를 참고하자 ^^;

### IOS

ios는 android보다 할 것도 많고 어렵다. 꽤 헤맸었기 때문에, 공식문서 또는 다른 사람들의 블로그도 많이 참조하자.

먼저 Info 탭을 열어서 다음과 같이 진행하자 

![ios step1](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/4.png)

![ios step2](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/5.png)

나는 flavor로 나누어져 있기 때문에, dev와 prod가 적용되는 dynamic link 의 도메인이 달라 ${DYNAMIC_LINK}로 세팅하고 DYNAMIC_LINK에 flavor에 따라 다른 값이 들어 갈 수 있도록 분기해주었다. 

flavor에 따른 분기 설정을 해주어야 한다면 Runner(최상단의 Runner) - Project의 Runner (왼쪽의 사이드 탭에 위치해 있다) - Build Settings를 눌러준다. 그리고 User-Defined 항목에 DYNAMIC_LINK key를 만들어 값을 입력해주면 된다.

![ios step3](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/6.png)

다음!

![ios step4](/assets/images/2021-11-19-[Flutter]Firebase Dynamic Links/7.png)

Capabilities는 어디서 찾아야 하는지 몰라 헤맸는데, Runner(제일 최상단에 있는 Runner)를 누르면 Signing&Capabilities 탭이 있다. 누르면 Associated Domains를 찾을 수 있다. 여기에 상단 이미지의 형식처럼 도메인을 작성해 넣는다. 만약 flavor라 도메인이 2개라면 2개 다 집어 넣는다.

```dart
applinks:qwerasdfdev.page.link
applinks:qwerasdf.page.link
```

그리고 커스텀 도메인일 경우 한 가지를 더 해주어야 하는데, 여기선 생략한다!

### Flutter

이제 플러터에서 dynamic link의 deep link path를 설정해보자.

만약 depp link path 와 앱에 설정된 값이 안맞으면, 앱에서 맨 처음 켰을 때 화면으로 돌아가게 된다.

```dart
return GetMaterialApp(
      getPages: [
        GetPage(
            name: '/mymenu',
            page: () => MyMenu()),
        GetPage(
            name: '/main',
            page: () => MainPage()),
      ],
    )
```

만약 getx 를 쓰고 있다면, 이런 식으로 path에 따른 화면 설정을 할 수 있다.

```dart
return MaterialApp(
      routes: {
        // "/" Route로 이동하면, FirstScreen 위젯을 생성합니다.
        '/': (context) => FirstScreen(),
        // "/second" route로 이동하면, SecondScreen 위젯을 생성합니다.
        '/second': (context) => SecondScreen(),
      },
    )
```

기본으로 사용중이라면 routes를 활용하면 된다.

그러면 이 경로로 이동시키는 부분을 어디에 작성하면 좋을까? 보통 material app 안에 있는 home에 연결된 화면에서 처리를 하는 것 같다 (나는 그렇게 했다) 나는 home에 연결된 화면이 splash 화면이기 때문에 splash 화면에서 처리를 해주었다. 

```dart
class DynamicLink {
  void initDynamicLinks(BuildContext context) async {
    FirebaseDynamicLinks.instance.onLink(
        onSuccess: (PendingDynamicLinkData dynamicLink) async {
      final Uri deepLink = dynamicLink?.link;

      if (deepLink != null) {
        // 앱이 resume 상태일 때(백그라운드에 있을 때) 
        _movePage(deepLinkPath: deepLink.path);
      }
    }, onError: (OnLinkErrorException e) async {
      print('onLinkError');
      print(e.message);
    });

    final PendingDynamicLinkData data =
        await FirebaseDynamicLinks.instance.getInitialLink();
    final Uri deepLink = data?.link;

    if (deepLink != null) {
      // dynamic link로 꺼진 앱을 킬 때
      _movePage(deepLinkPath: deepLink.path);
    } else {
      // 일반적으로 App을 켰을 때
      _movePage();
    }
  }

  void _movePage({String deepLinkPath}) async {
     if (user.registeredAt == null) {
      Get.offAll(() => Login());
    } else {
      if (deepLinkPath == null) {
				//이건 스플래쉬 화면이 너무 빨리 넘어가서 앱을 다이나믹링크가 아닌 일반적으로 킬 때, sleep 으로 스플래시 화면이 더 오래 띄워지게 해놨당
        sleep(const Duration(seconds: 2));
        Get.offAll(() => MainPage());
      } else {
				//여기서 routes 또는 getPages에서 설정한 path의 화면으로 넘어간다 
        Get.offAllNamed(deepLinkPath);
      }
    }
  }
}
```

**여기까지 하면 끝! 테스트를 돌려보자. 네이버나 크롬(또는 그 외 웹브라우저)를 켜서 dynamic link를 타이핑해서 열어보자!**  아이폰일 경우 Note앱에 링크를 복붙해서 tap 해서 열 수도 있다!
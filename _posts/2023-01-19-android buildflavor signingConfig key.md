---
title: "[Flutter][android]android buildflavor signingConfig key 설정하기"
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"

---

fastalane 으로 firebase distribution 에 앱을 배포하는 자동화를 사용하고 있다.

이 작업으로 실제 앱 배포 전 여러번 테스트 배포를 올려 팀원들과 QA를 진행 할 수 있었다.

그리고 이제 앱을 실제로 첫 배포하고(감격) 다음 배포전 첫 테스트 배포를 진행했다.

그런데 문제가 생겼으니…

### 문제 인식

구글 로그인이 안되는 것이었다! 

ios는 잘되고 안드로이드만 안되는 걸 봐선 firebase sha key 등록 누락이 원인 일거라고 생각했다.

지금까진 100% 확률로 맞았기 때문에…

### 문제 해결 중

내가 아닌 동료가 올렸기 때문에, 동료의 sha 키가 제대로 입력이 되었는지 확인했다.

sha1 키만 등록되어 있어서 sha256 키를 추가로 등록했다.

그 결과는?

### 문제 해결 실패

여전히 구글 로그인이 안되었다 🤔

뭐가 문제지…? 

### 문제 원인 다시 찾기

그래서 이번엔 내가 테스트 앱 배포를 해보기로 했다.

만약 나도 안된다면 문제 원인을 좁히는데 더 명확해지지 않겠는가?

### 문제 원인 발견

내가 올렸을 때도, 구글 로그인이 안되었다!!

이러면 개인의 문제가 아니라 전체의 문제가 되었다.

안드로이드에서 배포 전과 달라진 점을 생각해보면, 배포 할 때는, jks 를 만들고 구글 플레이스토어의 sha 키를 입력하고….!

아무래도 구글플레이스토어에서 제공하는 sha 키를 dev firebase 에 입력하지 않아 생기는 문제 같았다.

기존 안드로이드 build.gradle은 이렇게 작성되어 있었다.

```java
buildTypes{
	release{
		minifyEnabled true
		proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    signingConfig signingConfigs.release
	}
}
```

아마 대부분의 gradle 이 이렇게 작성되어 있겠지? 

release로 빌드하면, 위의 구문을 타면서 release 키를 앱 내부에 집어넣게 된다.

그래서 devRelease 로 빌드해서 올렸을 때, 구글 로그인이 구글 플레이스토어에서 제공하는 sha 키를 필요로 한건고, dev 파이어베이스에서는 구글 플레이스토어 sha 키가 등록되어 있지 않으니 로그인 절차가 제대로 진행되지 않은 것! 이라고 추론했다.

### 고민

이 문제를 빠르게 해결하려면, 간단하게 플레이스토어 sha 키를 dev 파이어베이스에 집어넣어주기만 하면 됐다.

그러나 그러고 싶지 않았다…

왜냐면 앞으로 실배포를 하다 보면 한번쯤은 devRelease 버전을 실수로 플레이스토어에 올릴 수도 있지 않은가?

지금 상태라면, devRelease 를 올리더라도 구글 로그인이 안될테니, 빠르게 대처할 수 있을텐데, 만약 구글로그인이 되는 devRelease가 배포된다면?

😱

이 문제를 휴먼 에러 없이 안전하게 풀 수 있는 방법을 고민했고,

prodRelease 로 빌드했을 때만, 릴리즈키를 삽입하고 싶었다.

### 해결

buildType 구문 안에 flavor 로 분기하고 싶었지만, 그건 안된다구 하더라… 😓

한참 찾다가 답은 [스택오버플로우](https://stackoverflow.com/questions/17040494/signing-product-flavors-with-gradle)에 있더라

바로 `productFlavors` 키워드를 쓰면 되는 것! 이 키워드로 실배포 버전에는 릴리즈키를, 테스트 배포 버전에는 debug 키를 붙일 수 있었다! 👏

```
buildTypes{
	release{
		minifyEnabled true
    proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    productFlavors.prod.signingConfig signingConfigs.release
    productFlavors.dev.signingConfig signingConfigs.debug
	}
}
```

### 추가 문제

바로 수정해서 fastlane에 돌리고, 앱을 다운받으려 했을 때! 또 문제가 생겼다.

바로 설치 실패 문구!

다운로드 버튼을 누르면 자꾸 `설치 실패` 문구만 뜨고 어떤 오류 메시지도 안났다.

그래서 배포 버전을  삭제하고 다시 올리고 삭제하고 다시 올리고… 반복하다가 fastlane 에서 OutOfMemory 오류가 떴다. 😿

찾아보니, gradle 을 올려라, build 파일을 삭제해라 등이 있었는데, 나는 안드로이드 스튜디오에서 Invalidate Caches 기능을 사용해 caches 를 싹 날려주었다.

그리고 fastlane 을 다시 실행하니 잘 올라가고, 설치도 잘 되었다!

휴~ 별 문제 아니라 다행이었다!
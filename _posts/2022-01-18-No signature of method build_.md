---
title: "[Flutter][Android]No signature of method: build_"
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"
---

```bash
No signature of method: build_
```

얼마 전(이라고 쓰고 엊그제) githubAction으로 CD구축을 하다가 도저히 aab가 안만들어져서, gradle문제인가 싶어 7.0 이상으로 올려봤다.

그러고 build를 돌려보는데 위의 에러가 나는게 아닌가

검색해보니 build.gradle의 문법 오류라는데, 이쪽은 손도 안댔는데 왜 오류가 나는지 몰라 몇 시간 내내 끙끙거리다 결국 gradle 버전을 낮췄다.

그리고 다음날 나랑 똑같은 문제로 씨름하는 분이랑 고민하다가 원인을 알게 되었다.

바로 proguard 문법이 달라져서 인데, 해결 방법은 아주 간단하다

```java
buildTypes {
        release {
            minifyEnabled true
            //useProguard true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.release
        }
    }
```

여기서 **useProguard true 코드를 없애주자**

gradle 7.0 버전 이상부터는 useProguard 가 없어졌다.

gradle 업그레이드 할 때, 자동으로 안빼준다... 

나처럼 몇 시간 버리지 말구, 다들 오류 잘 고쳤으면 좋겠다 ㅠㅠ
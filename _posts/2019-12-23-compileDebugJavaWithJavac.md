---
title: "[Flutter] compileDebugJavaWithJavac Error"
categories: flutter
classes: wide
---

지난주 쯤에 갑자기 난 에러였는데 비슷하게 에러나는 분들이 있을까 싶어 게시글을 올린다.

flutter app:compileDebugJavaWithJavac

라는 에러였는데, 서비스하고 있는 어플 특성 상 native 부분을 건드려야 했다.
그래서 native 코드를 짜고 (내가 짠건 아니지만) 잘 build 되었다.

그런데 어느날 갑자기 위의 javac 에 관한 에러가 났다.

flutter의 android 파일에서 나는 에러였기 때문에, 이쪽을 건드린적 없는 나는 매우 멘붕 상태에 빠졌고,
딱히 구글님도 명확한 해답을 주지 못했다.

그리고 해결을 했으니 그 방법은 gradle 버전을 낮추는 것.
(만인의 악 gradle)

3.5.3 이었던 gradle 버전을 3.4.1로 낮추니 쌩쌩이 잘 돌아간다.


갑자기 잘 쓰던 gradle 버전이 안돌아갈 수 있나 싶지만.... 
어째든 이 방식으로 해결한 후 다시 3.5 버전으로 gradle 버전을 올리니 다시 안된다.

명확히 gradle 버전 탓 인것....;;

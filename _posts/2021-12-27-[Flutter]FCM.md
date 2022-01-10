---
title: "[Flutter]Firebasae Cloud Messaging"
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"
---

서비스 앱을 만들 때, 거의 모든 웬만한 앱에는 다 들어가 있는 기능이 뭘까? 알람일 것이다. 이미 너무 많은 앱들은 알람 기능을 탑재하고 있고, 수 많은 앱들의 동시다발적인 알람은 사용자에게 많은 피로감을 준다. (나도 이 때문에, 앱깔면 제일 먼저 하는게 마케팅 수신동의 취소 누르기다)

그럼에도 기업들은 알람기능을 포기할 수 없다. 이 기능을 통해 리텐션을 올릴 수도 있고, 사용자가 서비스앱과 더 친근함을 느낄 수도 있고, 혹은 사용자의 귀차니즘을 해결하기 위해 무언가를(보통은 정보를) 알려주는 기능이 될 수도 있기 때문이다.

이런 편리?하고도 불편한 FCM을 구현해보자!

[https://firebase.flutter.dev/docs/messaging/overview/](https://firebase.flutter.dev/docs/messaging/overview/)

사실 이 문서에 정리가 비교적 잘 되어 있어서, 이 문서와 약간의(?) 서치를 겸용하면 만들 수 있다!

이 게시글은 내가 나중에 또 필요하면 보려고 작성하는 거라 일부 코드들이 어쩌면 생략 되어 있을 수도 있다ㅠㅠ

그리고 불확실하나 flutter에다가 firebase_messaging 라이브러리를 붙이고 

`await Firebase.*initializeApp*();` 를 했다면, 앱에서 세팅을 하지 않았는데도, 푸쉬 알람이 간다. (만약 이전에 파베DB나 remote config나 이미 파베 기능과 붙어 있는 경우) 이 때문에, production에 테스트 알람이 가는 불상사가 생겼다. 기존에 파베랑 연결되어 있는 기능이 있다면, 아래 세팅을 안해도 알람이 갈 수 있으니, 확인해보자. (이 경우는 android 타켓으로만 진행한거라 ios는 또 별도인지 모르겠다 휴)

내가 생각하기에 하단의 세팅은 알람(noti)에 대한 컨트롤(소리, 화면에 표시하기 등등), 알람(noti)를 눌렀을 때, 화면 동작, 화면 띄우기 등을 세팅하는 것 같다. 나중에 제대로 테스트 앱을 하나 파서 테스트 해봐야지.

 

## FCM 세팅하기

1. Firebase 사이트, APPLE 개발자 사이트에 세팅하기 (only IOS)

만약 제공 앱 중에 IOS가 있다면 파베 사이트에다가 IOS앱에 대한 설정을 해주어야 한다.

[https://firebase.flutter.dev/docs/messaging/apple-integration](https://firebase.flutter.dev/docs/messaging/apple-integration)

이와 관련된 링크를 보며 따라해보자.

참고로 내가 헷갈렸던 부분인데, 만약 애플로그인을 체크한 identifier가 있으면, 그 계정에다가 APNS를 추가 체크해주면 되고, 애플로그인 할 때 받았던 인증키 그대로 사용하면 된다.

뭔가 다들 아는 내용이라 찾는 정보에 제대로 안나왔지만... 나는 앱린이라 이런거 다 써주셔야 안 헤맨다구요ㅠㅠ

그리고 파베에 인증키 업로드 할 때, 인증키 형식은 **p8**이다! p12랑 헷갈리지 말자!

1. Flutter 세팅

```dart
firebase_messaging: ^11.2.0
```

먼저 pubspec.yaml 파일에다가 메세징 라이브러리를 집어 넣자!

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
 
}
```

main에 initializeApp()넣어주고

나 같은 경우에는 fcm을 처리해주는 controller를 따로 만들었다.

```dart
class FirebaseMessagingController extends GetxController{

  final FirebaseMessaging _firebaseMessaging = FirebaseMessaging.instance;
  @override
  void onInit() {
		_initNotification();
		_firebaseMessagingForegroundHandler();
		_firebaseMessagingBackgroundHandler();
    super.onInit();
  }

  Future<void> _initNotification() async {
    _firebaseMessaging.requestPermission();
  }

  Future<void> _firebaseMessagingForegroundHandler() async {
    FirebaseMessaging.onMessage.listen((RemoteMessage message) {
				//앱이 켜진 상태에서 알람을 눌렀을 때 
    });
    FirebaseMessaging.onMessageOpenedApp.listen((RemoteMessage message) {
      _initMessage(); //resume상태에 있을 때, 알람을 눌렀을 때
    });
  }

  Future<void> _firebaseMessagingBackgroundHandler(RemoteMessage message) async {
    await Firebase.initializeApp();
    _initMessage(); //백그라운드에 온 알람을 눌렀을 때
  }

  Future<void> _initMessage() async {
    _firebaseMessaging.getInitialMessage().then((RemoteMessage? message) {
      if (message != null) {
        if (message.data['a'] != null) {
		         //a 가 key 값인 value를 비교
						Get.toNamed("${message.data['a']}");
          }
        }  
      }
    });
  }
}
```

이런 식으로 하면, 알람에 따른 경로 분기처리 가능하다!

테스트하면서 local noti 처리 하는 것도 만들었는데, 꽤 귀찮다... 

1. local_notification 라이브러리를 추가해주고...

```dart
flutter_local_notifications: ^9.1.4
```

1. android는 퍼미션 추가를 해준다.

```dart
**<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
<uses-permission android:name="android.permission.VIBRATE" />
<uses-permission android:name="android.permission.WAKE_LOCK" />
<uses-permission android:name="android.permission.USE_FULL_SCREEN_INTENT" />**
<application
        android:icon="@mipmap/ic_launcher"
        android:usesCleartextTraffic="true"
        android:allowBackup="false"
        >
        <activity
            android:name=".MainActivity"
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
            android:hardwareAccelerated="true"
            **android:showWhenLocked="true"
            android:turnScreenOn="true"**
```

1. ios는 notification service를 만들어준다

그리고 다음과 같은 코드 작성 (swift 기준)

[https://firebase.flutter.dev/docs/messaging/apple-integration/](https://firebase.flutter.dev/docs/messaging/apple-integration/) 

```dart
import UserNotifications
import FirebaseMessaging

class NotificationService: UNNotificationServiceExtension {

    var contentHandler: ((UNNotificationContent) -> Void)?
    var bestAttemptContent: UNMutableNotificationContent?

    override func didReceive(_ request: UNNotificationRequest, withContentHandler contentHandler: @escaping (UNNotificationContent) -> Void) {
        self.contentHandler = contentHandler
        bestAttemptContent = (request.content.mutableCopy() as? UNMutableNotificationContent)
        
        if let bestAttemptContent = bestAttemptContent {
            Messaging.serviceExtension().populateNotificationContent(bestAttemptContent, withContentHandler: contentHandler)
        }
    }

    override func serviceExtensionTimeWillExpire() {
        // Called just before the extension will be terminated by the system.
        // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.
        if let contentHandler = contentHandler, let bestAttemptContent =  bestAttemptContent {
            contentHandler(bestAttemptContent)
        }
    }

}
```

1. appdelegate에 다음과 같은 코드 추가

ios 10 이상부터는 다음 코드를 넣어줘야 동작!

```dart
Bool {
      if #available(iOS 10.0, *) {
            UNUserNotificationCenter.current().delegate = self as? UNUserNotificationCenterDelegate
          }
```

1. info.plist에도 추가

```dart
<key>FirebaseAppDelegateProxyEnabled</key>
<false/>
```

ios가 할게 많아서 그렇지 일단 세팅만 하면 코드는 공통으로 쓰는거라 훨씬 낫다.

## FCM 구독하기

이제 세팅을 했다면, 또 뭘 할 수 있을까?

사용자를 나눠 원하는 사용자에게만 알림을 보내고 싶을 것이다.(예를 들어, 마케팅 수신을 한 사용자와 안한 사용자 등)

이런 경우엔 구독기능을 사용하면 된다! 방법도 아주아주 쉬운데, 다음과 같은 코드로 컨트롤 할 수 있다.

```dart
_firebaseMessaging.subscribeToTopic('something'); // 구독
_firebaseMessaging.unsubscribeFromTopic('something'); // 비구독
```

그럼 이 코드들로 유저를 나눴다면, 어떻게 알림을 구독자에게만 보낼 수 있을까?

![firebase subscription](/assets/images/2021-12-27-[Flutter]FCM/FCM.png)

파이어베이스 → 클라우드메세징 탭에서 다음과 같이 타겟에 something을 적어 something 구독자들에게만 알림을 날릴 수 있다! 이렇게 하면, 의도대로 사용자도 나누고 알림 날리기도 쉽고! 1석2조!

## +)여담

안드로이드 FCM을 테스트하면서 어제까지만 해도 되던 FCM이 안되는 현상을 발견했다. 휴 또 왜지왜지 왜 안되지를 백번쯤 외쳤을 때, 앱의 배터리 최적화 퍼미션이 허용되어 있으면, 백그라운드에서 완전히 종료한 앱은 FCM이 안온다는 글을 읽었다. 

왜 아무도 이런걸 안알려주냐구~! 안드로이드 일 때, 배터리 최적화 퍼미션 코드를 추가해보자

먼저 `permission_handler` 라이브러리를 추가하면, flutter에서 권한 획득하기 쉽다. 나는 이 라이브러리를 이용해 권한 alert를 띄웠다.

```dart
Future<void> _requestPermission() async {
    if (Platform.isIOS) { // IOS일 때도 권한 허용은 해줘야 한다.
      _firebaseMessaging.requestPermission();
    } else {
      if (PermissionStatus.denied == await Permission.ignoreBatteryOptimizations.status) {
        WidgetsBinding.instance!.addPostFrameCallback((_) async {
          await Permission.ignoreBatteryOptimizations.request();
        });
      }
    }
  }
```

이렇게 하면, 배터리 최적화 퍼미션을 받을 수 있다. 다만, 이 코드는 사용자가 퍼미션을 거부하면, 다음 접속 때도 또 배터리최적화 퍼미션이 뜬다. 다른 퍼미션과 다르게 영구적으로 안본다는 체크박스가 없는 듯 하다. 

휴~ 이렇게 또 flutter 정복하기~ 성공~!
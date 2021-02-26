---
title: flutter 서버 통신하기
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"
---

# pubspec.yaml
먼저 서버와 통신하기 위해서는 http 패키지가 필요합니다. 이는 flutter 기본 패키지가 아니기 때문에(ㅠㅠ) 직접 추가해주어야 하기 때문입니다.
이를 위해 pubspec.yaml 파일에 들어가서 

```java
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^0.1.2
  http: ^0.12.0
```

이렇게 수정해줍니다.<br>
이때 추가하는 http의 위치를 sdk 바로 밑에 놓거나 할 수 있는데, yaml 파일은 스페이스 하나의 차이로 글자를 구분하기 때문에, 꼭 flutter의 위치와 맞춰 써주어야 합니다. <br>
**여기서 끝이 아니라!!**
꼭 packages get 버튼을 누르거나 (또는 get packages) 터미널에 flutter pub get 라고 쳐서 패키지의 정보를 가져와야 합니다. <br>
이 작업을 안하면, dart파일에서 사용할 때, 무조건 빨간 줄 쳐지니까.... 꼭 잊지말자!





# 서버통신 구조, 서버에서 데이터 받아오기
 먼저, 구조는 JSON으로 받을 객체들을 묶은 Class 하나, URL을 받는 Class 하나, 화면에 뿌려줄 Stateful 또는 Stateless Widget입니다.
 
 우선,
 ```dart
 import 'package:flutter/material.dart';
import 'dart:convert';
import 'package:http/http.dart' as http;
import 'dart:async';
```
다음과 같이 상단에 import해주세요.

그리고 JSON을 받을 객체를 만들어봅시다.

```dart
class Item{
  String serverTime; 
  Item({this.serverTime}); // 객체를 생성자로 만들어주세요. 그런데 의문! 보통 생성자는 Item(this.serverTime) 이렇게 쓰는데
                           // 왜 여기서는 {} 괄호가 추가되었는지 모르겠다. 안쓰면 에러난다. 아는 분 알려주세용 ㅠㅠ
  Item.fromJson(Map<String, dynamic> json){ // json에서 보내는 정보에 맞게 내가 가진 변수를 매칭하는 작업입니다.
                                            // factory Item.fromJson~  이렇게 factory를 붙여도 됩니다.
    serverTime = json['serverTime'];          
  }
}
```

저는 서버에서 보내주는 서버시간을 받아보려고 합니다.
그래서 서버시간을 String으로 만들고 fromJson을 통해 JSON을 변환하는 작업을 만들었습니다.
그럼 fomJson은 어디서 튀어나온걸까요? 다음에 만들 url을 받아오는 class에서 튀어나왔습니다.

```dart
Future<Item> fetchItem() async{
  final url = '여기에는 url을 적어주세요';
  final response = await http.get(url);
  
  print('Iteminfo 통과');
  if (response.statusCode == 200) {
    final jsonBody = json.decode(response.body);
    print('iteminfo 통과222');

    return Item.fromJson(jsonBody); // 이렇게 하면 serverTime 값만 받습니다.
  } else {
    throw Exception('Failed to load post');
  }
}
```

이제 serverTime의 값을 받았으니 Widget에 뿌려주어야겠죠? Stateful이든 Stateless든 잘 뿌려주니 쓰고 싶은걸 쓰면 됩니다.

```dart
class ItemInfo extends StatefulWidget {
  @override
  _ItemInfoState createState() => _ItemInfoState();
}

class _ItemInfoState extends State<ItemInfo> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('itemInfo Interface'),
      ),
      body: Column(
        children: <Widget>[
          Text('Iteminfo Interface'),
          FutureBuilder(
              future: fetchItem(),
            builder: (context, snapshot){
                if(snapshot.hasData){
                  final serverTime = snapshot.data.serverTime; // 이렇게 snapshot으로 serverTime을 한번 받아서 변환해주어야합니다.
                  print(serverTime);
                  return Text('server Time : ' +  serverTime);
                }else if (snapshot.hasError){
                  return Text('error');
                }
                return CircularProgressIndicator();
            },
          ),
        ],
      ),
    );
  }
}
```

이렇게 하면 서버와 통신이 되는 것을 볼 수 있습니다!

# list 형태로 전송되는 json 받아오기
list 형태라 함은
```json
{
  "data": [
    { 
      "title": "백설공주와 난장이",
      "content": "옛날 옛적에",
      "author": "그림 형제"
    },
    { 
      "title": "신데렐라",
      "content": "신데렐라가 살았어요.",
      "author": "샤를 페로"
    },
    { 
      "title": "인어공주",
      "content": "붉은 머릿결의 인어공주는 ",
      "author": "안데르센"
    }
  ],
  "code": "0000",
  "success": true
}
```
(예시 json 만든다고 작가 이름들 검색해본건 안비밀)

이렇게 [] 안에 여러개의 데이터가 반복적으로 있는 json 형태를 말한다!
이를 받아서 뿌려주는 예시를 못찾아서... 이 글이 도움이 되기를 바랍니다 ^^~


우선 서버와 통신하기 위해 필요한 것은 "data"를 받을 class 하나, "data" class안의 "title", "content", "author"를 받아올 class 하나, Url 받을 class 하나, 데이터를 뿌려줄 Stateful 또는 Stateless Widget입니다.
만들 class가 하나 늘어난 것 빼곤 똑같습니다.

data를 담을 클래스를 만들어 봅시당
```dart
class Data {
  String title;
  String content;
  String author;

  Data({this.title, this.content, this.author});

  Data.fromJson(Map<String, dynamic> json) {
    title = json['title'];
    content = json['content'];
    author = json['author'];
  }
  /* toJson 은 보낼 때 사용하는거라 데이터 받기만 하는 지금은 필요가 없습니다.
  Map<String, dynamic> toJson() {
    final Map<String, dynamic> data = new Map<String, dynamic>();
    data['title'] = this.title;
    data['content'] = this.content;
    data['author'] = this.author;
    return data;
  }
  */
}
```
그리고 data를 품고 있는 info class 를 만들어줍니다.

```dart
class Info {
  List<Data> data;
  String code;
  bool success;

  Info({this.data, this.code, this.success});

  Info.fromJson(Map<String, dynamic> json) {
    if (json['data'] != null) {
      data = new List<Data>();
      json['data'].forEach((v) {
        data.add(new Data.fromJson(v));
      });
    }
    code = json['code'];
    success = json['success'];
  }
/* toJson 은 보낼 때 사용하는거라 데이터 받기만 하는 지금은 필요가 없습니다.
  Map<String, dynamic> toJson() {
    final Map<String, dynamic> data = new Map<String, dynamic>();
    if (this.data != null) {
      data['data'] = this.data.map((v) => v.toJson()).toList();
    }
    data['code'] = this.code;
    data['success'] = this.success;
    return data;
  }
 */
}
```

어휴 어렵다... 라고 생각하는 분들을 위한 chance~~ Time~

[json to dart](https://javiercbk.github.io/json_to_dart/) 

이곳에서 쉽게 위 두 개의 클래스를 만들 수 있습니다~ ^^~

url을 통해 json을 가져오는 class를 만들어봅시다~~
```dart
Future<Info> fetchInfo() async {
  final url =
      '통신 할 url 주소를 적습니다.';
  final response = await http.get(url);
  if (response.statusCode == 200) {
    final jsonBody = json.decode(response.body);
    print('통과');
    return Info.fromJson(jsonBody);
  } else {
    throw Exception('Failed to load post');
  }
}
```

이제 뿌려줄 화면만 만들면 되죠~

```dart
Widget _buildList(AsyncSnapshot snapshot, int index) {
  var title = snapshot.data.data[index].title;
  var content = snapshot.data.data[index].content;
  var author = snapshot.data.data[index].author;

  return Text('title : ' + title + '\n' + 'content : ' + content + '\n' + 'author : ' + author + '\n');
}
```

저는 받아온 json을 바탕으로 뿌려줄 위젯을 따로 빼서 만들었습니다. 바로 stateless나 stateful 안에 붙여도 상관없습니다.
나중에 깔끔하게 코딩하는 법을 공부해서 올려 보고 싶군요 ㅠㅠ
그리고 stateless widget을 작성해봅니다.

```dart
class Test extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Test'),
      ),
      body: Center(
        child: Column(
          children: <Widget>[
            Text(' 이것은 잘 되든 못 되든 나오는 텍스트입니다.'),
            FutureBuilder(
                future: fetchInfo(),
                builder: (context, snapshot) {
                  if (snapshot.hasData) {
                    final serverTime = snapshot.data.serverTime;
                    print(serverTime);
                    return Expanded( // column안에 listview를 만들려면 꼭 flexible이나 expanded로 감싸야 하더군요...
                                     //맨날 잊어서 오류나는 주범입니다...   
                        child: ListView.builder(
                      shrinkWrap: true,
                      itemCount: snapshot.data.data.length,
                      itemBuilder: (context, index) => _buildList(snapshot, index),
                    ));
                  } else if (snapshot.hasError) {
                    return Text('${snapshot.error}');
                  }
                  return CircularProgressIndicator(); 
                }),
          ],
        ),
      ),
    );
  }
}
```


이렇게 해서 돌리면 다들 잘 되시나욤? ㅎㅎ 
flutter는 너무 정보가 없어서 제가 공부하면서 공유할 만한 정보를 많이 포스팅 하도록 하겠습니다.
서버로 보내는 방법도 나중에 작성해봐야겠네욤



* 참고 사이트
  * https://pythonkim.tistory.com/128
  * https://flutter.dev/docs/cookbook/networking/fetch-data
  * 그 외 다수...


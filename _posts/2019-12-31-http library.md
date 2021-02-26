---
title: "[Flutter]http 라이브러리 사용법"
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"
---
<br>

예전에 작성한 flutter server 통신하기 게시글과 비슷합니다만, 이번에는 get, post, multipart 부분만 간략하게 작성해서 올립니다.

# 1. GET  


여기서는 json 데이터를 송수신 (및 변환) 하는 부분만 작성하겠습니다.   

```
class Test {
  Future<bool> test() async {
    var client = new http.Client();
    try {
      http.Response response = await client.get('주소');
      if (response.statusCode == 200) {
        final jsonBody = json.decode(response.body); // json 응답 값을 decode
        // jsonBody를 바탕으로 data 핸들링

        return true;
      } else { // 200 안뜨면 에러 
        return false;
      }
    } catch (e) {
      Exception(e);
    } finally {
      client.close();
    }
    return false;
  }
}
```


# 2. POST  
```
class Test {
  Future<bool> test() async {
    var client = new http.Client();
    try {
      http.Response response = await client.post(
          '주소',
        body: {
            'parm1' : 'value1',
          'parm2' : 'value2',
          'parm3' : 'value3'
        }, headers: {
      'Accept': 'application/json' // json 형식만 데이터를 받겠다. 
      }
      );
      if (response.statusCode == 200) {
        final jsonBody = json.decode(response.body); // json 응답 값을 decode
        // jsonBody를 바탕으로 data 핸들링

        return true;
      } else {
        return false;
      }
    } catch (e) {
      Exception(e);
    } finally {
      client.close();
    }
    return false;
  }
}
```


# 3. MULTIPART  
이미지 포함 전송
```
class Test {
  Future<bool> test(File imageFile) async {
  
    var client = new http.Client();
    try {
      MultipartRequest request = new http.MultipartRequest('POST', uri);
      request.fields['parm1'] = value1;
      request.fields['parm2'] = value2;
      request.fields['parm3'] = value3;
      request.fields['parm4'] = value4;
      request.fields['parm5'] = value5;
      
      //요청에 이미지 파일 추가
      request.files.add(await http.MultipartFile.fromPath('파일 이름', imageFile.path));
      
      
      response = await request.send();
      
      if (response.statusCode == 200) {
        final jsonBody = json.decode(response.stream.bytesToString()); // json 응답 값을 decode
        // jsonBody를 바탕으로 data 핸들링

        return true;
      } else {
        return false;
      }
    } catch (e) {
      Exception(e);
    } finally {
      client.close();
    }
    return false;
  }
}
```





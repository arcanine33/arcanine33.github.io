---
title: "[Flutter]dio 라이브러리 사용법"
categories: flutter
toc: true
toc_label: "목차"
toc_icon: "cog"
---
<br>
http 라이브러리를 응용해서 만든 dio 라이브러리가 있다.  
http와 같이 서버 통신을 할 수 있는 라이브러리인데,  
dio 라이브러리를 도입했다가 http 라이브러리에서는 전송되는 데이터가 길다는 이유로!(414 error) 다시 http 라이브러리로 롤백했다....


http와 크게 다른 것이 없는데, dio로 get, post, 그리고 사진을 포함한 전송을 간략하게 적어보겠다. 




# 1. GET
```
class Test {
  Future<bool> test() async {
    Dio dio = new Dio();
    try {
      Response response = await dio.get('주소');
      if (response.statusCode == 200) {
        final jsonBody = json.decode(response.data); // http와 다른점은 response 값을 data로 받는다. 
        // jsonBody를 바탕으로 data 핸들링

        return true;
      } else { // 200 안뜨면 에러 
        return false;
      }
    } catch (e) {
      Exception(e);
    } finally {
      dio.close();
    }
    return false;
  }
}
```

<br/>


# 2. POST
```
class Test {
  Future<bool> test() async {
    Dio dio = new Dio();
    try {
      Response response = await dio.post(
      '주소',
      queryParameters: {
          'parm1' : 'value1',
          'parm2' : 'value2',
          'parm3' : 'value3'
        }
      );
      if (response.statusCode == 200) {
        final jsonBody = json.decode(response.data); // http와 다른점은 response 값을 data로 받는다. 
        // jsonBody를 바탕으로 data 핸들링

        return true;
      } else { // 200 안뜨면 에러 
        return false;
      }
    } catch (e) {
      Exception(e);
    } finally {
      dio.close();
    }
    return false;
  }
}
```

<br/>


# 3. MULTIPART
```
class Test {
  Future<bool> test(File imageFile) async {
    FormData formData;
    Dio dio = new Dio();
    try {
    
      formData = FormData.fromMap({
          "file": imageFile != null ? await MultipartFile.fromFile(imageFile.path) : null
        });
    
    
      Response response = await dio.post(
      '주소',
      queryParameters: {
          'parm1' : 'value1',
          'parm2' : 'value2',
          'parm3' : 'value3'
        },
        data : formData
      );
      if (response.statusCode == 200) {
        final jsonBody = json.decode(response.data); // http와 다른점은 response 값을 data로 받는다. 
        // jsonBody를 바탕으로 data 핸들링

        return true;
      } else { // 200 안뜨면 에러 
        return false;
      }
    } catch (e) {
      Exception(e);
    } finally {
      dio.close();
    }
    return false;
  }
}
```


이렇게 작업을 했다.


개인적으로 dio 보다 http를 추천한다... 

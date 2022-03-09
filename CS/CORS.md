# CORS
[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL17-CORS)

<br>

## ✔️Intro

오늘은 제가 위코드 초기 때, 제 머리를 아프게 했던 CORS에 대한 내용입니다. 출처로 [Mozilla](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)와 [PyPi](https://pypi.org/project/django-cors-headers/) 를 참고하였습니다.

<br>

## ✔️Cross Origin Resource Sharing

CORS의 풀네임이며, 의미로는 추가 HTTP헤더를 사용하여, 한 출처에서 실행 중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 알려주는 브라우저 체제입니다. 웹 애플리케이션은 리소스가 자신의 출처(Origin)와 다를 때 교차 출처 HTTP 요청을 실행합니다.

제가 겪은 실제 사례를 예로 들자면, 위코드에서 프로젝트를 앞두고 프론트엔드-백엔드 통신을 하는 세션이 있었습니다. 프론트엔드는 리액트를 사용하였고, fetch함수에 제가 설정한 URL로 요청을 보냈는데 Cross-Origin.. 하면서 계속 에러가 발생했습니다. 근본적인 원인을 보니, 리액트의 기본 도메인은 http://localhost:5000 이었고, Django의 도메인은 http://localhost:8000 이었는데, 포트번호가 달라서 안됐던 것이었습니다. 즉, 제 출처는 :8000이고 프론트엔드의 출처는 :5000이니 다른 곳에서 요청이 들어와 연동이 안됐던 것이었습니다. 그래서 출처가 다른 것에 대한 요청을 허용해주기 위해 제가 세팅을 몇 개 더 해줬어야 했는데, 이 부분은 Django 설정 때 기술하겠습니다.

즉, 애플리케이션은 자신의 출처와 동일해야만 불러올 수 있으며, 다른 출처의 리소스를 불러오려면 그 출처에서 올바른 CORS 헤더를 포함한 응답을 리턴해야 합니다. 

Mozilla의 예를 보면 GET 요청을 보냈고, 보낸 사람의 Origin은 https://foo.example 입니다.

<br>

```shell
GET /resources/public-data/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.14; rv:71.0) Gecko/20100101 Firefox/71.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Connection: keep-alive
Origin: https://foo.example
```

<br>

요청에 대해 응답이 이루어졌다면, 응답의 헤더에 Access-Control-Allow-Origin이라는 Key에 Value가 "*"인 한 쌍이 생성됩니다. 쉽게 말해 모든 도메인에 접근해준다는 의미인데, 이 부분은 내가 원하는 도메인만 접근허용 하도록 수정 가능합니다.
```shell
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 00:23:53 GMT
Server: Apache/2
Access-Control-Allow-Origin: *
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Transfer-Encoding: chunked
Content-Type: application/xml
```

<br>

## ✔️CORS in Django

Django에서 CORS를 설정하기 위해 우선 django-cors-headers를 설치해야 합니다.
```shell
pip install django-cors-headers
```
그 다음 settings.py로 가서 설치한 내용을 추가해줍니다.

```python
'''
INSTALLED_APPS에는 corsheaders라는 이름으로 추가해야합니다.
'''
INSTALLED_APPS = [
    ... ,
    "corsheaders"
]

...

'''
MIDDLEWARE에는 가장 높은 곳에 설정해주는 것이 좋습니다.
HTTP요청이 들어오면 MIDDLEWARE의 위에서부터 훑고 응답을 뿌려주기 때문에
문제가 발생하지 않게 해주기 위해 최상단에 설정합니다.
'''
MIDDLEWARE = [
    "corsheaders.middleware.CorsMiddleware",
    ...
]

...

'''
첫 번째는 모든 Origin에 대해 접근 허용한다는 설정입니다.
두 번째는 내가 접근허용할 도메인을 설정하겠다는 의미입니다. 이전에는 CORS_ORIGIN_WHITELIST라고 설정했는데, 바뀌었다고 합니다.
세 번재는 타 도메인의 요청이 들어올 때 쿠키가 포함할지에 대한 설정입니다. Default는 False여서, True로 설정할 경우에만 사용하시면 됩니다.
'''
CORS_ORIGIN_ALLOW_ALL = True or False
CORS_ALLOWED_ORIGINS = [
    "http://example.com",
    "http://localhost:5000"
]
CORS_ORIGIN_CREDENTIALS = True or False

'''
실제 요청을 허용해 줄 메소드 리스트를 설정해줍니다.
해당 메소드의 경우, 타 도메인으로부터 요청이 와도 접근 허용해주겠다는 의미입니다.
특히, OPTIONS는 꼭 추가해야 합니다. 요청이 들어왔을 때 경우에 따라 OPTIONS 메소드를 거쳐 식별한 뒤 전송될 수 있기 때문입니다.
'''
CORS_ALLOW_METHODS = [
    "DELETE",
    "GET",
    "OPTIONS",
    "PATCH",
    "POST",
    "PUT"
]

'''
실제 요청에 대해 사용될 비표준 HTTP 헤더리스트들입니다.
예를 들어 Authorization의 경우, 유저의 인증/인가때만 필요하기 때문에 표준(항상 기본으로 깔릴)이 될 수 없습니다.
'''
CORS_ALLOW_HEADERS =[
    "accept",
    "accept-encoding",
    "authorization",
    "content-type",
    "dnt",
    "origin",
    "user-agent",
    "x-csrftoken",
    "x-requested-with"
]
```

그 외 여러 설정들인 PyPi를 참고하시면 됩니다.

<br>

## ✔️마치며..

개발자의 꿈을 갖기 전까지는 그냥 URL클릭하면 다 되는 줄 알았는데, 직접 개발자가 되어 보니 설정해줄 것이나 알아야 할 것이 많다는 걸 느꼈습니다. 서비스를 개발하는 입장에서 이러한 문제들로 인해 사용자가 불편함을 겪지 않게끔 많이 알아야 할 것 같습니다.

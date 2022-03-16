# WSGI & ASGI

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL20-WSGI-ASGI)

<br>


## ✔️ Intro

코로나가 정말 기승입니다. 저희 회사는 1주일동안 재택근무로 전환되었습니다. 인생 첫 재택근무인데 편하긴 하네요 허허

이번 포스팅에선 Django 프로젝트를 시작하면 보이는 wsgi/asgi부터 비동기구현까지 해볼 예정입니다.

<br>

## ✔️ 웹 서버

웹 서버는 웹 브라우저의 정적 요청과 동적 요청을 처리하는 서버입니다. 대표적인 웹 서버에는 Apache, Nginx 등이 있습니다. Django는 Nginx가 잘 어울리며, 저희 회사에서도 Nginx를 사용하고 있습니다. 

그리고 서버에 요청할 때, 정적 페이지와 동적 페이지에 요청을 합니다. 정적페이지에 대해 쉽게 설명하자면 css, js, jpg같은 파일을 요청하는 걸 정적페이지 요청이라고 합니다. 그리고 응답이 수시로 바뀌는 요청을 동적 페이지 요청이라고 합니다.

<br>

## ✔️ WSGI

Web Server Gateway Interface의 약자로 웹 서버 소프트웨어와 파이썬으로 작성된 웹 응용 프로그램 간의 표준 인터페이스입니다. 그리고 동적 페이지 요청을 처리하기 위해 호출합니다. 

Django에서 WSGI 서버는 wsgi.py 파일을 경유하여 프로그램을 호출합니다.

<br>

## ✔️ ASGI

Asynchronous Server Gateway Interface의 약자로 비동기 가능한 Python 웹서버, 프레임워크 및 응용 프로그램간의 표준 인터페이스를 제공하기 위한 WSGI 다음으로 나오게 되었습니다. WSGI가 동기 Python 앱에 대한 표준을 제공했다면, ASGI는 WSGI 이전 버전과의 호환성 구현과 여러 서버 및 애플리케이션 프레임워크를 통해 비동기 및 동기 앱 모두에 대한 표준을 제공합니다. 

쉽게 말해, WSGI에서는 지원하지 않았던 비동기에 대해서도 가능하게 해준다는 의미이며, Uvicorn을 설치하여 실행합니다.

gunicorn과 ASGI-Uvicorn 배포와 async, uvloop 함수를 이용한 비동기처리에 대해 해보겠습니다.

<br>

## ✔️ 동기 vs 비동기

동기/비동기에 대해 한 번 쯤은 들어보셨을거라 생각합니다. 저는 개발을 처음 배울 때 새로고침이 일어나면 동기, 안 일어나면 비동기인줄 알았습니다.

제가 동기/비동기에 대해 가장 쉽게 이해됐던 것이 커피 줄이었습니다. 내 앞에 있는 사람이 커피를 주문하고 나올 때 까지 기다렸다가 받아간다면 이건 동기입니다. 내가 기다리는 동안 아무것도 못 하기 때문입니다. 일 있어서 주문 줄을 이탈해버리면 다시 맨 뒤로 가야하기 때문입니다. 비동기의 경우, 주문하고 진동벨을 받아가는 것을 생각하시면 됩니다. 커피가 나오는 동안 다른 일을 할 수 있기 때문입니다.

즉, 기존 작업이 모두 끝난 다음 실행된다면 동기이고 기존 작업이 끝나지 않아도 실행할 수 있다면 비동기입니다.

<br>

## ✔️ Gunicorn with Django

[Django 공식문서](https://docs.djangoproject.com/ko/3.2/howto/deployment/wsgi/gunicorn/)에는 gunicorn과 Django를 사용하는 방법이 대해 나와있습니다.

Gunicorn이란, wsgi.py를 호출하기 위해선 WSGI 서버가 필요한데, 이 서버 역할을 해주는 웹 서버입니다.

우선, gunicorn을 설치합니다
```shell
pip install gunicorn
```

그리고 아래 명령어를 입력하여 Gunicorn에서 Django를 실행합니다.

```shell
gunicorn projectname.wsgi
```

실행했다면, URL을 입력하여 서버가 잘켜졌는지 테스트해봅니다. projectname은 내가 처음 Django 프로젝트를 만들 때 시작한 이름을 써주시면 됩니다. 저는 blogs라고 시작했습니다. 이렇게 설정하고 URL을 입력했을 때 잘 작동되면 성공입니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fcnlsa9%2Fbtrv6j1OgoN%2FTbpLe7dBd0nTjrEeydgKhk%2Fimg.png)

아니면 다른 명령어를 사용해도 됩니다.

```shell
gunicorn projectname.wsgi:application -- bind 0:8000
```

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcFp7mi%2Fbtrv3f0qqVo%2FIc0vDhD20dmXeLZSn0MhL0%2Fimg.png)

<br>

## ✔️ Uvicorn와 Django

Uvicorn은 속도를 중시하는 uvloop와 http tools에 기반을 둔 ASGI 서버입니다.

[uvloop](https://uvloop.readthedocs.io)는 내장된 asyncio 이벤트 루프의 빠른 대체제입니다. uvloop와 asyncio를 사용하면 파이썬에서 고성능 네트워킹 코드를 훨씬 쉽게 작성할 수 있습니다. uvloop는 async를 빠르게 만듭니다. 자세한 사용은 아래에서 하겠습니다.
 
우선, Uvicorn을 사용하기 위해 설치를 해줍니다.

```shell
pip install uvicorn gunicorn
```

그리고 설치한 Uvicorn을 실행하기 위해 다음 명령어를 입력합니다.

```shell
gunicorn projectname.asgi:application -k uvicorn.workers.UvicornWorker
```

projectname에는 Django 프로젝트를 시작할 때 만든 프로젝트명을 입력합니다. 저는 blogs로 만들어서 blogs.asgi 가 됩니다.
그리고 실행했을 때 이렇게 잘 돌아가는 걸 확인할 수 있습니다.
 
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FTvljz%2Fbtrwb5vNHPd%2F6VK6K7W3k5UD2H4FfYna30%2Fimg.png)

<br>

## ✔️ Django Celery

<br>

## ✔️ async & uvloop

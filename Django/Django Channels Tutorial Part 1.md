# Django Channels Tutorial Part 1

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL21-Django-Channels-Tutorial-Part-1)

<br>

## ✔️Intro

이전 포스팅인 WSGI & ASGI에서 동기/비동기에 대해 알아봤는데, 바로 이걸 위한 빌드업이었습니다. Django Channels란, 비동기 형태의 view를 제공하며, HTTP뿐만 아니라 기간 연결이 필요한 프로토콜(WebSocket, MQTT, 챗봇 등)도 처리할 수 있습니다. 공식문서를 참고하여 한 번 구현해보겠습니다.

<br>

## ✔️Install

아래 명령어를 입력하여, Channels를 설치해줍니다.

```shell
python -m pip install -U channels
```

그다음 settings.py의 INSTALLED_APPS에 channels를 추가해줍니다.

```python
INSTALLED_APPS = [
    'django.contrib.auth',
    ...
    'channels'
]
```

추가되었다면, asgi.py에 application을 추가해줍니다.

```python
import os

from channels.routing import ProtocolTypeRouter
from django.core.asgi import get_asgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "channels.settings")

application = ProtocolTypeRouter({
    "http": get_asgi_application(),
    })
```

DJANGO_SETTINGS_MODULE에서 channels.settings라고 설정한 건 Django 프로젝트를 처음 시작할 때 프로젝트명을 쓰면 됩니다. 저는 공교롭게도 프로젝트명을 channels라고 만들어서 chanels.settings라고 사용하게 되었습니다.

마지막으로 settings.py에 아래와 같은 세팅을 해줍니다.

```python
ASGI_APPLICATION = "channels.asgi.application"
```

기본 세팅이 끝났습니다. 이제 실시간 채팅을 위한 chat 앱을 생성해서 시작해보겠습니다.

<br>

## ✔️Creating the Chat app

<br>

이제 Chat이라는 앱을 생성해서 본격적으로 시작해보겠습니다.

```python
python manage.py startapp chat
```

그러면 chat이라는 app에 models.py, views.py 등 생겨나는데 __init__.py, views.py만 제외하고 다 삭제합니다.


![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fvt9Zt%2FbtrwnrNfRvP%2FAs5fcFW0QA760KGnAM0Qhk%2Fimg.png)

이렇게 두 개만 남도록 합니다. 그리고 chat 앱을 INSALLED_APPS에 추가합니다.

```python
INSTALLED_APPS = [
    'django.contrib.auth',
    ...,
    'channels',
    'chat'
]
```

<br>

## ✔️Add the index view

그리고 chat앱에 디렉토리를 아래와 같이 추가해줍니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FclvLKd%2FbtrwooicyZi%2Fc5zdR60u6SFn7omr8NlMbk%2Fimg.png)


그리고 Index.html에 아래 코드를 그대로 입력해줍니다. 

```javascript
<!-- chat/templates/chat/index.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <title>Chat Rooms</title>
</head>
<body>
    What chat room would you like to enter?<br>
    <input id="room-name-input" type="text" size="100"><br>
    <input id="room-name-submit" type="button" value="Enter">

    <script>
        document.querySelector('#room-name-input').focus();
        document.querySelector('#room-name-input').onkeyup = function(e) {
            if (e.keyCode === 13) {  // enter, return
                document.querySelector('#room-name-submit').click();
            }
        };

        document.querySelector('#room-name-submit').onclick = function(e) {
            var roomName = document.querySelector('#room-name-input').value;
            window.location.pathname = '/chat/' + roomName + '/';
        };
    </script>
</body>
</html>
```

html 작성완료됐으면 views.py에서 index.html을 보여줄 수 있도록 render합니다. (렌더링한다고 보면 됩니다)

```python
from django.shortcuts import render

def index(request):
    return render(request, 'chat/index.html')
```

View를 호출하기 위해 URL설정이 필요하겠죠? urls.py를 만들어서 설정해줍니다.

```python
# chat/urls.py

from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
]

# root urls
from django.conf.urls import include
from django.urls import path
from django.contrib import admin

urlpatterns = [
    path('chat/', include('chat.urls')),
    path('admin/', admin.site.urls),
]
```

마지막으로 서버를 켜서 http://localhost:8000/chat/ 이라고 인터넷에 입력하면 이렇게 화면이 나옵니다

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F6dSuE%2FbtrwpsYWTHa%2Fu1ln9xn6pdqwd1mWcEPO60%2Fimg.png)

여기까지가 기본 세팅의 끝입니다. 이제 직접 챗 서버를 도입해보도록 하겠습니다.

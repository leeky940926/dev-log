# Django Channels

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL21-Django-Channels)

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


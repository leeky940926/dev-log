# Django Channels Tutorial Part 2 & 3

[티스토리 포스팅 바로가기](https://kyleeee.tistory.com/entry/TIL21-Django-Channels-Tutorial-Part-2-3)

<br>


기본세팅이 되었으니, 채팅을 할 수 있는 방을 만들어보겠습니다.

chat/templates/chat에 room.html 파일을 생성하고 코드는 아래와같이 입력합니다.

```javascript
<!-- chat/templates/chat/room.html -->
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8"/>
    <title>Chat Room</title>
</head>
<body>
    <textarea id="chat-log" cols="100" rows="20"></textarea><br>
    <input id="chat-message-input" type="text" size="100"><br>
    <input id="chat-message-submit" type="button" value="Send">
    {{ room_name|json_script:"room-name" }}
    <script>
        const roomName = JSON.parse(document.getElementById('room-name').textContent);

        const chatSocket = new WebSocket(
            'ws://'
            + window.location.host
            + '/ws/chat/'
            + roomName
            + '/'
        );

        chatSocket.onmessage = function(e) {
            const data = JSON.parse(e.data);
            document.querySelector('#chat-log').value += (data.message + '\n');
        };

        chatSocket.onclose = function(e) {
            console.error('Chat socket closed unexpectedly');
        };

        document.querySelector('#chat-message-input').focus();
        document.querySelector('#chat-message-input').onkeyup = function(e) {
            if (e.keyCode === 13) {  // enter, return
                document.querySelector('#chat-message-submit').click();
            }
        };

        document.querySelector('#chat-message-submit').onclick = function(e) {
            const messageInputDom = document.querySelector('#chat-message-input');
            const message = messageInputDom.value;
            chatSocket.send(JSON.stringify({
                'message': message
            }));
            messageInputDom.value = '';
        };
    </script>
</body>
</html>
```

만든 html을 불러올 수 있게 views.py에도 로직을 짜야겠죠? views.py에 아래와 같이 입력해줍니다.

```python
def room(request, room_name):
    return render(request, 'chat/room.html', {
        'room_name': room_name
    })
```

그 다음, 요청을 보낼 수 있게 URL을 설정해야 합니다.

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('<str:room_name>/', views.room, name='room'),
]
```

이제 서버를 다시켜봅니다. http://localhost:8000/chat/ 에서 방을 입력하고 들어가면 내가 입력한 방이름의 URL로 붙게 됩니다. 이미지를 보면 저는 lobby라고 입력했으니 chat/lobby/ 이렇게 되는 것입니다. 

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbYSJhA%2FbtrwmZKhY4h%2FB165KKOTD5EYQEkXOD8pNK%2Fimg.png)


다만, 들어온방에서 무슨글자를 입력해도 아직 아무것도 화면에 나오지 않습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbUrF0l%2Fbtrwl8UCWIa%2FYqymYgIldoj8tSygdN9byk%2Fimg.png)

room.html을 잘 보면 URL이 ws:// 로 시작하는데 여기서 요구하는 URL과 맞지 않기 때문입니다. 즉, 요청이 제대로 들어가지 않은것입니다. 

Django를 해보면 아실 내용인데, HTTP 요청이 들어오면 URLcof를 참조하여 해당 View로 이동합니다. 마찬가지로 채널이 WebSocket 연결을 수락하면 루트 라우팅을 참고하여 유저를 조회한다음 다양한 기능을 호출하여 이벤트 처리를 합니다. Channels 공식문서에서는 유저를 Consumer라고 부르며 저는 편의상 소비자라고 하겠습니다.

<br>

## ✔️Create Consumer

chat 디렉토리에 consumers.py를 만듭니다. 

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbZ5EdE%2FbtrwnOBuyFs%2FofTbqzAM6LGBTPLWqGlmk1%2Fimg.png)

그리고 consumers.py에 아래와 같은 코드를 입력합니다. 이 코드는 모든 연결을 수락하고 클라이언트에서 메시지를 수신하며 해당 메시지를 동일한 클라이언트에 다시 동기화하는 WebSocket 소비자입니다. 

```python
import json
from channels.generic.websocket import WebsocketConsumer

class ChatConsumer(WebsocketConsumer):
    def connect(self):
        self.accept()

    def disconnect(self, close_code):
        pass

    def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']

        self.send(text_data=json.dumps({
            'message': message
        }))
```

이렇게 소비자를 만들었으면, 소비자에 대한 경로가 있는 앱에 대한 라우팅을 만들어야 합니다. consumers.py와 동일한 레벨의 디렉토리에 routing.py를 만들어줍니다.

```python
# chat/routing.py
from django.urls import re_path

from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/chat/(?P<room_name>\w+)/$', consumers.ChatConsumer.as_asgi()),
]
as_asgi()는 각 사용자 연결에 대해 소비자를 객체화할 ASGI 응용 프로그램을 얻기 위해 class method를 호출합니다. 이는 Django에서 as_view()와 같은 역할이라고 합니다.  그 다음 asgi.py에서 websocket을 설정해줍니다.
import os

from channels.auth import AuthMiddlewareStack
from channels.routing import ProtocolTypeRouter, URLRouter
from django.core.asgi import get_asgi_application
import chat.routing

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "mysite.settings")

application = ProtocolTypeRouter({
  "http": get_asgi_application(),
  "websocket": AuthMiddlewareStack(
        URLRouter(
            chat.routing.websocket_urlpatterns
        )
    ),
})
```

이 루트 라우팅 구성은 채널 서버에 연결할 때, ProtocolTypeRouter 먼저 연결 유형을 검사하도록 지정합니다.  WebSocket 연결인 경우 AuthMiddlewareStack의 값을 호출하게 되는 것입니다. migrate 후 서버를 켜면 될 것 같지만 단계가 더 남아있습니다.

바로, Consumer가 작동하려면 서로 대화할 수 있는 인스턴스가 여러개 있어야 한다는 것입니다.
이를 채널 레이어 활성화라고 하는데, 채널 레이어는 일종의 통신 시스템입니다. 채널레이어는 채널/그룹이라는 Abstraction을 제공합니다. 채널은 메시지를 보낼 수 있는 사서함입니다. 채널마다 채널명이 있으며 채널에 있으면 누구나 메시지를 보낼 수 있습니다. 게임의 같은 채널에서 서로 이야기를 나누는 걸 생각하시면 됩니다. 그룹은 관련 채널의 그룹을 말합니다. 그룹에 채널을 추가/제거할 수 있으며 그룹 내 채널에 메시지를 보낼 수 있습니다.

그래서, channelss_redis라는 걸 설치해야 한다고 합니다.

```shell
pip install channels_redis
```

설치 후 settings.py에 아래와 같이 세팅을 추가해줍니다.

```python
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels_redis.core.RedisChannelLayer',
        'CONFIG': {
            "hosts": [('127.0.0.1', 6379)],
        },
    },
}
```

설정되었으면, 채널 레이어가 Redis와 통신할 수 있는지 확인합니다. Shell을 열어서 아래 명령을 실행해봅니다.

```shell
>>> import channels.layers
>>> channel_layer = channels.layers.get_channel_layer()
>>> from asgiref.sync import async_to_sync
>>> async_to_sync(channel_layer.send)('test_channel', {'type': 'hello'})
>>> async_to_sync(channel_layer.receive)('test_channel')
{'type': 'hello'}
```

그리고 consumers.py의 코드도 아래와 같이 수정해줍니다.

```python
import json
from asgiref.sync import async_to_sync
from channels.generic.websocket import WebsocketConsumer

class ChatConsumer(WebsocketConsumer):
    def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = 'chat_%s' % self.room_name

        # Join room group
        async_to_sync(self.channel_layer.group_add)(
            self.room_group_name,
            self.channel_name
        )

        self.accept()

    def disconnect(self, close_code):
        # Leave room group
        async_to_sync(self.channel_layer.group_discard)(
            self.room_group_name,
            self.channel_name
        )

    # Receive message from WebSocket
    def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']

        # Send message to room group
        async_to_sync(self.channel_layer.group_send)(
            self.room_group_name,
            {
                'type': 'chat_message',
                'message': message
            }
        )

    # Receive message from room group
    def chat_message(self, event):
        message = event['message']

        # Send message to WebSocket
        self.send(text_data=json.dumps({
            'message': message
        }))
```

사용자가 메시지를 보내면 JS함수가 WebSocket을 통해 메시지를 ChatConsumer에게 전송합니다. ChatConsumer는 해당 메시지를 수신하여 방 이름에 해당하는 그룹으로 전달합니다. 그 다음 같은 그룹의 모든 ChatConsumer는 구룹에서 메시지를 수신하고 WebSokcet을 통해 JS로 다시 전달하여 채팅로그에 추가합니다.

<br>

## ✔️ChatConsumer 추가 설명

* self.scope['url_route']['kwargs']['room_name'] <br>
소비자에 대한 WebSocket 연결을 room_name 경로에서 매개변수를 가져옵니다.
모든 소비자는 특히 URL 경로 및 현재 인증된 사용자의 위치 또는 키워드 인수를 포함하여 연결에 대한 정보를 포함하는 범위가 있습니다.

<br>

* self.room_group_name = ... <br>
인용이나 이스케이프없이 사용자 지정 방 이름에서 직접 채널 그룹 이름을 만듭니다.
그룹 이름에는 문자/숫자/하이푼/마침표만 사용할 수 있습니다.

<br>

* asyn_to_sync(self.channel_layer.group_add) <br>
그룹에 가입합니다.
ChatConsumer는 동기식이지만 비동기식 채널 계층 메서드를 호출하기 때문에 async_to_sync()의 wrapper가 필요합니다.
그룹이름은 ASCII 영어/숫자/하이푼/마침표로 제한됩니다.

<br>

* self.accept <br>
WebSocket 연결을 수락합니다
connect() 메소드 내에서 accept()를 호출하지 않으면 연결이 거부되고 닫힙니다. 예를 들어 요청하는 사용자가 요청 작업을 수행할 권한이 없으면 연결을 거부할 수 있습니다.
연결을 수락하기로 한 경우, connect()의 마지막 작업으로 accept()를 호출하는 것이 좋습니다.

<br>

* async_to_sync(self.channel_layer.group_discard) <br>
그룹을 탈퇴합니다

<br>

* async_to_sync(self.channel_layer.group_send) <br>
그룹에 이벤트를 보냅니다
type 이벤트에는 이벤트를 수신하는 소비자에서 호출되어야 하는 메서드의 이름에 해당하는 특수 키가 있습니다.

<br>

## ✔️비동기로 만들어보기

비동기를 만들기 위해 consumers.py를 아래와 같이 변경하세요

```python
# chat/consumers.py
import json
from channels.generic.websocket import AsyncWebsocketConsumer

class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.room_name = self.scope['url_route']['kwargs']['room_name']
        self.room_group_name = 'chat_%s' % self.room_name

        # Join room group
        await self.channel_layer.group_add(
            self.room_group_name,
            self.channel_name
        )

        await self.accept()

    async def disconnect(self, close_code):
        # Leave room group
        await self.channel_layer.group_discard(
            self.room_group_name,
            self.channel_name
        )

    # Receive message from WebSocket
    async def receive(self, text_data):
        text_data_json = json.loads(text_data)
        message = text_data_json['message']

        # Send message to room group
        await self.channel_layer.group_send(
            self.room_group_name,
            {
                'type': 'chat_message',
                'message': message
            }
        )

    # Receive message from room group
    async def chat_message(self, event):
        message = event['message']

        # Send message to WebSocket
        await self.send(text_data=json.dumps({
            'message': message
        }))
```

* ChatConsumer는 AsyncWebsocketConsumer를 상속받는 것으로 바뀌었습니다.
* 모든 함수는 def가 아닌 async def로 바꿔줍니다.
* await는 I/O을 측정하는 비동기함수를 호출하기 위해 사용됩니다. 
* async_to_sync는 더이상 필요하지 않습니다.

<br>

## ✔️마치며..

Django Channels는 제가 위코드에서 웹소켓을 해보기 위해 공부할까 했었던 것이었습니다. 다만 프로젝트 일정이 빠듯해서 하지 못 했는데 지금이라도 하게 되어 다행입니다. 한 번 했다고 끝나는 게 아닌 꾸준히 하며 익혀야 할 것 같습니다.

